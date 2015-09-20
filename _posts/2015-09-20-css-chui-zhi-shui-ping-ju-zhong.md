---
layout: post
title:  【转载】解读CSS布局之-水平垂直居中
description: 
		对一个元素水平垂直居中，在我们的工作中是会经常遇到的，也是CSS布局中很重要的一部分，本文就来讲讲CSS水平垂直居中的一些方法。由于我们大搜车的日常工作中已经不再需要理会低版本IE，所以本文所贴出的方法，是没有去考虑IE的……
tags: 
  - css
  - 前端
  - 技术日记
  - 转载
show: true
---

一直想写篇关于“CSS水平垂直居中”的总结，今天看到**大搜车前端团队**的这篇文章，研读一番，果断转载。这篇文章根据CSS布局模块分类，针对不同的布局模块给出水平垂直居中的解决方案。 <a href="http://f2e.souche.com/blog/jie-du-cssbu-ju-zhi-shui-ping-chui-zhi-ju-zhong/" target="_blank">原文地址~</a>

---

对一个元素水平垂直居中，在我们的工作中是会经常遇到的，也是CSS布局中很重要的一部分，本文就来讲讲CSS水平垂直居中的一些方法。由于我们大搜车的日常工作中已经不再需要理会低版本IE，所以本文所贴出的方法，是没有去考虑IE的，如果有兼容需要，可以参见这篇文章：<a href="http://www.cnblogs.com/Dudy/p/4085292.html" target="_blank">http://www.cnblogs.com/Dudy/p/4085292.html</a> 。另外，文中的css都是用less书写的，如果看不懂less，可以把我给的demo链接打开，然后在控制台中查看最终的css，或者是点击codepen上的“View Compiled”按钮，可以查看编译后的css.

先看一张图，这是去年cssConf大会时阿里的 @寒冬winter 老师放出来的：

![图片描述][1]

如图所示，CSS布局是可以分为几大块的：

- 盒子内部的布局
    - 文本的布局
    - 盒模型本身的布局

- 盒子之间的布局visual formatting
	- 脱离正常流normal flow的盒子的布局
		- absolute布局上下文下的布局
		- float布局上下文下的布局
	- 正常流normal flow下的盒子的布局
		- BFC布局上下文下的布局
		- IFC布局上下文下的布局
		- FFC布局上下文下的布局
		- table布局上下文下的布局
		- css grid布局上下文下的布局

所有的CSS布局其实都是围绕着这些布局模块来的，水平垂直居中也一样。

---

##一. 文本的水平垂直居中

###line-height + text-align:center

<a href="http://codepen.io/Dudy/pen/aOKWWO?editors=110" target="_blank">Demo链接</a>

代码：

~~~
<div class='wrap'>  
  水平垂直居中水平垂直居中
</div> 
~~~

~~~
html,body{  
  margin: 0;
}

.wrap{
  line-height: 400px;
  text-align:center;

  height: 400px;
  font-size: 36px;
  background-color: #ccc;
}
~~~

这种方法只适合单行文字的水平垂直居中

##二. 利用盒模型的水平垂直居中

我们一般讲的盒模型都是说的块级盒的盒模型，也只有块级盒的盒模型用得多一点，块级盒block-level box又是分别由content-box、padding-box、border-box、margin-box组成的，如下图：

![图片描述][2]

也就说我任一个子盒子的水平和垂直方向的边与最外面盒子的间距都是可以控制的，因此也就有如下居中方法：

###padding填充

<a href="http://codepen.io/Dudy/pen/EjRvgp?editors=110" target="_blank">Demo链接</a>

代码：

~~~
<div class="wrap">  
  <div class="content"></div>
</div>
~~~

~~~
@wrapWidth : 400px;

.wrap{
  margin-left: auto;
  margin-right: auto;
  margin-top: 20px;
  width: @wrapWidth;
  height: @wrapWidth;
  background-color: #ccc;
}

