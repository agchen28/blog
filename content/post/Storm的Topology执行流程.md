---
date: 2017-04-21T16:11:52+08:00
draft: true
title: Storm的Topology执行流程
---

Topology运行流程
    (1)Storm提交后，会把代码首先存放到Nimbus节点的inbox目录下，之后，会把当前Storm运行的配置生成一个 stormconf.ser文件放到Nimbus节点的stormdist目录中，在此目录中同时还有序列化之后的Topology代码文件 
    (2) 在设定Topology所关联的Spouts和Bolts时，可以同时设置当前Spout和Bolt的executor数目和task数目，默认情况下， 一个Topology的task的总和是和executor的总和一致的。之后，系统根据worker的数目，尽量平均的分配这些task的执行。 worker在哪个supervisor节点上运行是由storm本身决定的 
    (3)任务分配好之后，Nimbus节点会将任务的信息提交到zookeeper集群，同时在zookeeper集群中会有workerbeats节点，这里存储了当前Topology的所有worker进程的心跳信息 
    (4)Supervisor 节点会不断的轮询zookeeper集群，在zookeeper的assignments节点中保存了所有Topology的任务分配信息、代码存储目 录、任务之间的关联关系等，Supervisor通过轮询此节点的内容，来领取自己的任务，启动worker进程运行 
    (5)一个Topology运行之后，就会不断的通过Spouts来发送Stream流，通过Bolts来不断的处理接收到的Stream流，Stream流是无界的。 
    最后一步会不间断的执行，除非手动结束Topology。 