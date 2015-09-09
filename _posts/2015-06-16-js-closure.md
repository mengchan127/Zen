---
layout: post
show: true
title: JavaScript 闭包
description: 作为一个初学者，学习一个新的概念无非就是知道 <b>“它是什么”、“什么原理”、“怎样创建”、“有什么用”、“怎么用”</b>，这篇文章就从这几个角度介绍闭包，欢迎小伙伴们拍砖~
tags:
  - javascript
  - 前端
---

看了《JS高级程序设计》和下面几篇文章，小总结下JS闭包~

- <a href="http://www.ruanyifeng.com/blog/2009/08/learning_javascript_closures.html" target="_blank">学习Javascript闭包（Closure）</a>
- <a href="http://www.cnblogs.com/rubylouvre/archive/2009/07/24/1530074.html" target="_blank">javascript的闭包</a>
- <a href="http://www.jb51.net/article/18303.htm" target="_blank">JavaScript 闭包深入理解(closure)</a>
- <a href="http://www.oschina.net/question/28_41112" target="_blank">理解 Javascript 的闭包</a>
- <a href="http://segmentfault.com/a/1190000002805295" target="_blank">JavaScript 中的闭包</a>


作为一个初学者，学习一个新的概念无非就是知道 **“它是什么”、“什么原理”、“怎样创建”、“有什么用”、“怎么用”**，这篇文章就从这几个角度介绍闭包，欢迎小伙伴们拍砖~

----------

##什么是闭包
上面几篇文章，每一篇对闭包的定义都不相同，其实这真是一个很难用语言完美描述出来的东西。个人更喜欢阮一峰写的:

>闭包就是能够读取其他函数内部变量的函数。

在JavaScript中，函数A内部的局部变量不能被A以外的函数访问到，只能被A内部的子函数B访问到，当在函数A外调用函数B时，便可通过B访问A中局部变量，那么子函数B就是闭包。(注意，是**子函数**哦~)
这样说比较抽象，举个栗子~

~~~
function outer() {

    var a = 100; // outer的局部变量
    
    function inner() { // 闭包
        console.log(a);
    }
    
    return inner; // 没有这条语句，闭包起不到在outer外部访问变量a的作用~
}
console.log(a); // 在外部直接访问a出错，Uncaught ReferenceError: a is not defined

var test = outer(); // outer运行完返回inner，赋值给test
test(); // 100，执行test()，相当于执行inner()，这样就可以访问到outer内部的a了

~~~

司徒正美在blog中从另一个角度解释了闭包：

>简单来说，闭包就是在另一个作用域中保存了一份它从上一级函数或作用域取得的变量（键值对），而这些键值对是不会随上一级函数的执行完成而销毁。周爱民说得更清楚，闭包就是“属性表”，闭包就是一个数据块，闭包就是一个存放着“Name=Value”的对照表。就这么简单。但是，必须强调，闭包是一个运行期概念。

##闭包的原理

说原理可能有些大，但这部分确实是要从内部机制的角度，解释下“为什么上面的栗子中通过inner就可以在outer外部访问outer内部的a”~

个人认为在JS中，有两个链很重要：`原型链` 和 `作用域链`。通过 `原型链` 可以实现`继承`，而与 `闭包` 相关的就是 `作用域链`。

还是上面的栗子，假设outer定义在全局作用域中

~~~
1  function outer() {

2     var a = 100; 
    
3     function inner() { 
4         console.log(a);
5     }
    
6     return inner; 
7  }

8  var test = outer(); 
9  test(); 
~~~

当执行到`1`处，outer函数定义，其作用域链中只有一个全局对象。
然后执行`8`，运行outer()。此时，outer的作用域链中有两个对象：`outer的活动对象`-->`全局对象`。
运行outer()，会执行outer里面的`3`，定义inner()，此时inner的作用域链是：`outer的活动对象`-->`全局对象`。
执行`9`，其实就是执行inner函数，此时其作用域链是：`inner的活动对象`-->`outer的活动对象`-->`全局对象`。

因为inner的作用域链中有`outer的活动对象`，所以它可以访问到outer的局部变量a。

常理来说，一个函数执行完毕，其执行环境的作用域链会被销毁。但是outer执行完毕后，其活动对象仍然保留在内存中，因为inner的作用域链仍在引用着这个活动对象。所以此时，outer的作用域链虽然销毁了，但是其活动对象仍在内存中。直到test执行完毕，outer的活动对象才被销毁。

也正因为如此，**闭包只能取得包含函数中任何变量的最后一个值，即包含函数执行完毕时变量的值。**改改之前的栗子~