.content{
  @contentWidth : 100px;
  width: @contentWidth;
  height: @contentWidth;
  padding: (@wrapWidth - @contentWidth) / 2;
  background-color: #333;
  background-clip:content-box;
}
~~~

也可以用css3的`calc()`动态计算:

<a href="http://codepen.io/Dudy/pen/RPJZVw?editors=110" target="_blank">Demo链接</a>

~~~
<div class="wrap">  
  <div class="content"></div>
</div>  
~~~

~~~
.wrap{
  margin-top: 20px;
  margin-left: auto;
  margin-right: auto;
  width: 400px;
  height: 400px;
  background-color: #ccc;
  .content{
    padding: -webkit-calc(~"(100% - 100px) / 2");
    padding: calc(~"(100% - 100px) / 2");
    width: 100px;
    height: 100px;
    background-color: #333;
    background-clip: content-box;
  }
}
~~~

注意这里我在calc中使用了一个"~"的写法，这是less中的一个语法，告诉less这里不被less所编译，要是被less编译了的话，css的calc函数的参数就不是100% - 100px，而是0%了。

###margin填充

<a href="http://codepen.io/Dudy/pen/jPKxYL?editors=110" target="_blank">Demo链接</a>

代码：

~~~
<div class="wrap">  
  <div class="ele"></div>
</div>  
~~~

~~~
.wrap{
  @wrapHeight : 400px;
  @contenHeight : 100px;
  overflow: hidden;
  width: 100%;
  height: @wrapHeight;
  background-color: #ccc;
  .ele{
    margin-left: auto;
    margin-right: auto;
    margin-top: (@wrapHeight - @contenHeight) / 2;
    width: 100px;
    height: @contenHeight;
    background-color: #333;
    color: #fff;
  }
}
~~~

使用margin填充我们需要知道元素的宽度，这点不太灵活，不过CSS3搞出了一个加fit-content的属性值，可以动态计算元素的宽度，<a href="http://codepen.io/Dudy/pen/yNEZVQ" target="_blank">Demo链接</a>

使用盒模型进行布局不会产生reflow，兼容也好，使用盒模型布局是一种布局思想，其实仅仅靠它就能实现很多visual formatting才能实现的布局，这是另一个话题，这里不展开。


##三. absolute布局上下文下的水平垂直居中

###50% + -50%

原理很简单，就是利用left：50%将盒子的左边先置于父容器的中点，然后再将盒子往左偏移盒子自身宽度的50%，这里有三种具体实现：

<a href="http://codepen.io/Dudy/pen/VLdzRv?editors=110" target="_blank">Demo链接</a>

~~~
<div class="wrap">  
  <div class="ele margin">水平垂直居中水平垂直<br>居中水平垂直居中水平<br>垂直居中水平垂直居<br>中水平垂直居中</div>
</div>

<div class="wrap">  
  <div class="ele translate">水平垂直居中水平垂直<br>居中水平垂直居中水平<br>垂直居中水平垂直居<br>中水平垂直居中</div>
</div>

<div class="wrap">  
  <div class="ele relative">
    <div class="ele-inner">水平垂直居中水平垂直<br>居中水平垂直居中水平<br>垂直居中水平垂直居<br>中水平垂直居中</div>
  </div>
</div>  
~~~

~~~
.wrap{
  position: relative;
  width: 100%;
  height: 200px;
  border:1px solid;
  background-color: #ccc;
  .ele{
    position: absolute;
    left: 50%;
    top: 50%;
    background-color: #333;
    &.margin{
      width: 160px;
      height: 100px;
      margin-left: -80px;
      margin-top: -50px;
    }
    &.translate{
      -webkit-transform:translate3d(-50%, -50%, 0);
      transform:translate3d(-50%, -50%, 0);
    }
    .ele-inner{
      position: relative;
      left: -50%;
      top: -50%;
      width: 100%;
      height: 100%;
      background-color: #333;
    }
    &.relative{
      width: 150px;
      height: 100px;
      background-color: transparent;
    }
  }
}
~~~

