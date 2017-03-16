---
layout: post
title:  "JustinDB - Database Model"
date:   2017-03-16 19:09:17 +0100
categories: DSP2017
---

### Database models
We can easly distinguish different groups of NoSQL databases the ways they represent data.
These characteristic is the answer that arose to specific problems not originally envisioned by the relational counterparts.

**1. Key/Value:**
They are conceptually like hashtables where values are stored and accessed by immutable key. This is a relatively easy to grasp model.
[JustinDB][justindb] along with e.g. `Riak` and `Redis` are exemplary products of such.

**2. Columnar:**
Its goal is to group similiar data into column families. They often characterizes itself with very good write throughput.
One of the most popular actively used database here is `Apache Cassandra`.

**3. Document**
These kind of datbases does not enforce a document schema. They owe their name because of storing hierarchical values called documents. Examples: `MongoDB`, `CouchDB`.

**4. Graph**
They are very good for modeling complex relationship between nodes e.g. friends (lets think about social media sites like Facebook or LinkedIn).
Examples: `Neo4j, OrientDB`

```
Be mind that some of them can be mixed together (e.g. key-value model on top of relational DB)
```

### JustinDB database model



[justindb]: https://github.com/speedcom/JustinDB