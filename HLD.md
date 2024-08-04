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
- Redis
  - While Redis is an in-memory (mostly) data store and it is not volatile, Memcached is an in-memory cache and it is volatile.
  - Redis has the option to backup data using…
    - RDB snapshot: snapshot of DB at every n minutes
    - Append only File. AOF. which writes every txn in file
    - Want to have caching which supports data types ?
      - Consider Redis. It supports multiple data types
  - Redis as PubSub
- You can configure the channel and subscribers can subscribe to it.
- Thinking about idempotency…
  - means..Multiple req has same result. 
  - Like, user can like post only once even after multiple api hits.
  - Payment should be considered only once even if retried.
  - If you don't retry API then idempotency is not required.
  - Generate some ID like paymentID before hitting payment api, so even on retry Payment gw can know if the req is coming for the first time or second time and appropriately can respond.
- Cassandra..
  - Good at: write heavy, read less & query on key & distributed and can save large amount of data
  - Bad at: random queries, aggregations
- RAID
  - A Redundant Array of Independent Disks (RAID) is a set of multiple drives put together with the purpose of improving a single drives performance. 
Use case for Document DB..
- If the data in your application has a document-like structure (i.e., a tree of one-to-many relationships, where typically the entire tree is loaded at once), then it’s probably a good idea to use a document model.
  - We are saved here from joins..
- Document DB supremacy…
  - The main arguments in favor of the document data model are schema flexibility, better performance due to locality, and that for some applications it is closer to the data structures used by the application. The relational model counters by providing better support for joins, and many-to-one and many-to-many relationships.
- Relationship among data preference…
  - 1:many -> Document DB
  - Many:1 -> Relational
  - Many:Many -> ( Relational <<< Graph Model) is more useful here.=
- ACID properties
  - Atomicity : Either txn happenes or do not happen, there is no intermediate state
  - Consistency
  - Isolation: Multiple txn occurs independently without interference
  - Durability
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
    - Reactive: Can adjust based on network speed / network loss
    - In Video calls, Processing mainly happens in Client side. 
- Prometheus and Grafana
    - Prometheus query language which collects data and gives queried results.
    - Used LevelDB for indexes
    - It also has local storage sub system which organizes data in chinks of constant size. Chinks stored in file per time series.
    - Graphs is a visualization tool which transforms metrics into visualization.
- Database per service pattern
  - NO direct DB access: loosely coupled system
  - Can use diff DB, scale independently
  - Avoid single point of failure, one DB down do not affect other services
  - For compliance
  - Complexities:
    - Multiple components needs to be synced which will have eventual consistency
    - Distributed txn needs to be done which is complex
- Garbage collection
    - Mark and Sweep algorithm: (indirect GC algo)
    - Mark: do DFS and mark all live references starting from root
    - Sweep: Free up memory which is not recognized in mark phase
    - Root: registers, global variables, thread stack
- Make your application as async as possible
  - To reduce blast radius
  - To remove dependency
  - The task which does not require a sync call should not be done sync.
- In master slave architecture write is performed on master only. So it is useful for reading heavy applications but write capacity does not increase much even after adding multiple masters also. Adding multiple masters has its own complexity.
- While changing DB index, we should analyze what other queries can go wrong and hit performance. ORMs are blind spots which queries they prepare and use. Check ORM queries periodically to check performance impact.
- API composition pattern in Microservices
  - Problem: client needs to talk to multiple services in order to get all details like logistics, payment, order
  - Put a composer in between who talks to all services and combine response
  - User API gateway as a composer
    - Simple to implement, low latency and api calls on client side, hides implementation, can work as rate limiting as well
    - Problem with availability, data consistency and distributed transactions.
    - Problem if one of the service goes down
    - Tools: KrakenD, Kong, Managed API gw services
- Dead letter queue
  - A separate queue from the main/live queue where we can store messages after N retries which cant be processed but may require engineering input to check the problem.
- High load on DB : In some cases where write on DB is high on specific times and if we can wait for some time to reflect data in DB then asynchronous write can be one option where all DB writes will go via queue.
- Serverless Architecture: If processing is based on some events and load is not continuous and is very less then lambda like service can be used which is serverless. It’s highly cost effective.
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

# CAP Theorem

- Consistency: Each nodes reurns latest data
- Availability: Each request returns non error response
- Partition tolerance: Even when some nodes are disconnected in network or having delays System is up and running