上面三个方法中，margin方法和relative方法都需要知道元素的宽高才行(relative方法只知道高也行)，适用于固定式布局，而transform方法则可以不知道元素宽高

###text-align:center + absolute

text-aign:center本来是不能直接作用于absolute元素的，但是没有给其left等值的行级absolute元素是会受文本的影响的，可以参考张老师的 <a href="http://www.zhangxinxu.com/wordpress/2011/12/position-absolute-text-align-center/" target="_blank">这篇文章</a>

<a href="http://codepen.io/Dudy/pen/BNVwJx?editors=110" target="_blank">Demo链接</a>

代码：

~~~
<div class="wrap">  
  <div class="ele"></div>
</div>  
~~~

~~~
.wrap{
  text-align: center;

  width: 100%;
  height: 400px;
  background-color: #ccc;
  font-size: 0;
}
.ele{
  position: absolute;
  margin-left: -(100px / 2);
  margin-top: (400px - 100px) / 2;

  width: 100px;
  height: 100px;
  display: inline-block;
  background-color: #333;
}
~~~

简单解释下，首先，text-align:center作用的是文本而不是absolute的元素，但是，当absolute元素为inline-block的时候，它会受到文本的影响，然后你可能会问这里没文本啊，我只能告诉你说这下面是有的，是个匿名的文本节点。具体的这里不展开，可以参考标准，然后理解这句话:

>If the inline box contains no glyphs at all, it is considered to contain a strut (an invisible glyph of zero width) with the A and D of the element's first available font

然后这个匿名文本由于受到text-align:center影响居中了，这个时候absolute盒子的左边跟父容器的中点对齐了，所以就还需要往回拉50%，这里用的是margin-left，你也可以用其它方式拉。然后就是垂直方向的对齐，垂直方向是不能被操作文本的属性影响的，所以我这里用的是margin-top来让它偏移下去。

###absolute + margin : auto

<a href="http://codepen.io/Dudy/pen/mJKqXa?editors=110" target="_blank">Demo链接</a>

代码：

~~~
<div class="wrap">  
  <div class="ele"></div>
</div>
~~~

~~~
html,body{  
  width: 100%;
  height: 100%;
  margin: 0;
}
.wrap{
  position: relative;
  width: 100%;
  height: 100%;
  background-color: #ccc;
  .ele{
    position: absolute;
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    margin: auto;
    width: 100px;
    height: 100px;
    background-color: #333;
  }
}
~~~

关于这种布局的原理，在标准中能找到如下解释：

<a href="http://www.w3.org/TR/CSS21/visudet.html#abs-non-replaced-width" target="_blank">w3c.org</a>中有这样一句话：

>The constraint that determines the used values for these elements is: 'left' + 'margin-left' + 'border-left-width' + 'padding-left' + 'width' + 'padding-right' + 'border-right-width' + 'margin-right' + 'right' = width of containing block

这句话说的是absolute性质的盒子，它的包含块的宽度等于它的盒模型的宽度 + left + right值，包含块的高度同理，盒模型包括margin-box、border-box、padding-box、content-box，而在这个居中方法中，.ele的left + right值是0，width是定值，width所在盒子包括了除了margin-box外的那三个box，margin都是auto值，按照上面那个公式，margin-left + margin-right的值应该等于包含块的宽度 - left的值 - right的值 - width的值，也就是说margin-left + margin-right的值等于除了width所占宽度外的剩下宽度，拥有剩下宽度后，就是平分其宽度，以让左右两边相等，达到居中，标准中给出了答案：


>If none of the three is 'auto': If both 'margin-left' and 'margin-right' are 'auto', solve the equation under the extra constraint that the two margins get equal values, unless this would make them negative, in which case when direction of the containing block is 'ltr' ('rtl'), set 'margin-left' ('margin-right') to zero and solve for 'margin-right' ('margin-left')

