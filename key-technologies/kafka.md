# Kafka

- Distributed Message broker

# Architecture

- If topic has 10 partitions, max 10 consumers are allowed to consume for a specific consumer group (Limitation)
- Kafka rebalances partitions and partition assignment when any new consumer comes or leaves.


# Sample Producer Consumer Code
ToDo

# Walmart's solution for decoupling Kafka message consumption with MPS
- MPS: Messaging Proxy Service
- In-memory queue will be processed by consumers. So no. of consumers are now not limited.
- Queue will act as proxy between Kafka and consumers
- MPS ensures ordered delivery
- MPS is a limited size bounded queue which picks data from Kafka and maintains in queue. Reader thread do this Kafka polling work
- If queue is full then Reader thread will pause and wont consume data from Kafka. Consumer must first process defined say 5000 messages and then only new messages will be available
- Writer threads will then call HTTP POST API call to consumer to process the message
- Main benefit Walmart got is Re-balancing solution and independent scaling based on load of events. 

# References:
1. https://medium.com/@bb8s/message-queue-deep-dive-into-kafka-6c57bb13b67c
2. [Lag Aware Kafka Balancing at Agoda](https://newsletter.systemdesigncodex.com/p/kafka-load-balancing-at-agoda) | [Agoda Blog](https://medium.com/agoda-engineering/how-we-solve-load-balancing-challenges-in-apache-kafka-8cd88fdad02b)
3. https://blog.bytebytego.com/p/the-trillion-message-kafka-setup