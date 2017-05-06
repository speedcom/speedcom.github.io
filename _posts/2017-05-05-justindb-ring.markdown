---
layout: post
title:  "JustinDB - The Ring"
date:   2017-05-06 18:00:00 +0100
categories: DSP2017
---

## The Ring

[JustinDB][justindb] applies modified version of **Consistent Hashing** to map objects along the edge of a circle (the ring).

Just to recall [JustinDB][justindb] is a key-value database so you can think about it as a *big distributed hashtable*.
Every signle data we save is identifiable in the system by its key (*UUID* actually).

But how exactly [JustinDB][justindb] know what node is responsible for handling request (storing/fetching value)? This is where already mentioned **Consistent Hashing** algorithm take place.

## Consistent Hashing
Let visualise cluster of 5 nodes size. This is our space we can cover with data. [JustinDB][justindb] employs such algorithm to uniform data distribution. This space is divided into equal partitions called **"vnodes"**. These vnodes are evenly distributed amongst participating physical nodes in the cluster.

[JustinDB][justindb] uses very simplistic version of mapping data UUID key to particular vnode.

{% highlight Scala %}
object UUID2RingPartitionId extends ((UUID, Ring) => Ring.RingPartitionId) {
  override def apply(id: UUID, ring: Ring): RingPartitionId = scala.math.abs(id.hashCode()) % ring.size
}
{% endhighlight %}

Participating nodes in a [JustinDB][justindb] cluster are homogeneous - every node in the cluster knows where data should reside within the cluster. Requests for data can target any node. It will horizontally access data from the proper nodes, and return the result.

**Ring** is represented as a wrapper around Map datastructure that know what **vnode** (other name: ring's partitionId) belongs to which cluster node.

{% highlight Scala %}
class Ring(private val ring: Map[RingPartitionId, NodeId])
{% endhighlight %}

You can find these snippets [here][justindb-snippets].


[justindb]: https://github.com/speedcom/JustinDB
[justindb-snippets]: https://github.com/justin-db/JustinDB/tree/master/justin-consistent-hashing/src/main/scala/justin/consistent_hashing