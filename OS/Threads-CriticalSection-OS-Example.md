
## 2 Threads accessing same shared resource.

The output of below program won't be 2 lacs but some random values < 2 lacs

```java
public class Test1CriticalSection implements Runnable {

    private int sharedVariable=0;

    public static void main(String args[]) throws InterruptedException {
        Test1CriticalSection obj = new Test1CriticalSection();
        Thread thread1 = new Thread(obj);
        Thread thread2 = new Thread(obj);

        thread1.start();
        thread2.start();

        thread1.join();
        thread2.join();

        System.out.println("Final value of SharedVariable: " + obj.sharedVariable);
    }

    @Override
    public void run() {
        for(int i=0;i<100000;i++){
            this.sharedVariable++;
        }
    }
}
```

## Semaphore Example

```java
public class SemaphorePractice implements Runnable{
    private Semaphore semaphore;

    public SemaphorePractice(int limit) {
        this.semaphore = new Semaphore(limit);
    }

    @Override
    public void run() {
        try {
            semaphore.acquire();
            String uuid = UUID.randomUUID().toString();
            for(int i=0;i<10;i++){
                System.out.println(uuid);
                try {
                    Thread.sleep(1000);
                } catch (InterruptedException e) {
                    throw new RuntimeException(e);
                }
            }
            semaphore.release();
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        }
    }

    public static void main(String[] args) throws InterruptedException {
        SemaphorePractice semaphorePractice = new SemaphorePractice(2);
        Thread t1 = new Thread(semaphorePractice);
        Thread t2 = new Thread(semaphorePractice);
        Thread t3 = new Thread(semaphorePractice);
        Thread t4 = new Thread(semaphorePractice);

        t1.start();
        t2.start();
        t3.start();
        t4.start();

        t1.join();
        t2.join();
        t3.join();
        t4.join();
    }
}
```

## Locks in Java
- synchronized block adds Monitor lock in critical section
    - Only 1 thread can go inside synchronized block
    - The problem with this approach is if multiple threads use same object then this will work
    - But if multiple *instances* of classes are created then each thread can go inside differen object's critical section
```java
synchronized {
    // Critical section
}
```
- 4 locks in Java
    - Reentrant Lock
    - ReadWriteLock
    - StampedLock
    - Semaphore

### Reentrant Lock
- Problem Statement: No matter which object thread has access to only one thread can enter in critical section
    - Reentrant lock can be used in this case
```java
import java.util.concurrent.locks.ReentrantLock;

public class ReentrantLockExample implements Runnable{
    private static ReentrantLock lock = new ReentrantLock();

    @Override
    public void run() {
        try {
            lock.lock(); // Only 1 thread will enter into this critical section
            System.out.println("Lock acquired by "  + Thread.currentThread().getName());
            Thread.sleep(4000);
            System.out.println("Lock released by "  + Thread.currentThread().getName());
        } catch (Exception e) {
            //...
        } finally {
            lock.unlock();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        ReentrantLockExample example1 = new ReentrantLockExample();
        ReentrantLockExample example2 = new ReentrantLockExample();
        Thread thread1 = new Thread(example1);
        Thread thread2 = new Thread(example2);

        thread1.start();
        thread2.start();
        thread1.join();
        thread2.join();
    }
}

```

### ReadWriteLock

- Shared Lock: (S): (ReadLock in Java)
    - Multiple threads can take shared lock
    - Thread can only read but not write
    - No other thread can put Exclusive lock on the resource
- Exclusive lock: (X): (WriteLock in Java)
    - Allows read + write operations
    - If any thread aquires exclusive lock other thread cant take S or X lock
- Usage: Reads >>>> Write requests 

```java
package com.adobe.threads;

import java.util.concurrent.locks.ReadWriteLock;
import java.util.concurrent.locks.ReentrantReadWriteLock;

public class ReadWriteLockExample {

    private static ReadWriteLock lock = new ReentrantReadWriteLock();
    private int data = 10;

    public int readData(){
        try {
            System.out.println("Trying to acquire read lock for " + Thread.currentThread().getName());
            lock.readLock().lock();
            System.out.println("Acquired read lock for " + Thread.currentThread().getName());
            Thread.sleep(3000);
            return data;
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        } finally {
            System.out.println("Releasing read lock for " + Thread.currentThread().getName());
            lock.readLock().unlock();
        }
    }

    public int updateData(int newData){
        try {
            System.out.println("Trying to acquire write lock for " + Thread.currentThread().getName());
            lock.writeLock().lock();
            System.out.println("Acquired write lock for " + Thread.currentThread().getName());
            data = newData;
            Thread.sleep(6000);
            return data;
        } catch (InterruptedException e) {
            throw new RuntimeException(e);
        } finally {
            System.out.println("Releasing write lock for " + Thread.currentThread().getName());
            lock.writeLock().unlock();
        }
    }

    public static void main(String[] args) throws InterruptedException {
        ReadWriteLockExample example = new ReadWriteLockExample();
        Thread thread1 = new Thread(()->{
            System.out.println("Reading Data [1]: " + example.readData());
        });
        Thread thread2 = new Thread(()->{
            System.out.println("Updated Data [2]: " + example.updateData(90));
        });
        Thread thread3 = new Thread(()->{
            System.out.println("Reading Data [3]: " + example.readData());
        });
        Thread thread4 = new Thread(()->{
            System.out.println("Reading Data [4]: " + example.readData());
        });

        thread1.start();
        thread2.start();
        thread3.start();
        thread4.start();

        thread1.join();
        thread2.join();
        thread3.join();
        thread4.join();
    }
}


/* OUTPUT:
Trying to acquire read lock for Thread-3
Trying to acquire write lock for Thread-1
Trying to acquire read lock for Thread-2
Trying to acquire read lock for Thread-0
Acquired read lock for Thread-2
Acquired read lock for Thread-3
Acquired read lock for Thread-0
Releasing read lock for Thread-0
Releasing read lock for Thread-2
Releasing read lock for Thread-3
Acquired write lock for Thread-1
Reading Data [3]: 10
Reading Data [4]: 10
Reading Data [1]: 10
Releasing write lock for Thread-1
Updated Data [2]: 90
*/
```

### StampedLock

Types of locks
1. **Optimistic** : No lock acquired. It works based on versioning. Update data if it matches read version. If updated by any other transaction then retry process.
2. **Pessimistic** : Shared / Exclusive lock

- Stamped lock
    - Read write lock
    - optimistic locking functionality

NEED TO UNDERSTAND FURTHER

### Condition in Java
