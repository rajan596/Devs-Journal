
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