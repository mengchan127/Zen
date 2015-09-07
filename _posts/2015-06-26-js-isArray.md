---
layout: post
show: true
title: JavaScript 检测数组
description: JS的经典问题之一就是“检测一个对象是不是数组”，本文总结了3种检测方法。
tags:
  - javascript
  - 前端
---

JS的经典问题之一就是“检测一个对象是不是数组”，本文总结了3种检测方法。

##instanceof检测方法

~~~
var arr = [];
if (arr instanceof Array) {
    // do something
}
~~~

**存在问题**
`instanceof` 操作符假设只有一个全局执行环境。如果网页中包含多个框架，那实际上存在两个以上的不同全局执行环境，从而存在两个以上不同版本的Array构造函数。如果从一个框架向另一个框架传入数组，那么传入的数组与第二框架中原生的数组分别具有各自不同的构造函数。从而将传入的数组误判为非数组。

如果只有一个全局执行环境，可以用 `instanceof` 检测数组。

##Array.isArray()方法

~~~
var arr = [];
if (Array.isArray(arr)) {
    // do something
}
~~~

这个方法克服了`instanceof`的问题，可以确定某个变量是不是数组，而不管它是在哪个全局执行环境中创建的。
但是支持`Array.isArray()`方法的浏览器有IE9+、Firefox4+、Safari5+、Opera10.5+和Chrome。对于不支持这个方法的浏览器怎么办呢？第三种——万能方法。


##万能方法

~~~
function isArray(arr) {
    return Object.prototype.toString.call(arr) == '[object Array]';
}

var arr = [];
isArray(arr);
~~~

**原理**
在任何值上调用Object原生的`toString()` 方法，都会返回一个`[object NativeConstructorName]` 格式的字符串。每个类在内部都有一个[ [Class] ]属性，这个属性就指定了上述字符串中的构造函数名`NativeConstructorName`。如：

~~~
var arr = [];
console.log(Object.pototype.toString.call(arr)); // "[object Array]"
~~~

这种思路也可用于检测某个值是不是原生函数或正则表达式。

~~~
// Array
Object.prototype.toString.call(value); // "[object Array]"

// Function
Object.prototype.toString.call(value); // "[object Function]"

// RegExp
Object.prototype.toString.call(value); // "[object RegExp]"

// Date
Object.prototype.toString.call(value); // "[object Date]"
~~~

>Object的toString()方法不能检测非原生的构造函数的构造函数名，因此，开发人员定义的任何构造函数都将返回[object Object]。