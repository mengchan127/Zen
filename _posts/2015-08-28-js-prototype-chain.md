---
layout: post
title: 小角度看JS原型链
description: 在解题之前，先再说说 原型、原型链，以及 Function 和 Object 的关系，这也是本文的重点。
tags:
  - javascript
  - 前端
---


##前言

在 segmentfault 上看到这样一道[题目][1]：

~~~
var F = function(){};
Object.prototype.a = function(){};
Function.prototype.b = function(){};
var f = new F();
~~~
问：f 能取到a,b吗？原理是什么？

乍一看真的有点懵，仔细研究了一下，发现还是对原型理解不透彻，所以总结一篇，填个洞~

---

##Function和Object
在解题之前，先再说说 原型、原型链，以及 Function 和 Object 的关系，这也是本文的重点。

###原型

- 在创建一个函数的时候，会自动为其创建一个原型对象，可以通过函数的`prototype`属性访问到。
- 创建一个构造函数的实例对象，该实例对象内部将包含一个指针（内部属性），指向构造函数的原型对象。ECMA-262 第5版中管这个指针叫`[[prototype]]`。虽然在脚本中没有标准的方式访问`[[prototype]]`，但Firefox、 Safari、 Chrome在每个对象上都支持一个属性 `__proto__`，用于访问其构造函数的原型对象。
- 重要的事情再说一遍：
  **构造函数通过 prototype 属性访问原型对象。**
  **实例对象通过 [[prototype]] 内部属性访问原型对象，浏览器实现了 __proto__ 属性用于实例对象访问原型对象。**

~~~
var F = function () {};
var f = new F();
// 假设F的原型对象是 p, 则
// F.prototype === p;
// f.__proto__ === p;
~~~

再重复一遍。。`prototype`说的是构造函数和原型对象之间的关系，`__proto__`说的是实例对象和原型对象之间的关系。

###原型链
类 A继承B，B继承C……其实就是A的原型对象中有指针指向B的原型对象，而B的原型对象中有指针指向C的原型对象……注意是原型对象之间的联系，A B C 这三个构造函数之间并没什么关系，所以才称为“原型链”吧~

假设a是A的实例对象，则 a 的原型链为下图中紫色线条所示，橙色线条连接了构造函数和其原型对象。

![图片描述][2]


由图可以看出，原型链的末端是`Object.prototype.__proto__`即`null`。当查找`a`的某个属性或方法时，首先查找`a`自身有没有，没有则沿着原型链一直查找，直到找到或者最后到`null`返回`undefined`。

###Function 和 Object
`Function` 和 `Object` 之间的关系有点绕：
`Object` 是构造函数，既然是函数，那么就是`Function`的实例对象；`Function`是构造函数，但`Function.prototype`是对象，既然是对象，那么就是`Object`的实例对象。

**一切对象都是`Object`的实例，一切函数都是`Function`的实例。**
`Object`是`Function`的实例，而`Function.prototype`是`Object`的实例。
    
二者的关系如下图所示。

![图片描述][3]

可见，`Object`作为构造函数，它有 `prototype` 属性指向 `Object.prototype` , 作为实例对象， 它有 `Object.__proto__` 指向`Function.prototype`。`Function`是构造函数，它有`prototype`属性指向`Function.prototype`，而`Function`是函数，从而也是`Function`的实例，所以它有`Function.__proto__`指向`Function.prototype`，从而 `Function.__proto__ === Function.prototype` 为 `true`。

可在Chrome控制台下进行验证，如图。

![图片描述][4]

##原题解析
解决原型链问题最好的办法就是画图了，经过前面的分析，这个图画起来应该不成问题，如下~

![图片描述][5]

f 的原型链为蓝色线所画，所以 f 可以访问到 a , 不能访问到 b 。

如果不画图，乍一看，可能会觉得f 可以访问到 b，那是可能跟我一样误认为`F.prototype`指向`Function.prototype`，但其实`F.prototype`是对象而不是函数，所以它的原型对象**不会**是 `Function.prototype`。

所以，原型链问题一应要画图啊~

##原题扩展
在上题中，f 只能访问 a，不能访问 b 。但 F 既可以访问 a ，又可以访问 b。
如果把题修改成下面的样子， `F.b()`的结果是什么呢？为什么呢？可以想一下哦~

~~~
var F = function(){};
Object.prototype.a = function(){};
Function.prototype.b = function(){ console.log('F.__proto__') };
F.prototype.b = function (){console.log('F.prototype');};
~~~

##总结
- 读到这里，有没有发现函数一个比较特殊的地方？
一般的对象，只有一个`__proto__`属性用于访问其构造函数的原型对象，而对于函数来说，它既是函数又是对象。
作为函数，它生来就有`prototype`属性指向其原型对象`函数名.prototype`。
作为Function的实例对象，它有`__proto__`属性指向`Function.prototype`
通常，这两个属性是指向两个对象的，但`Function`的这两个属性指向相同，都指向`Function.prototype`。

- 对于函数 `A( )` 来说，`A.prototype` 中的方法是供其实例对象调用的，自己并不会用；当A 作为实例运行时，调用的是 `A.__proto__` 中的方法。也就是说，作为构造函数使用时，走的是`A.prototype`这条链，方法、属性赋给其实例；作为对象使用时，走的是`A.__proto__`这条链。在不同的场景下，分清它的身份就不会错了。

---

整篇下来，感觉自己说的也比较絮叨……不足之处，还请各位指正~ 至于题目，真的不知道该叫什么好。。

愿本文能带给坚持看完的你一些收获~  ^_^

##参考
[js 原型的问题 Object 和 Function 到底是什么关系？—— 高票答案][6]


  [1]: http://segmentfault.com/q/1010000003503459
  [2]: {{ site.url }}/blog/blog_imgs/7.png
  [3]: {{ site.url }}/blog/blog_imgs/8.png
  [4]: {{ site.url }}/blog/blog_imgs/10.png
  [5]: {{ site.url }}/blog/blog_imgs/9.png
  [6]: http://segmentfault.com/q/1010000002736664
