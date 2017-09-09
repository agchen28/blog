---
date: 2017-05-05T15:15:22+08:00
draft: true
tag: ["备忘"]
title: mysql安装记录
---
### 下载

https://dev.mysql.com/downloads/mysql/

```
wget http://www.kakapart.com/files/mysql-5.7.18-linux-glibc2.5-x86_64.tar.gz
```

### 创建用户和用户组
```
sudo groupadd mysql
sudo useradd -r -g mysql -s /bin/false mysql
```

### 安装
```
tar mysql-5.7.18-linux-glibc2.5-x86_64.tar.gz//解压
cp mysql-5.7.18-linux-glibc2.5-x86_64 /usr/local/mysql/ -r
mkdir data  //mysql下创建data文件夹
chown -Rmysql:mysql mysql   //修改mysql目录下所有文件的权限
vim /etc/my.cnf     
yum install libaio //初始化需要依赖包
sudo bin/mysqld --initialize --user=mysql   //需要记住第一次登陆的密码
cp support-files/mysql.server /etc/init.d/mysql  //添加开机启动
service mysql start //启动服务
bin/mysql -u root -p    //登陆mysql
SET PASSWORD=PASSWORD('123456');   //设置密码
GRANT ALL PRIVILEGES ON *.* TO root@'%' identified by '123456'; //允许远程连接
```
my.cnf文件内容：  
```
[mysqld]
basedir=/usr/local/mysql
datadir=/usr/local/mysql/data
socket=/tmp/mysql.sock
[mysqld_safe]
log-error=/var/log/mysqld.log
pid-file=/var/run/mysqld/mysqld.pid
```