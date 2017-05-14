---
layout: post
title:  "JustinDB - HTTP API"
date:   2017-04-30 14:30:00 +0100
categories: DSP2017
---

## How to talk to JustinDB
[JustinDB][justindb] its a key/value store - you associate valeus with keys, and retrieve whem using the same keys. This means that basic commands are setting and getting values.

As I wrote in ["JustinDB - Modern REACTIVE NoSQL database"][justindb-reactive-database] one possibility to talk with [JustinDB][justindb] is **HTTP protocol**. The actions are currently related to HTTP methods (GET, PUT).


### **GET**

Its very simple form of read, responding with only the value.

```
GET /get?id={UUID}&r={r}
```

`r` is the number of nodes read from before returning

### **PUT**

The simplest write command in Riak is putting a value.

```
PUT /put body: { w: Int, value: Array[Byte], vectorClock: Base64 }
```
`w` is the number of nodes written to before considered successful.
`vectorClock` is the mechanism for partial ordering of values in [JustinDB][justindb]. You can read more about this mechanism [here][justindb-vector-clock].

Cheers! ✌️

[justindb]: https://github.com/speedcom/JustinDB
[justindb-reactive-database]: http://speedcom.github.io/dsp2017/2017/03/14/justindb-modern-reactive-nosql-database.html
[justindb-vector-clock]: http://speedcom.github.io/dsp2017/2017/04/21/justindb-data-versioning.html