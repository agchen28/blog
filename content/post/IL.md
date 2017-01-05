---
date: 2016-12-27T21:50:53+08:00
draft: true
title: IL
tags: [".NET基础"]
categories: ["正经的"]
---

与CPU无关的机器语言（基于栈，没有直接操作寄存器的指令，对底层CPU抽象）
计算机没有办法直接执行，必须托管给CLR执行，所以又称托管代码（managed code）
比大多数CPU机器语言都高级，是一种面向对象的机器语言。
以二进制的形式存在
跟其他任何机器语言一样，可以用IL汇编语言编写IL
ILAsm.exe(IL汇编器)
ILDasm.exe（IL反汇编器）
高级语言通常只公开了CLR的部分功能，IL汇编语言允许开发人员访问CLR的全部功能。
所以如果你选择的编程语言隐藏了你迫切需要的一个CLR功能，可以换用IL汇编语言或者提供了所需功能的另一种编程语言来实现。
混合语言编程如：
C#和VB能很好的执行IO操作，APL语言很好的执行金融计算

### VS添加ILDASM外部工具
打开工具——>外部工具，点添加标题自己命名，命令输入ildasm.exe所在目录。  
我这边就是这一串（C:\Program Files (x86)\Microsoft SDKs\Windows\v10.0A\bin\NETFX 4.6.1 Tools\ildasm.exe）

### 偷个图
![](/imgs/ildasm/1.jpg)

### 简单介绍
想简单介绍一下还简单不了。
必须先了解其他几个概念。P10
CLR
托管模块
元数据
IL代码
IL汇编语言
ILAsm.exe(IL汇编器)
ILDasm.exe（IL反汇编器）

### 用途
窥探编译器做了什么事情，了解程序的本质。  
