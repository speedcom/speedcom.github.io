---
layout: post
title:  "JustinDB - serialization that greatly improves performance"
date:   2017-04-08 20:00:00 +0100
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

Unfortunately Java Serializer is one of the exemplary mechanism known to be slow - it never was designed for high throughput messaging though. At the same time it's very convenient to use - when prototyping your project you can focus on logic implementation and leave this part for later time.

When talking about serialization mechanism in terms of [JustinDB][justindb] I've recently decided to move on and switch to using **Akka-Kryo** extension which provides custom Kryo-based serializers for Scala and Akka.

### JustinDB and Kryo got married 👰
[Kryo][kryo] is known to be a very fast and efficient serialization. I've decided to use [Romix’s kryo-based serialization library][akka-kryo] which is a well maintained project (exemplary implementation of own Akka Serializer).

You can find all defined [JustinDB][justindb] payload serializers [here][justindb-payload-serializers].

**SerializerInit** is an entrypoint for Akka-Kryo extension to know what payload/classes/messages we want to register into it. As you can see every registration line consists of msg class definition, its dedicated serializer function and ID.
This class is instantiated right after new process of [JustinDB][justindb] is rolling out.

```
class SerializerInit extends StrictLogging {

  def customize(kryo: Kryo): Unit = {
    logger.info("Initialized Kryo")

    // cluster
    kryo.register(classOf[justin.db.actors.protocol.RegisterNode], RegisterNodeSerializer, 50)

    // write -- request
    kryo.register(classOf[justin.db.actors.protocol.StorageNodeWriteDataLocal], StorageNodeWriteDataLocalSerializer, 60)

    // write -- responses
    kryo.register(classOf[justin.db.actors.protocol.StorageNodeFailedWrite],     StorageNodeWriteResponseSerializer, 70)
    kryo.register(classOf[justin.db.actors.protocol.StorageNodeSuccessfulWrite], StorageNodeWriteResponseSerializer, 71)
    kryo.register(classOf[justin.db.actors.protocol.StorageNodeConflictedWrite], StorageNodeWriteResponseSerializer, 72)

    // read - request
    kryo.register(classOf[justin.db.actors.protocol.StorageNodeLocalRead], StorageNodeLocalReadSerializer, 80)

    // read - responses
    kryo.register(classOf[justin.db.actors.protocol.StorageNodeFoundRead],      StorageNodeReadResponseSerializer, 90)
    kryo.register(classOf[justin.db.actors.protocol.StorageNodeConflictedRead], StorageNodeReadResponseSerializer, 91)
    kryo.register(classOf[justin.db.actors.protocol.StorageNodeNotFoundRead],   StorageNodeReadResponseSerializer, 92)
    kryo.register(classOf[justin.db.actors.protocol.StorageNodeFailedRead],     StorageNodeReadResponseSerializer, 93)
  }
}
```

Its worth to take a look at into how did I manage to serialize ADT (Abstract Data Type) which extends common interface (e.g. `StorageNodeFailedWrite`, `StorageNodeSuccessfulWrite`, `StorageNodeConflictedWrite` - they share the same serializer).

I've also declared in the Akka `serialization-bindings` section which classes should use kryo serialization. You can find it under [`application.conf`][justindb-application-conf] file.
```

object StorageNodeWriteResponseSerializer extends Serializer[StorageNodeWriteResponse] {

  private object Discriminator {
    val SuccessfulWrite = 1
    val FailedWrite     = 2
    val ConflictedWrite = 3
  }

  override def write(kryo: Kryo, output: Output, response: StorageNodeWriteResponse): Unit = response match {
    case StorageNodeSuccessfulWrite(id)               =>
      output.writeInt(Discriminator.SuccessfulWrite)
      output.writeString(id.toString)
    case StorageNodeFailedWrite(id)                   =>
      output.writeInt(Discriminator.FailedWrite)
      output.writeString(id.toString)
    case StorageNodeConflictedWrite(oldData, newData) =>
      output.writeInt(Discriminator.ConflictedWrite)
      DataSerializer.write(kryo, output, oldData)
      DataSerializer.write(kryo, output, newData)
  }

  override def read(kryo: Kryo, input: Input, `type`: Class[StorageNodeWriteResponse]): StorageNodeWriteResponse = input.readInt() match {
    case Discriminator.SuccessfulWrite =>
      StorageNodeSuccessfulWrite(UUID.fromString(input.readString()))
    case Discriminator.FailedWrite     =>
      StorageNodeFailedWrite(UUID.fromString(input.readString()))
    case Discriminator.ConflictedWrite =>
      StorageNodeConflictedWrite(
        oldData = DataSerializer.read(kryo, input, classOf[Data]),
        newData = DataSerializer.read(kryo, input, classOf[Data])
      )
  }
}
```
Solution is simply - I use specific discriminator numbers that are saved next to specific serialized payload (e.g. `1` for `StorageNodeSuccessfulWrite` payload). Pattern matching for the win! 👊

You can find more details about Akka-Kryo in its official [documentation][akka-kryo].

# Performance
Currently I'm doing some heavy test loading against cluster of [JustinDB][justindb] that runs on 5 JVMs (5 node cluster) with enabled replication factor set to 3. All tests speak to databse via http protocol. You can find them [here][justindb-load-tests]. Repository is almost empty but I've finally got a time to do some database stabilization and benchmarking around. As soon as I will get any meaningful result I will update this post (or write dedicated one).

[justindb]: https://github.com/speedcom/JustinDB
[akka-cluster]: http://doc.akka.io/docs/akka/current/java/cluster-usage.html
[akka-artery]: http://doc.akka.io/docs/akka/current/scala/remoting-artery.html
[akka-kryo]: https://github.com/romix/akka-kryo-serialization
[kryo]: https://github.com/EsotericSoftware/kryo
[justindb-payload-serializers]: https://github.com/justin-db/JustinDB/tree/master/justin-core/src/main/scala/justin/db/kryo
[justindb-application-conf]: https://github.com/justin-db/JustinDB/blob/master/src/main/resources/application.conf
[justindb-load-tests]: https://github.com/justin-db/JustinDB-load-testing
