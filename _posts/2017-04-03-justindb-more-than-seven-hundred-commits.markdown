---
layout: post
title:  "JustinDB - has got more than 700 commits!"
date:   2017-04-03 18:59:00 +0100
categories: [DSP2017]
---

### Next goal achieved 🎉
[JustinDB][justindb] is a project of mine which helps me to explore broad world of architecture of a database systems. With day of 23.04.2017 it passed **5 months of continous work** during which I achieved many set goals. Project has now more than **700 commits**, **6 watches**, **18 stars** and **4 forks** - these numbers mean very much to me (makes me very motivated to continue working on it).

The more I work on this project the more I realize how broad this topic actually is. DBMSs (Database Management Systems) are a critical components of modern computing and the result of decades of research in both industry and academia. Since they were among the earliest multi-user server systems to be developed thus pioneered many systems design techniques for scalability and relability (these days are concerns in many other contexts).

Builiding by own such advanced conceptually product is very demanding. There has been relatively sparse coverage in the literature of the systems design issues that make a DBMS work. Sometimes official docs of arleady created databases are a source of thruth and used as points of reference, particulary when multiple alternative designs have been adopted by different groups. Just think about all the concepts: process models, storage system design, parallel architecture, transaction system, querry processor and optimizer (plus some utilities and typical shared components). Very, very deep subject.


### Summary of current implementation 👷

I will let myself to point out what I've achieved/what topics were touched during mentioned 5 months of active development.
If you still hadn't a chance to get familiarize with JustinDB you can read my previous [post][justindb-reactive-database] (it covers many concepts).

* clustering - [JustinDB][justindb] is able to horizontally scale; we can add up and remove nodes from cluster configuration during runtime (depens on the user traffics this allows better utilization of server power consumption)

* replication - data is copied to N of nodes in the cluster (in order to achieve high availability); we are able to tune how many of replicas we want to read/write (R/W) before we return result to client (the main advantage of [JustinDB][justindb] is that its client applications can tune the values of N, R and W to achieve their desired levels of performance, availability and durability).

* records versioning - [JustinDB][justindb] uses concept of Vector Clocks here

* entropy replica solving - existing of copies of same data and highly distributed systems brings  high possibility of having not coverged replicas; this part is not finished fully yet though (Read-Repair and Active-Anti entropy algorithms)

* communication protocol - currently we can talk with [JustinDB][justindb] via http protocol

* pluggable backends - this is what I've described [here][justindb-pluggable-backends] recently

* serialization - during last week I was working on integration with [Akka-Kryo][akka-kryo] serialization plugin; I will describe this part more intensively in one of the next posts

* performance tests - [JustinDB][justindb] can be tested againts performance tests (not many of them though, heh) using this [repository][justindb-performance-tests]; tests are written with usage of [Gatling][gatling] toolkit

* CI/code coverage - every single time new commit is pushed to master branch Travis fires off new task running tests and countng code coverage; we can see relevant badges in README file

* modularized project - I've done many small/bigger refactoring into this; project is splitted into many sub-projects (its still monorepo though) which allows for better separation of concerns

* logo - yup, it has! 💖

* dockerizing/service discovery - I would like to start full [JustinDB][justindb] cluster under container with single command; I've dont first attemp in integration with [Consul][consul] and [Docker][docker] but its still big WIP

### Set goals 🏆
I am pretty happy with what I've actually achieved during this time. 💯 I'm aware of that I'm nowhere close to current existing solutions but it doesn't matter - the most important part for me is constant learning, having fun and getting familiarize with really great topic (who knows what this project can bring to me in the future).

Nevertheless, its not the end. Its beggining.

The most intriguing part to me about database now is cooperation of engine with storage - how to store and get effectively data to/from it. I know that this going to be really challenging (simply can't wait to implement own B+ tree data structure or sth similiar which will be a basic building block).

When not talking about technical specification I wish some day [JustinDB][justindb]:
* will have **100 stars** on GitHub
* will have more than **1000 commits**
* will be a point reference for people who would like to get familiarize with concepts that stand behind databases

Wish me luck! 🤞

[justindb]: https://github.com/speedcom/JustinDB
[justindb-reactive-database]: http://speedcom.github.io/dsp2017/2017/03/14/justindb-modern-reactive-nosql-database.html
[justindb-pluggable-backends]: http://speedcom.github.io/dsp2017/2017/03/24/justindb-support-for-pluggable-persistent-and-in-memory-storage-engines.html
[akka-kryo]: https://github.com/romix/akka-kryo-serialization
[gatling]: http://gatling.io/
[justindb-performance-tests]: https://github.com/justin-db/JustinDB-load-testing
[consul]: https://www.consul.io/
[docker]: https://docker.com/