这里的"three"指的是left, width, right。如果left、right和width都不为auto，同时margin-left和margin-right都是auto，除非特别情况，它们俩就是相等的，而这个例子中不在特殊情况之列，因此两者平分，此时达到了水平居中。而对于垂直方向的margin的auto值的计算，标准中也有如下两句话，跟水平方向的同理(这里的“three”指的是“top, height, bottom”)：

>the used values of the vertical dimensions must satisfy this constraint: 'top' + 'margin-top' + 'border-top-width' + 'padding-top' + 'height' + 'padding-bottom' + 'border-bottom-width' + 'margin-bottom' + 'bottom' = height of containing block

>if none of the three are 'auto': If both 'margin-top' and 'margin-bottom' are 'auto', solve the equation under the extra constraint that the two margins get equal values.

垂直方向也就因此也居中了。

这种方法能简单的做到居中，但是必须有width和height值

###适用于图片居中的网易nec的一个方法

<a href="http://codepen.io/Dudy/pen/GJGzJr?editors=110" target="_blank">Demo链接</a>

代码：

~~~
<div class="wrap">  
  <p>
    <img src="http://nec.netease.com/img/s/1.jpg" alt="" />
    <img src="http://nec.netease.com/img/s/1.jpg" alt="" />
  </p>  
</div>  
~~~

~~~
html,body{  
  width: 100%;
  height: 100%;
  margin: 0;
}

.wrap{
  position:relative;
  width: 100%;
  height: 100%;
  p{
    position:absolute;
    left:50%;
    top:50%;
  }
  img{
    &:nth-child(1){
      position:static;
      visibility:hidden;
    }
    &:nth-child(2){
      position:absolute;
      right:50%;
      bottom:50%;
    }
  }
}
~~~

这种方法主要是利用了一个图片进行占位，以让父容器获得高宽，从而让进行-50%偏移的图片能有一个参照容器作百分比计算。优点是可以不知道图片的大小，随便放张尺寸不超过父容器的图片上去都能做到居中。另外，兼容性好，如果是不使用nth-child选择器的花，IE6都是能顺利兼容的

##四. float布局上下文下的水平垂直居中

###float + -50%

<a href="http://codepen.io/Dudy/pen/xGzjZa?editors=110" taregt="_blank">Demo链接</a>

代码：

~~~
<div class="wrap">  
  <div class="ele">
    <div class="ele-inner">居中居中居中居中居中居中<br>居中居中居中居中居中居中居中居中居<br>中居中居中居中居中居中居中居中居中居<br>中居中居中居中居中居中居中</div>
  </div>
</div>  
~~~

~~~
.wrap{
  float: left;
  width: 100%;
  height: 400px;
  background-color: #ccc;
  .ele{
    float: left;
    position: relative;
    left: 50%;
    top: 50%;
  }
  .ele-inner{
    position: relative;
    left: -50%;
    -webkit-transform : translate3d(0, -50%, 0);
    transform : translate3d(0, -50%, 0);
    background-color: #333;
    color: #fff;
  }
}
~~~

这种方法的原理，首先是利用float属性将需要居中的元素的父元素.ele的宽度收缩，然后left:50%将.ele的左边和水平中线对齐，这个时候还没居中，还需要将其往回拉自身宽度的50%，于是.ele-inner便是真正需要水平居中的元素，我给它一个position:relative，将其往回拉自身宽度50%就行了。对于垂直方向，依然是先将.ele top:50%到垂直方向中点，但是这时给.ele-inner top:50%是不起作用的，因为如果没给父元素明确高度的话，这个50%是计算不出来的，因此，就有了transform : translate3d(0, -50%, 0)。

这种方法的好处是元素可以不定宽，任何时候都可以做到居中

我当时在w3cplus的站上发现这个方法后，当时觉得这个方法很好，兼容性好，又还可以不定宽，但当我用了一段时间后，发现了一个问题：

