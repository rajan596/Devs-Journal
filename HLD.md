# Short Notes

- Fear of data loss from RAM, hard disk or machine failure. 
  - Do Replication
  - Replication involves a leader and replicas approach.
- If too much data..
  - Do sharding (split data on diff machines)
- Fear of overloading on one machine.
  - Do horizontal scaling with load balancing techniques like consistent hashing, proxy, load balancer
- If the middleware component becomes a bottleneck in data handling for proxy..
  - Get IP address from zookeeper kind of infra and then directly connect to machine for communication
- Synchronous vs asynchronous replicas
  - Synchronous: choose this if it requires strong guarantee of data persistence.
      - Data gets written to replica and after write is done producer receives ack.
      - Strong consistency in case read is done from replica
  - Asynchronous: little low guarantee of data persistence.
      - Has higher throughput than synchronous replication.
- Eventual consistency if read is done from replica.
- We can configure X no of sync replicas and Y no of async replicas. Total replica = X + Y
- If application is read heavy and calculated data is not changed too frequently..
- Think if you can use a cache here
- Write heavy application….
  - Think if you can use a message queue if data is not required to be updated immediately
- Requires searching ?
  - Think of creating indexes for DB
- For text based search like auto complete/suggestions think of ES/Solr
- DB choices…
  - If ACID required..go for RDBMS
  - If ACID not require….can think for NoSQL…even if data is unstructured then good probability to use this schema
  - Ever increasing data + finite query => columnar DB - cassandra, HBase
  - User info, catalog type data -> Document DB : Mongo, couchbase
  -Think about query patterns ??
    - If query only on key -> use Key-store DB like DynamoDB
    - Query on multiple fields..can go with Relational or DocumentDB
    - Fuzzy search -> ES
  - Strong consistency -> go for relational
  - More focus on availability -> can go for NoSQL
- Proxies
  - Forward proxy = VPN
  - Reverse proxy = server side proxy = load balancing
- CAP theorem: Any n/w shared system can not have all 3 properties. Need to sacrifice one of them
  - MongoDB: CP system
  -Cassandra: AP system
- Want to reduce API latency
  - Think of caching….redis, memcached -- non relational DB
- Storing images, videos
  - Think of amazon s3 as blob storage,
- Think of CDN for faster delivery
- Non Relational DB
  - Built for scale
  - Built for aggregation
  - Horizontal partitioning inbuilt
- Document DB:
  - Not sure about schema, schema evolve over time
  - Supports heavy read, writes
  - No ACID properties and joins tough to achieve
  - Use case: Product details of amazon
- Columnar DB:
  - Use case: streaming data, event logging for analytics
  - Multi tenant architecture
  - Each customer have their own infra isolated
  - Finding nearby locations/restaurants using lat and long
- Elastic search
- MongoDB
- Cache data volatility

# Redis
- Redis
- While Redis is an in-memory (mostly) data store and it is not volatile, Memcached is an in-memory cache and it is volatile.
- Redis has the option to backup data using…
  - RDB snapshot: snapshot of DB at every n minutes
  - Append only File. AOF. which writes every txn in file
  - Want to have caching which supports data types ?
    - Consider Redis. It supports multiple data types
- Redis as PubSub
- You can configure the channel and subscribers can subscribe to it.
Thinking about idempotency…
means..Multiple req has same result. 
Like, user can like post only once even after multiple api hits.
Payment should be considered only once even if retried.
If you don't retry API then idempotency is not required.
Generate some ID like paymentID before hitting payment api, so even on retry Payment gw can know if the req is coming for the first time or second time and appropriately can respond.
Protobuf 
Cassandra..
Good at: write heavy, read less & query on key & distributed and can save large amount of data
Bad at: random queries, aggregations
RAID
A Redundant Array of Independent Disks (RAID) is a set of multiple drives put together with the purpose of improving a single drives performance. 
Use case for Document DB..
If the data in your application has a document-like structure (i.e., a tree of one-to-many relationships, where typically the entire tree is loaded at once), then it’s probably a good idea to use a document model.
We are saved here from joins..
Document DB supremacy…
The main arguments in favor of the document data model are schema flexibility, better performance due to locality, and that for some applications it is closer to the data structures used by the application. The relational model counters by providing better support for joins, and many-to-one and many-to-many relationships.
Relationship among data preference…
1:many -> Document DB
Many:1 -> Relational
Many:Many -> ( Relational <<< Graph Model) is more useful here.

