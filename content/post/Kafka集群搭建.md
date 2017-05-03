---
date: 2017-02-04T23:19:49+08:00
draft: false
tags: ["备忘"]
categories: ["技术相关"]
title: Kafka集群搭建
---

## 简介
Apache Kafka是一个分布式消息队列系统。

### 相关概念

- producer：生产者，负责将消息发布到kafka集群。

- consumer：消费者，从kafka集群中消费消息。

- broker：kafka集群中包含的服务器。

- topic：消息的主题。

- partition：每个 topic 包含一个或多个 partition。

### 消息的存储
topic被分成一个或多个patition，每个patition物理上对应一个文件夹（该文件夹存储该 patition 的所有消息和索引文件）。  
无论消息是否被消费，kafka 都会保留所有消息。有两种策略可以删除旧数据：  
1. 基于时间：log.retention.hours=168  
2. 基于大小：log.retention.bytes=1073741824

### 应用场景

- 日志收集

- 用户行为数据收集

## 搭建

### 环境准备
- 三台Linux服务器：  
192.168.142.200 linux01  
192.168.142.201 linux02  
192.168.142.202 linux03  
- 依赖于Zookeeper，所以需要先安装好Zookeeper

### 下载解压
```
wget  http://apache.opencas.org/kafka/0.9.0.1/kafka_2.11-0.9.0.1.tgz
tar -zxvf kafka_2.11-0.9.0.1.tgz
```

### 修改配置文件
```
#broker.id=0  每台服务器的broker.id都不能相同

log.dirs=/opt/kafka/logs/
#hostname
host.name=192.168.142.200

#设置zookeeper的连接端口
zookeeper.connect=192.168.142.200:2181,192.168.142.201:2181,192.168.142.202:2181
```

### 启动服务
```
./kafka-server-start.sh -daemon ../config/server.properties
#检查服务是否启动
jps
#创建Topic
./kafka-topics.sh --create --zookeeper 192.168.142.200:2181 --replication-factor 2 --partitions 1 --topic test
#创建一个broker，发布者
./kafka-console-producer.sh --broker-list 192.168.142.200:9092 --topic test
#创建一个订阅者
./kafka-console-consumer.sh --zookeeper localhost:2181 --topic test --from-beginning
#查看topic
./kafka-topics.sh --list --zookeeper localhost:2181
```

### 开机启动
```
touch kafkastart.sh  #创建启动脚本
chmod +x kafkastart.sh  #添加执行权限
vim /etc/rc.d/rc.local   #最后一行添加：sh /opt/kafka/kafkastart.sh & 
```
启动脚本内容：
```
/opt/kafka/kafka_2.11-0.10.1.0/bin/kafka-server-start.sh /opt/kafka/kafka_2.11-0.10.1.0/config/server.properties &
```