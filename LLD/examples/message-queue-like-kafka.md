# Requirements

- Queue shoule be In-memory
- It should support multiple topics, producers and subscribers
- Publisher can publish data(String) to any topic. 1:many relationshion. It will have unique client_id
- Subscriber can subscribe to many topics. It will have unique client_id
- Whenever a data is published to topic all the subscribers should receive that data
- Subscriber should be able to run in parallel (All the workers for (topic, subscriber) should run in parallel)
    - that means we will have separate workers for below cases
        - topic1 + subscriber1 -> Worker1
        - topic1 + subscriber2 -> Worker2
        - topic2 + subscriber1 -> Worker3

## Bonus Requirements
- Subscriber should be able to reset the offset. That means subscriber can consume old data as well

## Assumptions
- Here I am not creating getter/setters to avoid too much boiler-plate code. In actual interview try to separate classes in different files and let IDE generate getter/setter/constructors for speed.

# Entities
- KafkaService (singleton)
- Topic
- Client. Can act as a publisher / subscriber
- TopicSubscriptionInfo ( subscriberId, topicId, offsetId )

# Design
- This is `Multi threading` heavy problem and interviewer wants to check concurrency, thread handling capability


# Code
- We will be building system iteratively
    - `Tip`: benefit if iterative process is to ensure code written till now is bug free and satisfies required implemented till now.
        - As more code is added its difficult to debug and find the issue. Iteratively checking code execution gives confidence for adding new feature
        - Interviewer knows that system workes till this point. So we will certain milestones completed.
        - Early Run time error check and detection of any algorithmic errors
        - We have ready made test cases to test for next feature

## Iteration 1: Build a system without concurrency support
```java
package com.devsjournal.lld;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.HashSet;
import java.util.List;
import java.util.Map;
import java.util.Set;

public enum ConcurrentMessageQueue {
    /* Singleton ConcurrentMessageQueue using enum*/
    INSTANCE;

    Map<String, Topic> topics; // topicName -> topic object map
    Map<String, Set<Subscriber>> subscribers; // topicName -> set of subscribers

    ConcurrentMessageQueue() {
        this.topics = new HashMap<>();
        this.subscribers = new HashMap<>();
    }

    public void createTopic(Topic topic) {
        topics.put(topic.topicName, topic);
        subscribers.put(topic.topicName, new HashSet<>());
    }

    public void subscribe(String topic, Subscriber subscriber) {
        if(topics.containsKey(topic)) {
            subscribers.get(topic).add(subscriber);
        } else {
            System.err.println(String.format("Invalid request to subscribe to topic: %s by %s", topic, subscriber));
        }
    }

    public void publishMessage(Client publisher, Topic topic, String data) {
        topic.queue.add(data);
        for(Subscriber subscriber: subscribers.get(topic.topicName)){
            subscriber.consume(topic.topicName, data);
        }
    }
}

class Topic {
    String topicName;
    Integer endOffset;
    List<String> queue;

    public Topic(String topicName) {
        this.topicName = topicName;
        this.endOffset = 0;
        this.queue = new ArrayList<>();
    }
}

class Client {
    String clientId;

    public Client(String clientId) {
        this.clientId = clientId;
    }

    @Override
    public String toString() {
        return "Client{" +
                "clientId='" + clientId + '\'' +
                '}';
    }
}

class Subscriber extends Client {
    public Subscriber(String clientId) {
        super(clientId);
    }

    public boolean consume(String topicName, String data){
        System.out.printf("Received data by subscriber:%s from topic:%s data:%s%n", clientId,topicName, data);
        return true;
    }
}

class TestConcurrentMessageQueue {
    public static void main(String[] args) {
        ConcurrentMessageQueue kafka = ConcurrentMessageQueue.INSTANCE;

        // Create topic
        Topic topic1 = new Topic("topic1");
        Topic topic2 = new Topic("topic2");
        kafka.createTopic(topic1);
        kafka.createTopic(topic2);

        // Create Client1
        Client publisher1 = new Client("publisher1");
        Client publisher2 = new Client("publisher2");

        // Create client2
        Subscriber subscriber1 = new Subscriber("subscriber1");
        Subscriber subscriber2 = new Subscriber("subscriber2");
        Subscriber subscriber3 = new Subscriber("subscriber3");

        // Subscribe to topic
        kafka.subscribe("topic1",subscriber1);
        kafka.subscribe("topic1",subscriber2);
        kafka.subscribe("topic1",subscriber3);

        kafka.subscribe("topic2",subscriber2);

        kafka.publishMessage(publisher1, topic1, "Topic1 Message");
        kafka.publishMessage(publisher2, topic2, "Topic2 Message");
    }
}
```

Output
```bash
Received data by subscriber:subscriber1 from topic:topic1 data:Topic1 Message
Received data by subscriber:subscriber2 from topic:topic1 data:Topic1 Message
Received data by subscriber:subscriber3 from topic:topic1 data:Topic1 Message
Received data by subscriber:subscriber2 from topic:topic2 data:Topic2 Message
```

