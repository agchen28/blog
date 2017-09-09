---
date: 2017-02-11T16:11:52+08:00
draft: false
tags: ["备忘"]
categories: ["技术相关"]
title: Storm中Topology的执行过程
---

### Storm粗浅介绍

Apache Storm是一个开源的，分布式的流式计算系统。  
每次看到这么官方的句子都觉得不是很好理解。通俗一点讲，Storm运行在一堆机器上，将我们提交的计算任务分配到各个节点上进行运算，流式指的是数据会持续进来，而我们的计算任务会一直进行。  
Storm运行在Java虚拟机上，大部分采用Java和Clojure进行开发，使用ZooKeeper来协调集群中的状态信息。  

Storm API的基本概念：  

- topology ：Storm分布式计算结构。由一些数据源节点和普通计算节点还有一些边组成的有向无环图。ß

- spout：数据源节点

- bolt：普通计算节点。可以进行写数据到外部存储的操作，比如把数据写到数据库里

- stream：数据流，边

- tuple：记录，存在数据流中的记录

Storm集群中的基本组件：  

- Nimbus  
一个Storm集群包含一个主节点。主要职责是管理，协调和监控在集群上运行的topology，包括topology的发布，任务的指派。

- Supervisor  
一个Storm集群包含一个或者多个工作节点。supervisor等待nimbus分配任务后生成并监控workers（JVM进程）执行任务。

- Storm DRPC  
可选功能。提供了可扩展的分布式RPC能力。

- Storm UI  
可选功能。一个Web页面，展示Storm的相关信息，提供一些常用操作。

Storm的并发机制：

- Node  
指配置在Storm集群中的一个服务器，会执行topology的一部分运算。

- Worker  
指一个节点上相互独立运行的JVM进程。每个节点可以运行一个或多个worker。可以通过supervisor.slots.port参数配置，每个节点默认会有4个进程。每个topology默认使用一个进程来处理，可以通过config.setNumWorkers方法来设置进程数。如果没有可用的worker，提交的topology会处于等待的状态。

- Executor  
worker中的Java线程。Storm默认给每个executor分配一个task，每个task可指派给多个executor，多个task可以指派给同一个executor来执行。

- Task  
一个task可以简单理解为某个节点上运行的一个spout或者bolt实例。


### Topology运行流程  

- Topology提交后，代码会被存放到Nimbus节点下。接着会对该topology进行一番检查，比如当前topology名称是否已存在，bolt和spout命名是否规范等。

- Nimbus把任务分成一个个task，并且给每个task一个编号。系统根据worker的数目，尽量平均分配这些task的执行。 

- 任务分配好之后，Nimbus将任务信息提交到zookeeper上。

- Supervisor节点会不断的轮询zookeeper集群，领取自己的任务，启动worker进程运行。（领取到的task是nimbus对bolt或者spout实例序列化后的字节码文件，需要进行反序列化后得到spout或者bolt实例，并调用相应的方法。）

- 至此整个topology运行起来，除非显式地终止, 否则它会一直运行。