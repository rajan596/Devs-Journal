# Problem Statement - Design a multithreaded job scheduler

- User should be able to schedule Jobs..
    - One time Job with initial delay
    - Recurring event like every 5 mins
    - Recirring event with some delay
- A job can have multiple tasks, and each task can have a pre-requisite task as well. 
- Jobs should run in as much parallelization as possible.

# Real life Example
- SpringBoot provides annotation based scheduling functionality
- References: https://spring.io/guides/gs/scheduling-tasks

```java
	@Scheduled(fixedRate = 5000)
	public void sync() {
		log.info("The time is now {}", dateFormat.format(new Date()));
	}
```

# Entities
- JobScheduler
- Job (initialDelay, fixedDelay, fixedInterval) [Assumption: All duration in seconds]
- WorkerThread (for running jobs)
- JobType (ONETIME | RECURRING)

# Design
- We will need to make sure Job execution happens in background and do not block main thread
- Whenever any Job is added we will need to keep calculate its `startTime` and add it to `PriorityQueue` so jobs are sorted in ASC order of execution time.
- This problem is `Thread` and `Concurrency` handling heavy.
- We will need to pass Lambds / Runnable to job so when time comes same can be executed
- Make sure we create only one JobScheduler across application to avoid creating so many thread pools. We can use `Singleton` to avoid this.
- We will need to have limited thread to execute all scheduled work and it can be configurable like 3 to 10 to avoid any major impact on main thread processing.

# Code

```java
package com.devsjournal.lld;

import java.util.ArrayList;
import java.util.Date;
import java.util.List;
import java.util.PriorityQueue;
import java.util.concurrent.ExecutorService;
import java.util.concurrent.Executors;
import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public enum JobScheduler {
    INSTANCE;
    List<Job> jobs;
    PriorityQueue<Job> scheduledJobs;
    ExecutorService executorService;
    private static final int MAX_CONCURRENCY = 3;

    private static ReadWriteLock jobsLock = new ReentrantReadWriteLock();
    private static ReentrantLock scheduledJobsLock = new ReentrantLock();

    JobScheduler() {
        this.jobs = new ArrayList<>();
        this.scheduledJobs = new PriorityQueue<>((l, r) -> Math.toIntExact(l.nextScheduledTime - r.nextScheduledTime));
        this.executorService = Executors.newFixedThreadPool(MAX_CONCURRENCY);
    }

    public void add(Job job) {
        jobsLock.writeLock().lock();
        jobs.add(job);                   // Critical section
        jobsLock.writeLock().unlock();
    }

    public void startScheduler() throws InterruptedException {
        System.out.println(String.format("[%s][%s] ** Job Scheduler Started **", Thread.currentThread().getName(), new Date()));
        while (true) {
            if (!executeJob()) { // Execute one job
//                System.out.println(String.format("[%s][%s] Taking a nap ", Thread.currentThread().getName(), new Date()));
                Thread.sleep(1000); // If no job executed then sleep for a while 1 Sec is more than enough to check for next timestamp
            }
            rescheduleJobs();
        }
    }

    private boolean executeJob() {
        long currentTime = System.currentTimeMillis() / 1000;
        Job jobToExecute;
        try {
            scheduledJobsLock.lock();

            if (!scheduledJobs.isEmpty() && scheduledJobs.peek().nextScheduledTime <= currentTime) {
                jobToExecute = scheduledJobs.peek();
                scheduledJobs.remove();
                System.out.printf("[%s][%s] Scheduling Job: %s. Jobs in Queue: %s\n",Thread.currentThread().getName(),new Date(), jobToExecute, scheduledJobs.size());
                jobToExecute.setStatus(JobStatus.SCHEDULED);
                executorService.submit(() -> {
                    jobToExecute.runnable.run();
                    jobToExecute.increaseExecutionCount();
                    jobToExecute.setStatus(JobStatus.NEEDS_SCHEDULING);
                });
                return true;
            } else {
                return false;
            }
        } finally {
            scheduledJobsLock.unlock();
        }
    }

    private void rescheduleJobs() {
        jobsLock.readLock().lock();
        scheduledJobsLock.lock();
        for (Job job : this.jobs) {
            if (job.getStatus() == JobStatus.NEEDS_SCHEDULING) {
                if (job.jobType == JobType.RECURRING || (job.jobType == JobType.ONE_TIME && job.executionCount==0)) {
                    job.updateAndGetNextScheduledTime();
                    job.setStatus(JobStatus.SCHEDULED);
                    scheduledJobs.add(job);
                } else if(job.jobType == JobType.ONE_TIME && job.executionCount > 0){
                    job.setStatus(JobStatus.EXECUTED);
                    System.out.printf("[%s][%s] Job finished execution: %s\n", Thread.currentThread().getName(), new Date(), job);
                }
            }
        }
        scheduledJobsLock.unlock();
        jobsLock.readLock().unlock();
    }
}

class Job {
    JobType jobType;
    int initialDelay;
    int fixedDelay;
    int fixedInterval;
    Runnable runnable;
    long nextScheduledTime; // In epoc
    JobStatus status;
    String jobName;
    Integer executionCount; // To maintain how many times job has been executed. Helpful especially for One time Job to mark it as Executed

    public Job(String jobName,JobType jobType, int initialDelay, int fixedDelay, int fixedInterval, Runnable runnable) {
        this.jobName = jobName;
        this.jobType = jobType;
        this.initialDelay = initialDelay;
        this.fixedDelay = fixedDelay;
        this.fixedInterval = fixedInterval;
        this.runnable = runnable;
        this.nextScheduledTime = System.currentTimeMillis() / 1000 + initialDelay;
        this.status = JobStatus.NEEDS_SCHEDULING;
        this.executionCount = 0;
    }

    public JobStatus getStatus() {
        return status;
    }

    public void setStatus(JobStatus status) {
        this.status = status;
    }

    public long updateAndGetNextScheduledTime() {
        if (fixedDelay > 0) {
            this.nextScheduledTime = System.currentTimeMillis() / 1000 + fixedDelay;
        } else if (fixedInterval > 0) {
            this.nextScheduledTime = this.nextScheduledTime + fixedInterval;
        }
        return this.nextScheduledTime;
    }

    public void increaseExecutionCount() {
        this.executionCount++;
    }

    @Override
    public String toString() {
        return "Job{" +
                "jobName='" + jobName + '\'' +
                ", status=" + status +
                ", executionCount=" + executionCount +
                ", jobType=" + jobType +
                '}';
    }

}

enum JobType {
    ONE_TIME,
    RECURRING
}

enum JobStatus {
    SUBMITTED,          // Submitted for scheduling
    SCHEDULED,          // Scheduled in queue
    RUNNING,            // Executing
    NEEDS_SCHEDULING,   // Needs rescheduling after execution
    EXECUTED            // Executed all schedules
}

class CrickerCommentoryPoller implements Runnable {
    @Override
    public void run() {
        System.out.printf("[%s][%s] Polling Cricket commentary%n",Thread.currentThread().getName(), new Date());
    }
}
class LatestNewsPoller implements Runnable {
    @Override
    public void run() {
        System.out.printf("[%s][%s] Polling latest news%n",Thread.currentThread().getName(), new Date());
    }
}

class TestJobScheduler {
    public static void main(String[] args) throws InterruptedException {
        JobScheduler jobScheduler = JobScheduler.INSTANCE;
        Thread t = new Thread(() -> {
            try {
                jobScheduler.startScheduler();
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }); // To start scheduler in new thread to avoid blocking main thread...
        jobScheduler.add(new Job("OneTimeJob",JobType.ONE_TIME, 10, 0, 5, new CrickerCommentoryPoller()));
        jobScheduler.add(new Job("RecurringJob",JobType.RECURRING, 3, 0, 5, new LatestNewsPoller()));
        t.start();
        t.join();
    }
}

```

