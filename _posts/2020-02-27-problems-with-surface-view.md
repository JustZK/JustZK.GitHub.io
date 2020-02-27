---
layout: post
title: SurfaceView碰到的问题
date: 2020-02-27
Author: Zhu Kun
tags: [View, SurfaceView]
toc: true
comments: true
---

## 灰色覆盖，或者有一层颜色在上面

SurfaceView所在的父类容器设置的主题，导致surface上面有一层颜色

## 多个SurfaceView层叠显示问题

当场景中有多个SurfaceView的时候，上层的SurfaceView可能会被下层的遮挡，

这个时候需要使用setZOrderOnTop(true)

或者setZOrderMediaOverlay(true)来控制SurfaceView的显示层次。

 

Android SDK对两个函数的描述：

### 1、setZOrderOnTop

> **public void** **setZOrderOnTop** **(boolean onTop)**
>
> Added in [API level 5](http://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels)
>
> Control whether the surface view’s surface is placed on top of its window. Normally it is placed behind the window, to allow it to (for the most part) appear to composite with the views in the hierarchy. By setting this, you cause it to be placed above the window. This means that none of the contents of the window this SurfaceView is in will be visible on top of its surface.
>
> Note that this must be set before the surface view’s containing window is attached to the window manager.
>
> Calling this overrides any previous call to [setZOrderMediaOverlay(boolean)](http://developer.android.com/reference/android/view/SurfaceView.html#setZOrderMediaOverlay(boolean)).

控制这个surfaceView是否被放在窗口顶层。通常,为了使它与绘图树整合,它被放在窗口之后。通过这个函数，你可以使SurfaceView被放在窗口顶层。这意味着它所在的窗口的其他内容都不可见。（注:可以设置surfaceView透明来使其他内容可见）

这个函数必须在**窗口被添加到窗口管理器之前设置。**

调用这个函数会使之前调用的[setZOrderMediaOverlay(boolean)](http://developer.android.com/reference/android/view/SurfaceView.html#setZOrderMediaOverlay(boolean))无效；

### 2、setZOrderMediaOverlay

> **public void** **setZOrderMediaOverlay** **(boolean isMediaOverlay)**
>
> Added in [API level 5](http://developer.android.com/guide/topics/manifest/uses-sdk-element.html#ApiLevels)
>
> Control whether the surface view’s surface is placed on top of another regular surface view in the window (but still behind the window itself). This is typically used to place overlays on top of an underlying media surface view.
>
> Note that this must be set before the surface view’s containing window is attached to the window manager.
>
> Calling this overrides any previous call to [setZOrderOnTop(boolean)](http://developer.android.com/reference/android/view/SurfaceView.html#setZOrderOnTop(boolean)).

控制这个surfaceView是否被放在另一个普通的surfaceView上面（但是仍然在窗口之后）。这个函数通常被用来将覆盖层至于一个多媒体层上面。

这个**函数必须在窗口被添加到窗口管理器之前设置。**

调用这个函数会使之前调用的[setZOrderOnTop(boolean)](http://developer.android.com/reference/android/view/SurfaceView.html#setZOrderOnTop(boolean))无效。

## SurfaceView创建时，surfaceCreated调用的问题

正常流程执行surfaceView，surfaceCreated可以正常调用，SurfaceView预览界面写在XML文件中;但是试了下面两种方法surfaceCreated不会被调用：

方法一，使用handler延时加载

```java
mHandler.postDelayed(new Thread() {
    public void run() {
        surfaceHolder = surfaceView.getHolder();
        surfaceHolder.addCallback(this);
        surfaceHolder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);
    }
}，10);
```

方法二，点击按钮加载

```java
public void onClick(View view){
     surfaceHolder = surfaceView.getHolder();
     surfaceHolder.addCallback(this);
     surfaceHolder.setType(SurfaceHolder.SURFACE_TYPE_PUSH_BUFFERS);
}
```

后来尝试发现使用动态加载SurfaceView可以解决这个问题，就是new一个SurfaceView对象，然后addView(mSurfaceView)；

但是还是不明白为什么SurfaceView在XML布局中。上述两种方法就不可行？

原来正常流程中SurfaceView类中的变量mSurfaceCreated为false，

而使用postDelay方式，刚开始就被设置为mSurfaceCreated = true，

这样就导致了updateWindow方法中进不去设置不callback无法执行

```java
if (visible && mSurface.isValid()) { 
    if (!mSurfaceCreated && (surfaceChanged || visibleChanged)) {
        mSurfaceCreated = true;
        mIsCreating = true;
        if (DEBUG) Log.i(TAG, "visibleChanged -- surfaceCreated");
        if (callbacks == null) {
            callbacks = getSurfaceCallbacks();
        }
        for (SurfaceHolder.Callback c : callbacks) {
            c.surfaceCreated(mSurfaceHolder);
        }
    }

}
```