就是当居中元素的父元素left:50%时，如果元素宽度足够大，会超出外面的容器，而如果外面的容器又正好是overflow:auto的话，那就会在外面产生滚动条，问题DEMO链接在这里，后来我找到了一个办法：<a href="http://codepen.io/Dudy/pen/YPWeYY?editors=110" target="_blank">DEMO链接</a> ，基本思想就是利用元素超出父元素的左边不会产生滚动条的特性，有点奇淫技巧，但是能解决问题，有兴趣的可以看看

###margin-bottom : -50%

<a href="http://codepen.io/Dudy/pen/bdKMrB?editors=110" target="_blank">Demo链接</a>

代码：

~~~
<div class="wrap">  
  <div class="placeholder"></div>
  <div class='content'></div>
</div>  
~~~

~~~
.wrap{
  float: left;
  width: 100%;
  height: 400px;
  background-color: #ccc;
  @contentHeight : 100px;
  .placeholder{
    float: left;
    width: 100%;
    height: 50%;
    /*居中元素.content高度一半*/
    margin-bottom: -(@contentHeight / 2);
  }
  .content{
    position: relative;
    left: 50%;
    transform:translate3d(-50%, 0, 0);
    clear: both;
    /*演示用，实际不需要定宽*/
    max-width: 100px;
    height: @contentHeight;
    background-color: #333;
  }
}
~~~

这种方法是先让占位元素.placeholder占据50%高度，然后给一个居中元素高度一半的负的margin-bottom，然后下面的元素只要跟着摆放就能垂直居中了。水平方向就是利用translate做偏移，这个没什么好说的，你也可以换成其他办法。

这种方法就是各种固定死，首先最外层的父容器需要一个固定高度，以让.placeholder的height:50%有效，然后，margin-bottom也需要固定死，而且得需要知道居中元素高度。单纯就水平方向来说，这个方法比较适合需要兼容低版本IE的固定式布局的项目，因为兼容性好。


##五.BFC布局上下文下的水平垂直居中

BFC的全称是块级排版上下文，这里有篇文章对齐进行了简单的介绍，BFC布局上下文下的布局其实就是利用盒模型本身进行的布局，前面在利用盒模型布局的那一节中已经讲过了，这里就不重复了

##六.IFC布局上下文下的水平垂直居中

IFC又是个什么概念呢，你可以看看<a href="http://www.w3.org/TR/CSS21/visuren.html#inline-formatting" target="_blank">官方文档</a>，也可以简单的理解为 display为inline性质的行级元素的布局。

###text-align:center + vertical-align:middle

<a href="http://codepen.io/Dudy/pen/pJKVZa?editors=110" target="_blank">demo链接</a>

代码：

~~~
<div class="wrap">  
  <div class='placeholder'><!--占位元素，用来作为居中元素的参照物--></div>
  <div class="ele"></div>
</div>  
~~~

~~~
.wrap{
  width: 100%;
  height: 400px;
  /* min-height: 400px; */
  text-align:center;
  font-size: 0;
  background-color: #ccc;
  .placeholder,
  .ele{
    vertical-align: middle;
    display: inline-block;
  }
  .placeholder{
    overflow: hidden;
    width: 0;
    min-height: inherit;
    height: inherit;
  }
  .ele{
    width: 100px;
    height: 100px;
    background-color: #333;
  }
}
~~~

行级元素会受到text-align和vertical-align的影响，关于vertical-align，不太好理解，我多贴几篇文章：<a href="http://www.html-js.com/article/2952" target="_blank">@灵感idea</a>的，<a href="http://www.zhangxinxu.com/wordpress/2010/05/我对css-vertical-align的一些理解与认识（一）/" target="_blank">张鑫旭</a>的，<a href="https://developer.mozilla.org/en-US/docs/Web/CSS/vertical-align" target="_blank">MDN上</a>的，<a href="https://css-tricks.com/almanac/properties/v/vertical-align/" target="_blank">css-trick</a>上的，以及<a href="http://www.w3.org/TR/CSS21/visudet.html#line-height" target="_blank">官方文档</a>，这里首先是用text-center让inline-block水平居中，然后给一个vertical-align:middle，但是仅仅给vertical-align:middle是不够的，因为此时它还没有vertical-align对齐的参照物，所以就给了一个占位的inline-block，它的高度是100%。


