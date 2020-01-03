---
layout: post
title: Broadcast Receiver基础
date: 2020-01-03
Author: Zhu Kun
tags: [Broadcast Receiver]
toc: true
comments: true
---

## 广播有几种形式     ? 都有什么特点 ？

- 普通广播：开发者自身定义     intent的广播（最常用），所有的广播接收器几乎会在同一时刻接受到此广播信息，**接受的先后顺序随机**；
- 有序广播：发送出去的广播被广播接收者**按照先后顺序接收**，同一时刻只会有一个广播接收器能够收到这条广播消息，当这个广播接收器中的逻辑执行完毕后，广播才会继续传递，且优先级（priority）高的广播接收器会先收到广播消息。有序广播可以被接收器截断使得后面的接收器无法收到它；
- 本地广播：仅在自己的应用内发送接收广播，也就是只有自己的应用能收到，数据更加安全，效率更高，但只能采用**动态注册**的方式；
- 粘性广播：这种广播会**一直滞留**，当有匹配该广播的接收器被注册后，该接收器就会收到此条广播；

**推荐文章**： [Android四大组件：BroadcastReceiver史上最全面解析](https://link.juejin.im?target=https%3A%2F%2Fwww.jianshu.com%2Fp%2Fca3d87a4cdf3)

## 广播的两种注册方式     ？

![](http://justzk.github.io/images/broadcast-receiver-basics/broadcast-receiver-basics-1.png)

## 广播发送和接收的原理了解吗     ？（Binder机制、AMS）

![](http://justzk.github.io/images/broadcast-receiver-basics/broadcast-receiver-basics-2.png)

**推荐文章**： [广播的底层实现原理](https://link.juejin.im/?target=https%3A%2F%2Fwww.jianshu.com%2Fp%2F02085150339c)