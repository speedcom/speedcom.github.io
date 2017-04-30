---
layout: post
title:  "JustinDB - Pluggable persistent and in-memory storage engines"
date:   2017-03-24 14:10:17 +0100
categories: DSP2017
---

### Preface
JustinDB was built with a mind of a modular architecture that supports pluggable backends. It simply means that you can still use all its features along with different backends to support a variety of use cases.

### Interface
There is only on requirement that needs to be considered into account while implementing your own JustinDB's storage backend - `PluggableStorageProtocol` interface (you can find it [here][pluggablestorage-protocol]). Its neat and simple because only defines two operations:
- GET
- PUT

One for fetching data, another one for putting them into. Since they may cross the boundaries of our system (in order to make IO operations) both adress asynchronicity.

### Benefits ✴️
The main reason for designing a pluggable persistence component is to choose the storage engine best suited for an application’s access patterns.

Currently there are two implementations of the interface that JustinDB can work with:
- [in-memory][in-memory-storage]: it has nature of cache
- [persistent][persistent-storage] (big WIP though): this allows to infinitely store data

For example, you can combine JustinDB with **In-Memory module** to use it as a **big data cache**.
Alternatively, you can setup another cluster that combines JustinDB with **Persistent module** as a **big data database**.
Last but not least we are even able to define storage module per cluster node so that some of them can act as a cache and others like typical database.

![][justin-cache-persistent-image]

#### How engine is loaded 🔥

It uses reflection:
{% highlight Scala %}
object JustinDriver {

  def load(clazz: String): PluggableStorageProtocol = Class.forName(clazz)
    .newInstance()
    .asInstanceOf[PluggableStorageProtocol]
}
{% endhighlight %}

Exemplary usage taken from project (can be found [here][justindb-driver-usage]):
{% highlight Scala %}
val storage = JustinDriver.load(config.getString("justin-db.storage"))
{% endhighlight %}
It just reads path to implementation of `PluggableStorageProtocol` interface from project's `config` value e.g. `justin.db.storage.InMemStorage`


[pluggablestorage-protocol]: https://github.com/justin-db/JustinDB/blob/master/justin-core/src/main/scala/justin/db/storage/PluggableStorageProtocol.scala
[in-memory-storage]: https://github.com/justin-db/JustinDB/tree/master/justin-storage-in-mem
[persistent-storage]: https://github.com/justin-db/JustinDB/tree/master/justin-storage-persistent
[justindb-driver-usage]: https://github.com/justin-db/JustinDB/blob/master/justin-http-api/src/main/scala/justin/http_api/Main.scala#L35
[justin-cache-persistent-image]: ../../../../../justin-cache-persistent.png "Cluster with different loaded backends"
