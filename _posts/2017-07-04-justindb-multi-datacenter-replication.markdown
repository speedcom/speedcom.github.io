---
layout: post
title:  "JustinDB - Multi-Datacenter Replication"
date:   2017-07-04 08:10:00 +0100
---

## Preface
In the [previous][justindb-next-competition] blogpost I've declared there that I'm going to take a part in contest called ["Competition with cloud"][contest-with-cloud].

In this post I'm going to cover what exactly I've prepared and achieved. It also has a secondary purpose - it's going to serve as a final presentation for jury.

## Goal
I've written:

`My plan minimum is simple - Initializing Multiple Data Center Clusters on JustinDB.`
`I will use e24cloud + AWS/Scaleway as public cloud providers pair.`

Being now more strcit - I've enabled data replication in real-time between two physical [JustinDB][justindb] clusters provisioned onto two separated geo-regions/datacenters. I've achieved that by using 2 different cloud providers - [**e24cloud**][e24cloud] (Poland/Poznań) + [**Scaleway**][scaleway] (France/Paris).

*Note about cloud providers:*

* **e24cloud** - *efficient polish cloud computing platform powered by [Beyond.pl][beyond]*
* **Scaleway** - *dedicated ARM-Based cloud platform (IaaS) created by [Online.net][online.net]*

![][multi-data-center-clusters]
*Arrow shows the direct of sending replicas in real-time (from e24cloud cluster to Scaleway)*

## A bit about JustinDB
[JustinDB][justindb] is a distributed NoSQL eventually-consistent key-value database. It's written entirely in **Scala** (both object oriented and functional programming language with strong static type system) but what actually allows database to breathe is **Akka** (implementation of **Actor Model** on top of JVM). If you would like to know more about making the decision about tooling read this post: [JustinDB - why Scala and Akka?][why-scala-akka].

Every single data is identifiable by using **immutable keys**. Based on them and modified version of **Consistent Hashing** algorithm all requests (write/read) are partitioned (read as redistributed) between cluster nodes. Such cluster is a typical P2P system (nodes communicate each other constantly to know the current state of the cluster). All nodes are equal (they do the same job/has same role) - we have masterless environment to cooperate with.

![][partitioning]
*Partitioning. All requests are redistributed between nodes based on data identificator*.

To make sure that our saved data is always available we need some kind of redundancy - we keep **N** replicas (replica = data) that are spread among possibly different physical nodes.
![][replication]
*Replication. Single value is saved in N copies (here 3 times). Red arrow shows the direct of choosing nodes (node A is the first one; in our exemplary scenario in case of saving just single replica it would be put only there).*

If you are more interested about the whole topic I encourage you to read the following blogposts of mine:
* [JustinDB - Replication and Partitioning][justindb-replication-partitioning]
* [JustinDB - The Ring][justindb-ring]
* [JustinDB - Preference list][justindb-preference-list]

## JustinDB: Multi-Datacenter Replication

#### Motivation
[JustinDB][justindb] was developed with horizontal scaling in mind from its infancy. Many web applications, healthcare or gaming systems require this to provide disaster recovery, data geo-locality and ability to handle peak loads. [JustinDB][justindb] has come with new possibility to make this all even more available - Mutli-cluster Replication.

Claiming that faults across distributed systems never occur is silly (some legacy databases pretend that). Its typical that in system which consists on many running "things" some of them are not working correctly in particular point of time. The same applies even to Datacenter (yup, thats true!). That is why I've decided to implement one way of Mutli-Datacenter Replication mode - **Real-Time**. In real-time mode, continual, incremental synchronization occurs and replication is triggered by new updates.
During lack of availability of one Datacenter we transparently send requests to the another one - that way our app can process users commands further.

#### Benefits
* **Increase availability** - even in the event of a serious failure businesses must ensure continuity
* **Improve customer experience** - globally distributed app must be reliable and fast at the same time which means data needs to be served quickly (having multiple clusters we serve data to customers from the closest one)
* **Increase performance** - having more computation resources we are able to handle more requests

#### Architecture

In Multi-Datacenter Replication, a cluster can act as either the:
* **Primary** cluster (sometimes called **Source**) - the one that sends replica to other one,
* **Secondary** cluster (sometimes called **Sink**) - the one data is replicated to; it usually is deployed in different geo-region or Datacenter

We can establish bidirectional replication as well when cluster plays both role to other clusters. In our final scenario however (for the sake of the simplicity) we will keep to simpler version (one direction of sending replicas) where **e24cloud will act as a Primary and Scaleway as a Secondary one** (e24cloud ---> Scaleway).

![][multi-cluster-replication]

*Green arrow represents user's request. Red arrows represent all replica creation within cluster. Blue arrow represents multi-cluster replication.*

