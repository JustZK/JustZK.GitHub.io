---
layout: post
title: Handler.removeCallbacksAndMessages(null)的作用
date: 2019-12-11
Author: Zhu Kun
tags: [Handler]
toc: true
comments: true
---

在Android开发中会大量使用到Handler。

但是，由于与activity生命周期不一致，当一个Activity被销毁时，如果Handler对象有Message在排队，则无法释放，进而导致Activity对象无法释放，造成内存泄出。

解决的方法是在Activity被销毁时，在onDestroy()中调用Handler的removeCallbacksAndMessages()方法。此方法调用时会要求传一个Object参数。先看API：

> public final void removeCallbacksAndMessages(Object token)
>
> Added in API level 1 
>
> Remove any  pending posts of callbacks and sent messages whose obj is token . If token is **null**, all callbacks and messages will be removed.

意思就是当传入的参数为null时，则移除所有的callbacks和messages，这样就有效的避免了由Handler引起的内存溢出。