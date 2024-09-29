# MongoDB

- Each entry in Mongo is referred as Document
- Table is called as Collection in MongoDB
- Json Document is referred as BSON (Binary Json)
- In MongoDB Primary key is automatically set to _id
- Mongo dupported document embedding for 1:1 or 1:Many relationships, So no Joins required here.
- MongoDB shines when it comes to sharding. *One of the main readon for using MongoDB will be its sharding capability*
- Written in `C++`

# Types of Instances
There are 3 types of Mongo Instances
1. Mongos (A router that our API will hit)
2. Config server (Stores meta data and router required details)
3. Shard Server (Stores actual data)

## Mongos

MongoSh: MongoDB Shell

## Config Server
- Should be deployed as replica set for HA

## Shard Server
- Should be deployed as replica set for HA

# Storage Layer
- How data is stored in MongoDB ?
- 2 Storage Engines
    - Old: MMAPv1 (This is having faster reads compared to WiredTiger. So some companies still use thid model)
    - New: WiredTiger (Default from v3.2)

## WiredTiger
- Stores data in `B+ Tree` structure
- Supports
    - Compression
    - Encryption
    - Multi document transaction
    - Document level concurrency control with MVCC model (Multi version concurrency control)
- For most read and write operations, WiredTiger uses optimistic concurrency control
- WiredTiger uses MultiVersion Concurrency Control (MVCC).
- WiredTiger uses a write-ahead log (i.e. journal) in combination with checkpoints to ensure data durability.
- By default, WiredTiger uses `block compression` with the `snappy compression` library for all collections and prefix compression for all indexes
    - Snappy is a compression/decompression library. 
        - It does not aim for maximum compression, or compatibility with any other compression library; instead, it aims for very high speeds and reasonable compression. 
        - For instance, compared to the fastest mode of zlib, Snappy is an order of magnitude faster for most inputs, but the resulting compressed files are anywhere from 20% to 100% bigger. On a single core of a Core i7 processor in 64-bit mode, Snappy compresses at about 250 MB/sec or more and decompresses at about 500 MB/sec or more.
        - Cassandra, Couchbase, Hadoop, Lucene, CouchDB, VoltDB also uses snappy - Lib by Google
    - Compression setting can be applied at collection and index level as well

# Indexes in Mongo

- Index in Mongo can be created as below
```bash
db.blog.createIndex(
   {
     content: "text",
     "users.comments": "text",
     "users.profiles": "text"
   },
   {
     name: "InteractionsTextIndex"
   }
)
```
- Types of Indexes
    - Single Field
        - By default, all collections have an index on the _id field. 
        - Syntax : db.<collection>.createIndex( { <field>: <sortOrder> } )
        - sortOrder: 1 -> Keep information in ASC order, -1: DESC order
    - Compound Index
        - Index on multiple keys
        - Compound indexes collect and sort data from **two or more fields** in each document in a collection. Data is grouped by the first field in the index and then by each subsequent field.
        - The order of the indexed fields impacts the effectiveness of a compound index.
        - Code
        ```bash
            db.<collection>.createIndex( {
                <field1>: <sortOrder>,
                <field2>: <sortOrder>,
                ...
                <fieldN>: <sortOrder>
            } )
        ```
    - Multi Key Index
        - Multikey indexes collect and sort data from fields containing array values. Multikey indexes improve performance for queries on array fields.
        - You do not need to explicitly specify the multikey type. When you create an index on a field that contains an array value, MongoDB automatically sets that index to be a multikey index.
    - Geospatial Indexes
    - Hash Index
        - Hashed indexes collect and store hashes of the values of the indexed field.
        - **To enable sharding** for a collection that already contains data, you must create an index that supports the shard key.
        - Code
        ```bash
            db.<collection>.createIndex(
                {
                    <field>: "hashed"
                })
        ```
