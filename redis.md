
## 

- Used for caching data
- In-memory data store i.e, stores data in RAM
- It operates as a Single threaded 

## I/O multiplexing
- Redis do not get blocked in reading data from client socket using read() command but rather uses select() and poll() command to see out of all active socket pools where data is available to read and then triggeres socket read() command. This allows redis to avoid wait on read() command when data is not there.
- Once select() or poll() alerts Redis that data is ready to read on socket it reads and processed the event.