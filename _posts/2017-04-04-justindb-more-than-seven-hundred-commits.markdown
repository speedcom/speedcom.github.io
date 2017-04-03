---
layout: post
title:  "JustinDB - has got more than 700 commits!"
date:   2017-04-03 04:10:17 +0100
categories: [DSP2017]
---

### Next goal achieved 🎉
[JustinDB][justindb] is a project of mine which helps me to explore broad world of architectures of a databases system. With day of 23.04.2017 it passed **5 months of continous work** during which I achieved many set goals. Project has now more than **700 commits**, **6 watches**, **18 stars** and **4 forks** - these numbers mean very much to me (somebody may think that what I do in my sparse time is interesting).

The more I work on this project the more I realize how broad this topic actually is. DBMSs (Database Management Systems) are a critical components of modern computing and the result of decades of research in both industry and academia. Since they were among the earliest multi-user server systems to be developed thus pioneered many systems design techniques for scalability and relability (these days are concerns in many other contexts).

Builiding by own such advanced conceptually product is very demanding. There has been relatively sparse coverage in the literature of the systems design issues that make a DBMS work. Sometimes official docs of arleady created databases are a source of thruth and used as points of reference, particulary when multiple alternative designs have been adopted by different groups. Just think about all the concepts: process models, storage system design, parallel architecture, transaction system, querry processor and optimizer (plus some utilities and typical shared components).


### Summary of current implementation

I will let myself to point out what I've achieved/what topics were touched during mentioned 5 months of active development.
If you still hadn't a chance to get familiarize with JustinDB you can read my previous [post][justindb-reactive-database] (it covers many concepts).

* clustering - [JustinDB][justindb] is able to horizontally scale; we can add up and remove nodes from cluster configuration during runtime (depens on the user traffics this allows better utilization of server power consumption)

* replica records - data is copied to N of nodes in the cluster (in order to achieve high availability); we are able to tune how many of replicas we want to read/write (R/W) before we return result to client (N/R/W terminology).

* records versioning - [JustinDB][justindb] uses concept of Vector Clocks here

* entropy replica solving - existing of copies of same data and highly distributed systems brings the possibility of having not coverged replicas; this part is not finished fully yet though (Read-Repair and Active-Anti-Entropy are concepts I decided to use to figh against entropy)

* protocol - currently we can talk with [JustinDB][justindb] via http protocol

* pluggable backends - this is what I've described [here][justindb-pluggable-backends] recently

* serialization - during last week I was working on integration with [Akka-Kryo][akka-kryo] serialization plugin; I will describe this part more intensively in one of the next posts

* performance tests - [JustinDB][justindb] can be tested againts performance tests (not many of them though, heh) using this [repository][justindb-performance-tests]; tests are written with usage of [Gatling][gatling] toolkit

* CI/code coverage -

* modularized project -

* logo - yup, it has! 💖

* dockerizing/service discovery -

[justindb]: https://github.com/speedcom/JustinDB
[justindb-reactive-database]: http://speedcom.github.io/dsp2017/2017/03/14/justindb-modern-reactive-nosql-database.html
[justindb-pluggable-backends]: http://speedcom.github.io/dsp2017/2017/03/24/justindb-support-for-pluggable-persistent-and-in-memory-storage-engines.html
[akka-kryo]: https://github.com/romix/akka-kryo-serialization
[gatling]: http://gatling.io/
[justindb-performance-tests]: https://github.com/justin-db/JustinDB-load-testing