## Iteration 2: Adding concurrency support in above system
- Additional features
    - offset based consumption
    - concurrency i.e, separate worker thread for each (topic + subscriber)
- To manage concurrency lets introduce some new entities which will be useful 
- `TopicSubscriber` : Introduces to keep track of subsciber wise offset processed.
- `AtomicInteger` : Introducing AtomicInteger to manage atomic updates on offset counter. This is useful for concurrent data updates
- `synchronized` : Used this keyword at certain places to make sure for a specific object only one thread access the code block
- Effectively Manage Polling: Here for each worker thread when data is not there in topic its been kept on `wait` which will make thread to sleep and once some data is pushed to topic all other worker threads will be signalled for polling. This is done using `wait` and `notify` on `topicSubscription` object monitor.

```java
package com.devsjournal.lld;

import java.util.ArrayList;
import java.util.HashMap;
import java.util.HashSet;
import java.util.List;
import java.util.Map;
import java.util.Set;
import java.util.concurrent.atomic.AtomicInteger;

public enum Kafka {
    /* Singleton ConcurrentMessageQueue using enum*/
    INSTANCE;

    Map<String, Topic> topics; // topicName -> topic object map
    Map<String, TopicBroadcaster> topicBroadcasterMap; // Message broadcaster for each topic

    Kafka() {
        this.topics = new HashMap<>();
        this.topicBroadcasterMap = new HashMap<>();
    }

    public Topic createTopic(String topicName) {
        Topic topic = new Topic(topicName);
        topics.put(topic.topicName, topic);
        topicBroadcasterMap.put(topic.topicName, new TopicBroadcaster(topic));
        return topic;
    }

    public void subscribe(String topic, Subscriber subscriber) {
        this.topics.get(topic).addTopicSubscriber(new TopicSubscriber(subscriber));
    }

    public void publishMessage(Topic topic, String data) {
        topic.addNewData(data);
        new Thread(()->topicBroadcasterMap.get(topic.topicName).broadcast()).start();
    }
}

class Topic {
    String topicName;
    private List<String> queue;
    Set<TopicSubscriber> subscribers;

    public Topic(String topicName) {
        this.topicName = topicName;
        this.queue = new ArrayList<>();
        this.subscribers = new HashSet<>();
    }

    /**
     * Why synchronized here ?
     *      So that for a specific topic only one thread can enter in addNewData and append data in list
     *      If multiple threads call the same method on same list then it will result in unexpected behaviour / Exceptions
     * */
    public synchronized void addNewData(String data){
        this.queue.add(data);
    }

    public Integer getEndOffset(){
        return this.queue.size()-1;
    }

    public String getData(Integer offset) {
        return this.queue.get(offset);
    }

    public void addTopicSubscriber(TopicSubscriber topicSubscriber) {
        subscribers.add(topicSubscriber);
    }

    public Set<TopicSubscriber> getTopicSubscribers(){
        return this.subscribers;
    }
}

class TopicBroadcaster {
    private Topic topic;
    private Map<String, Worker> subscriberWorkers;

    public TopicBroadcaster(Topic topic) {
        this.topic = topic;
        this.subscriberWorkers = new HashMap<>();
    }

    /*
    * Wake up all the subscriber worker to consume new data
    * */
    public void broadcast(){
        for(TopicSubscriber topicSubscriber: topic.getTopicSubscribers()){
            Subscriber subscriber = topicSubscriber.subscriber;
            // Check if there exists any worker for subscriber. If don't exists create it.
            if(!subscriberWorkers.containsKey(subscriber.clientId)) {
                Worker newWorker = new Worker(topic, topicSubscriber);
                subscriberWorkers.put(subscriber.clientId, newWorker);
                // Start new thread for new worker
                new Thread(newWorker).start();
            }
            // Important: The main work of broadcaster is just to awake all workers. then worker will poll and consume the data
            subscriberWorkers.get(subscriber.clientId).wakeUp();
        }
    }
}

class Worker implements Runnable {
    Topic topic;
    TopicSubscriber topicSubscriber;

    public Worker(Topic topic, TopicSubscriber topicSubscriber) {
        this.topic = topic;
        this.topicSubscriber = topicSubscriber;
    }

    @Override
    public void run() {
        while(true) {
            /*
            * topicSubscriber acts as a monitor block here. Benefits
            * 1. Only one topic subscriber instance i.e, worker will enter in this block
            * 2. Worker object has kept monitor on `topicSubscriber` So whenever notify is called it will again start processing from that point.
            * */
            synchronized (topicSubscriber) {
                if(topic.getEndOffset() <= topicSubscriber.offset.get()){
                    /* Thread will sleep and won't waste CPU cycle in polling */
                    try {
                        topicSubscriber.wait();
                    } catch (InterruptedException e) {
                        throw new RuntimeException(e);
                    }
                } else {
                    Integer currentOffset = topicSubscriber.offset.get();
                    Integer nextOffset = currentOffset+1;
                    try {
                        topicSubscriber.subscriber.consume(topic.topicName, topic.getData(nextOffset));
                        topicSubscriber.offset.compareAndSet(currentOffset , nextOffset);
                    } catch (InterruptedException e) {
                        throw new RuntimeException(e);
                    }
                }
            }
        }
    }

    /*
    * When any new message comes in topic wakeUp will signal worker to start polling again
    * */
    public void wakeUp(){
        /* Only owner of the thread can call notify/wait method calls...
        * You can't wait()/notify() on an object unless the current thread owns that object's monitor. To do that, you must synchronize on it.
        * */
        synchronized (topicSubscriber){
            topicSubscriber.notify();
        }
    }
}

class TopicSubscriber {
    Subscriber subscriber;
    AtomicInteger offset;

    public TopicSubscriber(Subscriber subscriber) {
        this.subscriber = subscriber;
        this.offset = new AtomicInteger(-1);
    }
}

class Client {
    String clientId;

    public Client(String clientId) {
        this.clientId = clientId;
    }

    @Override
    public String toString() {
        return "Client{" +
                "clientId='" + clientId + '\'' +
                '}';
    }
}

class Subscriber extends Client {
    public Subscriber(String clientId) {
        super(clientId);
    }

    public boolean consume(String topicName, String data) throws InterruptedException {
        System.out.printf("[Consuming] data by subscriber:%s from topic:%s data:%s%n", clientId,topicName, data);
        Thread.sleep(2000);
        System.out.printf("\t[Consumed] data by subscriber:%s from topic:%s data:%s%n", clientId,topicName, data);
        return true;
    }
}

class TestConcurrentMessageQueue {
    public static void main(String[] args) throws InterruptedException {
        Kafka kafka = Kafka.INSTANCE;

        // Create topic
        Topic topic1 = kafka.createTopic("topic1");
        Topic topic2 = kafka.createTopic("topic2");

        // Create client2
        Subscriber subscriber1 = new Subscriber("subscriber1");
        Subscriber subscriber2 = new Subscriber("subscriber2");
        Subscriber subscriber3 = new Subscriber("subscriber3");

        // Subscribe to topic
        kafka.subscribe("topic1",subscriber1);
        kafka.subscribe("topic1",subscriber2);
        kafka.subscribe("topic1",subscriber3);

        kafka.subscribe("topic2",subscriber2);

        kafka.publishMessage(topic1, "Topic1 Message");
        kafka.publishMessage(topic2, "Topic2 Message");

        Thread.sleep(5000);
        kafka.publishMessage(topic1, "Topic1 Message after 5 Seconds");
        kafka.publishMessage(topic2, "Topic2 Message after 5 Seconds");
    }
}
```