# Output

```bash
[Thread-0][Sun Sep 22 14:51:14 IST 2024] ** Job Scheduler Started **
[Thread-0][Sun Sep 22 14:51:22 IST 2024] Scheduling Job: Job{jobName='RecurringJob', status=SCHEDULED, executionCount=0, jobType=RECURRING}. Jobs in Queue: 1
[pool-1-thread-1][Sun Sep 22 14:51:22 IST 2024] Polling latest news
[Thread-0][Sun Sep 22 14:51:27 IST 2024] Scheduling Job: Job{jobName='RecurringJob', status=SCHEDULED, executionCount=1, jobType=RECURRING}. Jobs in Queue: 1
[pool-1-thread-2][Sun Sep 22 14:51:27 IST 2024] Polling latest news
[Thread-0][Sun Sep 22 14:51:29 IST 2024] Scheduling Job: Job{jobName='OneTimeJob', status=SCHEDULED, executionCount=0, jobType=ONE_TIME}. Jobs in Queue: 1
[pool-1-thread-3][Sun Sep 22 14:51:29 IST 2024] Polling Cricket commentary
[Thread-0][Sun Sep 22 14:51:30 IST 2024] Job finished execution: Job{jobName='OneTimeJob', status=EXECUTED, executionCount=1, jobType=ONE_TIME}
[Thread-0][Sun Sep 22 14:51:32 IST 2024] Scheduling Job: Job{jobName='RecurringJob', status=SCHEDULED, executionCount=2, jobType=RECURRING}. Jobs in Queue: 0
[pool-1-thread-1][Sun Sep 22 14:51:32 IST 2024] Polling latest news
[Thread-0][Sun Sep 22 14:51:37 IST 2024] Scheduling Job: Job{jobName='RecurringJob', status=SCHEDULED, executionCount=3, jobType=RECURRING}. Jobs in Queue: 0
[pool-1-thread-2][Sun Sep 22 14:51:37 IST 2024] Polling latest news
[Thread-0][Sun Sep 22 14:51:42 IST 2024] Scheduling Job: Job{jobName='RecurringJob', status=SCHEDULED, executionCount=4, jobType=RECURRING}. Jobs in Queue: 0
[pool-1-thread-3][Sun Sep 22 14:51:42 IST 2024] Polling latest news

```

# Resources
- https://www.youtube.com/watch?v=l6uKzV7yZzM

