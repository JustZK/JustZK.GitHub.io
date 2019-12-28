---
layout: post
title: Service的两种启动方式？区别在哪？
date: 2019-12-22
Author: Zhu Kun
tags: [Service]
toc: true
comments: true
---

Service的两种启动模式

- **startService()**：通过这种方式调用startService，onCreate()只会被调用一次，多次调用startSercie会多次执行onStartCommand()和onStart()方法。如果外部没有调用stopService()或stopSelf()方法，service会一直运行。

- **bindService()**：如果该服务之前**还没创建**，系统回调顺序为onCreate()→onBind()。如果调用bindService()方法前服务**已经被绑定**，多次调用bindService()方法不会多次创建服务及绑定。如果调用者希望与正在绑定的服务**解除绑定**，可以调用unbindService()方法，回调顺序为onUnbind()→onDestroy()；

  ![](http://justzk.github.io/images/two-ways-to-start-service/two-ways-to-start-service-1.png)

**推荐文章： [Android Service两种启动方式详解](https://link.juejin.im/?target=https%3A%2F%2Fwww.jianshu.com%2Fp%2F4c798c91a613)**

