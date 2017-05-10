---
date: 2017-05-10T15:32:49+08:00
draft: true
tags: []
categories: ["技术相关"]
title: OpenResty搭建Web服务
---

### 简介
OpenResty® 是一款基于 NGINX 和 LuaJIT 的 Web 平台。其内部集成了大量精良的 Lua 库、第三方模块以及大多数的依赖项。用于快速构造出足以胜任 10K 乃至 1000K 以上单机并发连接的高性能 Web 应用系统。

### 安装
http://openresty.org/cn/installation.html  
以macOS为例：

#### 安装Homebrew
https://brew.sh/index_zh-cn.html  
```
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
```

#### 安装OpenResty
```
brew install homebrew/nginx/openresty
```
安装到该路径下：/usr/local/Cellar/openresty/

#### 设置环境变量
```
vim ~/.bash_profile
source ~/.bash_profile 
nginx -v
```

### 启动

#### 创建工作目录

```
mkdir ~/work
cd ~/work
mkdir helloworld/conf/
```

#### 创建nginx.conf

```
worker_processes  1;
error_log logs/error.log;
events {
    worker_connections 1024;
}
http {
    server {
        listen 8080;
        location / {
            default_type text/html;
            content_by_lua '
                ngx.say("<p>hello, world</p>")
            ';
        }
    }
}
```

#### 启动

```
nginx -p `pwd`/ -c conf/nginx.conf
curl http://localhost:8080/
```