这个方法对于居中元素不需要定宽高，而且元素根据vertical-align值的不同不仅仅可以实现居中，还可以将其放在上面下面等。缺点是父元素需定高

###text-align:center + line-height

<a href="http://codepen.io/Dudy/pen/ZGRmqL?editors=110" target="_blank">Demo链接</a>

代码：

~~~
<div class="wrap">  
  <div class="ele">居中居中居中居中居中居中<br>居中居中居中居中居中居中居中<br>居中居中居中居中居中居中居中居中<br>居中居中居中居中居中居中居中居中</div>
</div>  
~~~

~~~
.wrap{
  text-align: center;
  line-height: 400px;

  width: 100%;
  height: 400px;
  background-color: #ccc;
  font-size: 0;
  .ele{
    line-height: normal;
    vertical-align: middle;
    display: inline-block;
    background-color: #333;
    font-size: 18px;
    color: #fff;
  }
}
~~~

这个方法，首先是水平方向，text-align:center就行了，至于垂直方向，起作用的就是父容器的一个line-height和居中元素的vertical-align:middle，为什么这两个属性可以让一个inline-block垂直居中呢，这里重点是父容器在其下面产生了一个隐匿的文本节点，这个我在上面 text-align:center + absolute 那个方法的讲解中说到过了，然后这个这个隐匿文本节点会因line-height属性的作用而拥有了一个父容器一样高的行高，此时元素有了一个vertical-align对齐的参照物，再给其vertical-align:middle值就能垂直对齐了。

使用这个方法，居中元素无需定宽高，但缺点是得给父容器一个固定的行高才行。

###text-align:center + font-size

<a href="http://codepen.io/Dudy/pen/vOrvBP?editors=110" target="_blank">Demo链接</a>

代码：

~~~
<div class="wrap">  
  <div class="ele"></div>
</div> 
~~~

~~~
.wrap{
  text-align: center;
  font-size: 400px * 0.873;/*约为高度的0.873*/

  margin-left: auto;
  margin-right: auto;
  width: 400px;
  height: 400px;
  background-color: #ccc;
  .ele{
    vertical-align: middle;

    width: 100px;
    height: 100px;
    display: inline-block;
    background-color: #333;
  }
}
~~~

这个方法来自淘宝，基本原理还是让隐匿文本节点所占据的行高等于父容器的高度，然后给居中元素一个vertical-align:middle对齐的一个参照。只是这里把定义line-height值换成了定义font-size值，让font-siz足够大从而让其行高等于父容器高度。为了证明这个font-size的作用，我把居中元素换成文本

<a href="http://codepen.io/Dudy/pen/JdZwGa?editors=110" target="_blank">Demo链接</a>

代码：

~~~
<div class="wrap">  
  a
</div> 
~~~

~~~
.wrap{
  text-align: center;
  font-size: 400px * 0.873;/*约为高度的0.873*/

  margin-left: auto;
  margin-right: auto;
  width: 400px;
  height: 400px;
  background-color: #ccc;
}
~~~

效果：

![图片描述][3]

可以看到字母a垂直居中了，这个字母a就对应那个隐匿文本节点

##七.FFC布局上下文下的水平垂直居中

###父元素、子元素都定义flex：

<a href="http://codepen.io/Dudy/pen/PqaXOZ?editors=110">DEMO链接</a>

代码：

~~~
<div class="wrap">  
  <div class="ele">
  居中居中居中居中居中居中居中<br>
    居中居中居中居中居中居中居中<br>
    居中居中居中居中居中居中居中<br>
    居中居中居中居中居中居中居中<br>
    居中居中居中居中居中居中居中
  </div>
