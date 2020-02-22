---
layout: post
title: 启用全屏模式
date: 2020-02-22
Author: Zhu Kun
tags: [View, UI]
toc: true
comments: true
---

有些内容在全屏模式下会让用户获得最佳体验，例如视频、游戏、图片库、图书和演示文稿中的幻灯片。本页面介绍了如何采用全屏模式吸引用户更深入地了解相关内容，并防止用户意外退出应用。

![](http://justzk.github.io/images/enable-full-screen-mode/enable-full-screen-mode-1.png)

您想要启用全屏模式可能只是为了让应用最大限度地利用屏幕空间。

但需要注意用户来回切换应用查看通知、进行临时搜索等操作的频率。

使用全屏模式会导致用户无法轻松访问系统导航；

因此使用全屏模式的前提是：

对用户体验来讲，带来的益处不仅仅只是获得一点额外的空间（会带来更多益处，例如：能够避免在游戏过程中意外退出，或针对图片、视频和图书提供宝贵的沉浸式体验）。

## 全屏选项

Android 提供了三个用于将应用设为全屏模式选项：

1. 向后倾斜模式；
2. 沉浸模式；
3. 粘性沉浸模式。

在所有三种方法中，系统栏都是隐藏的，您的 Activity 会持续收到所有轻触事件。 它们之间的区别在于用户让系统栏重新显示出来的方式。

以下是每个不同选项的说明。

### 一、向后倾斜

![](http://justzk.github.io/images/enable-full-screen-mode/enable-full-screen-mode-2.png)

向后倾斜模式适用于用户不会与屏幕进行大量互动的全屏体验，例如在观看视频时。

当用户希望调出系统栏时，只需点按屏幕上的任意位置即可。

要启用向后倾斜模式，请调用 [setSystemUiVisibility() ](https://developer.android.com/reference/android/view/View.html?hl=zh_cn#setSystemUiVisibility(int))并传递 [SYSTEM_UI_FLAG_FULLSCREEN ](https://developer.android.com/reference/android/view/View.html?hl=zh_cn#SYSTEM_UI_FLAG_FULLSCREEN)和 [SYSTEM_UI_FLAG_HIDE_NAVIGATION](https://developer.android.com/reference/android/view/View.html?hl=zh_cn#SYSTEM_UI_FLAG_HIDE_NAVIGATION)。

当系统栏再次显示时，您可以接收回调以对您的界面进行其他适当的更新。请参阅[响应界面可见性更改](https://developer.android.com/training/system-ui/visibility.html?hl=zh_cn)。

### 二、沉浸模式

![](http://justzk.github.io/images/enable-full-screen-mode/enable-full-screen-mode-3.png)

![](http://justzk.github.io/images/enable-full-screen-mode/enable-full-screen-mode-4.png)

沉浸模式适用于用户将与屏幕进行大量互动的应用。 示例包括游戏、查看图库中的图片或者阅读分页内容，如图书或演示文稿中的幻灯片。 

当用户需要调出系统栏时，他们可从隐藏系统栏的任一边滑动。要求使用这种这种意图更强的手势是为了确保用户与您应用的互动不会因意外轻触和滑动而中断。

要启用沉浸模式，请调用 [setSystemUiVisibility()](https://developer.android.com/reference/android/view/View.html?hl=zh-cn#setSystemUiVisibility(int)) 并将 [SYSTEM_UI_FLAG_IMMERSIVE](https://developer.android.com/reference/android/view/View.html?hl=zh-cn#SYSTEM_UI_FLAG_IMMERSIVE) 标志与 [SYSTEM_UI_FLAG_FULLSCREEN](https://developer.android.com/reference/android/view/View.html?hl=zh-cn#SYSTEM_UI_FLAG_FULLSCREEN) 和 [SYSTEM_UI_FLAG_HIDE_NAVIGATION](https://developer.android.com/reference/android/view/View.html?hl=zh-cn#SYSTEM_UI_FLAG_HIDE_NAVIGATION) 一起传递。

如果您的应用拥有自己的控件，而这些控件在用户以沉浸模式观看内容时用不上，可让这些控件随系统栏同步消失及重新显示。 如果您有用于隐藏和显示应用控件的任何应用专用手势，上述建议同样适用。例如，如果轻触屏幕上的任意位置会显示或隐藏工具栏或调色板，也应相应地显示或隐藏系统栏。

当系统栏再次显示时，您可以接收回调以对您的界面进行其他适当的更新。请参阅[响应界面可见性更改](https://developer.android.com/training/system-ui/visibility.html?hl=zh-cn)。

### 三、粘性沉浸模式

在普通的沉浸模式中，只要用户从边缘滑动，系统就会负责显示系统栏，您的应用甚至不会知道发生了该手势。因此，如果用户实际上可能是出于主要的应用体验而需要从屏幕边缘滑动，例如在玩需要大量滑动的游戏或使用绘图应用时，您应改为启用“粘性”沉浸模式。

在粘性沉浸模式下，如果用户从隐藏了系统栏的边缘滑动，系统栏会显示出来，但它们是半透明的，并且轻触手势会传递给应用，因此应用也会响应该手势。

例如，在使用这种方法的绘图应用中，如果用户想绘制从屏幕最边缘开始的线条，则从这个边缘滑动会显示系统栏，同时还会开始绘制从最边缘开始的线条。无互动几秒钟后，或者用户在系统栏之外的任何位置轻触或做手势时，系统栏会自动消失。

要启用粘性沉浸模式，请调用 [setSystemUiVisibility()](https://developer.android.com/reference/android/view/View.html?hl=zh-cn#setSystemUiVisibility(int)) 并将 [SYSTEM_UI_FLAG_IMMERSIVE_STICKY](https://developer.android.com/reference/android/view/View.html?hl=zh-cn#SYSTEM_UI_FLAG_IMMERSIVE_STICKY) 标志与 [SYSTEM_UI_FLAG_FULLSCREEN](https://developer.android.com/reference/android/view/View.html?hl=zh-cn#SYSTEM_UI_FLAG_FULLSCREEN) 和 [SYSTEM_UI_FLAG_HIDE_NAVIGATION](https://developer.android.com/reference/android/view/View.html?hl=zh-cn#SYSTEM_UI_FLAG_HIDE_NAVIGATION) 一起传递。

在粘性沉浸模式下，如果系统界面可见性发生变化，您无法收到回调。因此，如果您需要粘性沉浸模式的自动隐藏行为，但仍想要知道系统界面何时重新显示以便把您自己的界面控件也显示出来，请使用普通的 [IMMERSIVE](https://developer.android.com/reference/android/view/View.html?hl=zh-cn#SYSTEM_UI_FLAG_IMMERSIVE) 标志并使用 [Handler.postDelayed()](https://developer.android.com/reference/android/os/Handler.html?hl=zh-cn#postDelayed(java.lang.Runnable, java.lang.Object, long)) 或类似方法，以在几秒后重新进入沉浸模式。

### 启用全屏模式

无论您想要使用哪种全屏模式，都必须调用 [setSystemUiVisibility()](https://developer.android.com/reference/android/view/View.html?hl=zh-cn#setSystemUiVisibility(int)) 并将其传递给 [SYSTEM_UI_FLAG_HIDE_NAVIGATION](https://developer.android.com/reference/android/view/View.html?hl=zh-cn#SYSTEM_UI_FLAG_HIDE_NAVIGATION) 和/或 [SYSTEM_UI_FLAG_FULLSCREEN](https://developer.android.com/reference/android/view/View.html?hl=zh-cn#SYSTEM_UI_FLAG_FULLSCREEN)。您可以包含 [SYSTEM_UI_FLAG_IMMERSIVE](https://developer.android.com/reference/android/view/View.html?hl=zh-cn#SYSTEM_UI_FLAG_IMMERSIVE)（用于普通沉浸模式）或 [SYSTEM_UI_FLAG_IMMERSIVE_STICKY](https://developer.android.com/reference/android/view/View.html?hl=zh-cn#SYSTEM_UI_FLAG_IMMERSIVE_STICKY)（用于粘性沉浸模式），也可以同时不含这两种标记以启用向后倾斜模式。

最好包含其他系统界面标志（例如 [SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION](https://developer.android.com/reference/android/view/View.html?hl=zh-cn#SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION) 和 [SYSTEM_UI_FLAG_LAYOUT_STABLE](https://developer.android.com/reference/android/view/View.html?hl=zh-cn#SYSTEM_UI_FLAG_LAYOUT_STABLE)），防止布局随着系统栏的隐藏和显示调整大小。您还应确保操作栏和其他界面控件同时处于隐藏状态。

以下代码说明了如何隐藏和显示 Activity 中的状态栏和导航栏，同时不会为了响应屏幕空间变化而调整布局大小：

```kotlin
    override fun onWindowFocusChanged(hasFocus: Boolean) {
        super.onWindowFocusChanged(hasFocus)
        if (hasFocus) hideSystemUI()
    }

    private fun hideSystemUI() {
        // Enables regular immersive mode.
        // For "lean back" mode, remove SYSTEM_UI_FLAG_IMMERSIVE.
        // Or for "sticky immersive," replace it with SYSTEM_UI_FLAG_IMMERSIVE_STICKY
        window.decorView.systemUiVisibility = (View.SYSTEM_UI_FLAG_IMMERSIVE
                // Set the content to appear under the system bars so that the
                // content doesn't resize when the system bars hide and show.
                or View.SYSTEM_UI_FLAG_LAYOUT_STABLE
                or View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION
                or View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN
                // Hide the nav bar and status bar
                or View.SYSTEM_UI_FLAG_HIDE_NAVIGATION
                or View.SYSTEM_UI_FLAG_FULLSCREEN)
    }

    // Shows the system bars by removing all the flags
    // except for the ones that make the content appear under the system bars.
    private fun showSystemUI() {
        window.decorView.systemUiVisibility = (View.SYSTEM_UI_FLAG_LAYOUT_STABLE
                or View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION
                or View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN)
    }
```

```java
 @Override
    public void onWindowFocusChanged(boolean hasFocus) {
        super.onWindowFocusChanged(hasFocus);
        if (hasFocus) {
            hideSystemUI();
        }
    }

    private void hideSystemUI() {
        // Enables regular immersive mode.
        // For "lean back" mode, remove SYSTEM_UI_FLAG_IMMERSIVE.
        // Or for "sticky immersive," replace it with SYSTEM_UI_FLAG_IMMERSIVE_STICKY
        View decorView = getWindow().getDecorView();
        decorView.setSystemUiVisibility(
                View.SYSTEM_UI_FLAG_IMMERSIVE
                // Set the content to appear under the system bars so that the
                // content doesn't resize when the system bars hide and show.
                | View.SYSTEM_UI_FLAG_LAYOUT_STABLE
                | View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION
                | View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN
                // Hide the nav bar and status bar
                | View.SYSTEM_UI_FLAG_HIDE_NAVIGATION
                | View.SYSTEM_UI_FLAG_FULLSCREEN);
    }

    // Shows the system bars by removing all the flags
    // except for the ones that make the content appear under the system bars.
    private void showSystemUI() {
        View decorView = getWindow().getDecorView();
        decorView.setSystemUiVisibility(
                View.SYSTEM_UI_FLAG_LAYOUT_STABLE
                | View.SYSTEM_UI_FLAG_LAYOUT_HIDE_NAVIGATION
                | View.SYSTEM_UI_FLAG_LAYOUT_FULLSCREEN);
    }
```

为了提供更好的用户体验，您可能还需要实现以下功能：

- 要实现状态之间的无缝切换，请将所有界面控件的可见性与系统栏保持同步。当应用进入沉浸模式后，任何界面控件也应随系统栏一起隐藏，然后在系统界面重新出现时也再次显示出来。为此，请实现 [View.OnSystemUiVisibilityChangeListener](https://developer.android.com/reference/android/view/View.OnSystemUiVisibilityChangeListener.html?hl=zh-cn) 以接收回调，如[响应界面可见性更改](https://developer.android.com/training/system-ui/visibility.html?hl=zh-cn)中所述。
- 实现 [onWindowFocusChanged()](https://developer.android.com/reference/android/app/Activity.html?hl=zh-cn#onWindowFocusChanged(boolean))。     如果您获得窗口焦点，则可能需要再次隐藏系统栏。 如果您失去了窗口焦点，例如由于在应用界面上出现对话框或弹出式菜单，您可能需要使用 [Handler.postDelayed()](https://developer.android.com/reference/android/os/Handler.html?hl=zh-cn#postDelayed(java.lang.Runnable, java.lang.Object, long)) 或类似方法取消之前调度的所有待处理“隐藏”操作。
- 实现用于检测 [onSingleTapUp(MotionEvent)](https://developer.android.com/reference/android/view/GestureDetector.OnGestureListener.html?hl=zh-cn#onSingleTapUp(android.view.MotionEvent)) 的 [GestureDetector](https://developer.android.com/reference/android/view/GestureDetector.html?hl=zh-cn)，以允许用户通过轻触您的内容手动切换系统栏的可见性。     简单的点击监听器不是这种情况的最佳解决方案，因为即使用户用手指在屏幕上拖动也会触发这类监听器（假设点击目标会占用整个屏幕）。

**注意：**当您使用 [SYSTEM_UI_FLAG_IMMERSIVE_STICKY](https://developer.android.com/reference/android/view/View.html?hl=zh-cn#SYSTEM_UI_FLAG_IMMERSIVE_STICKY) 标志时，滑动操作会使系统界面暂时以半透明状态显示，但不会清除任何标志，并且不会触发系统界面可见性更改监听器。