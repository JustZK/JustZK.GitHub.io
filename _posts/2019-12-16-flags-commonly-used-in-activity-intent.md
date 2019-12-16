---
layout: post
title: Activity的Intent常用的标记位Flags
date: 2019-12-16
Author: Zhu Kun
tags: [Activity, Flags, Intent]
toc: true
comments: true
---

Intent作为Android四大组件的沟通桥梁，

用Action确定了响应对象，

用Category规定了响应条件，

用Data描述了请求数据类型，

至于开启新组件的属性和模式，则交给了Flag。

下面是列举出来的几个Flag：



## 常用的标记位Flags

| Flags                              | 说明/应用场景                                                |
| ---------------------------------- | ------------------------------------------------------------ |
| FLAG_ACTIVITY_NEW_TASK             | 对应singleTask启动模式，其效果和在XML中指定该启动模式相同；  文档摘录：When using this flag, if a task is already running for  the activity you are now starting, then a new activity will not be started;  instead, the current task will simply be brought to the front of the screen  with the state it was last in. See FLAG_ACTIVITY_MULTIPLE_TASK for a flag to  disable this behavior.  翻译：当使用这个flag时，如果task中已经有了你要启动的activity的话，就不再启动一个新的activity了，当前**task**会被带到前台(不管这个activity是否在前台,有可能activity上边还压有别的activity)。如果不想要这种行为，可以用FLAG_ACTIVITY_MULTIPLE_TASK。  比如说原来栈中情况是A,B,C在C中启动D，如果在Manifest.xml文件中给D添加了Affinity的值和C所在的Task中的不一样的话，则会在新标记的Affinity所存在的Task中看是否这个activity已经启动,如果没启动,则直接将activity启动.如果启动了,直接将D所在的task带入到前台;如果是默认的或者指定的Affinity和Task一样的话，就和标准模式一样了启动一个新的Activity. |
| FLAG_ACTIVITY_SINGLE_TOP           | 对应singleTop启动模式，其效果和在XML中指定该启动模式相同；  比如说原来栈中情况是A,B,C,D在D中启动D(加入该flag)，栈中的情况还是A,B,C,D  详见：[Activity的LaunchMode（启动模式）和其的应用场景](onenote:#Activity的LaunchMode（启动模式）和其的应用场景&section-id={0E99CF7C-8F8F-5B47-A618-49B24583F4DE}&page-id={C39DF469-386C-1841-8A7A-0880A3D75E06}&end&base-path=https://d.docs.live.net/f634f5cd1abdf5be/文档/ZhuKun/Android/Activity.one) |
| FLAG_ACTIVITY_CLEAR_TOP            | 具有此标记位的Activity，当它启动时，在同一个任务栈中所有位于它上面的Activity都要出栈。  这个标记位一般会和singleTask模式一起出现，在这种情况下，被启动Activity的实例如果已经存在，那么系统就会回调onNewIntent。  如果被启动的Activity采用standard模式启动，那么它以及连同它之上的Activity都要出栈，系统会创建新的Activity实例并放入栈中；  比如说原来栈中情况是A,B,C,D在D中启动B(加入该flag), 栈中的情况将为A,B但是B会重新onCreate(),并没有执行onNewIntent().如果希望与singleTask效果相同,可以加入FLAG_ACTIVITY_SINGLE_TOP. |
| FLAG_ACTIVITY_EXCLUDE_FROM_RECENTS | 设置完之后，新的activity将不会添加到当前activity列表中，当某些情况下我们不希望用户通过历史列表回到我们的Activity的时候这个标记比较有用。  它等同于在XML中指定Activity的属性android:excludeFromRecents=“true”。 |

## 引用/详解

[Activity四种启动模式](https://www.cnblogs.com/meizixiong/archive/2013/07/03/3170591.html)

[android 任务栈及启动模式](https://blog.csdn.net/liang5630/article/details/41985329)

[android的task任务栈](https://www.cnblogs.com/CSU-PL/p/3794280.html)

[Intent Flag的几种介绍](https://blog.csdn.net/self_study/article/details/48055011)