</div>  
~~~

~~~
html,body{  
  width: 100%;
  height: 100%;
}

.wrap{
  display: flex;
 align-items: center;
  justify-content: center;
  width: 100%;
  height: 100%;
  background-color: #ccc;
  .ele{
    background-color: #333;
  }
}
~~~

###只有父元素定义flex，子元素定义margin:auto：

<a href="http://codepen.io/Dudy/pen/zGLzRN?editors=110">DEMO链接</a>

代码：

~~~
<div class="wrap">  
  <div class="ele">
  居中居中居中居中居中居中居中<br>
    居中居中居中居中居中居中居中<br>
    居中居中居中居中居中居中居中<br>
    居中居中居中居中居中居中居中<br>
    居中居中居中居中居中居中居中
  </div>
</div>  
~~~

~~~
html,body{  
  width: 100%;
  height: 100%;
}

.wrap{
  display: flex;
  width: 100%;
  height: 100%;
  background-color: #ccc;
  .ele{
    margin:auto;
    background-color: #333;
  }
}
~~~

margin:auto在这里垂直方向也生效了

flexbox是个很强大的布局模块，也就三个属性就搞定居中了，而且不论父容器还是居中元素都可以不定宽高。参考链接：<a href="http://www.w3cplus.com/css3/a-visual-guide-to-css3-flexbox-properties.html">图解CSS3 Flexbox属性</a>

##八.table布局上下文下的水平垂直居中

<a href="http://codepen.io/Dudy/pen/EjRGRO?editors=110">DEMO链接</a>

代码：

~~~
<div class='wrap'>  
    <div class='ele'>
      <div class="ele-inner">居中居中居中居中居中居中居中居中<br>居中居中居中居中居中居中居中居中<br>居中居中居中居中居中居中居中居中居中居中</div>
    </div>
</div>  
~~~

~~~
.wrap{
  width: 100%;
  height: 300px;
  display: table;
  background-color: #ccc;
}
.ele{
  text-align:center;
  vertical-align: middle;
  display:table-cell;
}                

.ele-inner{
  display: inline-block;
  background-color: #333;
}
~~~

原理就是把div模拟成表格（换成真正的表格标签也是可以的），然后给那几个属性就成了，这个没什么好讲的，不懂的去翻翻手册就明白了，然后@于江水写的一篇<a href="http://yujiangshui.com/about-table/">table那些事</a>还不错

##九.CSS grid布局上下文下的水平垂直居中

CSS3 grid layout是IE搞出来的一个布局模块，目前貌似还只有IE0和IE11支持，我没有研究过其居中的方法，有兴趣的可以看看<a href="http://www.w3cplus.com/blog/tags/356.html">大漠老师的介绍文章</a>

##十.其它未知归属的水平垂直居中方法

###使用button标签

<a href="http://codepen.io/Dudy/pen/aOKPgr?editors=110">DEMO链接</a>

代码：

~~~
<button>  
  <div>
    居中居中居中居中居中居中<br>
    居中居中居中居中居中居中<br>
    居中居中居中居中居中居中<br>
    居中居中居中居中居中居中<br>
  </div>  
</button>  
~~~

~~~
button{  
  width: 100%;
  height: 400px;
  background-color: #cccccc;
  border-width:0;
  &:focus{
    outline:none;
  }
  div{
    display: inline-block;
    font-size: 18px;
    background-color: #333;
    color: #fff;
  }
}
~~~

这种方法属于奇淫技巧，利用button标签天生外挂的这一技能对其里面的元素进行居中。

**（本文完）** &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;  by**<a href="http://f2e.souche.com/blog/author/yebo/" target="_blank">覃业博</a>**




[1]: {{ site.url }}/blog/blog_imgs/11.png
[2]: {{ site.url }}/blog/blog_imgs/12.png
[3]: {{ site.url }}/blog/blog_imgs/13.png