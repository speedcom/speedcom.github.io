---
layout: post
title:  "JustinDB - Preference list"
date:   2017-05-07 22:00:00 +0100
categories: DSP2017
---

## Preference List
As you might read [here][justindb-replication] [JustinDB][justindb] replicates its data on multiple hosts in order to achieve high availability and durability. Each data is replicated at `N` host (this term is also called `Replication Factor`). Each key is assigned to a **coordinator node** (a node handling a read or write operation). The coordinator is in charge of the replication of the data items that fall within its range. In addition to locally storing each key within its range, the coordinator replicates these keys at the N-1 clockwise successor nodes in the [Ring][justindb-ring].

The list of nodes that is responsible for storing a particular value is called a **preference list**.

This is the way JustinDB represents preference list from code perspective.

{% highlight Scala %}
case class PreferenceList(primaryNodeId: NodeId, replicasNodeId: List[NodeId]) {
  def size: Int = all.size
  def all: List[NodeId] = primaryNodeId :: replicasNodeId
}
{% endhighlight %}
*PrimaryNodeId is an Id of a coordinator node*

If you are interested more about code you can start exploring [here][justindb-preference-list]. 💪


[justindb]: https://github.com/speedcom/JustinDB
[justindb-ring]: http://speedcom.github.io/dsp2017/2017/05/06/justindb-ring.html
[justindb-replication]: http://speedcom.github.io/dsp2017/2017/04/13/justindb-replication-and-partitioning.html
[justindb-preference-list]: https://github.com/justin-db/JustinDB/blob/master/justin-core/src/main/scala/justin/db/replica/PreferenceList.scala