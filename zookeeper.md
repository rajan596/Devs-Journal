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


### References
1. https://medium.com/@jinlow/zookeeper-architecture-62a43cac53ce
2. (Zookeeper Whitepaper) https://www.usenix.org/legacy/event/atc10/tech/full_papers/Hunt.pdf
