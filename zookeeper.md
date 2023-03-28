# Zookeeper

Zookeeper has 2 type of nodes
1. Ephemeral nodes: Session nodes. Gets created when application creates it and gets deleted when application no longer exists.

Use: This is mainly used in distributed system to keep track of any failures. All other required application keeps watch on this kind of node and when it gets deleted required processing is done like trying to be controller.

2. Persistent nodes: ZNodes which stays in zookeeper until deleted. Mainly useful for storing configurations.


### References
1. https://medium.com/@jinlow/zookeeper-architecture-62a43cac53ce
2. (Zookeeper Whitepaper) https://www.usenix.org/legacy/event/atc10/tech/full_papers/Hunt.pdf
