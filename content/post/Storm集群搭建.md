---
date: 2017-02-04T23:30:07+08:00
draft: false
tags: ["备忘"]
categories: ["技术相关"]
title: Storm集群搭建
---

### 准备
- 安装java
- 安装zookeeper

### 下载
- http://storm.apache.org/downloads.html

### 安装
```
tar -zxvf apache-storm-1.0.3.tar.gz
mkdir data
vim conf/storm.yaml #修改配置文件
```
添加配置项(每行配置前面注意留一个空格)：
```
storm.zookeeper.servers:
     - "192.168.142.200"
     - "192.168.142.201"
     - "192.168.142.202"
 nimbus.host: "192.168.142.200"
 storm.local.dir: "/opt/storm/data"
```
### 启动
```
nohup bin/storm nimbus &    #启动nimbus
nohup bin/storm supervisor &    #启动supervisor
nohup bin/storm ui &    #启动UI
```

### 访问Storm UI
http://192.168.142.200:8080/