---
layout: post
title: JavaScript 原型
description: 原型是一个对象。<strong>所有对象都有原型。</strong>任何一个对象也都可以成为其他对象的原型。每个原型都有一个 constructor 属性指向其构造函数。
tags: 
  - javascript
  - 前端
show: true
---

今天研究了一下js的原型，把自己的理解写到这里，如有不正确的地方，还望指出，在此先谢过啦~

###什么是原型？

原型是一个对象。**所有对象都有原型。**任何一个对象也都可以成为其他对象的原型。
每个原型都有一个 `constructor` 属性指向其构造函数。

###怎么访问原型？

一个对象的原型被对象内部的 `[[Prototype]]` 属性所持有。
一句话就是，所有对象都有原型，其原型是该对象的内部属性。那么有哪些方法可以访问到这个内部属性呢？

 - ECMAScript 5 增加了方法 `Object.getPrototypeOf()` ，用于返回对象 `[[Prototype]]` 的值。IE 9+、Firefox 3.5+、Safari 5+、Opera 12+ 和 Chrome 都实现了该方法。
 - 除了IE，其他浏览器都支持非标准的访问器 `__proto__` 
 - 如果这两者都不起作用，我们就需要通过对象的构造函数找它的原型属性了。
 
~~~
    var a = {};
    Object.getPrototypeOf(a);
    a.__proto__;
    a.constructor.prototype;
~~~
 


###函数的 prototype 属性
不知亲刚刚有没有注意到访问 `[[Prototype]]` 的最后一个方法 `a.constructor.prototype;` ，直接使用了构造函数的 `prototype`属性。

在这里要多说两句，js中函数也是对象，所以函数也有原型，其原型也和其他对象一样，可以通过 `Object.getPrototypeOf()` 和 `__proto__` 访问。但是创建对象时，我们往往要使用构造函数 （`constructor`） 的原型，为了用起来方便，就给构造函数添加了 `prototype` 属性，用于直接访问其原型。由于所有的函数都可以成为构造函数，所以就造就了函数有那么一点点特（you）殊（shi）—— 一个函数可以通过其 `prototype` 属性直接访问其原型，或者说 **一个函数的 `prototype` 属性指向其原型对象**。

如果仅仅只是因为一个实例而使用原型是没有多大意义的，这和直接添加属性到这个实例是一样的。原型真正魅力体现在多个实例共用一个原型，原型对象的属性一旦定义，就可以被多个引用它的实例所继承，这种操作在性能和维护方面其意义是不言自明的。

这也是构造函数存在的原因，构造函数提供了一种方便的跨浏览器机制，这种机制允许在创建实例时为实例提供一个通用的原型。

###原型语法
在使用原型前，先写一下构造函数部分。

~~~
function Calculator (decimalDigits, tax) {
    this.decimalDigits = decimalDigits;
    this.tax = tax;
};
~~~

####分步声明
分开设置原型的每个属性。

~~~
Calculator.prototype.add = function (x, y) {
    return x + y;
};

Calculator.prototype.subtract = function (x, y) {
    return x - y;
};
~~~
这样，在new Calculator对象以后，就可以调用add方法来计算结果了。
此时， `Calculator.prototype` 的 `constructor` 属性指向 Calculator。即：
`Calculator.prototype.constructor = Calculator`

####更简单的原型语法
通过给Calculator的prototype属性赋值**对象字面量**来设定Calculator对象的原型。

    Calculator.prototype = {
        add: function (x, y) {
            return x + y;
        },
        subtract: function (x, y) {
            return x - y;
        }
    };


上面的代码中，将 Calculator.prototype 赋值为一个以对象字面量形式创建的新对象。最终结果相同，但是，**此时 constructor 属性不再指向 Calculator** ，即 `Calculator.prototype.constructor != Calculator` 。

每创建一个函数，就会同时创建它的 prototype 对象，这个对象会自动获得 constructor 属性。而我们这里使用的语法，本质上完全重写了默认的 prototype 对象，因此 constructor 属性也就变成了新对象的 constructor 属性 —— 指向 **Object** 构造函数。

如果 constructor 的值很重要，可以向下面一样设定：

~~~
Calculator.prototype = {

    // 修复constructor 属性
    constructor: Calculator, 
    
    add: function (x, y) {
        return x + y;
    },

    subtract: function (x, y) {
        return x - y;
    }
};
~~~

####其他方式
使用function立即执行的表达式来为prototype赋值，格式如下：

~~~
Calculator.prototype = function () {
    var add = function (x, y) {
        return x + y;
    },

    var subtract = function (x, y) {
        return x - y;
    }
    
    return {
        add: add,
        subtract: subtract
    }
} ();
~~~

###原型的动态性

~~~
var friend = new Person();

Person.prototype.sayHi = function () {
    alert("hi");
};

friend.sayHi(); // "hi" （没有问题）
~~~

在以上代码中，即使 friend 是在添加新方法之前创建的，但它仍然可以访问这个新方法。其原因可以归结为实例与原型之间的松散连接关系。当我们调用 `friend.sayHi()` 时，会首先在实例中搜索名为 sayHi 的属性，在没有找到的情况下，会继续搜索原型。因为实例实例与原型之间连接是一个指针，而非副本，因此可以在原型中找到新的 sayHi 属性并返回保存在那里的函数。

可以随时为原型添加属性和方法，并且修改能立即在所有对象实例中反映出来，但如果重写了原型对象，那结果就不一样了……

~~~
function Person () {
}

var friend = new Person();

Person.prototype = {
    constructor: Person,
    name: "Nicholas",
    age: 29,
    job: "software Engineer",
    sayHi: function () {
        alert("hi");
    }
};

friend.sayHi(); // error
~~~

在这个例子中，先创建了 friend 实例，然后又重写其原型对象。在调用 `friend.sayHi()` 时发生错误，因为friend 指向的原型中不包含以该名字命名的属性。

重写原型对象切断了现有原型与任何之前已经存在的对象实例之间的联系，这些实例仍然引用的是最初的原型。



###一切都是对象

这部分与原型没什么大关系，只是看到了觉有帮助，就插到这里了，莫要见怪 ：）

当然，也不是所有的都是对象，值类型就不是对象。

举个不太容易理解的例子，函数作为对象时，其属性还是函数的例子。

~~~
var fn = function () {
    alert(100);
};

fn.a = 10;
fn.b = function () {
    alert(123);
};
fn.c = {
    name: "福布斯",
    year: 1968
};
~~~
上段代码中，函数就作为对象被赋值了a、b、c三个属性，第二个属性值就是函数，这个有用吗？
可以看看jQuery源码。

在jQuery源码中，“jQuery”或者“$”，这个变量其实是一个函数，不信可以用 typeof 验证一下。

~~~
console.log(typeof $);  // function
console.log($.trim(" ABC "));
~~~
验明正身！的确是个函数。而经常使用的 $.trim() 也是个函数。

很明显，这就是在 $ 或者 jQuery 函数上加了一个 trim 属性，属性值是函数，作用是截取前后空格。要习惯的把js中的一切看作对象，只要是对象，就是属性的集合，属性是键值对的形式。


###参考资料

 - <a href="http://blog.jobbole.com/9648/" target="_blank">理解JavaScript原型</a>
 - <a href="http://www.cnblogs.com/wangfupeng1988/p/3977987.html" target="_blank">深入理解javascript原型和闭包（1）——一切都是对象</a>
 - <a href="http://www.nowamagic.net/librarys/veda/detail/1648" target="_blank">JavaScript探秘：强大的原型和原型链</a>

