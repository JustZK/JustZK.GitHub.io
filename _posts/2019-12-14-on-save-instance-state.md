---
layout: post
title: onSaveInstanceState()方法的作用 ? 何时会被调用？
date: 2019-12-14
Author: Zhu Kun
tags: [Activity]
toc: true
comments: true
---

发生条件：异常情况下（**系统配置发生改变时导致Activity被杀死并重新创建、资源内存不足导致低优先级的Activity被杀死**） 

- 系统会调用onSaveInstanceState来保存当前Activity的状态，此方法调用在onStop之前，与onPause没有既定的时序关系；
- 当Activity被重建后，系统会调用onRestoreInstanceState，并且把onSave(简称)方法所保存的Bundle对象**同时传参**给onRestore(简称)和onCreate()，因此可以通过这两个方法判断Activity**是否被重建**，调用在onStart之后；

![](https://justzk.github.io/images/on-save-instance-state/on-save-instance-state-1.png)