---
layout: post
title: Activity和Fragment的区别？
date: 2019-12-17
Author: Zhu Kun
tags: [Fragment]
toc: true
comments: true
---

- 相似点：都可包含布局、可有自己的生命周期

- 不同点： 

- 1. Fragment相比较于Activity多出4个回调周期，在控制操作上更灵活；
  2. Fragment可以在XML文件中直接进行写入，也可以在Activity中动态添加；
  3. Fragment可以使用show()/hide()或者replace()随时对Fragment进行切换，并且切换的时候不会出现明显的效果，用户体验会好；Activity虽然也可以进行切换，但是Activity之间切换会有明显的翻页或者其他的效果，在小部分内容的切换上给用户的感觉不是很好；

- ## 引用/详解

  [2019初中级Android开发社招面试解答](https://juejin.im/post/5c8211fee51d453a136e36b0)
