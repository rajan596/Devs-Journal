# Databases

## Types
1. CPU bound database: Where all indexes are fit in RAM and only limitation will be CPU
2. I/O bound database: Where indexes does not fit in RAM and goes to disk to acoomodate others. I/O is bottleneck here.

## ACID

### Availability

### Consistency
Represents if data is up to date in DB. i.e, If read is reflecting latest write in DB ?

- Linearizable consistency: Any read will give most uptodate data
    - Can have Low latency
    - Might not be highly available
- Evantual consistency: Read can have upto date data after some delay
- Casual consistency: 
    - If read related to earlier write then it should be executed in order.
    - It fails when performing aggregation operations..

### Integrity

### Durability

## Sharding and Partitioning
> Shard the database, partition the data.

Example: 
Database has P1, P2, P3, P4, P5 partitions. while...it has
Shard S1, S3.

So data can be like these...
1. S1: P1 + P2
2. S2: P3 + P4 + P 4

when request comes, it first decide partition and then decide in which shard this partition exists.

### Sharding
- Method of distributing data across **multiple servers**
- They are physical server
- Cons: Cross shard queries are expensive

### Partitioning
- Splitting data within same instance
- Data is considered to be partitioned.

:: Two categories:
[Vertical partitioning vs horizontal partitioning](https://blog.bytebytego.com/p/vertical-partitioning-vs-horizontal)
![](/assets/images/horizontal_vertical_partitioning.webp)

1) Horizontal Partitioning  : Rows are splitted
2) Vertical Partitioning    : columns are splitted

## Indexes
- Indexes are generally stored in-memory. Secondary indexes generally have reference to primary index. So its necessary to carefully select your index depending on the DB selected.

## Scaling
Increasing resources in order to accomodate higher read/write traffic on DB.

### Vertical scaling
- Increase single machine resources i.e, RAM, Disk, CPU
- It has physical limit on hardware. It cant scale after certain amount. 

### Horizontal scaling
- Add more machines to increase resources for DB.
- All machines work together as cluster to server requests.

## Master slave architectire
Master serves write requests and critical read requests while there are other read replicas which is only useful for 

# Transaction Isolation Levels

Transaction: When sequence of queries are grouped as one.

Isolation: How 2 parallel transaction can affect each other. 2 Transactions are isolated is queries in one transaction do not affect queries in other transaction.

### Read Uncommitted
- With this isolation level read queries may get dirty reads as well.
- Its fast
- Also problem when transaction is rolled back. In this case dirty read was never committed to DB

### Read committed
- It ensures only commited data is being returned to any read query 
- In this case uncommitted data for any transaction can have their own copy of uncommitted changes
- Sub sequent reads may get different values if there was a write on the same key by any other query or transaction. This may create problem is decision making is based in READ value of that key.

### Repeatable reads
- It behaves as if it has taken snapshot of data before transaction
- Once the COMMIT is performed all data is being committed to DB
- At this isolation level, we ensure that when a query reads a row, that row will remain unchanged for the entire transaction.
- This is known as **Snapshot Isolation** since a transaction is isolated from another by taking different data snapshots.
- If two transactions concurrently change the same key to different values, we must **roll back one transaction.**. This is called **Optimistic Concurrency Control** since we are optimistic about our changes until they are proven incompatible. 

### Serializable Isolation
- All transactions are executed in serialized or such that no 2 transactions affect each other
- This avoids Phantom reads. Phantom reads mean - when one read in transaction gets reasult X and same read query in transaction gets result Y. Here X != Y so some phantom/ghost value is added/removed.
- Highest level of Isolation

## Databases

1) MySQL
- #SQL #ACID 

2) PostgreSQL
- #SQL #ACID

3) MongoDB
- #NoSQL #ACID

4) Cassandra
- #NoSQL #ACID
- written in Java
- JVM paused execution while garbege collection

5) ScyllaDB
- #NoSQL #ACID
- Cassandra compatible DB written in C++