- Different types of Indexes which can be created in Mongo
    - Case Sensitive Index: Ignores case
    - Hidden Index
        - Not visible to query planner and can not be used to support query
        - Use: You can use hidden indexes to evaluate the **potential impact of dropping an index without actually dropping it.** If the impact is negative, you can unhide the index instead of having to recreate a dropped index. Hidden indexes are fully maintained and can be used immediately once unhidden.
        - Sort of Dry running index dropping process without actually dropping it.
    - Partial Index
        - Indexes data which meets mentioned filter criteria i.e, like count > 1000
        - Lower storage requirements
        - We want to only see popular content having likes count > 1000. Se we create index having filter criters as likes > 1000. This will reduce index memory footprint drastically as 90% content might be having likes < 1000.
        - Another use case: Get movies having rating >= 4
        - Code
        ```bash
            db.restaurants.createIndex(
                { cuisine: 1, name: 1 },
                { partialFilterExpression: { rating: { $gt: 5 } } }
                )
        ```
        - To use the partial index, a query must contain the filter expression (or a modified filter expression that specifies a subset of the filter expression) as part of its query condition.
    - Sparse Index
        - Sparse indexes only contain entries for documents that have the indexed field. These indexes skip documents that do not have the indexed field.
    - TTL Index
        - TTL indexes automatically remove documents from a collection after a certain amount of time. Use these indexes for data that only needs to persist for a finite amount of time, like machine generated event data, logs, and session information.
        - Use Case: We want to have queries for recent 30 min data only. So we can have 30 min as TTL and previous data will be removed from index
    - Unique Index
        - Unique indexes cause MongoDB to reject duplicate values for the indexed field.
    - References
        - https://www.mongodb.com/docs/manual/core/indexes/index-properties/
- References
    - https://www.mongodb.com/docs/manual/core/indexes/create-index/specify-index-name/

## Building Indexes in existing collection
- Index builds use an optimized build process that holds an `exclusive lock` on the collection at the *beginning* and *end* of the index build but not in-between.
- For creating unique index in existing collection, data can live during index build but once index build is finished it throws and Error and terminates the build.
- Create index like uniqueness can succeed on some of the shards but might fail on other shards. To make things consistent we must drop index from other shard where it was
- Things to keep in mind while index creation
    - At the time of Write heavy workload performance might degrade and index creation will take longer time to finish
    - We should either stop application or select a time where less write workload is expected for smoother index build
    - Index creation process utilised In-memory + Temp file to complete the process. By default it used 200 mb ram. If memory goes beyond this value then it used _tmp file directory and creates temp disk file.
        - We can also increase available RAM for index creation by setting `maxIndexBuildMemoryUsageMegabytes` and/or increase server RAM to fasten up index building time. But we need to ensure overall server memory do not get hit because of this
- The primary requires a minimum number of data-bearing voting members (i.e commit quorum), including itself, that must complete the build **before marking the index as ready for use**
- **Rolling Index Build**
    - For workloads which cannot tolerate performance decrease due to index builds, consider performing a rolling index build process
    - Rolling index builds take at most one replica set member out at a time, starting with the secondary members, and builds the index on that member as a standalone.
- Commit Quorum Contrasted with Write Concern
    - Index builds use commit quorums. The commit quorum specifies how many data-bearing voting members, or which voting members, including the primary, must be prepared to commit a simultaneous index build before the primary will execute the commit.
    - Write operations use write concerns. The write concern is the level of acknowledgment that the write has propagated to the specified number of instances.
    - The commit quorum specifies how many nodes must be ready to finish the index build before the primary commits the index build. In contrast, when the primary has committed the index build, the write concern specifies how many nodes must finish the index build before the command returns.

- Things to keep in mind while creating Indexes: https://www.mongodb.com/docs/manual/applications/indexes/
    - ESR Rule (Equality , Sort, Range)
        - Create index in this Order : Equality key -> Sort key -> range key
        - Reference: https://www.mongodb.com/docs/manual/tutorial/equality-sort-range-rule/

