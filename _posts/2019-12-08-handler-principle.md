---
layout: post
title: Handler原理
date: 2019-12-08
Author: Zhu Kun
tags: [Handler]
toc: true
comments: true
---

**Handler**，**Message**，**looper** 和 **MessageQueue** 构成了安卓的消息机制，

handler创建后可以通过 sendMessage 将消息加入消息队列，

然后 looper不断的将消息从 MessageQueue 中取出来，

回调到 Hander 的 handleMessage方法，从而实现线程的通信。

# 从两种情况来说：

**第一：**在UI线程创建Handler,此时我们不需要手动开启looper，因为在应用启动时，在ActivityThread的main方法中就创建了一个当前主线程的looper，并开启了消息队列，消息队列是一个无限循环，为什么无限循环不会ANR？因为可以说，应用的整个生命周期就是运行在这个消息循环中的，安卓是由事件驱动的，Looper.loop不断的接收处理事件，每一个点击触摸或者Activity每一个生命周期都是在Looper.loop的控制之下的，looper.loop一旦结束，应用程序的生命周期也就结束了。

我们可以想想什么情况下会发生ANR：

1、事件没有得到处理，

2、事件正在处理，但是没有及时完成，而对事件进行处理的就是looper，所以只能说事件的处理如果阻塞会导致ANR，而不能说looper的无限循环会ANR。

 

**第二：**另一种情况就是在子线程创建Handler，此时由于这个线程中没有默认开启的消息队列，所以我们需要手动调用looper.prepare(),并通过looper.loop开启消息

 主线程Looper从消息队列读取消息，当读完所有消息时，主线程阻塞。子线程往消息队列发送消息，并且往管道文件写数据，主线程即被唤醒，从管道文件读取数据，主线程被唤醒只是为了读取消息，当消息读取完毕，再次睡眠。因此loop的循环并不会对CPU性能有过多的消耗。