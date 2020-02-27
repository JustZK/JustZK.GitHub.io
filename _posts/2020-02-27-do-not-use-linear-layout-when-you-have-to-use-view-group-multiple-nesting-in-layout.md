---
layout: post
title: 布局中不得不使用ViewGroup多重嵌套时，不要使用LinearLayout
date: 2020-02-27
Author: Zhu Kun
tags: [View, LinearLayout, ViewGroup]
toc: true
comments: true
---

布局中不得不使用ViewGroup多重嵌套时，不要使用LinearLayout嵌套，改用RelativeLayout，可以有效降低嵌套数。

说明：

Android应用页面上任何一个View都需要经过measure、layout、draw三个步骤才能被正确的渲染。从xml layout的顶部节点开始进行measure，每个子节点都需要向自己的父节点提供自己的尺寸来决定展示的位置，在此过程中可能还会重新measure（由此可能导致measure的时间消耗为原来的2-3倍）。节点所处位置越深，套嵌带来的measure越多，计算就会越费时。这就是为什么扁平的View结构会性能更好。

同时，页面拥上的View越多，measure、layout、draw所花费的时间就越久。要缩短这个时间，关键是保持View的树形结构尽量扁平，而且要移除所有不需要渲染的View。理想情况下，总共的measure，layout，draw时间应该被很好的控制在16ms以内，以保证滑动屏幕时UI的流畅。

要找到那些多余的View（增加渲染延迟的view），可以用Android Studio Monitor里的Hierarachy Viewer工具，可视化的查看所有的view。

**正例：**

```java
<?xml version="1.0" encoding="utf-8"?>
<android.support.constraint.ConstraintLayout>
    <RelativeLayout>
        <TextView/>
        ...
        <ImageView/>
    </RelativeLayout>
</android.support.constraint.ConstraintLayout>
```

**反例：**

```java
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout>
    <LinearLayout>
        <RelativeLayout>
            <TextView/>
            ...
            <ImageView/>
        </RelativeLayout>
    </LinearLayout>
</LinearLayout>
```

多重嵌套导致measure以及layout等步骤耗时过多。

扩展参考：

1.  https://developer.android.com/studio/profile/hierarchy-viewer.html
2.  http://mrpeak.cn/android/2016/01/11/android-performance-ui
3.  https://www.safaribooksonline.com/library/view/high-performance-android/9781491913994/ch04.html#figure-story_tree