---
layout: post
title:  "JustinDB - Database Model"
date:   2017-03-16 19:09:17 +0100
categories: DSP2017
---

### Database models
Althought there are [numerous][nosql-database] differents NoSQL databases we can group them the ways they represent logical data. This characteristic is the answer that arose to specific problems not originally envisioned by the relational counterparts.

```
Be mind that the following models can be mixed together (e.g. key-value model built on top of relational DB)
```

**1. Key/Value:**
They are conceptually like hashtables where values are stored and accessed by immutable key. Such products usually offer relatively simple interface we work against with; more complex queries are often deprecated.
[JustinDB][justindb] along with e.g. `Riak` and `Redis` are exemplary products of such.

**2. Columnar:**
Its goal is to group similiar data into column families. They often characterizes itself with very good write throughput.
One of the most popular actively used database here is `Apache Cassandra`.

**3. Document:**
These kind of datbases does not enforce a document schema. They owe their name because of storing hierarchical values called documents. Examples: `MongoDB`, `CouchDB`.


They are very good for modeling complex relationship between nodes e.g. friends (lets think about social media sites like Facebook or LinkedIn).
Examples: `Neo4j, OrientDB`


#### JustinDB database model
Choosing logical model is a very important part at a time of architecture modeling.

I've decided to make JustinDB key-value oriented database. Reasoning was simple:
- we all intuitively know how key-value data structure works (think about e.g. `HashMap`)
- I wasn't forced to think about implementation of Query Planner (thing that translate complex queries (e.g. SQL query) into understandable structures by db engine against which it gets appropriate data)

That way I was able to implement more interesting parts to me of distributed databases. Whats more important its broadly used model so there are many available docs we can read.

Making such a decision has an important tradeoffs and impact how we design the rest of the system:
- how do we query complex set of data?
- how do we replicate data and spread them (do partitioning actually) across the cluster?

Trying to get answers is the real challenge and whole fun is starting exactly here.

#### When using key/value store make sense
Databases such as JustinDB almost always performs best when you build your application around basic CRUD application (create, read, update, and delete) on objects.

JustinDB's key/value architecture enables it to be more performant than relational databases in many scenarios because it doesn't need to perform lock, union, join, or other operations when working with objects. Instead, it interacts with objects on a one-by-one basis, using primary key lookups.

#### When using key/value store is less of a good fit
If you application demands a high query load by any means other than key/value lookup e.g. SQL-style `SELECT * FROM table` operations - JustinDB will not be as efficient as other databases. Other thing, so to speak, JustinDB's simple data model means that your data must be denormalized if your system is to be reasonably performant.

### Summary
Making decision about chosing key/value logical model was one of the most important part while architecting my own NoSQL database. Since its relatively easy concept to wrap head around it could help me to move faster to other fancy "distributed" stuff.


[justindb]: https://github.com/speedcom/JustinDB
[nosql-database]: http://nosql-database.org/