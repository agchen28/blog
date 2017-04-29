---
date: 2017-01-28T22:28:31+08:00
draft: false
tags: ["备忘"]
categories: ["技术相关"]
title: Zookeeper集群搭建
---

Zookeeper是一个分布式协调服务。
### 准备
三台Linux服务器：  
192.168.142.200 linux01  
192.168.142.201 linux02  
192.168.142.202 linux03  

### 安装JDK
```
yum list java*
yum -y install java-1.8.0-openjdk*
```

### 下载Zookeeper解压
```
cd /opt/zookeeper/
wget http://mirrors.cnnic.cn/apache/zookeeper/zookeeper-3.4.6/zookeeper-3.4.6.tar.gz
tar -zxvf zookeeper-3.4.6.tar.gz
```

### 修改配置文件
```
cp zoo_sample.cfg zoo.cfg
```
需要修改的配置项：
```
tickTime=2000
initLimit=10
syncLimit=5
dataDir=/opt/zookeeper/data
clientPort=2181
server.1=192.168.142.200:2888:3888
server.2=192.168.142.201:2888:3888
server.3=192.168.142.202:2888:3888
#server.1 这个1是服务器的标识也可以是其他的数字， 表示这个是第几号服务器，用来标识服务器，这个标识要写到快照目录下面myid文件里
#第一个端口是master和slave之间的通信端口，默认是2888，第二个端口是leader选举的端口，集群刚启动的时候选举或者leader挂掉之后进行新的选举的端口默认是3888
```

### 创建myid文件
```
#server1
echo "1" > /opt/zookeeper/data/myid
#server2
echo "2" > /opt/zookeeper/data/myid
#server3
echo "3" > /opt/zookeeper/data/myid
```

### 启动服务
```
./zkServer.sh start #启动服务（3台都需要操作）
./zkServer.sh status    #检查服务器状态
jps #jps(Java Virtual Machine Process Status Tool)是JDK 1.5提供的一个显示当前所有java进程pid的命令
```

### 设置开机启动
```
cd /etc/rc.d/init.d/
touch zookeeper #创建一个空文件
chmod +x zookeeper  #添加可执行权限
chkconfig --add zookeeper   #把zookeeper添加到开机启动
chkconfig --list    #列出所有的系统服务
service zookeeper start/stop    #启动或停止zookeeper服务
```
zookeeper文件内容：
```
#!/bin/bash
#chkconfig:2345 20 90
#description:zookeeper
#processname:zookeeper

ZOOKEEPER_PATH=/opt/zookeeper/zookeeper-3.4.6/bin/

case $1 in
          start) su root ${ZOOKEEPER_PATH}/zkServer.sh start;;
          stop) su root ${ZOOKEEPER_PATH}/zkServer.sh stop;;
          status) su root ${ZOOKEEPER_PATH}/zkServer.sh status;;
          restart) su root ${ZOOKEEPER_PATH}/zkServer.sh restart;;
          *)  echo "require start|stop|status|restart"  ;;
esac
```