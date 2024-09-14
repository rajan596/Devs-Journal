# Zookeeper

## Data storage on disk
Any changes to znodes are appended to transaction log and when the log file size increases, a snapshot of the current state of znodes is written to the filesystem. ZooKeeper tracks a fuzzy state of its own data tree within the snapshot files. This data tree is constantly evolving.


## Configuration Parameters
https://zookeeper.apache.org/doc/r3.1.2/zookeeperAdmin.html#sc_configuration

- the location where *ZooKeeper will store the in-memory database snapshots* and, unless specified otherwise, the transaction log of updates to the database.
dataDir=${datadir}

- 
dataLogDir=${datalogdir}

---
Zookeeper has 2 type of nodes
1. Ephemeral nodes: Session nodes. Gets created when application creates it and gets deleted when application no longer exists.

Use: This is mainly used in distributed system to keep track of any failures. All other required application keeps watch on this kind of node and when it gets deleted required processing is done like trying to be controller.

2. Persistent nodes: ZNodes which stays in zookeeper until deleted. Mainly useful for storing configurations.


## 4 letter words

### mntr
The "mntr" command in ZooKeeper provides a detailed summary of the current state of the ZooKeeper server. The output of the command includes the following information:

1. ZooKeeper version: The version number of the ZooKeeper software running on the server.
2. Latency min/avg/max: The minimum, average, and maximum time in milliseconds it takes for a ZooKeeper server to respond to a request.
3. Received/sent: The number of packets received and sent by the ZooKeeper server.
4. Connections: The number of currently established client connections to the ZooKeeper server.
5. Outstanding requests: The number of client requests that are currently waiting for a response from the server.
6. Zxid: The highest transaction ID seen by the ZooKeeper server.
7. Mode: The current operational mode of the ZooKeeper server (either standalone or leader/follower in a cluster).
8. Node count: The number of znodes (nodes in the ZooKeeper namespace) currently stored by the server.
9. Watch count: The number of registered watchers on znodes.
10. Ephemerals count: The number of ephemeral znodes (nodes that exist only as long as the client that created them remains connected).
11. Approximate data size: The approximate size of the data stored in all znodes.
12. Open file descriptor count: The number of open file descriptors used by the ZooKeeper server.
13. Max file descriptor count: The maximum number of file descriptors allowed for the ZooKeeper server process.

This information can be useful for monitoring the performance and state of a ZooKeeper server, and for troubleshooting issues that may arise.

### References
1. https://medium.com/@jinlow/zookeeper-architecture-62a43cac53ce
2. (Zookeeper Whitepaper) https://www.usenix.org/legacy/event/atc10/tech/full_papers/Hunt.pdf
