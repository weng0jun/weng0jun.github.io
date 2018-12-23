---
layout: post
title: "Bigtable Paper Reading"
---
首先这是一篇工程论文, 工程论文以解决具体的工程问题为宗旨。阅读工程论文也要以工程问题为主线并从以下几个方面展开：
* 解决了什么问题？
* 具体的工程目标是什么？
* 解决方案是什么？
* 有哪些关键的实现细节？
* 性能评估
* 工程经验

## 解决了什么问题？
Bigtable的出现主要是为了解决利用大规模廉价服务器存储海量（P级）**结构化**数据的问题，而GFS仅仅解决了如何利用大规模廉价服务器存储海量**非结构化**数据的问题。这个问题来自于Google内部很多产品对实时处理海量结构化数据的需求，如：Google搜索的web索引、Google Earth、Google Finance等。那么解决这个问题总共花了多少人力呢？答案是在两年半的时间里用了7个人年，这其中就包括Google传奇程序员Jeffrey Dean。这再一次印证了牛X的系统往往都是由少数几个超级程序员搞出来的。

> Bigtable is a distributed storage system for managing structured data that is designed to scale to a very large size: petabytes of data across thousands of commodity servers.

> These applications place very different demands on Bigtable, both in terms of data size (from URLs to web pages to satellite imagery) and latency requirements (from backend bulk processing to real-time data serving).
 
以下是他们在设计这套系统的时候脑子里装的那个具体的例子：
<img width="" height="" src="/images/bigtable-design-concrete-example.png"/>
## 具体的工程目标是什么？
以下是bigtable主要的几个工程目标，这些目标指导了各种各样设计上的决策。
* 通用性（applicablity），这套系统必须是通用的，能够满足Google内部的各种产品对海量结构化数据的处理需求。
* 可扩展性（scalability），由于这套系统是建立在大规模廉价服务器之上，所以系统一定要有很强的可扩展性。也就是系统的各项指标要能够随着机器数量实现线性增长。
* 高性能，读写性能一定要好。
* 高可用，服务一定要稳定。

> Bigtable is designed to reliably scale to petabytes of data and thousands of machines. Bigtable has achieved several goals: wide applicability, scalability, high performance, and high availability. 

## 解决方案
论文中的以下这句话对最终的解决方案给出了清晰的定义：

> A Bigtable is a sparse, distributed, persistent multidimensional sorted map.

* Map

> The map is indexed by a rowkey, column key, and a timestamp; each value in the map is an uninterpreted array of bytes. 

> (row:string, column:string, time:int64) → string

* Sparse

Bigtable Model的灵活性体现在每个Column Family可以有任意多个Column，每个Cell又可以包含相同数据的多个版本，这种灵活性导致Bigtable非常适合存储稀疏的、半结构化的数据。

* Multidimensinal

传统关系型DBMS中的表都是二维的（2-dimensional），而bigtable中的表是多维了，表中的每个cell加了timestamp之后就变成三维的了，因此bigtable的表达能力也更强。

* Sorted

> Bigtable maintains data in lexicographic order by rowkey.

## 有哪些关键的实现细节？
Bigtable建立在GFS和Chubby两个基础组件之上。GFS是Google的分布式文件系统，bigtable利用GFS实现持久化，Google设计了一种叫SSTable的文件格式用来存储bigtable的数据。Chubby是Paxos算法的一种实现，bigtable用一个由5个replica组成的Chubby集群来解决分布式环境下的高可用问题。具体用法包括：
* 用来保证任意时刻只有一个master节点；
* 用来存储bigtable在GFS中的引导位置（bootstrap location）；
* 用来注册和删除tablet server；
* 用来存储table的metadata；

在GFS和Chubby的基础之上，bigtable实现了以tablet为核心的分布式数据存储服务。以下是利用B+树实现的tablet三级索引结构：
<img width="" height="" src="/images/bigtable-design-tablet-location-hierarchy.png"/>

## 性能评估
本文的工程实验设计可以作为分布式数据存储系统的典范，主要从性能和可扩展性两个方面对系统进行评估，其中性能评估主要包含以下几个方面：
* 随机读
* 随机读（内存）
* 随机写
* 连续读
* 连续写
* scans
下面是实验结果：
<img width="" height="" src="/images/bigtable-performance-test-result.png"/>

## 工程经验
最后作者还分享了很多弥足珍贵的工程经验：
* 第一个是由分布式系统本身的复杂性导致的工程上的难度，这是由于很多原先在单机系统成立的前提假设在分布式系统环境下都不成立了。

> One lesson we learned is that large distributed systems are vulnerable to many types of failures, not just
the standard network partitions and fail-stop failures assumed in many distributed protocols.

* 不要随意引入新功能。深以为然，引入任何性的功能都会对系统的简洁性构成威胁，所以在没有搞清楚该功能的本质需求之前不要急着coding。对于这类功能最好的策略就是拖延。

> Another lesson we learned is that it is important to delay adding new features until it is clear how the new
features will be used.

* 强调数据埋点的重要性。

> A practical lesson that we learned from supporting Bigtable is the importance of proper system-level monitoring (i.e., monitoring both Bigtable itself, as well as the client processes using Bigtable).

* 简洁是王道。越是复杂的系统越能体现简洁的重要性，这里的简洁同时包含clean code和clean design。代码和设计的简洁性和其存活时间成正比，所以引入任何复杂性之前一定要谨慎！

> The most important lesson we learned is the value of simple designs.

## 结论
 * 工程原则

 没有完美的解决方案，只有解决问题的解决方案。Google的GFS、Bigtable和MapReduce的出现都是从解决具体的问题出发的，这些系统都是基于具体的用例进行设计和实现的，而一些不是基于一些不确定的假设（如：bigtable是基于web索引的那个用例进行设计的）。这样做的一大好处是避免over design，可以延迟引入一些不明确的需求。另外，很多理论上会出现的问题，在实践中往往并不会出现，工程就是要避免一开始就花太多的精力去处理这些问题。比如OSI七层网络模型仅停留在理论层面，工程上最终采用的是简化的TCP/IP 4层协议。

 * 单一职责

 最近10年出现了各种各样的数据库系统，打破了原来关系型数据库一统天下的局面，这背后的工程理念正是单一职责原则。下图是Werner Vogels对这些数据库系统及其功能进行的分类说明：
 <img width="" height="" src="/images/purpose-built-databases.png"/>
 
 > Werner Vogel: A one size fits all database doesn't fit anyone
 
 * 简单与复杂
 
 大道至简，人和人认知上的一大区别是能不能把复杂的事情看简单了。系统亦如此，如果某个系统设计的不够简洁，一定是有某个环节没有想清楚，这个不够清楚的环节可以上至公司层面，下至某个具体的函数。而单一职责原则能够帮助你想清楚这个问题。

