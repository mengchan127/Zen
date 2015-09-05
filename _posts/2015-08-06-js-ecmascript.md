---
layout: post
title: ECMAScript各版本简介及特性
description: 今天跟一位同学交流，她说面试的时候被问到“ECMAScript有哪些版本，他们之间各有什么区别？” 想想ECMAScript版本的问题还真没有考虑过，只知道最新的6，之前的5。本着了解ECMAScript历史的心态，查了查资料，总结如下~不足之处，还请指正~
tags:
  - javascript
  - 前端
---

今天跟一位同学交流，她说面试的时候被问到“ECMAScript有哪些版本，他们之间各有什么区别？”
想想ECMAScript版本的问题还真没有考虑过，只知道最新的6，之前的5。
本着了解ECMAScript历史的心态，查了查资料，总结如下~不足之处，还请指正~

---

##术语

###ECMAScript

Sun(现在的Oracle)公司持有着“Java”和“JavaScript”的商标。这就让微软不得不把自己的JavaScript方言称之为“JScript”。然后，在这门语言被标准化的时候，就必须使用一个与二者都不同的名字。“ECMAScript”就这样诞生了，这个名字的来由是因为执行标准化的组织是Ecma国际。通常来说，术语“ECMAScript”和“JavaScript”指的是同一个东西。但如果把JavaScript看成是“Mozilla或其他组织的ECMAScript实现”，那么ECMAScript就是实现JavaScript所依据的标准。

###ECMA-262

Ecma国际 (一个标准化组织)创建了ECMA-262规范,这个规范就是ECMAScript语言的官方标准。

###ECMAScript 6

指的就是ECMA-262规范的第六版,同时也是当前最新的正式规范。

###Ecma第39号技术委员会 (TC39)

是一组开发ECMA-262标准规范的人(Brendan Eich和其他一些人)。

##历史

###ECMAScript 1

1997年6月发布，本质上与javascript 1.1 相同——只不过只不过删除了所有针对浏览器的代码并作了一些较小的改动：ECMAScript要求支持Unicode标准，而且对象也变成了平台无关的。

###ECMAScript 2

1998年6月发布，主要是编辑加工的结果。这一版的内容更新是为了与ISO/IEC-16262保持严格一致，没有作任何新增、修改或删节处理。因此，一般不使用第2版来衡量ECMAScript实现的兼容性。

###ECMAScript 3

1999年12月发布，是对ECMAScript标准第一次真正的修改。新增了对**正则表达式、新控制语句、try-catch异常处理**的支持，修改了字符处理、错误定义和数值输出等内容。
从各方面综合来看，第3版标志着ECMAScript成为了一门真正的编程语言。

###ECMAScript 4

于2008年7月发布前被废弃……命运坎坷

###ECMAScript 5

2009年12月发布，该版本力求澄清第3版中的歧义，并添加了新的功能。新功能包括：**原生JSON对象、继承的方法、高级属性的定义**以及引入**严格模式**。

###ECMAScript 6

2015年6月17日发布。截止发布日期，JavaScript的官方名称是ECMAScript 2015，Ecma国际意在更频繁地发布包含小规模增量更新的新版本，下一版本将于2016年发布，命名为ECMAScript 2016。从现在开始，新版本将按照ECMAScript+年份的形式发布。
S6是继ES5之后的一次主要改进，语言规范由ES5.1时代的245页扩充至600页。ES6增添了许多必要的特性，例如：模块和类以及一些实用特性，例如Maps、Sets、Promises、生成器（Generators）等。
尽管ES6做了大量的更新，但是它依旧完全向后兼容以前的版本，标准化委员会决定避免由不兼容版本语言导致的“web体验破碎”。结果是，所有老代码都可以正常运行，整个过渡也显得更为平滑，但随之而来的问题是，开发者们抱怨了多年的老问题依然存在。

##ECMAScript 5新特性详解

移步下面三篇文章~

- [ECMAScript5 Object的新属性方法][1]
- [ECMAScript5 Array新增方法][2]
- [ECMAScript5的其它新特性][3]

##ECMAScript 6新特性及代码示例

移步这里~
[ECMAScript 6 — New Features: Overview & Comparison][4]

新增特性的兼容性看这里 [http://kangax.github.io/compat-table/es6/][5]

---

##补充

- [ECMAScript 6新特性介绍][6]
- [ES6中非常实用的新特性介绍][7]

---

##参考文章：
[[译]ECMAScript:ES.next和ES6以及ES Harmony之间的区别][8]


  [1]: http://www.cnblogs.com/dolphinX/p/3348467.html
  [2]: http://www.cnblogs.com/dolphinX/p/3353868.html
  [3]: http://www.cnblogs.com/dolphinX/p/3354319.html
  [4]: http://es6-features.org/#Constants
  [5]: http://kangax.github.io/compat-table/es6/
  [6]: http://segmentfault.com/a/1190000002930417
  [7]: http://segmentfault.com/a/1190000003492656
  [8]: http://www.cnblogs.com/ziyunfei/archive/2012/09/24/2699065.html