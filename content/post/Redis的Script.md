---
date: 2016-09-09T23:28:44+08:00
draft: false
tags: ["redis"]
categories: ["技术相关"]
title: Redis的Script
---

### EVAL
Redis 2.6.0开始，通过内置的Lua解释器，可以使用EVAL命令对Lua脚本进行求值。  
```
EVAL script numkeys key [key ...] arg [arg ...]
```

- script 参数是一段 Lua 5.1 脚本程序，它会被运行在 Redis 服务器上下文中。  

- numkeys 参数用于指定键名参数的个数。

- key [key ...] 表示键名参数，从 EVAL 的第三个参数开始算起，表示在脚本中所用到的那些 Redis 键，这些键名参数可以在 Lua 中通过全局变量 KEYS 数组，用 1 为基址的形式访问(如： KEYS[1] ， KEYS[2] )。

- arg [arg ...] 是附加参数，可以在 Lua 中通过全局变量 ARGV 数组访问。  

```
> eval "return {KEYS[1],KEYS[2],ARGV[1],ARGV[2]}" 2 key1 key2 first second
1) "key1"
2) "key2"
3) "first"
4) "second"
```
在 Lua 脚本中，可以使用两个不同函数来执行 Redis 命令： 

- redis.call()  
在执行命令的过程中发生错误时，脚本会停止执行，并返回一个脚本错误。  

- redis.pcall()  
出错时不引发错误，而是返回一个带 err 域的 Lua 表。

### 脚本的原子性

Redis保证脚本会以原子性(atomic)的方式执行：当某个脚本正在运行的时候，不会有其他脚本或 Redis 命令被执行。  
这也意味着，当你不得不使用一些跑得比较慢的脚本时，其他客户端会因为服务器正忙而无法执行命令。

### EVALSHA
EVALSHA 命令接受的第一个参数是脚本的 SHA1 校验和。我们可以乐观地使用 EVALSHA 命令，只有当服务器返回 NOSCRIPT 错误时，我们才使用 EVAL 命令，这样可以避免带宽的浪费。