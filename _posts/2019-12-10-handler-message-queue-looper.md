---
layout: post
title: Handler、MessageQueue 和 Looper
date: 2019-12-10
Author: Zhu Kun
tags: [Handler]
toc: true
comments: true
---

## 1、Handler、MessageQueue 和 Looper 之间的关系

首先是 Handler、MessageQueue 和 Looper 之间的关系。我们用下面的这个图来表示：

![](https://justzk.github.io/images/handler-message-queue-looper/handler-message-queue-looper-1.png)

也就是说，一个线程中可以定义多个 Handler 实例，但是每个 Handler 实际上引用的是同一个 Looper。

当然，我们要在创建 Handler 之前先创建 Looper。

而每个 Looper 又只对应一个 MessageQueue。

该 MessageQueue 会在创建 Looper 的时候被创建。

在 MessageQueue 中使用 Message 对象来拼接一个单向的链表结构，依次来构成一个消息队列。每个 Message 是链表的一个结点，封装了我们发送的信息。

 

## 2、Handler 的消息发送过程

然后，我们再来分析下 Handler 中的消息是如何被发送的。同样，我们使用一个图来进行分析：

![](https://justzk.github.io/images/handler-message-queue-looper/handler-message-queue-looper-2.png)

根据上文的内容我们将 Handler 发送消息的方法分成 **post** 和 **send** 两种类型。

post 的用来发送 **Runnable** 类型的数据，

send 类型的用来发送 **Message** 类型的数据。

但不论哪种类型最终都会调用 Handler 的 **sendMessageAtTime()**方法来加入到 MessageQueue 的队列中。

区别在于，post 类型的方法需要经过 Handler 的 **getPostMessage()**包装成 Message 之后再发送。

 

# 3、Looper 的执行过程

当消息被添加到队列之后需要执行消息，这部分内容在 Looper 的 **loop()** 方法中。但是这部分内容稍显复杂，因为涉及 Native 层的一些东西。我们这里仍然使用图来进行描述：

![](https://justzk.github.io/images/handler-message-queue-looper/handler-message-queue-looper-3.png)

当我们调用 Looper 的 loop() 方法之后整个 Looper 循环就开始不断地处理消息了。在上图中就是我们用**绿色标记的一个循环**。当我们在循环中调用 MessageQueue 的 next() 方法来获取下一个消息的时候，会调用 **nativePollOnce()** 方法，该方法可能会造成**线程阻塞和非阻塞**，

1、当线程为非阻塞的时候就会从 Native 层回到 Java 层，从 MessageQueuue 中取得一个消息之后给 Looper 进行处理。

2、如果获取的时候造成线程阻塞，那么有两种情况会唤醒阻塞的线程：

- 一个是当一个新的消息被加入到队列中，并且将会早于之前队列的所有消息被触发，那么此时将会重新设置超时时间。
- 如果达到了超时时间同样可以从睡眠状态中返回，也就回到了     Java 层继续处理。所以，Native 层的 Looper 的作用就是通过阻塞消息队列获取消息的过程阻塞 Looper。

（详解：https://juejin.im/post/5bdec872e51d4551ee2761cb#heading-3）