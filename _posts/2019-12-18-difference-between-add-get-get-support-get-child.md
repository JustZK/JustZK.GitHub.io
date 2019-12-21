---
layout: post
title: getFragmentManager、getSupportFragmentManager 、getChildFragmentManager之间的区别？
date: 2019-12-18
Author: Zhu Kun
tags: [Fragment]
toc: true
comments: true
---

- getFragmentManager()所得到的是所在fragment     的**父容器**的管理器， getChildFragmentManager()所得到的是在fragment 里面**子容器**的管理器，     如果是fragment嵌套fragment，那么就需要利用getChildFragmentManager()；
- 因为Fragment是3.0     Android系统API版本才出现的组件，所以3.0以上系统可以直接调用getFragmentManager()来获取FragmentManager()对象，而3.0以下则需要调用getSupportFragmentManager()     来间接获取；