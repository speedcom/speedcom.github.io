---
layout: post
title:  "iTermocil - divide and conquer your terminal like a PRO"
date:   2017-03-28 15:20:17 +0100
categories: DSP2017
---

### iTermocil
Last week I've introduced to you very neat script called [`Z.sh`][z.sh-post]. 💖 Next to it I consider [`iTermocil`][itermocil] as a perfect complement of your heavily used dev toolbox. 🔧

#### What is it?
As [official documentation][itermocil] stands out it allows us to setup pre-configured layouts of windows and panes in iTerm2, having each open in a specified directory and execute specified commands.

Correct. 👌

As a developer we can relatively often deal with situations in which we open the same set of tools in terminal in order to run specific project we work on actively. Its a good occasion to automate such thing and this is what `iTermocil` is perfect for. 👊

### How do I use it with JustinDB?
Its worth to notice that JustinDB can be started in a cluster - in order to emulate real-world environment we can represent it on local machine as a pool of processes (single process represents single node of the cluster).

I've preconfigured bunch of different iTermocil's configs. Depends on how big I want to have custer (3 or 5 nodes most of the time) I start cluster bootstraping with dedicated configuration.

I'm running:

```itermocil justin-cluster-5``` or ```itermocil justin-cluster-3```

Puff, magic happens here 💭. This is what I see after a while - working [`JustinDB`][justindb] cluster I can run tests against it.

![][itermocil-image]

[itermocil]: https://github.com/TomAnthony/itermocil
[justindb]: https://github.com/speedcom/JustinDB
[z.sh-post]: http://speedcom.github.io/dsp2017/hacks/2017/03/20/z-move-around-catalogs-in-terminal-like-a-pro.html
[itermocil-image]: ../../../../../itermocil.png "Cluster with different loaded backends"