- ACID properties
  - Atomicity : Either txn happenes or do not happen, there is no intermediate state
  - Consistency
  - Isolation: Multiple txn occurs independently without interference
  - Durability
- PACELC theorem
  - PACELC theorem is an extension to the CAP theorem. It states that in case of network partitioning (P) in a distributed computer system, one has to choose between availability (A) and consistency (C) (as per the CAP theorem), but else (E), even when the system is running normally in the absence of partitions, one has to choose between latency (L) and consistency (C).
- When writing db query…
  - Always validate query params
  - Carefully check edge case if query can scan millions of rows due to bad output.
  - Always put limit in query if possible
- NoSQL follows BASE consistency model..
  - BA: Basic availability
  - Soft state
  - Eventual consistency
- When DB performance needs to improve quickly….
  - Can do vertical scaling for a time being
  - Use connection pool in DB query….
  - Effectively establishes connection and reuses it
  - To avoid so many connections on DB…you can use Connection Proxy like RDS.
- Build Microservices with loose coupling and high cohesion
  - Loose coupling: Min dependency b/w services. I.e, expose min things to others. And changes in 1 service do not lead to change in other services. Loose coupling advocates for the separation of unrelated components.
  - High Cohesion: Keep similar functionalities together in 1 microservice. grouping together of related components.
- GraphQL:
  - Query language for APIs
  - API aggregator…unified common apis exposed to clients.
  - GraphQL queries access not just the properties of one resource but also smoothly follow references between them. While typical REST APIs require loading from multiple URLs, GraphQL APIs get all the data your app needs in a single request. Apps using GraphQL can be quick even on slow mobile network connections.
- TypeScript:
  - JS with syntax with types
- Dependency Injection
  - Dependency injection is a software design pattern in which one or more dependencies (or services) are injected, or passed by reference, into a dependent object.
  - Useful for testing and mocking module responses.
- WebRTC vs websockets
  - Websockets:
    - For bidirectional communication
    - Uses TCP
    - Good for signaling
  - WebRTC: 
    - Unidirectional
    - Peer 2 Peer : Anyone can talk to anyone
    - Uses UDP
    - Developed by google, good for audio/video streaming

Prometheus and Grafana
Prometheus query language which collects data and gives queried results.
Used LevelDB for indexes
It also has local storage sub system which organizes data in chinks of constant size. Chinks stored in file per time series.
Graphs is a visualization tool which transforms metrics into visualization.
Database per service pattern
NO direct DB access: loosely coupled system
Can use diff DB, scale independently
Avoid single point of failure, one DB down do not affect other services
For compliance
Complexities:
Multiple components needs to be synced which will have eventual consistency
Distributed txn needs to be done which is complex
Garbage collection
Mark and Sweep algorithm: (indirect GC algo)
Mark: do DFS and mark all live references starting from root
Sweep: Free up memory which is not recognized in mark phase
Root: registers, global variables, thread stack
Make your application as async as possible
To reduce blast radius
To remove dependency
The task which does not require a sync call should not be done sync.
In master slave architecture write is performed on master only. So it is useful for reading heavy applications but write capacity does not increase much even after adding multiple masters also. Adding multiple masters has its own complexity.
While changing DB index, we should analyze what other queries can go wrong and hit performance. ORMs are blind spots which queries they prepare and use. Check ORM queries periodically to check performance impact.
API composition pattern in Microservices
Problem: client needs to talk to multiple services in order to get all details like logistics, payment, order
Put a composer in between who talks to all services and combine response
User API gateway as a composer
Simple to implement, low latency and api calls on client side, hides implementation, can work as rate limiting as well
Problem with availability, data consistency and distributed transactions.
Problem if one of the service goes down
Tools: KrakenD, Kong, Managed API gw services
- Dead letter queue
  - A separate queue from the main/live queue where we can store messages after N retries which cant be processed but may require engineering input to check the problem.
