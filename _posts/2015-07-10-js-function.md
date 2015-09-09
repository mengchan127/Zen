---
layout: post
show: true
title: 为function添加值为function的属性
description: 今天在某个js交流群里看到这样一个问题——亲们，如果我定义一个 function A() { } 可以给这个函数添加一个方法吗？
tags:
  - javascript
  - 前端
---

今天在某个js交流群里看到这样一个问题：

>亲们，如果我定义一个`function A() { }` 可以给这个函数添加一个方法吗？例如：`A.use = function() { }`

我回答说“可以，js里`function` 是对象"，但再往深一点想，对这个问题还真有点模糊，果断验证一下~

先说下答案：**这种写法是可以的**。

---

理论上来说，js里function是对象，给对象添加属性是可以的。但这样的函数执行是怎样的呢？两个函数有什么联系呢？

~~~
function A() {
    console.log('I am A');
}

A.use = function () {
    console.log('123');
};

A(); // I am A
A.use(); // 123
~~~

运行结果可知，这两个函数在运行的时候并没有什么相互的影响，也体现了函数名就是个引用，`A` 和 `A.use` 是两个不同的引用，运行的时候指向不同的内存区域，所以没有影响。

上面的代码改为：

~~~
var A = function() {
    console.log('I am A');
}

A.use = function () {
    console.log('123');
};

A(); // I am A
A.use(); // 123
~~~
用函数表达式来定义A( )，这样是不是就明显多了，或者说习惯多了

---

上面的结果表明两个函数运行时互不影响，但是真的一点关系都没有吗？想起前几天总结的`this`，就好奇地验证了下这两个函数的`this`是什么。

~~~
function A() {
    console.log(this);
}

A.use = function () {
    console.log(this);
};

A(); // Window
A.use(); // function A()
~~~

A.use()中的this是`function A()`，这也符合this的使用规则:

> 函数作为对象的方法调用时，this指向这个对象。

只不过在这里，这个对象是个函数而已。

---

我在写 <a href="http://segmentfault.com/a/1190000002867132" target="_blank">《JavaScript原型》</a> 里面摘抄过下面这段：

在jQuery源码中，“jQuery”或者“$”，这个变量其实是一个函数，可以用 `typeof` 验证一下。

~~~
console.log(typeof $);  // function
console.log($.trim(" ABC ")); // ABC
~~~

验明正身！“$”的确是个函数。而经常使用的 $.trim() 也是个函数。

很明显，这就是在 $ 或者 jQuery 函数上加了一个 trim 属性，属性值是函数，作用是截取前后空格。

要习惯的把js中的一切看作对象，只要是对象，就是属性的集合，属性是键值对的形式。

当时只是记住了可以这样写，今天算是真的明白了 ：）