Output
```bash
[Consuming] data by subscriber:subscriber2 from topic:topic1 data:Topic1 Message
[Consuming] data by subscriber:subscriber3 from topic:topic1 data:Topic1 Message
[Consuming] data by subscriber:subscriber1 from topic:topic1 data:Topic1 Message
[Consuming] data by subscriber:subscriber2 from topic:topic2 data:Topic2 Message
	[Consumed] data by subscriber:subscriber3 from topic:topic1 data:Topic1 Message
	[Consumed] data by subscriber:subscriber2 from topic:topic1 data:Topic1 Message
	[Consumed] data by subscriber:subscriber2 from topic:topic2 data:Topic2 Message
	[Consumed] data by subscriber:subscriber1 from topic:topic1 data:Topic1 Message
[Consuming] data by subscriber:subscriber2 from topic:topic1 data:Topic1 Message after 5 Seconds
[Consuming] data by subscriber:subscriber2 from topic:topic2 data:Topic2 Message after 5 Seconds
[Consuming] data by subscriber:subscriber3 from topic:topic1 data:Topic1 Message after 5 Seconds
[Consuming] data by subscriber:subscriber1 from topic:topic1 data:Topic1 Message after 5 Seconds
	[Consumed] data by subscriber:subscriber2 from topic:topic1 data:Topic1 Message after 5 Seconds
	[Consumed] data by subscriber:subscriber2 from topic:topic2 data:Topic2 Message after 5 Seconds
	[Consumed] data by subscriber:subscriber3 from topic:topic1 data:Topic1 Message after 5 Seconds
	[Consumed] data by subscriber:subscriber1 from topic:topic1 data:Topic1 Message after 5 Seconds
```

# Future Enhancements
- Any Bug Fixes
- Validation and Exceptions check. i.e, subscription for invalid topic should give exception

# References
- https://www.youtube.com/watch?v=4BEzgPlLKTo&t=99s
