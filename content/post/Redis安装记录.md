---
date: 2017-04-08T11:04:56+08:00
draft: true
tags: ["备忘"]
categories: ["技术相关"]
title: Redis安装记录
---

### 下载安装
```
wget http://download.redis.io/releases/redis-3.2.8.tar.gz
tar xzf redis-3.2.8.tar.gz
cd redis-3.2.8
make
#可使用root用户执行`make install`，将可执行文件拷贝到/usr/local/bin目录下。这样就可以直接敲名字运行程序了。
make install
```
### 设置开机启动
启动脚本 redis_init_script 位于Redis的 /utils/ 目录下。
- 根据启动脚本要求，将修改好的配置文件以端口为名复制一份到指定目录。需使用root用户。
```
mkdir /etc/redis
cp redis.conf /etc/redis/6379.conf
```
- 修改配置文件
```
bind:ip地址
port：端口号。默认6379
requirepass:设置密码
slaveof：从服务器需要配置
masterauth：主服务器的密码
```
- 将启动脚本复制到/etc/init.d目录下，本例将启动脚本命名为redisd（通常都以d结尾表示是后台自启动服务）。
```
cp redis_init_script /etc/init.d/redisd
```
- 设置为开机自启动  
在启动脚本开头添加如下两行注释以修改其运行级别
```
#!/bin/sh
# chkconfig:   2345 90 10
# description:  Redis is a persistent key-value database#
```
```
#设置为开机自启动服务
chkconfig redisd on
#打开服务
service redisd start
#关闭服务
service redisd stop
```

### 检测
```
ps -ef |grep redis  #检测后台进程是否存在
netstat -lntp | grep 6379   #检测6379端口是否在监听
redis_cli -a 123456 #使用`redis-cli`客户端检测连接是否正常
```