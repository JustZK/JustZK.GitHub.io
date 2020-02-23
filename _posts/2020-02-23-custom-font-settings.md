---
layout: post
title: 自定义字体设置
date: 2020-02-23
Author: Zhu Kun
tags: [Font, View, UI]
toc: true
comments: true
---

## typeface、fontFamily、textStyle介绍

### 一、typeface 字体

typeface 枚举类型，值如下

- normal（默认字体）
- sans （无衬线字体）
- serif（有衬线字体）
- monospace（等宽字体）

**sans 和 serif 介绍：**
 在西方国家罗马字母阵营中，字体分为两大种类：Serif和Sans Serif。
 serif（有衬线字体）：在字的笔划开始及结束的地方有额外的装饰，而且笔划的粗细会因直横的不同而有不同。
 sans serif（无衬线字体）：没有额外的装饰，笔划粗细大致差不多

![](http://justzk.github.io/images/custom-font-settings/custom-font-settings-1.jpg)

**monospace 介绍：**
 打字机体虽然也属于Sans Serif，但由于是等宽字体，所以另外独立出Monospace这一种类
 monospace（等宽字体）：指每个字符宽度都一样。优点容易对齐，经常用来显示代码。

### 二、fontFamily 字型家族

什么是字型家族？它和typeface 的区别？要想了解首先得了解下什么是 typeface、font
 typeface：字体，是一个抽象的总体概念（它是一款“设计”），例如：宋体、楷体
 font：是指特定尺寸、特定字重、字偶间距等信息的一种 Typeface 的具体实现
 fontFamily 是font的一个集合

**使用提示可知 fontFamily 值如下**

- sans-serif
- sans-serif-condensed
- sans-serif-smallcaps
- serif
- serif-monospace
- monospace
- casual
- cursive

### 三、textStyle 字体样式

**textStyle 标记类型，值如下**

- normal（默认字体）
- bold （加粗）
- italic（斜体）

### 四、三者关系

查看TextView源码(API 27)可知：TextView构造方法大致如下

```java
public TextView(Context context, AttributeSet attrs, int defStyleAttr, int defStyleRes) {
...
    setTypefaceFromAttrs(fontTypeface, fontFamily, typefaceIndex, styleIndex);
...
}
```

**setTypefaceFromAttrs方法**

```java
/**
 *
 * @param fontTypeface 要设置的 Typeface
 * @param familyName 要设置的 fontFamily
 * @param typefaceIndex 要设置的 typeface
 * @param styleIndex 要设置的 style
 */
private void setTypefaceFromAttrs(Typeface fontTypeface, String familyName,int typefaceIndex, int styleIndex) {
    Typeface tf = fontTypeface;
    if (tf == null && familyName != null) {
        // 有fontFamily时，用fontFamily
        tf = Typeface.create(familyName, styleIndex);
    } else if (tf != null && tf.getStyle() != styleIndex) {
        tf = Typeface.create(tf, styleIndex);
    }
    if (tf != null) {
        setTypeface(tf);
        return;
    }
    switch (typefaceIndex) {
        case SANS:
            tf = Typeface.SANS_SERIF;
            break;

        case SERIF:
            tf = Typeface.SERIF;
            break;

        case MONOSPACE:
            tf = Typeface.MONOSPACE;
            break;
    }
    // 其它都用typeface
    setTypeface(tf, styleIndex);
}
```

**总结：**

1.typeface、fontFamily：都是设置字体，都设置时优先使用 fontFamily。
 2.textStyle：设置字体的样式

## 设置自定义字体

### 方式1：直接设置

**1.把字体ttf文件放到assets/fonts目录下，没有此目录手动创建**

![](http://justzk.github.io/images/custom-font-settings/custom-font-settings-2.png)

**2.设置**

```java
Typeface typeface = Typeface.createFromAsset(getAssets(), "fonts/aa.ttf");
textView.setTypeface(typeface);
```

**3.优化**

```java
Override
public Typeface getTypeface() {
    return Typeface.createFromAsset(getContext().getAssets(), "fonts/aa.ttf");
}
```

使用优化：继承自TextView，重写getTypeface()，返回自定义typeface
 typeface创建优化：可用map封装后进行获取，代码略

**总结：**

适用于：指定TextView

### 方式2：反射设置

**1.把系统的typeface替换为自定义的**

```java
Typeface typeFace = Typeface.createFromAsset(getAssets(), "fonts/aa.ttf");
try {
    // xml属性值与Typeface属性值对应
    // normal      Typeface.DEFAULT
    // sans        Typeface.SANS_SERIF
    // serif       Typeface.SERIF
    // monospace   Typeface.MONOSPACE
    Field field = Typeface.class.getDeclaredField("SERIF" );
    field.setAccessible( true);
    field.set( null, typeFace );
} catch (NoSuchFieldException e) {
    e.printStackTrace();
} catch (IllegalAccessException e) {
    e.printStackTrace();
}
```

**2.设置typeface值为 sans、 serif、 monospace其中一种**，可在TextView、Activity、Application里面的xml样式里面设置

```java
android:typeface="serif"
```

**原理介绍：**

1. xml设置完typeface为指定值后，最终xml会解析为代码执行（例如：设置android:typeface="serif"最终会解析调用setTypeface(Typeface.SERIF)）
2. 预先把Typeface的某个系统字体通过反射强制替换自定义的Typeface（例如：把Typeface.SERIF强制替换为自定义的字体）
3. xml中设置typeface为已替换的字体即可（例如：android:typeface="serif"）举例说明：例如：xml设置字体android:typeface="serif"，代码设置的字体是Typeface.SERIF，可是Typeface.SERIF里面的内容是强制替换后的自定义的字体

**总结：**

适用于：App字体样式不多的情况，因为你能替换的就SANS_SERIF、SERIF、MONOSPACE这几个值

 

### 方式3：fontFamily设置

**1.把字体ttf文件放到res/fonts目录下，没有此目录手动创建或使用Android studio创建**

![](http://justzk.github.io/images/custom-font-settings/custom-font-settings-3.png)

**2.设置**

```java
1.TextView设置
android:fontFamily="@font/aa"
2.activity、application样式里设置
<item name="android:fontFamily">@font/aa</item>
```

**3.兼容**
 因为 fontFamily API16 新增，所以要使用低版本的兼容库 com.android.support:appcompat-v7:26.0.0 以上

```java
1.TextView设置
app:fontFamily="@font/aa"
2.activity、application样式里设置
<item name="fontFamily">@font/aa</item>
```

**总结：**

适用于：各种情况

## 设置下载字体（了解）

**为什么要下载字体：**最主要的原因就是可以减少**apk的体积**，因为字体放到apk内会增大apk体积，通过下载就不会。
 **使用范围：**此功能是Android 8.0（API26）新增，但是兼容库已兼容到API14，所以可放心使用
 **原理：**所有的app都可以从字体提供app（例如：Google Play Services）里获取字体，字体提供app会把字体缓存到本地，所以如果多个app用相同的字体，它们则会共用同一个字体文件，优点：减少手机内存和磁盘空间，提高整体系统的运行状况。

**使用前提**：手机上必须有一个字体提供app，目前了解的国内没有，国外的只有Google Play Services（版本11及以上），由于目前国内很少安装Google Play Services，所以国内此功能目前没有用

### 方式1：Android studio创建可下载字体

Android studio 3.0及以上可下载，以下以Android studio 3.1.3介绍

1.选择more Fonts

![](http://justzk.github.io/images/custom-font-settings/custom-font-settings-4.png)

2.选择创建可下载字体

![](http://justzk.github.io/images/custom-font-settings/custom-font-settings-5.png)

3.点击ok，则会在font目录下创建对应的字体xml配置文件，内容如下：

![](http://justzk.github.io/images/custom-font-settings/custom-font-settings-6.png)

创建证书文件font_certs.xml

![](http://justzk.github.io/images/custom-font-settings/custom-font-settings-7.png)

**参数介绍：**

- fontProviderAuthority     字体提供app的权限
- fontProviderPackage     字体提供app的包名，用于获取是哪个字体提供app
- fontProviderQuery     字体提供app的字体名称，用于获取是哪个字体
- fontProviderCerts     字体提供app的证书
             

**总结：**Android studio 生成的字体使用了兼容库，已兼容低版本；证书的生成由Android studio 自动生成，不用管理

 

4.第3步点击ok后，也会在AndroidManifest的application节点下生成如下内容：

![](http://justzk.github.io/images/custom-font-settings/custom-font-settings-8.png)

创建预加载字体文件preloaded_fonts.xml

![](http://justzk.github.io/images/custom-font-settings/custom-font-settings-9.png)

**介绍：**以上是声明使用预加载字体，如果没有使用预加载字体，则会在第一次设置可下载字体的时候，会先下载字体然后设置，这样会增加布局绘制时间，所以在AndroidManifest下声明要预加载的字体，则会在app启动时就加载即可解决这个问题，如果下载出现超时或无网等失败则会使用默认字体

 

### 方式2：代码创建可下载字体

以下使用了兼容库，参数和上面一致

```java
// 创建请求
FontRequest request = new FontRequest(
        "com.google.android.gms.fonts",
        "com.google.android.gms",
        query,
        R.array.com_google_android_gms_fonts_certs);

// 请求回调
FontsContractCompat.FontRequestCallback callback = new FontsContractCompat.FontRequestCallback() {
    @Override
    public void onTypefaceRetrieved(Typeface typeface) {
        // 成功
        mDownloadableFontTextView.setTypeface(typeface);
    }

    @Override
    public void onTypefaceRequestFailed(int reason) {

    }
};
// 请求
FontsContractCompat.requestFont(MainActivity.this, request, callback, getHandlerThreadHandler());
```

**总结：**适用于：不适用国内