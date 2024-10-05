# Introduction
- It was built by Facebook in 2007 to supports its Rapidly growing inbox search feature
- Cassandra is NoSQL Column Oriented Data store.
- Open source, distributed NoSQL DB
- Wide column database : Not all col is required to be present for all the rows
- Eventual consistenty model
- Cassandra is written in `Java` It faces problem of thread stopage at the time of Garbage Collection
- When a column has a write conflict between replicas, it is resolved via "last write wins".
- Does not support Transactions
- LSM Tree bases storage structure
- Cassandra does not have a concept of Foreign keys / relations. Doesn't favour normalisation.
- Performance can scale linearly with addition of nodes
- Combines distributed nature of Amazon's Dynamo and Column bases data store nature of Google's Big table
- Cassandra follows `leaderless architecture` avoiding any single point of failure

# Use cases
- **Store key value data** with high availability. Reddit uses Cassandra as persistent layer
- **Time series data model**
- **Write heavy application**

# Entities in Cassandra
- KeySpace: Equivalent to database in relational world
- Table
- Row
- Column
    - Cassandra do not store column with NULL value. This saves a lot of space.

# Cassandra Keys
- Primary Key = Partition Key + Clustering Key
- Partition Key: One or more columns used to decide which partition new data row will belong (Decides how data is distributed)
- Clustering Key: Zero or more column Used for Sort order (Can consider this as sort key) (Decides How data is stored within node)
- By default cassandra uses Murmur3 hash function to generate Hash from given input data to generate partition key

# Partitioning
- Cassandra achieves horizontal scaling by partitioning data across multiple machines
- Cassandra used `Consistent hashing` to partition data with virtual node concept to distribute evently data

# Replication
- Keyspaces have replication configurations specified and this affects the way Cassandra replicates data.
- If replication factor is 3 then cassandra will first pick main vnode to save data and then 2 more vnodes in clock wise direction to choose replicas
- Strategies for replication
    - NetworkTopologyStrategy: Recommended for Production and is physical hardware aware strategy which tries to replicate across data centre and keep in mind of same rack failure
    - SimpleStrategy: Finds replicas by going in clockwise direction

# Consistency
- Cassandra gives flexibility to choose between Consistency vs Availability
- Allows to choose from different consistency levels
    - ONE
    - ALL
    - QUORUM: Requires majority of replicas to respond 

# Query
- Any node can act as a cordinator in Cassandra. Client can connect to any node
- Each client knows about configuration and where data is present. This is because of Gossip protocol 

# Storage Model
- Cassandra is designed for High write throuput
- Cassandra storage model is based on **LSM Tree** - Log structured Merge tree
- Cassandra follows approach which favours write speed over read speed
- Cassandra workds on Append only mode and do not update actual data. It makes new entry with updated data and at the time of read considers entry with latest timestamp
- LSM Tree
    - Commit Log: WAL for durability
    - Memtable: An in-memory sorted data structure stores write data. Sorts based on Primary key
    - SSTable: Sorted String Table. Memtable is flushed to disk as an immutable SSTable after some threshold size is hit or some period of time elapses.
- Read path
    - Cassandra first checks Memtable
    - if not found then it checks with Bloom Filter in which SSTable data can be found
    - In a SSTable it reads in reverse order to find the latest data
- Compaction
    - Periodically Cassandra removes past row (outdated) entries and compacts SSTable. It can also merge multiple SSTables in single SSTable to save space and efficient search.
- SSTable Indexing
    - Cassandra maintains a file which says Key x is present at byte offset 4567 in SSTable for faster lookup

# Gossip
- Cassandra nodes communicate via Gossip protocol to exchange state information
- Cassnadra with the help of Gossip can find that certain node is unreachable. When write request comes for that node cordinator nodes keeps that write request with itself and when node comes online it handsoff that request to designated write request.

# Data Modeling
- Query driven data modeling
- Partition Key: determines the partition that the data is on
- Partition Size: max partition size
- Clustering Key: How the data should be sorted if required
- Data Denormalization

# Usage
- Uber uses Cassandra as a Managed service (Cassandra team manages cluster) [Uber Blog](https://www.uber.com/en-IN/blog/how-uber-optimized-cassandra-operations-at-scale/)

# References
- https://www.hellointerview.com/learn/system-design/deep-dives/cassandra
- https://blog.bytebytego.com/p/how-uber-scaled-cassandra-for-tens
- https://www.uber.com/en-IN/blog/how-uber-optimized-cassandra-operations-at-scale/
- https://github.com/apache/cassandra
- https://cassandra.apache.org/doc/latest/cassandra/architecture/dynamo.html