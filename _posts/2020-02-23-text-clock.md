---
layout: post
title: TextClock
date: 2020-02-23
Author: Zhu Kun
tags: [TextClock, TextView, View, UI]
toc: true
comments: true
---

TextClock是在Android 4.2(API 17)后推出的用来替代DigitalClock的一个控件！可以以字符串格式显示当前的日期和时间。

TextClock提供了两种不同的格式， 一种是在24进制中显示时间和日期，另一种是在12进制中显示时间和日期。

TextClock可以切换字体，也可以跟随系统语言来切换繁体，英语等语言格式。

简单看看源码

```java
/**
 * Sets whether this clock should always track the current user and not the user of the
 * current process. This is used for single instance processes like the systemUI who need
 * to display time for different users.
 *
 * @hide
 */
public void setShowCurrentUserTime(boolean showCurrentUserTime) {
    mShowCurrentUserTime = showCurrentUserTime;

    chooseFormat();
    onTimeChanged();
    unregisterObserver();
    registerObserver();
}
```

进入 chooseFormat()

```java
private void chooseFormat() {
    chooseFormat(true);
}
```

```java
private void chooseFormat(boolean handleTicker) {
    final boolean format24Requested = is24HourModeEnabled();

    LocaleData ld = LocaleData.get(getContext().getResources().getConfiguration().locale);

    if (format24Requested) {
        mFormat = abc(mFormat24, mFormat12, ld.timeFormat24);
    } else {
        mFormat = abc(mFormat12, mFormat24, ld.timeFormat12);
    }

    boolean hadSeconds = mHasSeconds;
    mHasSeconds = DateFormat.hasSeconds(mFormat);

    if (handleTicker && mAttached && hadSeconds != mHasSeconds) {
        if (hadSeconds) getHandler().removeCallbacks(mTicker);
        else mTicker.run();
    }
}
```

**is24HourModeEnabled()**,是否使用24进制时间显示，true则进入 mFormat = abc(mFormat24, mFormat12, ld.timeFormat24);

```java
private static CharSequence abc(CharSequence a, CharSequence b, CharSequence c) {
    return a == null ? (b == null ? c : b) : a;
}
```

简单看完，具体使用，无需任何操作，直接在XML布局中

```java
<TextClock
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:fontFamily="sans-serif"
    android:format24Hour="HH:mm"
    android:includeFontPadding="false"
    android:textSize="16sp" />
```

## TextClock表示24小时和12小时的时间

```java
android:format24Hour="HH:mm"
android:format12Hour="hh:mm"
显示：15:10
显示：03:10
分别表示12小时和24小时的表达格式，HH和hh表示当时时间小于10时会在前面补0
如果不需要补0，直接定义
android:format24Hour="H:mm"
android:format12Hour="h:mm"
显示：15:10
显示：3:10
```

## TextClock表示年/月/日/ 24小时制 时/分

```java
	<TextClock
	        android:layout_width="wrap_content"
	        android:layout_height="wrap_content"
	        android:format12Hour="yyyy/MM/dd-hh:mm"/>
	显示：2017/01/06-15:08
	
	如果想显示上午或者下午，或者日期显示格式为2017-01-06
	<TextClock
	        android:layout_width="wrap_content"
	        android:layout_height="wrap_content"
	        android:format12Hour="yyyy-MM-dd-hh:mmaa"/>
	显示：2017-01-06-15:08PM
```

## 英语格式显示日期

```java
	<TextClock
	        android:layout_width="wrap_content"
	        android:layout_height="wrap_content"
	        android:format12Hour="MM/dd/yyyy h:mmaa"/>
	显示：01/06/2017 3:12PM
```

## 月份显示英语月份简写

```java
	<TextClock
	        android:layout_width="wrap_content"
	        android:layout_height="wrap_content"
	        android:format12Hour="MMM/dd/yyyy h:mmaa"/>
	显示：Jan/06/2017 3:13PM
```

## 月份显示英语月份全拼

```java
	<TextClock
	        android:layout_width="wrap_content"
	        android:layout_height="wrap_content"
	        android:format12Hour="MMMM/dd/yyyy h:mmaa"/>
	显示：January/06/2017 3:13PM
```

## 月份显示英语月份全拼，带上星期简写

```java
	<TextClock
	        android:layout_width="wrap_content"
	        android:layout_height="wrap_content"
	        android:format12Hour="E/MMMM/dd/yyyy h:mmaa"/>
	显示：Fri/January/06/2017 3:13PM
```

## 月份显示英语月份全拼，带上星期全拼

```java
	<TextClock
	        android:layout_width="wrap_content"
	        android:layout_height="wrap_content"
	        android:format12Hour="EEEE/MMMM/dd/yyyy h:mmaa"/>
	显示：Friday/January/06/2017 3:13PM
```

## 自定义显示效果

```java
	<TextClock
	        android:layout_width="wrap_content"
	        android:layout_height="wrap_content"
	        android:format24Hour="今天是yyyy/MM/dd,EEEE,当前时间是hh:mmaa"/>
	显示：今天是2017/01/06,Friday,当前时间是15:20PM
	如果系统语言为中文，则显示：今天是2017/01/06,星期五,当前时间是15:20下午
```

## 设置显示字体

```java
	android:fontFamily=”sans-serif-light” ，有多种字体可选。
```

具体的字体设置详见：[自定义字体设置](https://justzk.github.io/custom-font-settings/)