---
layout: post
title: css中的line-height
description: 简单说说CSS中我们常用但又不一定真正了解的<b>line-height</b>属性
tags:
  - css
  - 前端
---

又已经快十天没有写文章了，一周一篇其实好艰难的说……本来想接着上篇 [事件（上篇）][1] 总结事件类型的，可是看完之后整理下还是有点乱，就一直拖着没写……实在是不能再拖下去了，今天就简单说说CSS中我们常用但又不一定真正了解的`line-height`属性。

##基本概念

###行高、行距

**行高是指文本行基线间的垂直距离**。那什么是基线呢？记不记得`vertical-align`属性有个`baseline`值，这个`baseline`就是基线。看张“盗图”（选自下面的参考文章），其实我也修改了一下啦~

![图片描述][2]
**注意**：倒数第二根是基线哦，最下面那根是底线，不是基线。

图中两条红线之间的距离就是**行高**（line-height），上一行的底线和下一行的顶线之间的距离就是**行距**，而同一行顶线和底线之间的距离是**font-size**的大小，行距的一半是**半行距**，半行距、font-size、line-height之间的关系看图片的右下角就一目了然了~

    半行距 = （line-height - font-size）/2

当然，半行距也可能为负值（当line-height < font-size），这时候两行之间就会重叠，如下图所示：
![图片描述][3]

###4种box
要说的4种盒子分别是`inline box`、`line box`、`content area`、`containing box` ~

- inline box (行内框)
每个行内元素会生成一个行内框，行内框是一个浏览器渲染模型中的一个概念，无法显示出来，行内框的高度等于`font-size`，**设定`line-height`时，行内框的高度不变，改变的是行距**。

- line box （行框）
行框是指本行的一个虚拟的矩形框，由该行中行内框组成。行框也是浏览器渲染模式中的一个概念，无法显示出来。**行框高度等于本行中所有行内框高度的最大值。**当有多行内容时，每一行都有自己的行框。

- content area （内容区）
内容区是围绕着文字的一种box，无法显示出来，其高度取决于`font-size`和`padding`。***个人觉得***：内容区的高度 = font-size + padding-top + padding-bottom，有待查证，也期待小伙伴们给出答案~

- containing box 
containing box 是包裹着上述三种box的box，有点绕哈~看图

![图片描述][4]

原谅我画图水平有限，不过仔细辨认还是能看出来的~  ^_^

##取值
一般情况下，浏览器默认的line-height为1.2。可以自定义 line-height 覆盖这个初始值，那么该怎样设置line-height呢？有以下5种方式：

|------+----|
|  值  |描述|
|------|:---|
|normal|默认。设置合理的行间距。|
|number|设置数字，此数字会与当前的字体尺寸相乘来设置行间距，即number为当前font-size的倍数。|
|length|设置固定的行间距。|
|%|基于当前字体尺寸的百分比行间距。|
|inherit|规定应该从父元素继承 line-height 属性的值。|
|-------+----|

看起来如此简单~但是，`line-height`是个**可继承**属性，它的继承规则有那么一点点复杂……

##继承
需要提前说明的是：`line-height`的大小与`font-size`息息相关，除了指定`line-height`为多少`px`，剩下的设置方式都是**基于font-size算出来的**。
下面逐个讲一讲~
 
- inherit
这个其实没什么说的，继承父元素`line-height`的值，所以父元素的是多少就是多少。
如果其后代元素不设置`line-height` 的话，也会是这个值。

- length
假设设置 line-height 为20px，那么该行的该行的行高就是20px，与 font-size 无关，不会随着 font-size 做相应比例的缩放。
这个长度值（20px）会被后代元素继承，所有的后代元素会使用这个相同的、继承的 line-height (20px)，除非后代元素设定 line-height 。

- 百分比
假设自身的 font-size 为16px，line-height 设为120%。那么其行高为：16 * 120% = 19.2px。即 line-height 是根据自身的 font-size 计算出来的。
子元素会继承父元素的line-height，那么它继承的是什么呢，百分比（120%）？还是19.2px？
答案是后者，19.2px，即父元素`line-height`计算后的最终值。

- normal
line-height 设置为 normal 的时候，行高取决于浏览器的解析，一般是1.2。
与前面不同的是，line-height 设置为 normal 的元素，其子元素不再继承其line-height计算后的最终值，而是根据子元素自身的 font-size 进行计算。见下表~

  |-------+---------+-----------+--------------------|
  |element|font-size|line-height|计算后的lline-height|
  |-------|---------|-----------|--------------------|
  |body|16px|normal|16px * 1.2 = 19.2px|
  |h1|32px|normal|32px * 1.2 = 38.4px|
  |-------+---------+-----------+--------------------|

  可见，子元素随着自身 font-size 的大小而做相应比例的缩放。

- 纯数字
如果既想要 normal 的灵活，又想设置一个自定义的值，那就要用 **`纯数字`** 啦~
纯数字方式与 normal 唯一的不同，就是数值的大小，纯数字可以自己随意设定，而 normal 的值是浏览器决定的。

  |-------+---------+-----------+--------------------|
  |element|font-size|line-height|计算后的lline-height|
  |-|-|-|-|
  |body|16px|1.5|16px * 1.5 = 24px|
  |h1|32px|1.5|32px * 1.5 = 48px|
  |-------+---------+-----------+--------------------|
    
    其后代元素会继承这个数值（比如 1.5），然后根据自身的 font-size 算出自身的line-height。

总结如下：

|-------+---------+-----------+--------------------|
|设置方式|line-height|计算后的line-height|子元素继承的line-height|
|-|
|inherit|父元素的line-height值|不用计算|父元素的line-height值|
|length|20px|不用计算|20px|
|%|120%|自身font-size (16px) * 120% = 19.2px|继承父元素计算后的line-height值 19.2px，而不是120%|
|normal|1.2|自身font-size (16px) * 1.2 = 19.2px |继承1.2，line-height = 自身font-size(32px) * 1.2 = 38.4px|
|纯数字|1.5|自身font-size (16px) * 1.2 = 19.2px|继承1.5，line-height = 自身font-size(32px) * 1.5 = 48px|
|-------+---------+-----------+--------------------|

那么，哪一种是最好的方式呢？
一般来数，设置行高的值为 **`纯数字`** 是最推荐的方式，因为其会随着对应的 font-size 而缩放。

---

这是对`line-height`的一点总结，欢迎小伙伴们拍砖哈~


##参考
[MDN line-height][6]

[深入了解css的行高Line Height属性][7]

[CSS行高——line-height][8]


  [1]: http://segmentfault.com/a/1190000003007361
  [2]: {{ site.url }}/blog/blog_imgs/4.png
  [3]: {{ site.url }}/blog/blog_imgs/5.png
  [4]: {{ site.url }}/blog/blog_imgs/6.png
  [6]: https://developer.mozilla.org/zh-CN/docs/Web/CSS/line-height
  [7]: http://www.cnblogs.com/fengzheng126/archive/2012/05/18/2507632.html
  [8]: http://www.cnblogs.com/dolphinX/p/3236686.html
  [9]: http://www.cnblogs.com/leejersey/p/3662612.html
