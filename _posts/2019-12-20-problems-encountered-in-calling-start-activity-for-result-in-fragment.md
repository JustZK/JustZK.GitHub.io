---
layout: post
title: Fragment中调用startActivityForResult遇到的问题
date: 2019-12-20
Author: Zhu Kun
tags: [Fragment, StartActivityForResult]
toc: true
comments: true
---

## Fragment中调用startActivityForResult要注意几种情况

### 一、调用的方法

1. 用getActivity方法发起调用，只有父Activity的onActivityResult会调用，Fragment中的onActivityResult不会被调用
2. 直接发起startActivityForResult调用，当前的Fragment的onActivityResult，和父Activity的onActivityResult都会调用
3. 用getParentFragment发起调用，则只有父Activity和父Fragment的onActivityResult会被调用，当前的Fragment的onActivityResult不会被调用。

> 这里2和3的前提是如果父activity中重写了onActivityResult，父Activity的onActivityResult中必须添加super.onActivityResult()  

总结起来就是：从哪里发起调用，最终就会走到哪里。

#### 二、onActivityResult的requestCode、resultCode

![](http://justzk.github.io/images/problems-encountered-in-calling-start-activity-for-result-in-fragment/problems-encountered-in-calling-start-activity-for-result-in-fragment.png)