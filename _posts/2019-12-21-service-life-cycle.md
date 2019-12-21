---
layout: post
title: Service的生命周期
date: 2019-12-21
Author: Zhu Kun
tags: [Service]
toc: true
comments: true
---

- Service的生命周期涉及到六大方法     

- - **onCreate()**：如果service没被创建过，调用startService()后会执行onCreate()回调；如果service已处于运行中，调用startService()不会执行onCreate()方法。也就是说，onCreate()只会在第一次创建service时候调用，多次执行startService()不会重复调用onCreate()，此方法适合完成一些初始化工作；
  - **onStartComand()**：服务启动时调用，此方法适合完成一些数据加载工作，比如会在此处创建一个线程用于下载数据或播放音乐；
  - **onBind()**：服务被绑定时调用；
  - **onUnBind()**：服务被解绑时调用；
  - **onDestroy()**：服务停止时调用；

- 推荐文章： [Android组件系列----Android Service组件深入解析](https://link.juejin.im?target=https%3A%2F%2Fwww.cnblogs.com%2Fsmyhvae%2Fp%2F4070518.html)