# Replication
- A *replica set* in MongoDB is a group of mongod processes that maintain the same data set.
- Provides High availability, Fault tolerance from machine failure and increased read capacity
- Among all nodes having same data copy - one will be primary and others will be secondary nodes
- **Arbitar Node**
    - In some circumstances (such as you have a primary and a secondary but cost constraints prohibit adding another secondary), you may choose to add a mongod instance to a replica set as an arbiter.
    - An arbiter participates in elections but does not hold data (i.e. does not provide data redundancy). For more information on arbiters, see Replica.
- Async Replication: Replica's replicate Primary's OpLog and appy its operations on its data and copies data in async way
- Replication Lag: Replication lag is a delay between an operation on the primary and the application of that operation from the oplog to the secondary.
- Automatic Failover 
    - When primary do not communicate with secondaries within configured timeout (default 10 sec) leader election happens and new leader is elected.
    - The median time before a cluster elects a new primary should not typically exceed `12 seconds`
    - During this time read request can be served from Secondary/replica nodes but any request to Primary or Write will fail.
    - MongoDB Drivers typically handles failover scenarios and retries internally
- Read Preference
    - By default, *clients read from the primary* however, clients can specify a read preference to send read operations to secondaries.
- Mirrored Reads
    - Mirrored reads reduce the impact of primary elections following an outage or planned maintenance.
    - Mirrored reads pre-warm the caches of electable secondary replica set members.
    - To pre-warm the caches of electable secondaries, the primary mirrors a sample of the supported operations it receives to electable secondaries.
    - Mirrored reads do not affect the primary's response to the client. The reads that the primary mirrors to secondaries are "fire-and-forget" operations. The primary doesn't await responses.
- Initial Sync
    - When new DB instance comes up it checks where to get initial data from and then syncs all the databases present on other machine except `local` database
    - When the initial sync finishes, the member transitions from STARTUP2 to SECONDARY.
    - File Copy Based Initial Sync
        - Available in MongoDB Enterprise only.
        - File copy based initial sync runs the initial sync process by copying and moving files on the file system.
        - This sync method can be faster than logical initial sync.
        - Fault tolerance: It tries to retry initial sync based on temp network failure or can decide to stop and choose another healthy node to replicate from
- Replication
    - Secondary members replicate data continuously after the initial sync
    - Secondary members copy the oplog from their sync from source and apply these operations in an asynchronous process.
- Types of Secondary Members
    - **Priority 0 Member**
        - Configuring a replica set member with priority 0 might be desired if the particular member is *deployed in a data center that is distant from the main deployment* and therefore has higher latency. It may serve *local read requests* well, but might not be an ideal candidate to perform the duties of a primary due to its latency.
        - A priority 0 member is a member that cannot become primary and cannot trigger elections.
        - In other word this node is configured to not become Primary node
        - Default Priority level is `1`. So, other members would be preffered to become primary node
    - **Hidden Member**
        - A hidden member maintains a copy of the primary's data set but is invisible to client applications.
        - Must be priority 0 member
        - Use hidden members for dedicated tasks such as reporting and backups.
    - **Delayed Member**
        - Delayed members contain copies of a replica set's data set. However, a delayed member's data set reflects an earlier, or delayed, state of the set. For example, if the current time is 09:52 and a member has a delay of an hour, the delayed member has no operation more recent than 08:52.
        - may help you recover from various kinds of human error
        - Must be priority 0 members. 
        - Must be hidden members. 
- References: https://www.mongodb.com/docs/manual/replication/

# Sharding
- Hashed Sharding
- Range based Sharding
- References: https://www.mongodb.com/docs/manual/sharding/

# Elections
- Replica sets use Elections to determine next Primary node
- Used in automatic failover mechanism


# References
- https://medium.com/@edberttantra/database-adventures-mongodb-internals-and-benchmarking-d2cd6e128128
- Mongo Repository: https://github.com/mongodb/mongo/tree/master/src/mongo/db
- Official Documentation: https://www.mongodb.com/docs/manual/core/wiredtiger/
- https://google.github.io/snappy/
- Last Read on: https://www.mongodb.com/docs/manual/applications/replication/ 


