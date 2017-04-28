---
date: 2017-04-28T16:24:53+08:00
draft: false
tags: [".NET"]
categories: ["技术相关"]
title: lock
---
说到c#中的锁，可能有些人只知道lock这个东西。其实lock是c#编译器提供给Monitor类的专属关键字。  
Monitor只是FCL中提供的混合线程同步构造中的其中一个。  
Monitor这么有名气主要是因为它是C#中资历最老，使用最广的线程同步构造。  
### 使用方法
贴一段单例的代码：  
```csharp
    public sealed class Singleton
    {
        private static Object s_lock = new Object();
        private static Singleton uniqueInstance;

        private Singleton() 
        {
        }
        
        public static Singleton GetSingleton()
        {
            if (uniqueInstance == null)
            {
                //保证同时只有一个线程执行以下代码块
                lock (s_lock)
                {
                    if (uniqueInstance == null)
                        uniqueInstance = new Singleton();
                }
            }
            return uniqueInstance;
        }
    }
```
C#编译器看到lock关键字会偷偷帮我们做一些事情，上面的代码等同于：  
```csharp
    Boolean lockTaken = false;
    try {
        Monitor.Enter(s_lock, ref lockTaken);
        if (uniqueInstance == null)
            uniqueInstance = new Singleton();
    }finally{
        if (lockTaken) Monitor.Exit(s_lock);
    }
```

### 实现方式
当一个对象在堆中被创建的时候，都会有两个额外的开销字段：类型对象指针和同步块索引。  
这个同步块索引就是提供给Monitor的。  
每个堆上的对象都要为它预留一个字段，而且它还拥有自己的专属关键字，由此可见，Monitor的地位真的不一般。 
#### 工作原理 
CLR初始化时，在堆中分配一个同步块数组。  
一个对象在构造时，它的同步块索引初始化为-1，表示不引用任何同步块。  
调用Monitor.Enter时，对象的同步块索引会引用到同步块数组中的一个空白同步块。  
调用Monitor.Exit时，检查是否有其他线程正在等待该同步块，如果没有，同步块就自由了，原先对象的同步块索引重新设为-1。  
同步块数组能创建更多的同步块，所以同时同步大量对象时，不必担心系统会用光同步块。  
<img src="/imgs/lock/lock.png" width = "1000" height = "620" align=center />  


### 需要注意

- 建议永远不要给Monitor.Enter方法传递类型对象。

- 避免锁String对象，因为字符串是可留用的。

- 禁止传递值类型，因为值类型会在堆上被装箱成新的对象，造成每次都是锁不同的对象。

