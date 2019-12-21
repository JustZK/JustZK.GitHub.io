---
layout: post
title: Fragment中调用getActivity为null的问题
date: 2019-12-21
Author: Zhu Kun
tags: [Fragment]
toc: true
comments: true
---

在使用fragment的时候经常会遇到getActivity()为null的情况。

比如我在一个异步网路请求的回调中调用了getActivity()就会出现空指针问题。

之前解决这个问题，通常都是直接加空指针判断来规避，但是这并没有真正解决问题。

很多人都曾被这个问题所困扰，如果app长时间在后台运行，再次进入app的时候可能会出现crash，而且fragment会有重叠现象。如果系统内存不足、切换横竖屏、app长时间在后台运行，**Activity都可能会被系统回收然后重建，但Fragment并不会随着Activity的回收而被回收，创建的所有Fragment会被保存到Bundle里面**，从而导致Fragment丢失对应的Activity。

下面是FragmentActivity的部分源码

```java
protected void onSaveInstanceState(Bundle outState) {
    super.onSaveInstanceState(outState);
    Parcelable p = mFragments.saveAllState();
    if (p != null) {
        outState.putParcelable("android:support:fragments", p);
    }
}
```

如果从最近使用的应用里面点击我们的应用，系统会恢复之前被回收的Activity，这个时候FragmentActivity在oncreate里面也会做Fragment的恢复，

```java
@Override
protected void onCreate(Bundle savedInstanceState) {
    mFragments.attachActivity(this, mContainer, null);
    // Old versions of the platform didn't do this!
    if (getLayoutInflater().getFactory() == null) {
        getLayoutInflater().setFactory(this);
    }
    super.onCreate(savedInstanceState);
    NonConfigurationInstances nc = (NonConfigurationInstances)
            getLastNonConfigurationInstance();
    if (nc != null) {
        mAllLoaderManagers = nc.loaders;
    }
    if (savedInstanceState != null) {
        Parcelable p = savedInstanceState.getParcelable(FRAGMENTS_TAG);
        mFragments.restoreAllState(p, nc != null ? nc.fragments : null);
    }
    mFragments.dispatchCreate();
}
```

假设我们的页面叫MyActivity（继承自FragmentActivity），其中用到的Fragment叫MyFragment。

出现上面这种情况时，app发生的变化如下：

1、在前面提到的几种情况下系统回收了MyActivity

2、通过onSaveInstanceState保存MyFragment的状态

3、用户再次点击进入app

4、由于MyActivity被回收，系统会重启MyActivity，根据之前保存的MyFragment的状态恢复fragment

5、MyActivity的代码逻辑中，会再次创建新的MyFragment

6、页面出现混乱，覆盖了两层的fragment。假如恢复的MyFragment使用到了getActivity()方法，会报空指针异常

 

对于上面的问题，可以考虑下面这两种解决办法：

  1、不保存fragment的状态：在MyActivity中重写onSaveInstanceState方法，将super.onSaveInstanceState(outState);注释掉，让其不再保存Fragment的状态，达到fragment随MyActivity一起销毁的目的。

  2、重建时清除已经保存的fragment的状态：在恢复Fragment之前把Bundle里面的fragment状态数据给清除。方法如下：

```java
if(savedInstanceState!=null){
    String FRAGMENTS_TAG="Android:support:fragments";
    savedInstanceState.remove(FRAGMENTS_TAG);
}
```

