---
layout: post
title:  "JustinDB - executable JAR"
date:   2017-05-20 09:10:00 +0100
categories: DSP2017
---

## Preface

In the ["iTermocil - divide and conquer your terminal like a PRO"][itermocil] blogpost I showed to you a way of running local [JustinDB][justindb] cluster (imitation). It runs [sbt][sbt] process under project catalog in different terminal panes. As this process was convenient in continous development I have had sometimes the feeling its simply too slow (a couple of parallely working sbt processes trying to crunch the same sources at the same time) and it would be better to run already having generated package.

```
Patience has its limits. Take it too far, and it's cowardice.
George Jackson
```

## Assembly

I've finally decided to employ `sbt-assembly` plugin. It creates a fat JAR of your project with all of its dependencies. 👊
Now simply by invoking `sbt assembly` command I am able to build new version of [JustinDB][justindb] JAR.
That way I've got the possibility of running full standalone [JustinDB][justindb] instance in very fast way!

Take a look at the gif to see how I build exemplary [JustinDB][justindb] JAR and then form cluster on top of it (with passed appropriate parameters).



[justindb]: https://github.com/speedcom/JustinDB
[sbt]: http://www.scala-sbt.org/
[itermocil]: http://speedcom.github.io/dsp2017/hacks/2017/03/28/iTermocil-divide-and-conquer-your-terminal.html