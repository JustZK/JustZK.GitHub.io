---
layout: post
title: 判断当前ImageView设置的是哪张图片
date: 2020-02-26
Author: Zhu Kun
tags: [ImageView, View]
toc: true
comments: true
---

## 方法一：设置一个TAG

```java
guide_line=findViewById(R.id.guide_line);
guide_line.setTag("Offline");
```



## 方法二：

```java
guide_line.getDrawable(this,R.drawable.online).getConstantState();
```

