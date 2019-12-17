---
layout: post
title: Fragment的生命周期 ？与Activity生命周期的不同 ？
date: 2019-12-17
Author: Zhu Kun
tags: [Fragment]
toc: true
comments: true
---

- Fragment从创建到销毁整个生命周期中涉及到的方法依次为：

  onAttach()→onCreate()→   onCreateView()→onActivityCreated()→onStart()→onResume()→onPause()→onStop()→onDestroyView()→

  onDestroy()→onDetach()；

  其中和Activity有不少名称相同作用相似的方法，而不同的方法有:     

- - **onAttach()**：当Fragment和Activity建立关联时调用；
  - **onCreateView()**：当fragment创建视图调用，在onCreate之后；
  - **onActivityCreated()**：当与Fragment相关联的Activity完成onCreate()之后调用；
  - **onDestroyView()**：在Fragment中的布局被移除时调用；
  - **onDetach()**：当Fragment和Activity解除关联时调用；

- 推荐文章：[Android之Fragment优点](https://link.juejin.im?target=https%3A%2F%2Fwww.cnblogs.com%2Fshaweng%2Fp%2F3918985.html)

![](http://justzk.github.io/images/fragment-life-cycle/fragment-life-cycle-1.png)

![](http://justzk.github.io/images/fragment-life-cycle/fragment-life-cycle-2.png)