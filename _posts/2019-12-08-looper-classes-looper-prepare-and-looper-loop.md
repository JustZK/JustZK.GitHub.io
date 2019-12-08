---
layout: post
title: Looper类，Looper.prepare()和Looper.loop()
date: 2019-12-08
Author: Zhu Kun
tags: [Handler]
comments: true
---

# 工作线程：

在android应用程序中，我们创建的Activity、Service、Broadcast等都是在主线程（UI线程）处理的，但一些比较耗时的操作，如I/O读写的大文件读写，数据库操作以及网络下载需要很长时间，为了不阻塞用户界面，出现ANR的响应提示窗口，这个时候我们可以考虑创建一个工作线程（继承Thread类或者实现Runnable接口）来解决。

# 使用工作线程容易出现的问题：

对于Android平台来说UI控件都没有设计成为线程安全类型，所以需要引入一些同步的机制来使其刷新，否则使用工作线程更新UI会出现异常。

# Looper：

针对以上问题，android采用消息循环机制来处理线程间的通信，Android消息循环是针对线程的（每个线程都可以有自己的消息队列和消息循环），Android系统中Looper负责管理线程的消息队列和消息循环， 可以通过Loop.myLooper()得到当前线程的Looper对象，通过Loop.getMainLooper()可以获得当前进程的主线程的Looper对象。

# Looper对象是什么呢？

其实Android中每一个Thread都对应一个Looper，Looper可以帮助Thread维护一个消息队列，负责在多线程之间传递消息的一个循环器。一个线程可以存在（当然也可以不存在）一个消息队列和一个消息循环（Looper），但是创建的工作线程默认是没有消息循环和消息队列的，如果想让该线程具有消息队列和消息循环，需要在线程中首先调用Looper.prepare()来创建消息队列，然后调用Looper.loop()进入消息循环。

 

# Android官方文档中Looper的介绍：

Class used to run a message loop for a thread. Threads by default do not have a message loop associated with them; to create one, call prepare() in the thread that is to run the loop, and then loop() to have it process messages until the loop is stopped.

Most interaction with a message loop is through the Handler class.

This is a typical example of the implementation of a Looper thread, using the separation of prepare() and loop() to create an initial Handler to communicate with the Looper.

用于为线程运行消息循环的类。 默认情况下，线程没有与之关联的消息循环; 创建一个，在运行循环的线程中调用prepare()，然后循环()让它处理消息，直到循环停止。

大多数与消息循环的交互都是通过Handler类进行的。

这是一个典型的Looper线程实现示例，使用prepare()和loop()的分离来创建一个与Looper通信的初始Handler。

```java
class LooperThread extends Thread {
    public Handler mHandler;

    public void run() {
        Looper.prepare();

        mHandler = new Handler() {
            public void handleMessage(Message msg) {
                // process incoming messages here  
            }
        };

        Looper.loop();
    }
}
```

 Android中的Looper类，是用来封装消息循环和消息队列的一个类，用于在android线程中进行消息处理。handler其实可以看做是一个工具类，用来向消息队列中插入消息的。

#  (1) Looper类用来为一个线程开启一个消息循环。 

 **默认情况下android中新诞生的线程是没有开启消息循环的。**（主线程除外，主线程系统会自动为其创建Looper对象，开启消息循环。）

Looper对象通过MessageQueue来存放消息和事件。一个线程只能有一个Looper，对应一个MessageQueue。

#  (2) 通常是通过Handler对象来与Looper进行交互的。Handler可看做是Looper的一个接口，用来向指定的Looper发送消息及定义处理方法。

默认情况下Handler会与其被定义时所在线程的Looper绑定，比如，Handler在主线程中定义，那么它是与主线程的Looper绑定。

**mainHandler = new Handler() 等价于new Handler（Looper.myLooper()）.** 

Looper.myLooper()：获取当前进程的looper对象，类似的 Looper.getMainLooper() 用于获取主线程的Looper对象。 

#  (3) 在非主线程中直接new Handler() 会报如下的错误: 

E/AndroidRuntime( 6173): Uncaught handler: thread Thread-8 exiting due to uncaught exception 

E/AndroidRuntime( 6173): java.lang.RuntimeException: Can't create handler inside thread that has not called Looper.prepare() 

原因是**非主线程中默认没有创建Looper对象，需要先调用Looper.prepare()启用Looper。** 

#  (4) Looper.loop()： loop函数从MessageQueue中从前往后取出Message，然后通过Handler的dispatchMessage函数进行消息的处理（可见消息的处理是Handler负责的），消息处理完了以后通过Message对象的recycle函数放到Message Pool中，以便下次使用，通过Pool的处理提供了一定的内存管理从而加速消息对象的获取。

注意：Looper.loop()中是个while循环，只有对它所在线程的Looper调用了quit()函数，Looper.loop()函数才能完成，其后的代码才能得以运行。一个线程对应一个Looper，一个Looper对应一个消息队列MessageQueue。