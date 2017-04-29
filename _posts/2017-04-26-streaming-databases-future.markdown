---
layout: post
title:  "Streaming Databases - the future"
date:   2017-04-25 08:20:00 +0100
categories: DSP2017
---

## Streaming World

We leave in the world where **streaming** has started to be a serious part of building distributed applications. In this ecosystem we face a lot of innovation at open source including such frameworks like _Apache Spark/Storm/Flink_, _Kafka/Samza_ or proprietary services such _AWS Lambda_ and _Google's DataFlow_.

Every single serious application needs some kind of data storage in order to store results and get them back later.
Thought, there is one thing that seems to be very constant at this field - **MONOPOL of RDBMs** (_Relational Database Management Systems_).

In typical software developer mindset these solutions seems to exists as the only one even though they often do not fit to application requirements!

In this artcile I'm going to cover a little bit different model - streaming database. Ever hear about such?

## Data Stream Management System (DSMS)

In typical RDBMs before we do any query against it we load needed data which are stored in atomic transactional way. Then we perform some subsequent queries to analyze what we want.

However, try to imagine the class of applications which have to deal with constant contiguous load of new data and do analysis of whem in time they arrive to system. Once the result is computed we present it in real time. Financial systems, network monitoring, server logs monitoring, traffic intensity and many more - systems with **massive load**. Minimalization of latency is crucial here and relying on RDBM system can lead to serious bottlenecks.

Contigous, dynamic, changing over time set of new data - this is what we call **STREAM**.

First term is coined. Next one that goes in pair is **Continous Queries**.

### STREAM model and Continous Queries

So how exactly such databases work?

Continous queries expect to working until they are deleted from system on coming contiguous stream of data (potentially ifinit). They do its work every single time new data arrives to system (not when client make requests). Client simply register itself and listen for generated results on the fly.

### Current projects

Most of them are done by Academia world but either are no longer supported or were just a playground for new ideas. I'm a fan of promising [PipelineDB][pipelinedb] which is an extension of Postgres database adding _CQL_ (continous query language; extension of SQL) for handling streaming data. Worth to mention is also [Kafka Streams][kafka-streams] which is build on top of Kafka distributed queue.

## Summary

Streaming programming is getting more and more attention. However, from perspective of databases its still something new and not very popular as it should be - lack of comercialization and too much pinned on RDBMs in typical developers mindset.

[pipelinedb]: https://www.pipelinedb.com/
[kafka-streams]: https://www.confluent.io/blog/introducing-kafka-streams-stream-processing-made-simple/