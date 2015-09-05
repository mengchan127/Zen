---
layout: post
title: WebRTC简介及其与SIP互通
description:  WebRTC，名称源自网页实时通信（Web Real-Time Communication）的缩写，是一个支持网页浏览器进行实时语音对话或视频对话的技术，是谷歌2010年以6820万美元收购Global IP Solutions公司而获得的一项技术。
tags: 
  - webrtc
---

最近研究一下 `webrtc` ，看了几篇paper，之前也尝试运行验证了几个demo，现在把我的理解总结到这里。

##WebRTC

###简介

> WebRTC，名称源自网页实时通信（Web Real-Time Communication）的缩写，是一个支持网页浏览器进行实时语音对话或视频对话的技术，是谷歌2010年以6820万美元收购Global IP Solutions公司而获得的一项技术。

这是百度百科上的介绍，维基百科也差不多。对完全小白来讲，可能不是很理解这句话。

首先，**什么是实时通信？**
举个直白的例子，我们平时打电话就是实时通信。现在有很多实时通信的软件，比如 `丁丁`、`有信`……这是手机app。PC客户端像`Xlite`、`Linphone`等等。这些客户端接入网络，注册到相应的服务器上就可以进行音频通信了，支持视频的还能进行视频通信。拿`Xlite`来说，它的信令机制采用的是sip协议。`SIP`协议是`IMS`网络广泛使用的信令协议，已经很成熟。两个uesr 通过`Xlite`客户端注册到`sip server`（如 Asterisk）上，就可以互相拨打对方的号码音视频通信了，不过就`Xlite`来说，语音通话是免费的，但是视频的话，是要支付money软件才提供视频功能的……

其次，**为什么要提出WebRTC？**
一直以来，用户如果想通过互联网进行实时通信，就需要安装软件，要么就得在浏览器中安装插件。**WebRTC的宗旨是`不需用户安装任何插件`，直接使用`浏览器`就可以进行实时音视频通信。**就是如果WebRTC实现了，我们打开浏览器，输入网址，登陆进去，拨打号码，就可以互相音视频了。不再需要安软件，也不需要安装额外的浏览器插件。Web版QQ大家都用过吧，现在还只能发发消息发发表情，如果引入WebRTC，那音视频传文件都不在话下，现在QQ客户端有的功能，通过网页访问都能体验，估计到时候都不愿意再装体积越来越大的QQ客户端了吧。

最后，**需要知道的内容**

- WebRTC已经纳入HTML5标准
- 目前支持webrtc的浏览器有 `Chrome` `Firefox` `Opera`，`IE`不支持~
- WebRTC没有指定具体的信令协议，具体的信令协议留给应用程序实现。
- webRTC使用`JSEP`协议建立会话，什么是JSEP后面说
- WebRTC采用`ICE`实现NAT穿越
- WebRTC客户端之间可以进行点对点的媒体传输。

###JSEP

>JSEP（JavaScript Session Establishment Protocol，JavaScript会话建立协议）是一个信令API，允许开发者构建更强大的应用程序以及增加在信令协议选择上的灵活性。

建立会话最关键的就是媒体的协商，WebRTC虽然没有指定具体的信令协议，但是媒体协商采用了`SDP`协议。JSEP是干什么的呢，一方面提供接口如`createOffer()`供web应用程序调用生成`SDP`，另一方面提供`ICE`功能接口。这些功能都由浏览器实现，浏览器
WebRTC传输信令（`offer/answer`）采用`Websocket`。
需要说明的是，如果web应用程序不使用额外的信令协议，仅使用JSEP，两个WebRTC client *（同一个WebRTC client程序，两处登陆）* 之间也是可以建立链接的，即只要应用程序能解析用`WS`传递过来的`Offer/Answer`消息，提取出其中的`SDP`和`ICE`信息就可以了。

github上`codelab`demo 就是不用其他信令协议，直接使用JSEP生成offer/answer信令，然后采用ws协议传输实现的。

JSEP并不是信令协议，可以在JSEP的基础上引入SIP等信令协议，使WebRTC应用功能更加完备。

##WebRTC与SIP互通
要想让WebRTC与sip互通，要解决两个层面的问题：**信令层**和**媒体层**。
两个网络使用的信令机制不同，所以要进行信令的转换，才能完成媒体的协商，建立会话。媒体层要完成编码的转换，以及rtp/srtp转换等功能。这里主要说项信令层面的互通。

###信令互通方案
目前sip和webrtc信令上互通有两种解决方案：

- 用JavaScript实现sip协议栈，webrtc应用程序基于这个协议栈开发。这样webrtc client发出的信令就是sip信令，但一般采用websocket为信令传输协议。这样的webrtc client就可以直接注册到支持ws的sip server上了。
`jssip` 、`sipml5` 都是这种解决方案。
- 通过转换网关实现协议的转换，从而互通。一个开源的网关项目就是 `webrtc2sip`。
webrtc2sip是一个功能很完善的网关，既实现了信令层，也实现了媒体层，编码转换功能很强大，也可以直接当做媒体网关，用于编解码，沟通两端的媒体。