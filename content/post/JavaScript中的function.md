---
date: 2017-03-21T08:50:12+08:00
draft: false
tags: ["JavaScript"]
categories: ["技术相关"]
title: JavaScript中的function
---

JavaScript设计得最出色的就是它的函数的实现。它几乎近于完美。--Douglas Crockford  

### 第一类值（first-class values）
JavaScript中的函数就是对象。  
可以使用对象的地方都可以使用函数。  
比如可以赋值给变量，保存在其他对象中，可以作为函数的参数或者作为函数的返回值。  
函数跟其他对象不一样的地方在于可以在它后面加个括号调用它。  
据我了解，函数作为第一类值的语言还有Go，Lua。这种设计使得函数使用起来非常灵活。

#### C#中实现方法的传递
方法与函数的区别以我的理解来说，方法是面向对象中对于函数的说法，当一个函数属于一个对象，该函数就称为该对象的方法。  
由于C#和Java属于完全面向对象编程语言，所有的函数都是某个对象的方法，即使静态方法也是属于某个类型对象的方法。  
所谓编程，就是将一组需求分解成一组函数和数据结构的技能。  
那么所谓面向对象编程，就是将一组需求分解成一组对象，对象中就包含与本身相关的数据和用来操作数据的方法。
  
C#中通过委托（delegate）来实现类似的操作。  
委托也是C#中的一种类型，只不过它比其他类型来得特殊一点，该类型的实例就是方法的包装器。也就是说，我们可以把一个方法塞到一个委托对象里面，然后通过使用这个对象来使用塞到里面的方法。  

### 调用

- 方法调用模式  
当一个函数属于一个对象时，我们通过使用该对象的点操作符来调用该函数，这种调用方式就是方法调用模式。  
方法内部的this会被绑定到该对象，这个绑定是在调用的时候发生。
```js
var myObject = {
    value: 0,
    incrament: function (inc) {
        this.value += typeof inc === 'number' ? inc : 1;
    }
};
myObject.increment();
document.writeln(myObject.value);//1
myObject.increment(2);
document.writeln(myObject.value);//3
```

- 函数调用模式  
当一个函数并非一个对象的属性时，那么它就是被当作一个函数来调用。  
此模式调用函数时，this被绑定到全局对象。这是语言设计上的一个错误。倘若语言设计正确，那么当内部函数被调用时，this应该仍然绑定到外部函数的this变量。（Douglas Crockford大神的原话）  
看解决方案：
```js
//给myObject增加一个double方法。
myObject.double = function () {
    var that = this;
    var helper = function () {
        that.value = add(that.value, that.value);
    };
    helper();    //以函数的形式调用helper
};
//以方法的形式调用double
myObject.double();
document.writeln(myObject.value);//6
```

- 构造器调用模式  
函数调用前面加上new。  
会创建一个连接到该函数的prototype成员的新对象，同时this会被绑定到那个新对象上。

```js
//创建一个名为Quo的构造器函数。它构造一个带有status属性的对象。
var Quo = function (string) {
    this.status = string;
};
//给Quo的所有实例提供一个名为get_status的公共方法。
Quo.prototype.get_status = function () {
    return this.status;
};
//构造一个Quo实例
var myQuo = new Quo("confused");
document.writeln(myQuo.get_status());
```
构造器函数一般保存在大写格式命名的变量里，如果把构造器函数当初普通函数来调用，会发生跟你期望的不一样的事情，比如函数内部的this就会指向全局变量。  

- Apply调用模式  
apply方法是JavaScript函数拥有的一个方法。（因为函数也是对象，所以函数也可以拥有方法）  
它接收两个参数，第一个是要绑定给this的对象，第二个是一个参数数组。  
```js
//构造一个包含status成员的对象
var statusObject = {
    status: 'A-OK'
};
//statusObject并没有继承自Quo.prototype，
//但我们可以在statusObject上调用get_status方法，
//尽管statusObject并没有一个名为get_status的方法。
var status = Quo.prototype.get_status.apply(statusObject);//status值为‘A-OK’
```
### 闭包  
关于闭包不想扯太多，感觉说得越多越让人糊涂。  
先来看一下定义：  
闭包是指有权访问另一个函数作用域中的变量和参数的函数。  
函数可以访问它被创建时所处的上下文环境，这被称为闭包。  
两个定义可以结合起来理解，意思都差不多。  
再看个例子：  
```js
//该函数返回一个包含两个方法的对象，
//并且这些方法继续享有访问value变量的特权。
var myObject = (function () {
    var value = 0;
    return {
        increment: function (inc) {
            value += typeof inc === 'number' ? inc : 1;
        },
        getValue: function () {
            return value;
        }
    };
}());
```
不过可能还是不太好理解，所以还是得先扯一下词法作用域和作用域链。
#### 词法作用域  
等同于静态作用域，指作用域在词法解析的阶段就已经确定，不会改变。   
看个例子：   
```js
var foo = 1;
function static() {
    alert(foo);
}
!function () {  //!的作用是将函数声明变成函数表达式
    var foo = 2;
    static();
}();
//在js中，会弹出1而非2，因为static的scope在创建时，记录的foo是1。
//如果js是动态作用域，那么他应该弹出2。
```
词法作用域与动态作用域的区别：  
词法作用域的函数中遇到既不是形参也不是函数内部定义的局部变量的变量时，去函数定义时的环境中查询。  
动态域的函数中遇到既不是形参也不是函数内部定义的局部变量的变量时，到函数调用时的环境中查。  
#### 作用域链  
每个执行环境都有一个表示变量的对象——变量对象。  
全局环境的变量对象始终存在，而像某个函数的变量对象，则只有在函数执行的过程中存在（正常情况下）。  
作用域链就是存放这些变量对象的。  
当某个函数被调用时，会创建一个执行环境及相应的作用域链。此时该作用域链的最顶层自然是全局环境的变量对象，依次向下是外部函数的变量对象，当前执行环境的变量对象会被塞到最底层。  
在函数的执行过程中，为读取和写入变量的值，就需要在作用域链中查找变量，这样就实现了内部函数可以访问外部函数的变量或者参数，而且是访问它们本身而不是一个副本。  
一般来讲，当函数执行完毕后，局部活动对象就会被销毁，内存中仅保存全局作用域，但是闭包的情况又有所不同。  
在另一个函数内部定义的函数会将包含函数（即外部函数）的活动对象添加到它的作用域链中。  
在内部函数从外部函数中被返回后，它的作用域链被初始化为包含外部函数的活动对象和全局变量对象。更为重要的是，外部函数在执行完毕之后，其活动对象也不会被销毁，因为匿名函数的作用域链仍然在引用这个活动对象。换句话说，当外部函数返回后，其执行环境的作用域链会被销毁，但它的活动对象仍然会留在内存中；直到内部函数被销毁后。  
由于闭包会携带包含它的函数的作用域，因此会比其他函数占用更多的内存。