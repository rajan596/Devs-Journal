# Redis

# References
- [Redis Documentation](https://redis.io/docs/latest/develop/get-started/)

## Summary

- Used for caching data
- In-memory data store i.e, stores data in RAM
- It operates as a Single threaded process

## Benefits
- Simple sharding and replication strategy
- Redis TTL auto expiry
- Lua scripts for complex logic and atomic operations

## Redis as a Pub Sub

- Client can publish in **channel** and interested subscribers can subscribe from given channel
- Redis Pub/Sub is synchronous. Subscribers and publishers must be connected at the same time in order for the message to be delivered.
- It limits use cases to those that can tolerate potential message loss.
- Redis Pub/Sub is considered a “Fire & Forget” messaging system
- Redis Pub/Sub is fan-out only, meaning that when a publisher sends a message, it is broadcast to all active subscribers.

References: 
- https://medium.com/@joudwawad/redis-pub-sub-in-depth-d2c6f4334826

## I/O multiplexing
- Redis do not get blocked in reading data from client socket using read() command but rather uses select() and poll() command to see out of all active socket pools where data is available to read and then triggeres socket read() command. This allows redis to avoid wait on read() command when data is not there.
- Once select() or poll() alerts Redis that data is ready to read on socket it reads and processed the event.

## GeoSpatial Search
- 