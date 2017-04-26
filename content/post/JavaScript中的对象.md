---
date: 2017-02-21T09:34:12+08:00
draft: false
tags: ["JavaScript"]
categories: ["技术相关"]
title: JavaScript中的对象
---
在JavaScript中，数组是对象，函数是对象，正则表达式是对象，当然，对象自然也是对象。  
JavaScript里的对象是无类型（class-free）的。它对新属性的名字和属性的值没有限制。  

### 原型（prototype）

- 原型对象  
每个对象都链接到一个原型对象，并且它可以从中继承属性（听起来是不是像CLR中的类型对象）。所有通过对象字面量创建的对象都连接到Object.prototype,它是JavaScript中的标配对象。  
原型链接在更新时是不起作用的。当我们对某个对象做出改变时，不会触及改对象的原型。  
原型链接只有在检索值的时候才被用到。如果我们尝试去获取对象的某个属性值，但该对象没有此属性名，那么JavaScript会试着从原型对象中获取属性值。如果那个原型对象也没有该属性，那么再从它的原型中找，依次类推，知道该过程最后到达终点Object.prototype。如果想要的属性完全不存在于原型链中，那么结果就是undefined值。  
原型关系是一种动态的关系。如果我们添加一个新的属性到原型中，该属性会立即对所有基于该原型创建的对象可见。

```js
//这种继承方式被称作原型式继承
//通过修改对象的原型对象来实现
if (typeof Object.create !== 'function') {
    Object.create = function (o) {
        var F = function () { };
        F.prototype = o;
        return new F();
    };
}
var another_stooge = Object.create(stooge);
```

- 构造函数、原型对象与实例对象之间的关系

```js
function SuperType() {
    this.property = true;
}
SuperType.prototype.getSuperValue = function () {
    return this.property;
};
function SubType() {
    this.subproperty = false;
}
//继承了SuperType
SubType.prototype = new SuperType();
SubType.prototype.getSubValue = function () {
    return this.subproperty;
};
var instance = new SubType();
alert(instance.getSuperValue()); //true
```
每个构造函数都有一个原型对象，原型对象都包含一个指向构造函数的指针，而实例都包含一个指向原型对象的内部指针。  
它们之间的关系如下图：  
<img src="/imgs/yxl/yxl.png" width = "652" height = "531" align=center />

