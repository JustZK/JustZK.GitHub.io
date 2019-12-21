---
layout: post
title: Fragment中add与replace的区别（Fragment重叠）
date: 2019-12-18
Author: Zhu Kun
tags: [Fragment]
toc: true
comments: true
---

- add不会重新初始化fragment，replace每次都会。所以如果在fragment生命周期内获取获取数据,使用replace会重复获取；
- 添加相同的fragment时，replace不会有任何变化，add会报IllegalStateException异常；
- replace先remove掉相同id的所有fragment，然后在add当前的这个fragment，而add是覆盖前一个fragment。所以如果使用add一般会伴随hide()和show()，避免布局重叠；
- 使用add，如果应用放在后台，或以其他方式被系统销毁，再打开时，hide()中引用的fragment会销毁，所以依然会出现布局重叠bug，可以使用replace或使用add时，添加一个tag参数；

![](http://justzk.github.io/images/difference-between-add-and-replace-in-fragment/difference-between-add-and-replace-in-fragment.png)