---
layout: post
title: 如何保证Service不被杀死？
date: 2020-02-25
Author: Zhu Kun
tags: [Service]
toc: true
comments: true
---

一、onStartCommand方式中，返回START_STICKY或者START_REDELIVER_INTENT 

- START_STICKY：如果返回START_STICKY，表示Service运行的进程被Android系统强制杀掉之后，Android系统会将该Service依然设置为started状态（即运行状态），但是不再保存onStartCommand方法传入的intent对象
- START_NOT_STICKY：如果返回START_NOT_STICKY，表示当Service运行的进程被Android系统强制杀掉之后，不会重新创建该Service
- START_REDELIVER_INTENT：如果返回START_REDELIVER_INTENT，其返回情况与START_STICKY类似，但不同的是系统会保留最后一次传入onStartCommand方法中的Intent再次保留下来并再次传入到重新创建后的Service的onStartCommand方法中

二、提高Service的优先级     在AndroidManifest.xml文件中对于intent-filter可以通过android:priority =     "1000"这个属性设置最高优先级，1000是最高值，如果数字越小则优先级越低，同时适用于广播；

三、在onDestroy方法里重启Service     当service走到onDestroy()时，发送一个自定义广播，当收到广播时，重新启动service；

四、提升Service进程的优先级     进程优先级由高到低：前台进程 一 可视进程 一 服务进程 一 后台进程 一 空进程     可以使用startForeground将service放到前台状态，这样低内存时，被杀死的概率会低一些；

五、系统广播监听Service状态

六、将APK安装到/system/app，变身为系统级应用

**注意：**以上机制都不能百分百保证Service不被杀死，除非做到系统白名单，与系统同生共死