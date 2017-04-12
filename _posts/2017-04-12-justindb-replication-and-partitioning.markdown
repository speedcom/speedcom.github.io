---
layout: post
title:  "JustinDB - replication and partitioning"
date:   2017-04-09 10:00:00 +0100
categories: [DSP2017]
---

## Preface
Availability is the proportion of time a system is in a functioning condition. If a user cannot access the system, it is said to be unavailable. The manner in which a data set is distributed between multiple nodes is very important and this is how [JustinDB][justindb] is able to remain highly available, tolerating outages and network partitions.

[JustinDB][justindb] combines two styles of distribution to achieve this: **replication** and **partiions**.

## Replication
**Replication** is making copies of the same data on multiple nodes; this allows more servers to take part in the computation. [JustinDB][justindb] replicates by default.

If one node goes down, nodes that contain replicated data remain available to server requests. Besides improving availability of the system it also ameliorate performance by ma making additional bandwidth applicable to a new copy of the data. Imagine that you have cluster of two nodes: **A** and **B**. If all you do is replicate that to 2 servers, you would have 2 duplicate databases.

However, besides its obvious advantages replication brings many downsides. Since there are now independent copies of the data that has to be kept in sync on multiple machines it means ensuring that the replication follows a consistency model. There is also some network overhead; values must be routes to all replicated nodes on write.

## Partitioning
**Partitioning** is a way of dividing a set of keys onto separate physical servers. We pick one node to exclusively host a range of keys. This is used to reduce the impact of dataset growth since each partition is a subset of the data. This technique also improves performance by limiting the amount of data to be examined on single partition.

Data is partitioned via some intrinsic mechanism which deducts what partition is going to be chosen for a particular value (its very often application-specific).

Imagine that you have cluster of two nodes: **A** and **B**. Its goal is to store numbers from 1 to 100. We can say that range from 1 to 50 goes to **A** node (partition) and rest to **B** partition (this is our pattern of splitting data and is all about actually).

However, having independent partitions comes with some cost - we must keep track of what range of values live on which node. Think also about potential different rate of growth and inefficient access across partitions. There is also additional downside - if one node goes down, that entire partition of data in unavailable.

And this is why [JustinDB][justindb] uses both **replication** and **partitioning**.

## Replication + Partitioning 💕
[JustinDB][justindb] partition data across multiple nodes, as well as replicate that data into a couple of them. Thanks to such decision it get improves availability and increase capacity and the same time.

![][partition-and-replication-image]

Try to imagine a cluster of 5 nodes with set replication factor to 3. That means when we create a new value in system its saved to 3 standalone nodes. We should be able to get this data laterback from every of these nodes.

I've mentioned that partitioning mechanism apply some specific pattern of splitting data - thats correct. How [JustinDB][justindb] actually reason what nodes should be taken into account when storing new value (along with replicas)?

It uses modified version of **Consistent Hashing** algorithm that is widely used by such databases like **Cassandra** or **Riak**. I'm going to cover this topic more extensively in next post.

Stay tuned! ✌️

[justindb]: https://github.com/speedcom/JustinDB
[partition-and-replication-image]: ../../../../../partition-with-replication.jpg "Partition with replication"
