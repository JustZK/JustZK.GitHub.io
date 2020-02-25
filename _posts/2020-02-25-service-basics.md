---
layout: post
title: Service基础
date: 2020-02-25
Author: Zhu Kun
tags: [Service]
toc: true
comments: true
---

## Service的生命周期？

详见：[Service的生命周期？](https://justzk.github.io/service-life-cycle/)

## Service的两种启动方式？区别在哪？

详见：[Service的两种启动方式？区别在哪？](https://justzk.github.io/two-ways-to-start-service/)

## 如何保证Service不被杀死 ？

详见：[如何保证Service不被杀死？](https://justzk.github.io/how-to-ensure-that-services-are-not-killed/)

## 能否在Service开启耗时操作 ？ 怎么做 ？

Service默认并不会运行在子线程中，也不运行在一个独立的进程中，它同样执行在主线程中（UI线程）。

换句话说，不要在Service里执行耗时操作，除非手动打开一个子线程，否则有可能出现主线程被阻塞（ANR）的情况；

## 用过哪些系统Service ？

![](http://justzk.github.io/images/service-basics/service-basics-1.png)

## 了解ActivityManagerService吗？发挥什么作用？

- ActivityManagerService是Android中最核心的服务     ， 主要负责系统中四大组件的启动、切换、调度及应用进程的管理和调度等工作，其职责与操作系统中的进程管理和调度模块类似；
- 推荐文章： [ActivityManagerService分析——AMS启动流程](https://link.juejin.im?target=https%3A%2F%2Fblog.csdn.net%2Fcaohang103215%2Farticle%2Fdetails%2F79597260)