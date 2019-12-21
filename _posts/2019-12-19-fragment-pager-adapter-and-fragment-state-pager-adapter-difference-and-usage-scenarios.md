---
layout: post
title: FragmentPagerAdapter与FragmentStatePagerAdapter的区别与使用场景
date: 2019-12-18
Author: Zhu Kun
tags: [Fragment, PagerAdapter, Adapter]
toc: true
comments: true
---

- 相同点  ：二者都继承PagerAdapter
- 不同点 ：**FragmentPagerAdapter**的每个Fragment会持久的保存在FragmentManager中，只要用户可以返回到页面中，它都不会被销毁。因此适用于那些数据**相对静态**的页，Fragment**数量也比较少**的那种； **FragmentStatePagerAdapter**只保留当前页面，当页面不可见时，该Fragment就会被消除，释放其资源。因此适用于那些**数据动态性**较大、**占用内存**较多，多Fragment的情况；