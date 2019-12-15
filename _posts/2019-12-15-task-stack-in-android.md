---
layout: post
title: Android中的任务栈Task
date: 2019-12-15
Author: Zhu Kun
tags: [Activity, Task, AndroidManifest]
toc: true
comments: true
---

## 什么是任务栈(Task)

官方文档是这么解释的：任务是指在执行特定作业时与用户交互的一系列 Activity。 这些 Activity 按照各自的打开顺序排列在堆栈（即“返回栈”）中。

其实就是以栈的结构（先进后出）将依次打开的activity记录.

## 为什么要用任务栈

为了记录用户开启了那些activity，记录这些activity开启的先后顺序，google引入任务栈（task stack）概念，帮助维护好的用户体验。

## 如何查看当前系统的任务栈

手机中 --> 长按home或者多任务键会进到 **概览屏幕**的一个界面

命令行中 --> adb shell dumpsys activity

## 概览屏幕(Overview Screen)

概览屏幕（也称为最新动态屏幕、最近任务列表或最近使用的应用）是一个系统级别 UI，其中列出了最近访问过的Activity和任务。 用户可以浏览该列表并选择要恢复的任务，也可以通过滑动清除任务将其从列表中删除。 对于 Android 5.0 版本（API 级别 21），包含多个文档的同一 Activity 的多个实例可能会以任务的形式显示在概览屏幕中。例如，Google Drive 可能对多个 Google 文档中的每个文档均执行一个任务。每个文档均以任务的形式显示在概览屏幕中。

## Task（任务栈）中Activity的特点:

1. 可以来自不同的app
2. 可以运行在不同进程

## taskAffinity

除了launchMode可以用来调配Task，的另一属性taskAffinity，也是常常被使用。taskAffinity，是一种物以类聚的思想，它倾向于将taskAffinity属性相同的Activity，扔进同一个Task中。

TaskAffinity特点如下：

1. TaskAffinity参数标识着Activity所需要任务栈的名称，默认情况下，一个应用中所有的Activity所需要的任务栈名称都为改应用的包名。
2. taskAffinity的约束力弱于launchMode，只有当中的allowTaskReparen     ting设置为true，抑或是调用方将Intent的flag添加FLAG_ACTIVITY_NEW_TASK属性时才会生效，在其他情况下没有实际意义
3. TaskAffinity属性的值不能与当前应用包名相同，否则其值跟没有差不多

**实例**：建立两个demo App分别命名为 A 和 B。从 A 的主界面通过意图跳转到 B 的主界面界面。此时两个app的界面位于同一个App A的栈内。如图：

![](https://justzk.github.io/images/task-stack-in-android/task-stack-in-android-1.png)

## Activity的task相关属性

1. **allowTaskReparenting**

   这个属性用来标记一个Activity实例在当前应用退到后台后，是否能从启动它的那个task移动到有共同affinity的task，“true”表示可以移动，“false”表示它必须呆在当前应用的task中，默认值为false。

   比如在app1的activityA中打开app2的activity2，按home键，回到后台后，这时在launcher中点击app1,页面显示的是app1的activityA（是在此时将activity2转移到app2的task中）。再点击app2,则显示的是activity2,点击back,则会在app2所在的任务栈中回退。

   需要注意的是，如果app1退居后台之后，没有再次启动app1，而是直接启动app2，将不会出现以上现象。重新宿主的动作发生在appB再次启动的过程中。

2. **android:clearTaskOnLaunch**

   这个属性用来标记是否从task清除除根Activity之外的所有的Activity，“true”表示清除，“false”表示不清除，默认为“false”。

   这里有点我们必须要注意的，这个属性只对任务栈内的root Activity起作用，任务栈内其他的Activity都会被忽略。

   如果android:clearTaskOnLaunch属性为“true”，每次我们重新进入这个应用时，我们只会看到根Activity，任务栈中的其他Activity都会被清除出栈。

3. **android:finishOnTaskLaunch**

   finishOnTaskLaunch属性与clearTaskOnLaunch 有些类似，它们的区别是finishOnTaskLaunch是作用在自己身上(把自己移除任务栈，不影响别的Activity)，而clearTaskOnLaunch则是作用在别人身上(把别的Activity移除任务栈)

   如果我们把Activity的android:finishOnTaskLaunch属性值设置为true时，离开这个Activity所依赖的任务栈后，当我们重新返回时，该Activity将会被finish掉，而且其他Activity不会受到影响。

   如果这个属性和android:allowReparenting都设定为“true”，则这个属性优先级高。 

4. **android:alwaysRetainTaskState**

   如果当前Activity的android:alwaysRetainTaskState设置为true时，那么该Activity所在的任务栈将不会受到任何清理命令的影响，一直保持当前任务栈的状态。

   默认情况下，如果一个应用在后台呆的太久例如30分钟，用户从主选单再次选择该应用时，系统就会对该应用的task进行清理，除了根Activity，其他Activity都会被清除出栈，但是如果在根Activity中设置了此属性之后，用户再次启动应用时，仍然可以看到上一次操作的界面。

   这个属性对于一些应用非常有用，例如Browser应用程序，有很多状态，比如打开很多的tab，用户不想丢失这些状态，使用这个属性就极为恰当。

5. **android:documentLaunchMode**

   **intoExisting**

   该 Activity 会对文档重复使用现有任务。这与不设置 FLAG_ACTIVITY_MULTIPLE_TASK 标志、但设置 FLAG_ACTIVITY_NEW_DOCUMENT 标志所产生的效果相同，如上文的使用 Intent 标志添加任务中所述。 

   **always**

   该 Activity 为文档创建新任务，即便文档已打开也是如此。使用此值与同时设置 FLAG_ACTIVITY_NEW_DOCUMENT 和 FLAG_ACTIVITY_MULTIPLE_TASK 标志所产生的效果相同。 

   **none**

   该 Activity 不会为文档创建新任务。概览屏幕将按其默认方式对待此 Activity：为应用显示单个任务，该任务将从用户上次调用的任意 Activity 开始继续执行。 

   **never**

   该 Activity 不会为文档创建新任务。设置此值会替代 FLAG_ACTIVITY_NEW_DOCUMENT 和 FLAG_ACTIVITY_MULTIPLE_TASK 标志的行为（如果在 Intent 中设置了其中一个标志），并且概览屏幕将为应用显示单个任务，该任务将从用户上次调用的任意 Activity 开始继续执行。 

   **对于除 none 和 never 以外的值，必须使用 launchMode="standard" 定义 Activity。如果未指定此属性，则使用 documentLaunchMode="none"。**