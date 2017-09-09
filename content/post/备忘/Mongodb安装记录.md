---
date: 2016-06-08T11:05:10+08:00
draft: false
tags: ["备忘"]
categories: ["技术相关"]
title: Mongodb安装记录
---

### 下载
https://www.mongodb.com/download-center#community  
选择相应的版本下载

### 安装
```
mkdir mongodb
cd mongodb
mkdir db
mkdir logs
tar -zxvf mongodb-linux-x86_64-3.4.3.tgz
cd mongodb-linux-x86_64-3.4.3/bin
vim mongodb.conf
```
mongodb.conf内容：
```
dbpath=/opt/mongodb/db
logpath=/opt/mongodb/logs/mongodb.log
port=27017
fork=true
nohttpinterface=true
bind_ip=192.168.142.200
#auth=true #开启用户认证
```

### 启动
```
/opt/mongodb/mongodb-linux-x86_64-3.4.3/bin/mongod --config /opt/mongodb/mongodb-linux-x86_64-3.4.3/bin/mongodb.conf
```

### 检测
```
ps -ef |grep mongo  #检测后台进程是否存在
netstat -lntp | grep 27017   
./mongo 192.168.142.200:27017
```

### 开机启动
```
vim /etc/init.d/mongodb
chmod +x /etc/init.d/mongodb    #赋予执行权限
service mongodb start/stop
chkconfig mongodb on    #设为开机启动
```
脚本如下（看不懂）：
```
#!/bin/bash
# mongod - Startup script for mongod
# chkconfig: 35 80 15
# description: Mongo is a scalable, document-oriented database.
# processname: mongod
#config: /db/conf/mongodb/mongod.conf
# pidfile: /var/run/mongo/mongo.pid

source /etc/rc.d/init.d/functions

# things from mongod.conf get there by mongod reading it

if [ $(id -u) != "0" ]; then
    echo "Permission Denied! Please use root to run again!"
    exit 1
fi

test -d /var/run/mongodb || (mkdir -p /var/run/mongodb ; chown mongod:mongod /var/run/mongodb)

# NOTE: if you change any OPTIONS here, you get what you pay for:
# this script assumes all options are in the config file.
CONFIGFILE="/opt/mongodb/mongodb-linux-x86_64-3.4.3/bin/mongodb.conf"
SYSCONFIG="/etc/sysconfig/mongod"

export PATH=$PATH:/opt/mongodb/mongodb-linux-x86_64-3.4.3/bin

DBPATH=`awk -F= '/^dbpath/{print $2}' "$CONFIGFILE"`
OPTIONS=" --config $CONFIGFILE"
mongod=${MONGOD-/opt/mongodb/mongodb-linux-x86_64-3.4.3/bin/mongod}
echo "db path is: "$DBPATH
echo $mongod
MONGO_USER=leftfist
MONGO_GROUP=leftfist

[ -r "$SYSCONFIG" ] && source "$SYSCONFIG"

super() {
    su - $MONGO_USER -c "PATH=$PATH://opt/mongodb/mongodb-linux-x86_64-3.4.3/bin; $*"
}

start()
{
  echo -n $"Starting mongod: "
#  daemon --user "$MONGO_USER" "numactl --interleave=all" $mongod $OPTIONS
#daemon --user "$MONGO_USER" $mongod $OPTIONS
#  
#   su - $MONGO_USER -c "$mongod $OPTIONS" -m -p
#  su - $MONGO_USER
  $mongod $OPTIONS
#  super $mongod $OPTIONS
  echo $mongod$OPTIONS
  RETVAL=$?
  echo
  [ $RETVAL -eq 0 ] && touch /var/lock/subsys/mongod
}

stop()
{
  echo -n $"Stopping mongod: "
  killproc -p "$DBPATH"/mongod.lock -d 300 /opt/mongodb/mongodb-linux-x86_64-3.4.3/bin/mongod
  RETVAL=$?
  echo
  [ $RETVAL -eq 0 ] && rm -f /var/lock/subsys/mongod
}

restart () {
        stop
        start
}

ulimit -n 12000
RETVAL=0

case "$1" in
  start)
    start
    ;;
  stop)
    stop
    ;;
  restart|reload|force-reload)
    restart
    ;;
  condrestart)
    [ -f /var/lock/subsys/mongod ] && restart || :
    ;;
  status)
    status $mongod
    RETVAL=$?
    ;;
  *)
    echo "Usage: $0 {start|stop|status|restart|reload|force-reload|condrestart}"
    RETVAL=1
esac

exit $RETVAL
```