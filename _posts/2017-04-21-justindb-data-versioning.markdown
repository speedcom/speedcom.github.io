---
layout: post
title:  "JustinDB - data versioning (part 1)"
date:   2017-04-15 14:15:00 +0100
categories: [DSP2017]
---

## Preface
[JustinDB][justindb] provides **eventaul consistency**, which allows for updates to be propagated to all replicas **asynchronously**.

[JustinDB][justindb] has a **tunable availability** characteristic. It is highly available to serve requests, with the ability to tune its level of availability–nearing, but never quite reaching, strong consistency. As you may have read previous post about [replication][justindb-replication] you know that every data is replicated to N distinct physical nodes (servers). Besides that it allows you to decide how many nodes must be written to or read from per request. These values are settings labeled **N** (the number of nodes to replicate to), **R** (the number of nodes read from before returning), and **W** (the number of nodes written to before considered successful).

Under certain failure scenarios (e.g. network partitions), updates may not arrive at all replicas for an extended period of time. Its worth to notice that updates in the presence of e.g. node failures can potentially result in an object having distinct version sub-hitories, which the system wil need to reconcile in the future.

[JustinDB][justindb] uses **Vector Clock** in order to capture causality between different versions of the same object.

## Vector Clock

[_Wikipedia_][wiki-vector-clock]
```
A vector clock is an algorithm for generating a partial ordering of events in a distributed system and detecting causality violations.
```
Sounds a bit confusing.

At it basis, Vector Clocks just measures a sequence of events and keep a running history of updates.
Its effectively a list of (node, counter) pairs. One vector clock is associated with every version of every objects.

This is how Vector Clock is modeled in [JustinDB][justindb]:

{% highlight Scala %}
case class VectorClock[Id](private val clock: Map[Id, Counter]) {
  def get(id: Id): Option[Counter] = clock.get(id)

  def increase(id: Id): VectorClock[Id] = {
    val searchedCounter = clock.getOrElse(id, Counter.zero)
    val updatedCounter  = searchedCounter.addOne

    VectorClock(clock + (id -> updatedCounter))
  }

  def toList: List[(Id, Counter)] = clock.toList

  def keys: Set[Id] = clock.keys.toSet
}

object VectorClock {

  def apply[Id](): VectorClock[Id] = VectorClock(Map.empty[Id, Counter])

  def empty[Id](id: Id): VectorClock[Id] = VectorClock(Map(id -> Counter.zero))

  def merge[Id](receiverId: Id, vc1: VectorClock[Id], vc2: VectorClock[Id]): VectorClock[Id] = {
    val mergedClocks = vc1.clock ++ vc2.clock

    val mergedCounter = (vc1.clock.get(receiverId), vc2.clock.get(receiverId)) match {
      case (Some(counter1), Some(counter2)) => Counter.max(counter1, counter2)
      case (None, Some(counter2))           => counter2
      case (Some(counter1), None)           => counter1
      case (None, None)                     => Counter.zero
    }

    val counter = mergedCounter.addOne

    VectorClock(mergedClocks + (receiverId -> counter))
  }
}
case class Counter(value: Int) extends AnyVal {
  def addOne: Counter = this.copy(value = value + 1)
}

object Counter {
  def max(c1: Counter, c2: Counter): Counter = {
    Counter(scala.math.max(c1.value, c2.value))
  }

  def zero: Counter = Counter(0)
}
{% endhighlight %}

[justindb]: https://github.com/speedcom/JustinDB
[justindb-replication]: http://speedcom.github.io/dsp2017/2017/04/13/justindb-replication-and-partitioning.html
[wiki-vector-clock]: https://www.wikiwand.com/en/Vector_clock