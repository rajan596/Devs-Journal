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