- CAP theorem: Any n/w shared system can not have all 3 properties. Need to sacrifice one of them
  - We have all 3 Characteristics as long as there is no network failure.
  - If partition happend then non-sync nodes will become unavailable and may return error to connected clients. 
  - CP system : Availavility is sacrified when Partition happens in distributed system.
    - Useful in Banking system
    - MongoDB, Apache HBase, Couchbase, Redis
  - AP Systen: Always available with eventual consistent data
    - Offers eventual consistency
    - Very useful when hige number of data needs to be processed and System must be available all the time
    - Cassandra, CosmosDB, Amazon DynamoDB, CouchDB
  - CA System: 
    - CS database cant exists in distributed systems except MySQL Dbs
    - Ex. PostgreSQL, MySQL --> Microsoft SQL Server, Oracle, MySQL

- PACELC theorem: PACELC theorem is an extension to the CAP theorem. It states that in case of network partitioning (P) in a distributed computer system, one has to choose between availability (A) and consistency (C) (as per the CAP theorem), but else (E), even when the system is running normally in the absence of partitions, one has to choose between latency (L) and consistency (C).

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

# Replication

3 Main methods to replicate the data

### Single leader replication
- One Leader, Multiple followers. Or Master ->>> Slave
- Leader handles Write requests and read (when req) and Followers handles read requests only
- Followers keep syncing with Leaders updated date. They may fall behind
- Types of replications
  - Async Replication:
    - Write are accepted by leader, committed locally and returned success to client assuming it will forward the data without fail (or with crash condition as well)
    - This wasy data replication is async with respect to user write request
    - Con: Does not guarentee durability in case leader node crahes/ fails to propogate changes
    - Pros: Faster in terms of write requests
  - Sync Replication:
    - Leader commit changes locally, forward update to all followers, upon receicing ack from all followers client gets success response
- When new follower joins...
  - Leader maintains periodic snapshot (replication logs)
  - When new follower joins snapshot is sent to follower with log sequence number OR binlog cordinates details
  - all the updates after log seq no is also sent to follower
- When fololowers gets offline
  - follower must know last txn it completed
  - It then tries to execute all txn after that seq no
  - If leader rolls over txn logs and follower comes online after that period then syning becomes very difficult
- Handling leader failure
  - Periodically check heart beat of Leader and check if its dead
  - One of the follower becomes new leader. It may follow leader election. Follower with most up to date data should be elected as new leader to have min data loss
  - configure clients to redirect writes to new leader
  - all followers to sync with new leader
  - If old leader comes online it will act as a follower and not a leader
- **Split Brain**
  - When 2 node believes each one of them is leader and advertise to the clients
  - Both starts to accept write and results in conflicts or data loss or data curruption
- Deciding timeout for leader failover
  - If too short then any network delay or big query can cause false failover
  - If too big then.. It may cause system to unavailable for that time an potential data loss
- Types of log replication
  1. Statement based: 
    - Approach: Log all the SQL statements and propogate to followers
    - problems:
      - statements like now() and rand() can result in non-deterministic behaviour on follower nodes. In this case master can replace non-deterministic values with actual values
      - Triggers, stored procedures can have diff outcomes in diff follower nodes. Care must be taken here.
  2. Write ahead log (WAL):
    - It is append only file
    - Log may be one that stores data for SSTables of LSM Tree
    - A log to track every change when its B tree as a storage structure
    - Kafka is based on WAL
    - Problems:
      - Size of WAL can grow too large
      - WAL is couples with the storage engine. When upgrading version it becomes problamatic to upgrade cluster by upgrading replicas and doing failover
  3. Logical or row based:
    - Decouples logs with storage engine selection
    - Log Logical things like 
      - row insert -> log all columns
      - row update -> log all the changes to column
      - row delete -> log primary key
    - Also called *change data capture*
    - Pros: Any storage engine can read logical changes and can be exported to any external system
  4. Trigger based replication:
    - Works on triggers. Trigger is handled by Application code which decides to replicate code or not ?
    - Higher overhead compared to other methods
    - Normally coping changes to seperate table where external process can read
- Ideal choice when application is read heavy. Followers can be increased as read load increases
- Single leader is bottleneck when write requests increases
- Delay between write happening on master and follower is called **Replication Lag**
- Follower catch-up with master eventually which is called as **Eventual consistency**
- Read after write consistency OR Read your own write : This is a guarantee that a user is always able to immediately read the writes user has submitted
  - To achieve read after write consistency, Required queries need to made on Leader node
- **Monotonic Reads**: Reading old data after reading latest data from replicas
- **Consistent Prefix Read**: The consistent prefix reads guarantee says that if a sequence of writes happen in a certain order then the writes are served to the reader in the same order.

