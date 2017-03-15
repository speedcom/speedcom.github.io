---
layout: post
title:  "JustinDB - Modern REACTIVE NoSQL database"
date:   2017-03-14 07:29:17 +0100
categories: DSP2017
---

### What is this all about
[NoSQL][no-sql] term was coined a decade ago. We have on market at least a dozen of different NoSQL databases with specific data model. They are so popular that many of engineers are not afraid to take them as a tool of first choice while bootsraping a new project.

Its tend to think that they are "scalable out of the box product" - lets assume thats true. But...

* What exactly causes it?
* What kind of algorithms and data structures are used in place?
* What trade-off database engineers are need to deal with?
* What makes that they are so-said scalable?

These are the questions we are going to answer through a couple of next posts.

### Why yet another database
[JustinDB][justindb] is an attempt to write our own distributed database. Its mainly educational goal. This project is far from being production ready in mind (such non-travial things are built over several years). Since its a complete writing from scratch I was happy to make it the way I would like - I was the one who decided what  algorithms, data structures and architecture will be. Being frankly, at current point JustinDB is a pretty faithfull implementation of [whitepaper][dynamodb-whitepaper] written by Amazon research team describing architecture of DynamoDB database (they prominent product used actively over decade in AWS).

Lets sum up main characteristic of [JustinDB][justindb] (these will be covered more extensively in next posts):
* key-value model:
every single value written to database has its uniqe own key across the whole database (cluster as well); database is value type agnostic which simply mean it can store e.g. strings, numbers and even binaries

* high-availablity:
this trait is naturraly welcome when we want to have solution that works possibly all the time; when one node of the cluster's node is not available during job processing then rest of them takes care of it

* partitioning:
data is split across the cluster by using technique called Consistent Hashing (JustinDB uses its modified version); it simply deduct what node of the cluster should store the pushed data and which node should be asked in order to get particular data by its key

* replication:
having splitted data across the cluster is not sufficient when we want to achive high-availability; data needs to be copied to a couple of distinct nodes

* data multi-versioning:
there are the cases when client wants to update its data; in distributed system its highly possible scenario that while updating specific record node with desired replica is not available so we can't update it to the newest version; we should follow such situations in order to not override our data with older versions (nobody wants stales reads); to achive that JustinDB uses Vector Clocks, CRDTs and different levels of fightining against data entropy e.g. Active-Anti Entropy and Merkle Trees (its used e.g. in Bitcoin)

* concurrency:
whole architecture is built on top of Actor Model - it greatly simplify modeling of concurrent operations

* [CAP theorem][cap-theorem]:
From perspective of this theorem JustinDB was designed to be AP system - highy available (A) and partition tolerant (P) and the same time. This was achived by bringing relaxed model of data (so called Eventually Consistent).

* stack:
database is entirely written in Scala using Akka library (e.g. its Cluster module); Admin UI is written using Elm language (still in progress though)

aaand last part...

* reactivness:
What does it mean? Is this sth that other databases don't have? From official site of [manifesto][reactive-manifesto] we can read that characteristic of such systems are: responsivness, resiliency, elasticity, message driven approach. JustinDB has (or try to have) all of it. Its very broad topic we will cover in one of the next post.

### Summary
JustinDB is realatively young project which still lack of many welcome features (some of them are in progress, some not even started). Its OOS so its really welcome to have contributors - I think that this topic can be very interesting to many of you.

You can find an official JustinDB respository [here][justindb].

In next post we are going to cover one of the above point more extensively looking closer how exactly NoSQL databases work and how JustinDB solely solves it.

[no-sql]: https://www.wikiwand.com/en/NoSQL
[justindb]: https://github.com/speedcom/JustinDB
[dynamodb-whitepaper]: http://www.allthingsdistributed.com/files/amazon-dynamo-sosp2007.pdf
[reactive-manifesto]: http://www.reactivemanifesto.org
[cap-theorem]: https://www.wikiwand.com/en/CAP_theorem
