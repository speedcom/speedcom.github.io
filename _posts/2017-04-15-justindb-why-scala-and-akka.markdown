---
layout: post
title:  "JustinDB - why Scala and Akka?"
date:   2017-04-15 10:25:00 +0100
categories: [DSP2017]
---

## Preface
As you may read [JustinDB - Modern REACTIVE NoSQL database][JustinDB - Modern REACTIVE NoSQL database] post you know that [JustinDB][justindb] is written in **Scala** language and built on top of **Akka** toolkit.

## Why these?
Because of the assumptions that were made during designing architecture of [JustinDB][justindb]. I wanted to make highly-available, fault-tolerant, performant, concurrent and as less as error-prone product during runtime.

This means that I need to have tools that embrace these ideas from the ground up.

### Scala
Scala is the language which is perfect to build high throughput and low latency systems. It is a statically typed language that runs on the JVM (the best managed runtime out there). This trait means:
* excellent performance (comparing even to Java which is relatively a lower level language) 🔥
* compiler is your friend - catching bugs and design errors early in the development process; easier to do refactoring

Citing Wikipedia:

```
The name Scala is a portmanteau of scalable and language, signifying that it is designed to grow with the demands of its users.
```

Last but not least, Scala was designed to be concise. It is a very expressive language thus beeing statically typed language at the same time. It has many features of functional programming languages, just naming a few: type inference, immutability, lazy evaluation, pattern matching and many many more. All of these help to build better context of the program.

Scala, as woking on JVM, inherits its concurrent primitives. It also brings high-level concurrency abstractions, each tailored toward a specific class of programmings tasks.

Its not a surprise that Scala has became one of the most widely used language in **Big Data** world.

### Akka
Akka is a toolkit and runtime for building highly concurrent, distributed, and fault tolerant applications on the JVM.
Does it sound like something that would be a good fit for our purposes? Don't even ask.

Writing concurrent programs is hard but at the same time it **REALLY MATTERS**.
Many multi-threading abstractions assume the presence of shared memory (futures/promises, concurrent data structures, STM and so on and on). This assumption limits them to applications running on a single computer.

Akka’s approach to handling concurrency is based on the **Actor Model**. In this programming model, we create program represented by a large number of entities (called actors) that execute computations independently and communicate by passing messages.

```
All the complexity of creating and scheduling threads, receiving and dispatching messages, and handling race conditions and synchronization, is relegated to the framework to handle transparently.
```

Very prominent part about **Akka** is its **Cluster** module which is used by [JustinDB][justindb]. Thanks to it we can make our system to span across multiple machines that communicate through a computer network. The actor model allows you to write programs that run inside a single process, multiple processes on the same machine (this is how actually I test my local instance of database), or on multiple machines (this is called **location transparency**).

## Summary
[JustinDB][justindb] tries to take the best from **Scala** and **Akka**. Both tools could help me to quickly build barebones of the distributed database and move forward with new features which prove that they simplifies and facilitates the development of such products. 💕

[justindb]: https://github.com/speedcom/JustinDB
[JustinDB - Modern REACTIVE NoSQL database]: http://speedcom.github.io/dsp2017/2017/03/14/justindb-modern-reactive-nosql-database.html