~~~
 function outer() {

    var a = 100; 
    
    function inner() { 
        console.log(a);
    } 
    
    a = a + 50; // 改变a的值
    
    return inner; 
}

var test = outer(); 
test(); // 150，取得的a是150，而不是100
~~~

正是因为作用域链，只能里面的访问外面的，外面的不能访问里面的。也是基于作用域链，聪明的大师们想出了闭包，使得外面的可以访问里面的。掌握作用域链很关键啊~

##创建闭包的方式
创建闭包最常见的方式就是**在一个函数里面创建一个函数**，之前举得栗子就是。

司徒正美大神给出了3种闭包实现：

~~~
with(obj){
    //这里是对象闭包
}
~~~

~~~
(function(){
    //函数闭包
 })();
~~~

~~~
try{
   //...
} catch(e) {
   //catch闭包 但IE里不行
}
~~~

##闭包的作用

**1. 在函数外面读取函数的局部变量**
前面一直在说这个事儿~

**2. 在内存中维持一个变量**

~~~
function outer() {

    var a = 100; 
    
    function inner() { 
        console.log(a++);
    } 
    
    return inner; 
}

var test = outer(); 
test(); // 100
test(); // 101
test(); // 102
~~~
栗子中a一直在内存中，每执行一次`test()`，输出值加1。因为`test`在全局执行环境中，所以a一直在内存中，如果`test`在其他执行环境中，当这个执行执行环境销毁的时候，a就不会再在内存中了。

**3. 实现面向对象中的对象**
javascript并没有提供类这样的机制，但是可以通过闭包来模拟类的机制。把父函数当作对象（object）使用，把闭包当作它的公用方法（Public Method），把内部变量当作它的私有属性（private value）。

~~~
function Person(){
    var name = 'XiaoMing';

    return {
        setName : function(theName){
            name = theName;
        },

        getName : function(){
            return name;
        }
    };
}
var person = new Person();
console.log(person.name); // undefined
console.log(person.getName()); // XiaoMing
~~~

##几个闭包示例
参考的这几篇文章基本都给出了闭包示例，悄悄摘选司徒正美大神的几个放到这里~

~~~
//*************闭包uniqueID*************

uniqueID = (function(){

    var id = 0; 
    return function() { 
        return id++; // 返回,自加
    };  
})(); 

document.writeln(uniqueID()); //0
document.writeln(uniqueID()); //1
document.writeln(uniqueID()); //2
document.writeln(uniqueID()); //3
document.writeln(uniqueID()); //4
~~~

~~~
//*************闭包阶乘*************

var a = (function(n) {
    if (n<1) { 
        alert("invalid arguments"); 
        return 0; 
    }
    if(n==1) { 
        return 1; 
    }
    else { 
        return n * arguments.callee(n-1); 
    }
})(4);

document.writeln(a);
~~~

~~~
//***********实现面向对象中的对象***********

function Person(){
    var name = 'XiaoMing';

    return {
        setName : function(theName){
            name = theName;
        },

        getName : function(){
            return name;
        }
    };
}
var person = new Person();
console.log(person.name); // undefined
console.log(person.getName()); // XiaoMing
~~~

~~~
//***********避免 Lift 效应***********

var tasks = [];
for (var i = 0; i < 5; i++) {
    // 注意有一层额外的闭包
    tasks[tasks.length] = (function (i) {
        return function () {
            console.log('Current cursor is at ' + i);
        };
    })(i);
}

var len = tasks.length;
while (len--) {
    tasks[len]();
}
~~~

运行结果：

~~~
Current cursor is at 4
Current cursor is at 3
Current cursor is at 2
Current cursor is at 1
Current cursor is at 0

~~~

**补充：Lift效应**

~~~
var tasks = [];
for (var i = 0; i < 5; i++) {
    tasks[tasks.length] = function () {
        console.log('Current cursor is at ' + i);
    };
}

var len = tasks.length;
while (len--) {
    tasks[len]();
}
~~~

上面这段代码输出`5`次

~~~
Current cursor is at 5
~~~

这一现象称为 `Lift效应`。原因在于

>在引用函数外部变量时，函数执行时外部变量的值由运行时决定而非定义时

实际编程的时候，很容易遇见`lift效应`，加一层闭包就可以解决哦~

##需注意的问题
由于闭包会使得函数中的变量都被保存在内存中，内存消耗很大，所以不能滥用闭包，否则会造成网页的性能问题，在IE中可能导致内存泄露。解决方法是，在退出函数之前，将不使用的局部变量全部删除。
如果有**非常庞大**的对象，且预计会在**老旧的引擎**中执行，则使用闭包时，注意将闭包不需要的对象置为空引用。


---

这是我对于闭包学习的总结，错误和不足欢迎大家指正~





 