High load on DB : In some cases where write on DB is high on specific times and if we can wait for some time to reflect data in DB then asynchronous write can be one option where all DB writes will go via queue.
Serverless Architecture: If processing is based on some events and load is not continuous and is very less then lambda like service can be used which is serverless. It’s highly cost effective.
- DynamoDB
  - NoSQL key value store fully managed by amazon with a very low latency
- When you want to ensure data consistency between 2 tables. One approach is 2 way handshake
  - You send data to other service
  - Other services acks back and update current service’s data
- Consistency Models
  - Serialised / Linearised : Ordering imposed
  - Eventual consistency : Eventually all nodes gets synced. Not all read gived most recent writes
  - Casual consistency: Groups relavant operations and executes in order.
  - Quorum: configurable consistency when having multiple nodes
    - R + W > N --> To ensure strong consistency
    - R + W <= N --> Eventual consistency

# Vertical vs Horizontal Scaling
- Vertical: Buying bigger machines
- Horizontal: Buying more machines

# Authentication (TBA)
- How api requests will be authenticated
- How Authorisation will work ?

# Service Discovery

- https://www.nginx.com/blog/service-discovery-in-a-microservices-architecture/
- https://www.redhat.com/en/topics/integration/what-is-a-service-registry

# Consistent Hashing
- Partitioning is used to distribute data across nodes. But how do we handle node addition/removal with maintaining uniform data distribution  : **Consistent Hashing** comes to the rescue
- Each node is assigned a range and Hash(key) = value --> is then mapped to the range which selects node.
- If any node is deleted then next node's range increases and data moves there which creates ununiform data distribution.
- To solve this, Consistent Hashing used virtual nodes to maintain uniform data.
- DynamoDB,Memcached and Cassandra uses Consistent Hashing 
- References:
  - https://www.youtube.com/watch?v=UF9Iqmg94tk

# Load Balancing vs Reverse Proxy vs API Gateway

- **Load Balancing** 
  - Process of distributing network traffic across a group of backend servers is known as Load Balancing
  - Acts as reverse proxy by routing requests to different backend servers
  - Does the job of request routing as per configured algorithm in LB
  - Can keep an eye on server healths and adds / removes servers as per load criteria
  - Types of Load Balancers:
    - Network Load Balancer
      - Functions at Layer 4 specifically handling TCP traffics, distributing traffic at transport layer
      - Makes routing decisions using IP address and destination port
    - Application Load Balancer
      - Operates at Layer 7 of OSI model - at Application level traffic routing
      - Enhances security & simplifies the application by always ensuring the use of the latest SSL/TLS ciphers and protocols.
  - LB algorithms
    - Round Robin: sequencially 1 by 1 to diff servers
    - Sticky round robin: subsequent requests from a client go to the same service instance that handled the initial request.
    - Weighted round-robin: The administrator can assign weights to each service, determining the proportion of requests each service handles.
    - Hash : This algorithm applies a hash function to the IP or URL of incoming requests. The instances to which requests are routed depend on the hash function’s result.
    - Least connections : new requests are directed to the service instance with the fewest concurrent connections.
    - Least response time : new requests are sent to the service instance with the quickest response time.
- **Reverse Proxy**
  - acts as a mediator between client and server
  - A reverse proxy sits in front of an origin server and ensures that no client ever communicates directly with that origin server
  - Benifits
    - It can do load balancing
    - It can cache the response
- **API Gateway**
  - Extension of reverse proxy




# References
- https://github.com/donnemartin/system-design-primer
- DDIA notes: https://docs.google.com/document/d/1Yn6ee7WEK6r6q49mVX7uqbEtoYc9zizaQeIQy_c9Oh8/edit
- https://drive.google.com/file/d/16wtG6ZsThlu_YkloeyX8pp2OEjVebure/view
- https://www.youtube.com/c/HusseinNasser-software-engineering/videos
- [Mark and Sweep Garbage Collection Algorithm] https://www.youtube.com/watch?v=4qLf0FJMyf0
- https://systemdesign.one/
