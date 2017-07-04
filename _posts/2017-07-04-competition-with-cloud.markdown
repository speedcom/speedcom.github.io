---
layout: post
title:  "JustinDB - summary of \"Competition with cloud\""
date:   2017-07-04 08:10:00 +0100
categories: DSP2017
---

## Preface
In the [previous][justindb-next-competition] blogpost I've declared there that I'm going to take a part in competition called ["Competition with cloud"][contest-with-cloud] (I encourage you to read it in order to know who create it and what's the purpose of it).

In this post I'm going to cover what I've exactly prepared (this blogpost is going to be my final presentation to jury).

## Goal

`My plan minimum is simple - Initializing Multiple Data Center Clusters on JustinDB.`
`I will use e24cloud + AWS/Scaleway public cloud providers.`

Being more strcit - I've enabled data replication in real-time between two physical [JustinDB][justindb] clusters provisioned onto two separated geo-regions/data centers. I've achieved that by using 2 different cloud providers - [**e24cloud**][e24cloud] (Poland/Poznań) + [**Scaleway**][scaleway] (France/Paris).

*Note about cloud providers:*

* **e24cloud** - *it's an efficient polish cloud computing platform powered by [Beyond.pl][beyond]*
* **Scaleway** - *dedicated ARM-Based cloud platform (IaaS) created by [Online.net][online.net]*

![][multi-data-center-clusters]

## A bit about JustinDB

[justindb-next-competition]: http://speedcom.github.io/dsp2017/2017/05/20/justindb-next-competition.html
[contest-with-cloud]: https://chmurowisko.pl/konkurs-z-chmura
[justindb]: https://github.com/speedcom/JustinDB
[e24cloud]: http://e24cloud.com/en/
[scaleway]: https://www.scaleway.com/
[beyond]: https://beyond.pl/en/
[online.net]: https://www.online.net/en

[multi-data-center-clusters]: ../../../../../img/competition-with-cloud/multi-data-center-clusters.png "Multiple Data Center Clusters"
