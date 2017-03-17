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

**4. Graph:**
They are very good for modeling complex relationship between nodes e.g. friends (lets think about social media sites like Facebook or LinkedIn).
Examples: `Neo4j, OrientDB`


#### JustinDB database model
Choosing logical model is a very important part at a time of architecture modeling.

I've decided to make JustinDB key-value oriented database. Reasoning was simple - it's easy to grasp concept and I was not forced to write my own queries planner (which was not something I was crazy about). Thanks to that I was able to implement more interesting parts of distributed databases to me. Whats more important its pretty broadly used model so there are many available docs we can read (and get the appropriate knowledge in the end).

Making such a decision has an important tradeoffs:
- how we queries complex set of data?
- how we replicate data and spread them (do partitioning actually) across the cluster?



[justindb]: https://github.com/speedcom/JustinDB
[nosql-database]: http://nosql-database.org/