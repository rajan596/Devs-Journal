# Redis

## References
- [Redis Documentation](https://redis.io/docs/latest/develop/get-started/)

## Summary
- Used for caching data
- In-memory data store i.e, stores data in RAM. So lightening fast performance
- It operates as a Single threaded process
- Single machine can handle 1,00,000 requests/second

## Benefits / Usage
- Simple sharding and replication strategy
- Redis TTL auto expiry
- Lua scripts for complex logic and atomic operations
- Redis will remove entries from Memory when memory is full based on eviction policy
- At least once guarentee
- Usage
    - Cache frequently used DB data in redis
    - Can be used for Rate limiter. Sample example below..
        - INCR product_service_rate_limit --> Atomic increment
        - EXPIRE product_service_rate_limit 60 
    - Streams - Way to distribute work among multiple workers
        - Ordered list of items
    - Leaderboard | Sorted Set
        - ZADD leaderboard_contest_101 200 "rajan" # add rajan entry with score 200 in leaderboard_contest_101 sorted set
        - We can also remove n top keys from sorted set
    - Geospatial Index
        - GEOADD homes_list -122.2 37.89 rajan_home
        - GEOSEARCH homes_list FROMLONLAT -122.2 37.89 BYRADIUS 5km WITHDIST
    - PubSub : Chat Room. At most one guarentee. Reliability issue. Fast

## Scaling with Redis
- If data is huge single Redis instance cant handle it
- Redis node can have its replica or secondary machine. 
- Data is partitioned to diff machines based on key hash using key space
- Redis writes to disk at specific intervals. So Redis might loose some data in-between this time

## Redis as a Key-Value pair cache


## Redis as a Pub Sub

- Client can publish in **channel** and interested subscribers can subscribe from given channel
- Redis Pub/Sub is synchronous. Subscribers and publishers must be connected at the same time in order for the message to be delivered.
- It limits use cases to those that can tolerate potential message loss.
- Redis Pub/Sub is considered a “Fire & Forget” messaging system
- Redis Pub/Sub is fan-out only, meaning that when a publisher sends a message, it is broadcast to all active subscribers.
- References
    - https://medium.com/@joudwawad/redis-pub-sub-in-depth-d2c6f4334826

## GeoSpatial Search

## Bloom Filters

# Architecture

## Why Redis is Fast even if its Single threaded ?

- Pure In-memory operations
- I/O Multiplexing with Non-Blocking I/O and Client Connection Management
    - Redis do not get blocked in reading data from client socket using read() command but rather uses select() and poll() command to see out of all active socket pools where data is available to read and then triggeres socket read() command. This allows redis to avoid wait on read() command when data is not there.
    - Once select() or poll() alerts Redis that data is ready to read on socket it reads and processed the event.
    - This was Redis creates only single thread for managing multiple concurrent connections
- Client library optimizations
    - Often client library creates only one connection thread which is used across all threads to maintain min connections required with server
- Redis is designed for operations that are not CPU-intensive. The primary bottlenecks in Redis typically come from memory and network bandwidth rather than CPU. Redis recommends to increase performance deploy more servers and build a cluster.

# References
- https://medium.com/@aditimishra_541/why-is-redis-so-fast-despite-being-single-threaded-dc06ba33fc75