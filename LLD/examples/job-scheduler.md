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
        this.scheduledJobs = new PriorityQueue<>((l,r)-> Math.toIntExact(l.nextScheduledTime - r.nextScheduledTime));
        this.executorService = Executors.newFixedThreadPool(MAX_CONCURRENCY);
    }

    public void add(Job job) {
        jobsLock.writeLock().lock();
        jobs.add(job);                   // Critical section
        jobsLock.writeLock().unlock();

        scheduledJobsLock.lock();
        scheduledJobs.add(job);         // Critical section
        scheduledJobsLock.unlock();
    }

    public void startScheduler() throws InterruptedException {
        System.out.println("** Job Scheduler Started **");
        while(true) {
            if(!executeJob()) { // Execute one job
                Thread.sleep(1000); // If no job executed then sleep for a while 1 Sec is more than enough to check for next timestamp
            }
            rescheduleJobs();
        }
    }

    private boolean executeJob(){
        long currentTime = System.currentTimeMillis()/1000;
        Job jobToExecute;
        try {
            scheduledJobsLock.lock();

            if(!scheduledJobs.isEmpty() && scheduledJobs.peek().nextScheduledTime <= currentTime) {
                jobToExecute = scheduledJobs.peek();
                scheduledJobs.remove();
                executorService.submit(()->{
                    jobToExecute.setStatus(JobStatus.RUNNING);
                    jobToExecute.runnable.run();
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

    private void rescheduleJobs(){
        jobsLock.readLock().lock();
        for(Job job: this.jobs){
            if(job.getStatus() == JobStatus.NEEDS_SCHEDULING) {
                if(job.jobType == JobType.RECURRING) {
                    job.updateAndGetNextScheduledTime();
                    job.setStatus(JobStatus.SCHEDULED);
                    scheduledJobs.add(job);
                } else {
                    job.setStatus(JobStatus.EXECUTED);
                    System.out.println("Job finished execution " + job);
                }
            }
        }
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

    public Job(JobType jobType, int initialDelay, int fixedDelay, int fixedInterval, Runnable runnable) {
        this.jobType = jobType;
        this.initialDelay = initialDelay;
        this.fixedDelay = fixedDelay;
        this.fixedInterval = fixedInterval;
        this.runnable = runnable;
        this.nextScheduledTime = System.currentTimeMillis()/1000 + initialDelay;
        this.status = JobStatus.NEEDS_SCHEDULING;
    }

    public JobStatus getStatus() {
        return status;
    }

    public void setStatus(JobStatus status) {
        this.status = status;
    }

    public long updateAndGetNextScheduledTime() {
        if(fixedDelay > 0) {
            this.nextScheduledTime = System.currentTimeMillis()/1000 + fixedDelay;
        } else if (fixedInterval > 0){
            this.nextScheduledTime = this.nextScheduledTime + fixedInterval;
        }
        return this.nextScheduledTime;
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

class CrickerCommentoryPoller implements Runnable{
    @Override
    public void run() {
        System.out.println("["+Thread.currentThread().getName() + "] Polling Cricket commentary at time: " + new Date());
    }
}

class TestJobScheduler {
    public static void main(String[] args) throws InterruptedException {
        JobScheduler jobScheduler = JobScheduler.INSTANCE;
        Thread t = new Thread(()-> {
            try {
                jobScheduler.startScheduler();
            } catch (InterruptedException e) {
                throw new RuntimeException(e);
            }
        }); // To start scheduler in new thread
        jobScheduler.add(new Job(JobType.ONE_TIME, 4, 0,5, new CrickerCommentoryPoller()));
        jobScheduler.add(new Job(JobType.RECURRING, 2, 0,5, new CrickerCommentoryPoller()));
        t.start();
        t.join();
    }
}

```

# Resources
- https://www.youtube.com/watch?v=l6uKzV7yZzM

