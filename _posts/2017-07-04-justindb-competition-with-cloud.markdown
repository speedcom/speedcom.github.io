---
layout: post
title:  "JustinDB - summary of \"Competition with cloud\""
date:   2017-07-04 08:10:00 +0100
---

## Preface
In the [previous][justindb-next-competition] blogpost I've declared there that I'm going to take a part in competition called ["Competition with cloud"][contest-with-cloud] (I encourage you to read it in order to know who create it and what's the purpose of it).

In this post I'm going to cover what I've exactly prepared (this blogpost is going to be my final presentation to jury).

## Goal
I've written:

`My plan minimum is simple - Initializing Multiple Data Center Clusters on JustinDB.`
`I will use e24cloud + AWS/Scaleway public cloud providers.`

Being now more strcit - I've enabled data replication in real-time between two physical [JustinDB][justindb] clusters provisioned onto two separated geo-regions/data centers. I've achieved that by using 2 different cloud providers - [**e24cloud**][e24cloud] (Poland/Poznań) + [**Scaleway**][scaleway] (France/Paris).

*Note about cloud providers:*

* **e24cloud** - *it's an efficient polish cloud computing platform powered by [Beyond.pl][beyond]*
* **Scaleway** - *dedicated ARM-Based cloud platform (IaaS) created by [Online.net][online.net]*

![][multi-data-center-clusters]
*Arrow shows the direct of sending replicas in real-time (from e24cloud cluster to Scaleway)*

## A bit about JustinDB
[JustinDB][justindb] is a distributed NoSQL key-value database. It's written entirely in **Scala** (both object oriented and functional programming language with strong static type system) but what actually allows db to breathe is **Akka** (implementation of **Actor Model** on top of JVM). If you would like to know more about making the decision about tooling read this post: [JustinDB - why Scala and Akka?][why-scala-akka].

Every single data is identifiable by using **immutable keys**. Based on them and modified version of **Consistent Hashing** algorithm all requests (write/read) are partitioned (read as redistributed) between cluster nodes. Such cluster is a typical P2P system (nodes communicate each other constantly to know the current state of the cluster). All nodes are equal (they do the same job/has same role) - we have masterless environment to cooperate with.

![][partitioning]
*Partitioning. All requests are redistributed between nodes based on data identificator*.

To make sure that our saved data is always available we need some kind of redundancy - we keep **N** replicas (replica = data) that are spread among possibly different physical nodes.
![][replication]
*Replication. Single value is saved in N copies (here 3 times). Red arrow shows the direct of choosing nodes (node A is the first one; in case of saving just single value it would be put only there in our exemplary scenario).*

If you are more interested about the whole topic I encourage you to read the following blogposts of mine:
* [JustinDB - Replication and Partitioning][justindb-replication-partitioning]
* [JustinDB - The Ring][justindb-ring]
* [JustinDB - Preference list][justindb-preference-list]


## Provisioned clusters

[justindb-next-competition]: http://speedcom.github.io/dsp2017/2017/05/20/justindb-next-competition.html
[contest-with-cloud]: https://chmurowisko.pl/konkurs-z-chmura
[justindb]: https://github.com/speedcom/JustinDB
[e24cloud]: http://e24cloud.com/en/
[scaleway]: https://www.scaleway.com/
[beyond]: https://beyond.pl/en/
[online.net]: https://www.online.net/en
[why-scala-akka]: http://speedcom.github.io/dsp2017/2017/04/15/justindb-why-scala-and-akka.html
[justindb-replication-partitioning]: http://speedcom.github.io/dsp2017/2017/04/13/justindb-replication-and-partitioning.html
[justindb-ring]: http://speedcom.github.io/dsp2017/2017/05/06/justindb-ring.html
[justindb-preference-list]: http://speedcom.github.io/dsp2017/2017/05/07/justindb-preference-list.html

<!-- IMG -->
[multi-data-center-clusters]: ../../../../../img/competition-with-cloud/multi-data-center-clusters.png "Multiple Data Center Clusters"
[partitioning]: ../../../../../img/competition-with-cloud/partitioning.png "Partitioning"
[replication]: ../../../../../img/competition-with-cloud/replication.png "Replication"
