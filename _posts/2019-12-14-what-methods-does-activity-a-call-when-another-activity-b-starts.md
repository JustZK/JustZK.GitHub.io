---
layout: post
title: Activity A 启动另一个Activity B 会调用哪些方法？如果B是透明主题的又或则是个DialogActivity呢 ？
date: 2019-12-14
Author: Zhu Kun
tags: [Activity]
toc: true
comments: true
---

## Activity A 启动另一个Activity B，回调如下 ：

```java
Activity A 的onPause() → 

Activity B 的onCreate() → 

						 onStart() → 

             onResume() →

Activity A 的onStop()；
```

## **如果B是透明主题又或则是个DialogActivity：**

则**不会**回调A的onStop；