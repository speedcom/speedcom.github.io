---
layout: post
title:  "JustinDB - data versioning"
date:   2017-04-15 14:15:00 +0100
categories: [DSP2017]
---

## Preface
[JustinDB][justindb] provides **eventaul consistency**, which allows for updates to be propagated to all replicas **asynchronously**.

[JustinDB][justindb] has a **tunable availability** characteristic. It is highly available to serve requests, with the ability to tune its level of availability–nearing, but never quite reaching, strong consistency. As you may have read previous post about [replication][justindb-replication] you know that every data is replicated to N distinct physical nodes (servers). Besides that it allows you to decide how many nodes must be written to or read from per request. These values are settings labeled **N** (the number of nodes to replicate to), **R** (the number of nodes read from before returning), and **W** (the number of nodes written to before considered successful).

Under certain failure scenarios (e.g. network partitions), updates may not arrive at all replicas for an extended period of time. Its worth to notice that updates in the presence of e.g. node failures can potentially result in an object having distinct version sub-hitories, which the system wil need to reconcile in the future.

[JustinDB][justindb] uses **Vector Clocks** in order to capture causality between different versions of the same object.

## Vector Clocks


[justindb]: https://github.com/speedcom/JustinDB
[justindb-replication]: http://speedcom.github.io/dsp2017/2017/04/13/justindb-replication-and-partitioning.html