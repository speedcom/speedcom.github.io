---
layout: post
title:  "JustinDB - serialization that greatly improves performance"
date:   2017-04-07 08:00:00 +0100
categories: [DSP2017]
---

### Preface
[JustinDB][justindb] is a highly distributed system which forms cluster of nodes that communicate each other via message passing. All of these messages (think about internal communication of nodes or typical data replication) are send over wire (network). In order to being able do that we have to serialize and deserialize passing state to single bytes back and forth. That can costs. Sometimes a lot. We tend to think that network is always the slowest part of distributed systems communication. Numerous tests however show that using not an efficient serializer can lead to getting times that are on par with a network hop. Thats crazy!

[JustinDB][justindb] is build on top of [Akka Cluster][akka-cluster] toolkit.
Akka has a built-in Extension for serialization which allows us to use the built-in serializers or even write our own.
The mechanism is both used by Akka internally to serialize messages and ad-hoc serialization of whatever message you might need it for.

Quick notes to follow:
1. Current remoting module of Akka uses standard Java Serializer mechanism.
2. Since the 2.4.11 release of Akka it is possible to entirely disable the default Java Serialization mechanism.
3. New remoting implementation (codename [Artery][akka-artery], still experimental though) does not use Java serialization for internal messages by default.
4. All messages that are send in the same local Actor System don't need to be serialized as in-memory message passing is done via references.

Unfortunately Java Serializer is one of the exemplary mechanism known to be slow - it never was designed for high throughput messaging though. At the same time it's very convenient to use - when prototyping your project you can focus on logic implementation and leave this part for later.

When talking about serialization mechanism in terms of [JustinDB][justindb] I've recently decided to move on and switch to using **Akka-Kryo** extension which provides custom Kryo-based serializers for Scala and Akka.

### JustinDB and Kryo got married 👰
[Kryo][kryo] is known to be a very fast and efficient serialization. I've decided to use [Romix’s kryo-based serialization library] which is a well maintained project.



[justindb]: https://github.com/speedcom/JustinDB
[akka-cluster]: http://doc.akka.io/docs/akka/current/java/cluster-usage.html
[akka-artery]: http://doc.akka.io/docs/akka/current/scala/remoting-artery.html
[akka-kryo]: https://github.com/romix/akka-kryo-serialization
[kryo]: https://github.com/EsotericSoftware/kryo