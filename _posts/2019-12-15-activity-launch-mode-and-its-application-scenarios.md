---
layout: post
title: Activity的LaunchMode（启动模式）和其的应用场景
date: 2019-12-15
Author: Zhu Kun
tags: [Activity, LaunchMode, AndroidManifest]
toc: true
comments: true
---

## 四种启动模式、应用场景

**作用概述**：规定是否新建activity,是否公用task（task：应用启动时，会创建一个对应的task。task主要负责对activity进行管理。）

**使用方法**：我们可以在AndroidManifest.xml配置<activity>的android:launchMode属性为以上四种之一即可,默认情况下为标准模式。

|           LaunchMode           | 说明/应用场景                                                |
| :----------------------------: | :----------------------------------------------------------- |
|     standard  （标准模式）     | 这是系统默认的启动模式。标准模式下，只要用intent  跳转，系统会自动创建一个新的activity实例，不管这个实例是否已经存在，此模式的Activity默认会进入启动它的Activity所属的任务栈中并且放在栈顶。按下回退键，系统会从顶部开始一个一个清除Activity。 |
|   singleTop （栈顶复用模式）   | 如果新Activity已**经位于任务栈的栈顶**，那么此Activity不会被重新创建，同时会回调**onNewIntent**方法； 如果新Activity实例**已经存在但不在栈顶**，那么Activity依然会被重新创建；  singleTop模式,只在当前任务栈中生效；  如果通过startActivityForResult启动一个设置了singleTop的activity,singleTop模式将无效；  **onNewIntent()使用Tips**     方法体中需手动调用setIntent(intent),否则之后的getIntent()获取的都是旧的intent对象;          被onNewIntent方式打开的activity,对生命周期的影响：    1、 之前activity是resume状态,onNewIntent()后只会调用onResume()方法   2、 否则按照 onNewIntent->onRestart->onStart->onResume->  **应用场景**  这种启动模式的用例之一就是搜索功能。假设我们创建了一个搜索框，点击搜索的时候将导航到一个显示搜索结果列表的SearchActivity中，为了更好的用户体验，这个搜索框一般也会被放到SearchActivity中，这样用户想要再次搜索就不需要按返回键。  想像一下，如果每次显示搜索结果的时候我们都启动一个新的activity，10次搜索10个activity，那样当我们想返回最初的那个activity的时候需要按10次返回。  所以我们应该这样，如果栈顶已经有一个SearchActivity，我们将Intent发送给现有的activity，让它来更新搜索结果。这样就只会有一个在栈顶的SearchActivity，只需点一次back就可以回到之前的activity。  不管怎样，singleTop和它的调用者处在一个任务中。如果你想要让intent发送给另一个任务中处于栈顶的Activity，是不行的。  而当Intent来自于另外一个应用的时候，新的Activity的启动方式和standard模式是一致的。 |
|  singleTask （栈内复用模式）   | 只要Activity在一个任务栈中存在，则清除此activty旧实例以上的所有activity，并回调**onNewIntent**方法，此模式启动Activity A，系统首先会寻找是否存在A想要的任务栈；  如果不存在，就会重新创建一个任务栈，然后把创建好A的实例放到栈中；  启动一个singleTask模式的activity,会首先在系统中找与它的**taskAffinity**属性一致的任务栈,（如果没有对activity设置该属性的话,默认为application的**taskAffinity**，如果application也没有设置,则为app的包名  ）     **先找task**    1、没有特别指定taskAffinity,则为当前的task  2、如果指定了taskAffinity,先在系统中查找task,如果找不到则创建一个新的task,将activity作为root放置其中.     **启动Activity**    如果第一步中的task中已经有了这个activity的实例，则将其显示(将task中该activity上层的activity都pop出任务栈)，同时intent将被通过onNewIntent()发送.    **对设置为singleTask的activity的总结**     并不是一定会在新的任务栈中打开，(具体要根据taskAffinity看系统中是否已经有这个任务栈了)。   如果需要在新的任务栈中启动,就需要为activity设置独立的taskAffinity。   如果任务栈中已存在该activity,那么会将上层的所有activity弹出。   如果当前activity是在新的任务栈中打开的话，那么之后在该activity中通过默认方式启动的activity都在这个新的任务栈(这个跟我们接下里要讲的singleInstance有区别)。   如果是在新的任务栈中启动的话,最近任务列表(android的多任务键按下后)会有两个,可选择返回至相应的任务栈。   当作为startActivityForResult启动的目标时       ：    4.x版本：会立刻在上个activity中onActivityResult中返回一个为cancel的resultCode.(不管新的activity是否是在新的任务栈中启动)    5.x版本：不管是否定义了taskAffinity,都会把将要被启动的activity的启动模式忽略,onActivityResult方法会正常回调    **应用场景**  该模式的使用场景多类似于邮件客户端的收件箱或者社交应用的时间线Activity(朋友圈) |
| singleInstance  （单实例模式） | 这是一种加强的singleTask模式，具有此种模式的Activity只能单独地位于一个任务栈中，且此任务栈中只有唯一一个实例；  注意：5.0以上的部分手机，一个应用存在多个栈的情况下，切到后台，后台会出现多个后台同一个应用的界面，这是因为界面不在同一个栈中。  当作为startActivityForResult启动的目标时(下文中的它都是指被启动的activity)  4.x版本.在新的任务栈中启动,并立刻在启动它的activity中的onActivityResult中返回一个为cancel的**resultCode.****singleInstance的特点还在**  5.x版本.并不会在新的任务栈中启动,而是直接在当前任务栈启动(会出现多个实例),启动它的activity的onActivityResult方法会在它关闭后,正常回调。**重点是被它开启的activity将运行在另外一个新的任务栈中****.**  **应用场景**  呼叫来电界面 InCallScreen |

## 备注一

**问题：**启动模式为**singletask**，activity无法获取到intent的传值问题？

**原因：**

1、启动一个为启动过的activity ，会获取到新的 intent。也就是说就是启动模式设置为：singletask，第一次启动界面是传递的数据是可以接受到的。

2、当此界面再次被启动时，因为他的启动模式是：singletask，所以不会重新创建一个activity，而是启动旧的activity实例，通过getIntent()方法获取到的 intent 也是旧的Intent,没有携带新的数据。所以无法获取到数据。

**解决方法：**重写这个方法，并且把调用setIntent(intent)，对Intent进行更新。此方法在activity（启动模式为：singletask）的界面第二次启动时，在onresume（）方法之前被回调。

```java
@Override
protected void onNewIntent(Intent intent) {
    // TODO Auto-generated method stub
    super.onNewIntent(intent);
    setIntent(intent);
}
```

## 引用/详解

[Activity的任务栈Task以及启动模式与Intent的Flag详解](onenote:#Activity的任务栈Task以及启动模式与Intent的Flag详解&section-id={280D5031-EED2-604E-A8FF-85718786BF3C}&page-id={9E6EB08D-028D-D342-8074-1E0B5C7E5A1F}&end&base-path=https://d.docs.live.net/f634f5cd1abdf5be/文档/zhukun19950109@live.com 的笔记本/Android/Activity.one)

[Activity四种启动模式](https://www.cnblogs.com/meizixiong/archive/2013/07/03/3170591.html)

[android 任务栈及启动模式](https://blog.csdn.net/liang5630/article/details/41985329)

[android的task任务栈](https://www.cnblogs.com/CSU-PL/p/3794280.html)

[Intent Flag的几种介绍](https://blog.csdn.net/self_study/article/details/48055011)