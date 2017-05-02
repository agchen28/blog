---
date: 2016-11-26T09:33:54+08:00
draft: false
tags: ["dotnet"]
categories: ["技术相关"]
title: CSharp中的委托
---
  
委托也是C#中的一种类型，只不过它比其他类型来得特殊一点，该类型的实例就是方法的包装器。也就是说，我们可以把一个方法塞到一个委托对象里面，然后通过使用这个对象来使用塞到里面的方法。  
既然委托是一个类，我们就来看一下这个类中有哪些重要的方法和字段。
```csharp
//当我们定义一个委托时
internal delegate void Feedback(Int32 value);
//编译器会定义一个完整的类：
internal class Feedback : System.MulticastDelegate{
    //构造器
    public Feedback(Object @obejct,IntPtr method);
    //这个方法的原型和你定义的委托一样
    //当在一个委托对象后面加上括号调用的时候其实就是调用Invoke方法
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