This is how it goes:
1. We make a negotation process between two clusters so they are aware to each other.
2. User send requests to Primary Cluster (first DC).
3. Primary cluster once received the message send it immediately to Secondary cluster which happens asynchronously next to saving all replicas by itself.
4. Secondary cluster gets replicated message and save all replicas itself.

Requirements:
Its obvious that to enable replication between clusters it requires additional configuration (we need to set up address of secondary cluster node to particular node from primary one). It is important to note that both clusters must have certain attributes in common (have the same [ring][justindb-ring] size).

## Provisioned clusters

![][e24cloud-justindb-cluster]
![][scaleway-justindb-cluster]

*Deployed [JustinDB][justindb] clusters with the same configuration/size (3 nodes + etcd service discovery). On the left - e24cloud, on the right - Scaleway.*

`justin-dc-{1,2,3}` nodes are the ones that form the real [JustinDB][justindb] cluster. However cluster requires establishing a set of *seed nodes* (for cluster bootstarping purpose) where etcd tool is used to elect the initial ones and publishing a list of them afterward (we have zero-configuration deployment therefore).

#### Deployment

[JustinDB][justindb] has got *dockerized* a bit time ago and since then it's a native process of installing it on any environment (VM/Clouds).

![][dockerized-justindb]

Every `JustinDB Docker image` was installed as follow (more or less):

```
docker run \
  --name justindb \
  -p 9000:9000 -p 2551:2551 -d \
  justindb/justindb:0.1 \
  -Djustin.node-id=$NODE_ID \
  -Djustin.system=justin-dc0 \ // or justin-dc1
  -Djustin.ring.members-count=3 \
  -Dakka.remote.netty.tcp.hostname=$HOST_IP \
  -Dakka.remote.netty.tcp.bind-port=2551 \
  -Dakka.remote.netty.tcp.bind-hostname=172.17.0.2 \
  -Dconstructr.coordination.host=$ETCD_IP
```

also `etcd`:

```
docker run \
  --detach \
  --name etcd \
  --publish 2379:2379 \
  quay.io/coreos/etcd:v2.3.7 \
  --listen-client-urls http://0.0.0.0:2379 \
  --advertise-client-urls http://$ETCD_IP:2379
```

## Final test scenario

I've decided to record a short summary video showing that all this thing actually works. We can see that data pushed to e24cloud cluster is also replicated to external Scaleway cluster (its available to be read from there).

[![JustinDB - Multi-Datacenter Replication](http://imgur.com/6Vlf0na.png)](https://youtu.be/GjH2kv0mDkA "JustinDB - Multi-Datacenter Replication")

*Click on the video (hosted on youtube)*

#### Scenario steps description:
1. We are making a negotation process between two clusters by sending one of the Scaleway JustinDB actor address to e24cloud JustinDB.
2. After the successful negotation process we push simple value to e24cloud. We expect that saving all 3 replicas will succeed (W=3). During saving replicas e24cloud JustinDB replicates data to Scaleway cluster in the background.
3. After a short time we are able to read data from Scaleway cluster (from every node because of enabled replication per cluster). We expect that all replicas are saved per cluster (we achieve strong data consistency that way; this is called tunable consistency).

In this quick summary video I've showed that replication between two different JustinDB clusters deployed to two different geo-regions/datacenters is duable and works in real-time manner.

## Important repositories to follow:
* [JustinDB][justindb] - highly available key-value distributed data store
* [JustinDB-chaos][justindb-chaos] - tests that impact on JustinDB cluster subjected to e.g. network partitioning
* [JustinDB-perf][justindb-perf] - performance tests against JustinDB
* [JustinDB-cli][justindb-cli] - manage JustinDB from Command Line

<!-- LINKS -->
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
[justindb-chaos]: https://github.com/justin-db/JustinDB-chaos
[justindb-perf]: https://github.com/justin-db/JustinDB-perf
[justindb-cli]: https://github.com/justin-db/JustinDB-cli

<!-- IMG -->
[multi-data-center-clusters]: ../../../../../img/competition-with-cloud/multi-data-center-clusters.png "Multiple Data Center Clusters"
[partitioning]: ../../../../../img/competition-with-cloud/partitioning.png "Partitioning"
[replication]: ../../../../../img/competition-with-cloud/replication.png "Replication"
[multi-cluster-replication]: ../../../../../img/competition-with-cloud/multi-cluster-replication.png "Multi-Cluster Replication"
[e24cloud-justindb-cluster]: ../../../../../img/competition-with-cloud/e24cloud-dc.png "e24cloud JustinDB cluster"
[scaleway-justindb-cluster]: ../../../../../img/competition-with-cloud/scaleway-dc.png "Scaleway JustinDB cluster"
[dockerized-justindb]: ../../../../../img/competition-with-cloud/justindb-docker-cli.gif "Dockerized JustinDB"
