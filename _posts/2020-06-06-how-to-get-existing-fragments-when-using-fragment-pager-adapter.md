---
layout: post
title: FragmentPagerAdapter/FragmentStatePagerAdapter获取Fragment实例/引用
date: 2020-06-06
Author: Zhu Kun
tags: [Fragment, FragmentPagerAdapter, FragmentStatePagerAdapter]
toc: true
comments: true
---

**注意**：在这个答案中，我将参考FragmentPagerAdapter及其源代码。但是一般的解决方案也应该适用于FragmentStatePagerAdapter。

如果您正在阅读此文章，您已经知道FragmentPagerAdapter/ FragmentStatePagerAdapter是您创建的ViewPager的Fragments适配器，

当在Activity重新创建（无论是设备旋转还是系统杀死您的App来重新获得内存）之后，这些Fragments不会再重新创建，而是[从FragmentManager中检索得到实例](http://grepcode.com/file/repository.grepcode.com/java/ext/com.google.android/android/5.0.2_r1/android/support/v4/app/FragmentPagerAdapter.java?av=f#90)。

现在您的Activity需要参考上面的方法来获取Fragments实例。

你在创建Fragments没有一个id或者tag来标记它，应为这是FragmentPagerAdapter [在内部设置它们的](http://grepcode.com/file/repository.grepcode.com/java/ext/com.google.android/android/5.0.2_r1/android/support/v4/app/FragmentPagerAdapter.java?av=f#100)

所以问题是如何在没有这些信息的情况下获得对它们的实例/引用

我在此类似问题上看到的许多解决方案都依赖于Fragment通过调用FragmentManager.findFragmentByTag()和模仿[内部创建的标记"android:switcher:" + viewId + ":" + id](http://grepcode.com/file/repository.grepcode.com/java/ext/com.google.android/android/5.0.2_r1/android/support/v4/app/FragmentPagerAdapter.java?av=f#173)

问题是您依赖Android内部源代码，众所周知，内部源代码不能保证永远保持不变。

Google的Android工程师可以轻松地决定更改tag结构，这会破坏你的代码，使您无法找到现有的实例/引用Fragments。



## 第一种方法：重新instantiateItem - 不使用 tag

这是一个简单的示例，该示例说明如何获取对的Fragments返回引用FragmentPagerAdapter，而不依赖于上的内部tags集合Fragments。关键是要修改继承[instantiateItem()](http://grepcode.com/file/repository.grepcode.com/java/ext/com.google.android/android/5.0.2_r1/android/support/v4/app/FragmentPagerAdapter.java?av=f#83)和保存Fragments的实例/引用在这个方法里面，而不是在getItem()。

```java
public class SomeActivity extends Activity {
    private FragmentA m1stFragment;
    private FragmentB m2ndFragment;

    // other code in your Activity...

    private class CustomPagerAdapter extends FragmentPagerAdapter {
        // other code in your custom FragmentPagerAdapter...

        public CustomPagerAdapter(FragmentManager fm) {
            super(fm);
        }

        @Override
        public Fragment getItem(int position) {
            // Do NOT try to save references to the Fragments in getItem(),
            // because getItem() is not always called. If the Fragment
            // was already created then it will be retrieved from the FragmentManger
            // and not here (i.e. getItem() won't be called again).
            switch (position) {
                case 0:
                return new FragmentA();
                case 1:
                return new FragmentB();
                default:
                // This should never happen. Always account for each position above
                return null;
            }
        }

        // Here we can finally safely save a reference to the created
        // Fragment, no matter where it came from (either getItem() or
        // FragmentManger). Simply save the returned Fragment from
        // super.instantiateItem() into an appropriate reference depending
        // on the ViewPager position.
        @Override
        public Object instantiateItem(ViewGroup container, int position) {
            Fragment createdFragment = (Fragment) super.instantiateItem(container, position);
            // save the appropriate reference depending on position
            switch (position) {
                case 0:
                    m1stFragment = (FragmentA) createdFragment;
                    break;
                case 1:
                    m2ndFragment = (FragmentB) createdFragment;
                    break;
            }
            return createdFragment;
        }
    }

    public void someMethod() {
        // do work on the referenced Fragments, but first check if they
        // even exist yet, otherwise you'll get an NPE.

        if (m1stFragment != null) {
            // m1stFragment.doWork();
        }

        if (m2ndFragment != null) {
            // m2ndFragment.doSomeWorkToo();
        }
    }
}
```

## 第二种方法：重新instantiateItem - 使用 tag

**注意**：这不适用于FragmentStatePagerAdapter，因为在创建Fragments没有设置tags

```java
@Override
public Object instantiateItem(ViewGroup container, int position) {
    Fragment createdFragment = (Fragment) super.instantiateItem(container, position);
    // get the tags set by FragmentPagerAdapter
    switch (position) {
        case 0:
            String firstTag = createdFragment.getTag();
            break;
        case 1:
            String secondTag = createdFragment.getTag();
            break;
    }
    // ... save the tags somewhere so you can reference them later
    return createdFragment;
}
```

## 最后

**不要忘记**，在你的使用的Activity中，你不想持有Fragments实例/引用，你必须考虑将它设置为null

**另外**，如果您正在使用FragmentStatePagerAdapter，则您不想保留对您的硬引用，Fragments可能有很多硬引用，而硬引用将不必要地将它们保留在内存中。可以将Fragment引用保存在WeakReference变量而不是标准引用中。像这样：

```java
WeakReference<Fragment> m1stFragment = new WeakReference<Fragment>(createdFragment);
// ...and access them like so
Fragment firstFragment = m1stFragment.get();
if (firstFragment != null) {
    // reference hasn't been cleared yet; do work...
}
```



Copy from [How to get existing fragments when using FragmentPagerAdapter](https://stackoverflow.com/questions/14035090/how-to-get-existing-fragments-when-using-fragmentpageradapter)