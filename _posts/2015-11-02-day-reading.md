---
layout: post
show: true
title: Day-Reading 02
description: requestAnimationFrame、
tags:
  - day-reading
  - 前端
---

<h2>11.02</h2>

<h4><a href="http://www.cnblogs.com/2050/p/3871517.html" target="_blank">性能更好的js动画实现方式——requestAnimationFrame</a></h4>
- requestAnimationFrame 会把每一帧中的所有DOM操作集中起来，在一次重绘或回流中就完成，并且重绘或回流的时间间隔紧紧跟随浏览器的刷新频率，一般来说，这个频率为每秒60帧。
- 在隐藏或不可见的元素中，requestAnimationFrame将不会进行重绘或回流，这当然就意味着更少的的cpu，gpu和内存使用量。

<h4><a href="http://www.cnblogs.com/Wayou/p/requestAnimationFrame.html" target="_blank">equestAnimationFrame, Web中写动画的另一种选择</a></h4>

---