---
layout: post
title: View基础
date: 2020-02-25
Author: Zhu Kun
tags: [Service]
toc: true
comments: true
---

## 讲下View的绘制流程？

View的工作流程主要是指measure、layout、draw这三大流程，即测量、布局和绘制，

其中measure确定View的测量宽/高，

layout确定View的最终宽/高和四个顶点的位置，

而draw则将View绘制到屏幕上

View的绘制过程遵循如下几步：

- 绘制背景     background.draw(canvas)
- 绘制自己（onDraw）
- 绘制     children（dispatchDraw）
- 绘制装饰（onDrawScollBars）

![](http://justzk.github.io/images/view-basics/view-basics-1.png)

推荐文章： [官方文档](https://developer.android.com/reference/android/view/View) [Android View的绘制流程](https://www.jianshu.com/p/5a71014e7b1b) [Android应用层View绘制流程与源码分析](https://blog.csdn.net/yanbober/article/details/46128379)

## MotionEvent是什么？包含几种事件？什么条件下会产生？

MotionEvent是手指接触屏幕后所产生的一系列事件。典型的事件类型有如下：

- ACTION_DOWN：手指刚接触屏幕
- ACTION_MOVE：手指在屏幕上移动
- ACTION_UP：手指从屏幕上松开的一瞬间
- ACTION_CANCELL：手指保持按下操作，并从当前控件转移到外层控件时触发

正常情况下，一次手指触摸屏幕的行为会触发一系列点击事件，考虑如下几种情况：

- 点击屏幕后松开，事件序列：DOWN→UP
- 点击屏幕滑动一会再松开，事件序列为DOWN→MOVE→.....→MOVE→UP

## 描述一下View事件传递分发机制？

View事件分发本质就是对MotionEvent事件分发的过程。即当一个MotionEvent发生后，系统将这个点击事件传递到一个具体的View上

点击事件的传递顺序：Activity（Window）→ViewGroup→ View

事件分发过程由三个方法共同完成：

- dispatchTouchEvent：用来进行事件的分发。如果事件能够传递给当前View，那么此方法一定会被调用，返回结果受当前View的onTouchEvent和下级View的dispatchTouchEvent方法的影响，表示是否消耗当前事件
- onInterceptTouchEvent：在上述方法内部调用，对事件进行拦截。该方法只在ViewGroup中有，View（不包含     ViewGroup）是没有的。一旦拦截，则执行ViewGroup的onTouchEvent，在ViewGroup中处理事件，而不接着分发给View。且只调用一次，返回结果表示是否拦截当前事件
- onTouchEvent：     在dispatchTouchEvent方法中调用，用来处理点击事件，返回结果表示是否消耗当前事件

## 如何解决View的事件冲突 ？ 举个开发中遇到的例子 ？

常见开发中事件冲突的有ScrollView与RecyclerView的滑动冲突、RecyclerView内嵌同时滑动同一方向

滑动冲突的处理规则：

- 对于由于外部滑动和内部滑动方向不一致导致的滑动冲突，可以根据滑动的方向判断谁来拦截事件。
- 对于由于外部滑动方向和内部滑动方向一致导致的滑动冲突，可以根据业务需求，规定何时让外部View拦截事件，何时由内部View拦截事件。
- 对于上面两种情况的嵌套，相对复杂，可同样根据需求在业务上找到突破点。

滑动冲突的实现方法：

- **外部拦截法**：指点击事件都先经过父容器的拦截处理，如果父容器需要此事件就拦截，否则就不拦截。具体方法：需要重写父容器的onInterceptTouchEvent方法，在内部做出相应的拦截。
- **内部拦截法**：指父容器不拦截任何事件，而将所有的事件都传递给子容器，如果子容器需要此事件就直接消耗，否则就交由父容器进行处理。具体方法：需要配合requestDisallowInterceptTouchEvent方法。

## scrollTo()和scollBy()的区别？

- scollBy内部调用了scrollTo，它是基于当前位置的相对滑动；而scrollTo是绝对滑动，因此如果使用相同输入参数多次调用scrollTo方法，由于View的初始位置是不变的，所以只会出现一次View滚动的效果
- 两者都只能对View内容的滑动，而非使View本身滑动。可以使用Scroller有过度滑动的效果

**推荐文章：** [View 的滑动原理和实现方式](https://link.juejin.im?target=https%3A%2F%2Fwww.jianshu.com%2Fp%2Fa177869b0382)

## Scroller是怎么实现View的弹性滑动？

- 在MotionEvent.ACTION_UP事件触发时调用startScroll()方法，该方法并没有进行实际的滑动操作，而是记录滑动相关量（滑动距离、滑动时间）
- 接着调用invalidate/postInvalidate()方法，请求View重绘，导致View.draw方法被执行
- 当View重绘后会在draw方法中调用computeScroll方法，而computeScroll又会去向Scroller获取当前的scrollX和scrollY；然后通过scrollTo方法实现滑动；接着又调用postInvalidate方法来进行第二次重绘，和之前流程一样，如此反复导致View不断进行小幅度的滑动，而多次的小幅度滑动就组成了弹性滑动，直到整个滑动过成结束     

![](http://justzk.github.io/images/view-basics/view-basics-2.jpg)

## invalidate()和postInvalidate()的区别 ？

invalidate()与postInvalidate()都用于刷新View，主要区别是invalidate()在主线程中调用，若在子线程中使用需要配合handler；而postInvalidate()可在子线程中直接调用。

## SurfaceView和View的区别？

- View需要在UI线程对画面进行刷新，而SurfaceView可在子线程进行页面的刷新
- View适用于主动更新的情况，而SurfaceView适用于被动更新，如频繁刷新，这是因为如果使用View频繁刷新会阻塞主线程，导致界面卡顿
- SurfaceView在底层已实现双缓冲机制，而View没有，因此SurfaceView更适用于需要频繁刷新、刷新时数据处理量很大的页面（如视频播放界面）

## 自定义View如何考虑机型适配?

- 合理使用warp_content，match_parent
- 尽可能的是使用RelativeLayout
- 针对不同的机型，使用不同的布局文件放在对应的目录下，android会自动匹配。
- 尽量使用点9图片。
- 使用与密度无关的像素单位dp，sp
- 引入android的百分比布局。
- 切图的时候切大分辨率的图，应用到布局当中。在小分辨率的手机上也会有很好的显示效果。