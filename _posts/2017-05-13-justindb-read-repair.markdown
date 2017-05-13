---
layout: post
title:  "JustinDB - solving data entropy: Read Repair"
date:   2017-05-13 14:00:00 +0100
categories: DSP2017
---

## Preface
[JustinDB][justindb] brings many techniques to achieve **high availability** and **duratibility** at the same time. To name few:
* [**The Ring**][justindb-ring] - **Consistent Hashing** algorithm trying to distribute workload across all cluster evenly
* [**Preference list**][justindb-preference-list] - list of nodes responsible for storing/fetching particular data replica
* [**Replication and Partitioning**][justindb-replication-partitioning] - creating multiple replicas of same data and splitting them across cluster nodes
* [**Vector Clocks**][justindb-vector-clock] - following made updates to every replica

[JustinDB][justindb] states that is **eventually consistent** database meaning that write will replicate to other nodes in time but there can be a bit of delay during which all nodes do not contain the same value. I've already wrote about how an **R/W** quorum can deal with differing values when write/read requests overlap at least one node. [JustinDB][justindb] has also a single strategy to address conflicting writes - **Vector Clocks**.

All this difference between replicas in time is called *entropy*. How to deal with it?

## Data Entropy
Entropy is a byproduct of **eventual consistency**.
[JustinDB][justindb] has created (or has plan to do) 2 *anti-entropy* strategies:
* **Read Repair** (our todays hero)
* **Active Anti-Entropy** (I will write more about it in next post)

### Read Repair

As the name states this technique solves *entropy* while reading data. When a successful read happens but not all replicas agree upon the value, *Read Repair* is triggered. How [JustinDB][justindb] reason about agreement? With [Vector Clocks][justindb-vector-clock]!

For example, we fetch data with ID=1 and R=3 (N is set to 3 also) - for some reason one of the fetched replica is considered to be "older" than rest. Once the final result could be easly deducted and returned to client behind the scene **Read Repair** mechanism was fired of in asynchronous manner decided to update source node with old replica to the newest value.

However, data which is never read can eventually be lost as nodes fail or/and are replaced. This is exactly where **Active Anti-Entropy** comes to the party!

[justindb]: https://github.com/speedcom/JustinDB
[justindb-ring]: http://speedcom.github.io/dsp2017/2017/05/06/justindb-ring.html
[justindb-replication-partitioning]: http://speedcom.github.io/dsp2017/2017/04/13/justindb-replication-and-partitioning.html
[justindb-preference-list]: http://speedcom.github.io/dsp2017/2017/05/07/justindb-preference-list.html
[justindb-vector-clock]: http://speedcom.github.io/dsp2017/2017/04/21/justindb-data-versioning.html