### Multi leader replication
- Multiple leader accepts write
- Also called as master-master or active/active replication
- Each node processing a write must forward all updates to other leader nodes and accept updates from other leader nodes.
- Use cases: 
  - multiple data centre and data is served to user from closer data centre
  - To handle failure of complete data centre
- Similar exampled
  - Working on multiple devices which all handles write while being offline and when online propogates write to other devices and gets synced
  - Online colloborative editing software: Google Docs
- Pros
  - This strategy is better when *Network Problem* happens. When 2 data centre experiences networ partition they can still serve write requests and eventually gets synces with other leaders.
  - If one data centre fails we still have data with other leaders and this one can come up online and gets synces. In single leader follower will be promoted as Leader which may encounter data loss depending on replication strategy.
- Cons
  - Same data modified in multiple leaders needs **conflict resolution**
  - Writes will lost from leader if its not replicated to other leader
  - For DB, auto increment, trigger and integrity can be problamatic to maintain
- Conflict Avoidance
  - Route all updates for a user to same leader so all sub sequent writes are serialised.
    - Problems: When user moved geo or when data centre experiences failure
- Conflict Resulution
  - **Latest write wins**: maintain latest write and override it. Cassandra uses it. It resolves conflicts with the cost of *durability*.
    - Bad choice is data loss is unacceptable.
    - Can be used in Cache system where data loss is tolerable.
    - Cassandra recommends using immutable keys ( using UUID) that once written dont change to aovid data loss. 
  - Merge/Concatenate writes and let user decide the result. Evernote does the same it will display B/C to user.
  - Store conflicting write to separate Data structure and custom application code resolves it.
- Automatic Conflict Resolution
  - Google Doc: uses operational transormation technology
  - Git: mergeable persistant data structures which maintains version history and have ability auto resolve conflicts.
- Multi leader topologies
  - All to All: Each leader writes data to all other leader
  - Circular: Leader node replicates data to adjacent clockwise or anti clockwise leader node.
  - Star: One node forwards update to all other nodes

### Leaderless replication
- DBs using leaderless replication
  - Amazon Dynamo DB
  - Cassandra
  - Riak
  - Voldemort
- Read Repair: When client reads and checks for latest data, client can sub sequently write the latest data to the replica
- Anti entropy Process: Background process on replica nodes checks other replicas, checks difference and syncs with its value

##### Quoram: 
- **W + R > N**
- W: No. of write to nodes before sending ack
- R: No. of read from nodes before sending response
- N: No. of Replica nodes in cluster 
- In reality cluster can have 1000s of nodes but designated node for replication will be only subset of nodes.
- Like For key x -> Replicate on A,B,C nodes
- A system with N nodes can tolerate floor(N/2) node failures. Generally N is odd number.
- Reads will fail when fewer than R nodes are available and writes will fail when fewer than W nodes are available.
- When multiple nodes can accept write -> Problem is when concurrent write happens for same record on multiple nodes. <CONFLICT>

Sloppy Quorum: 
- When write request comes and all designates subset of nodes for writes are not available then write is made at non designates nodes and Write req is accepted.
- Cassandra, Dynamo, Voldemort, all offer sloppy quorum feature.
- Later on when all the designated nodes that should replicate the value become reachable, the value is copied over to these designated nodes from the nodes that temporarily hold the value. This is known as hinted handoff.

References:
- https://medium.com/double-pointer/system-design-interview-course-31ddb8dfdafc

### Version Vector

- Version per Key schema
- WHen multiple replicas are involved each replica is assiged a version for each key

<TBD: Learn more about version vectors>

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

# Spark : Stream Processing Unit
- Can be used to consume data from Kafka and generate reports in real-time <To be confirmed>
- 

# Hadoop
- Use cases:
  - Pulling out reports
  - Custom queries on data for analytics purpose


# References
- https://github.com/donnemartin/system-design-primer
- DDIA notes: https://docs.google.com/document/d/1Yn6ee7WEK6r6q49mVX7uqbEtoYc9zizaQeIQy_c9Oh8/edit
- https://drive.google.com/file/d/16wtG6ZsThlu_YkloeyX8pp2OEjVebure/view
- https://www.youtube.com/c/HusseinNasser-software-engineering/videos
- [Mark and Sweep Garbage Collection Algorithm] https://www.youtube.com/watch?v=4qLf0FJMyf0
- https://systemdesign.one/
- Interview Prepration Medium Article: https://medium.com/double-pointer/system-design-interview-course-31ddb8dfdafc
