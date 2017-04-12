---
layout: post
title:  "JustinDB - replication and partitioning"
date:   2017-04-10 10:00:00 +0100
categories: [DSP2017]
---

## Preface
Availability is the proportion of time a system is in a functioning condition. If a user cannot access the system, it is said to be unavailable. The manner in which a data set is distributed between multiple nodes is very important and this is how [JustinDB][justindb] is able to remain highly available, tolerating outages and network partitions.

[JustinDB][justindb] combines two styles of distribution to achieve this: **replication** and **partiions**.

### Replication
**Replication** is making copies of the same data on multiple nodes; this allows more servers to take part in the computation. [JustinDB][justindb] replicates by default.

If one node goes down, nodes that contain replicated data remain available to server requests. Besides improving availability of the system it also ameliorate performance by ma making additional bandwidth applicable to a new copy of the data. Imagine that you have cluster of two nodes: **A** and **B**. If all you do is replicate that to 2 servers, you would have 2 duplicate databases.

However, besides its obvious advantages replication brings many downsides. Since there are now independent copies of the data that has to be kept in sync on multiple machines it means ensuring that the replication follows a consistency model. There is also some network overhead; values must be routes to all replicated nodes on write.

### Partitions


[justindb]: https://github.com/speedcom/JustinDB
