---
date: 2017-03-21T08:50:12+08:00
draft: false
tags: ["JavaScript","C#"]
categories: ["技术相关"]
title: JavaScript中的function
---

JavaScript设计得最出色的就是它的函数的实现。它几乎近于完美。--Douglas Crockford

### 第一类值（first-class values）
JavaScript中的函数就是对象。  
可以使用对象的地方都可以使用函数。  
比如可以赋值给变量，保存在其他对象中，可以作为函数的参数或者作为函数的返回值。  
对象字面量产生的对象都连接到Object.prototype这个原型对象。函数对象连接到Function.prototype（该原型对象本身连接到Object.prototype）。函数跟其他对象不一样的地方在于可以在它后面加个括号调用它。
据我了解，函数作为第一类值的语言还有Go，Lua。这么设计使得函数使用起来非常灵活。

### C#中实现方法的传递
方法与函数的区别以我的理解来说，方法是面向对象中对于函数的叫法，当一个函数属于一个对象，该函数就称为该对象的方法。  
由于C#和Java属于完全面向对象编程语言，所有的函数都是某个对象的方法，即使静态方法也是属于某个类型对象的方法。  
所谓编程，就是将一组需求分解成一组函数和数据结构的技能。  
那么所谓面向对象编程，就是将一组需求分解成一组对象，对象中就包含与本身相关的数据和用来操作自身数据的方法。
  
C#中通过委托（delegate）来实现类似的操作。  
委托是C#中的一种类型，该类型的对象就是方法的包装器，也就是说，我们可以把一个方法塞到一个委托对象里面，然后就可以通过使用这个对象来使用塞到里面的方法。  
具体实现：  
既然委托是一个类，我们就来看一下这个类中有哪些重要的方法和字段。
```csharp
internal delegate void Feedback(Int32 value);
//编译器会定义一个完整的类：
internal class Feedback : System.MulticastDelegate{
    //构造器
    public Feedback(Object @obejct,IntPtr method);
    //这个方法的原型和你定义的委托一样
    //当在一个委托对象后面加上括号调用的时候其实就是调用这个方法
    public virtual void Invoke(Int32 value);
    //以下方法实现对回调方法的异步回调
    public virtual IAsyncResult BeginInvoke(Int32 value, AsyncCallback callback, Object @object);
    public virtual void EndInvoke(IAsyncResult result);
}
```
所有的委托类型都派生自MulticastDelegate，MulticastDelegate又派生自System.Delegate。MulticastDelegate有三个最重要的非公共字段。  

- _target    Oject类型     指出方法的对象，如果是静态方法置为null。

- _methodPtr    IntPtr类型    一个内部的整数值，CLR用它标识要回调的方法。

- _invocationList    Object类型    该字段通常为null。构造委托链时它引用一个委托数组。

### 总结
Java中的实现方式以我目前的了解程度可能还说不清楚，不过应该可以猜到会和C#中的实现方式差不多，要仔细研究下不然不敢瞎说。  
可能有点偏离主题了，讲JavaScript函数其实还会涉及到闭包还有4种调用模式一些复杂的东西，这些可能还要找个时间再补上。