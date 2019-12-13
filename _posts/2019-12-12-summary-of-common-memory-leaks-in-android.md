---
layout: post
title: Android常见的内存泄漏汇总
date: 2019-12-12
Author: Zhu Kun
tags: [Memory Leaks]
toc: true
comments: true
---

## 集合类泄漏

先看一段代码

```java
List<Object> objectList = new ArrayList<>();
for (int i = 0; i < 10; i++) {
	Object o = new Object();
	objectList.add(o);
	o = null;
}
```

上面的实例，虽然在循环中把引用**o**释放了，但是它被添加到了**objectList**中，所以**objectList**也持有对象的引用，此时该对象是无法被**GC**的。因此对象如果添加到集合中，还必须从中删除，最简单的方法

```java
//释放objectList
objectList.clear();
objectList=null;
```

## 单例造成的内存泄漏

由于单例的静态特性使得其生命周期跟应用的生命周期一样长，所以如果使用不恰当的话，很容易造成内存泄漏。比如下面一个典型的例子。

```java
private static SingleInstanceClass instance;

private android.content.Context mContext;
private SingleInstanceClass(Context context) {
	this.mContext = context;
}

public SingleInstanceClass getInstance(Context context) {
  if (instance == null) { 
    instance = new SingleInstanceClass(context);
  }
  return instance; 
}
```

正如前面所说，静态变量的生命周期等同于应用的生命周期，此处传入的**Context**参数便是祸端。如果传递进去的是**Activity**或者**Fragment**，由于单例一直持有它们的引用，即便**Activity**或者**Fragment**销毁了，也不会回收其内存。特别是一些庞大的Activity非常容易导致OOM。

 

正确的写法应该是传递**Application**的Context，因为**Application**的生命周期就是整个应用的生命周期，所以没有任何的问题。

```java
private static SingleInstanceClass instance;

private Context mContext;

private SingleInstanceClass(Context context) {
	// 使用Application 的context
	this.mContext = context.getApplicationContext();
}

public SingleInstanceClass getInstance(Context context) {
	if (instance == null) {
		instance = new SingleInstanceClass(context);
	}
	return instance;
}
```

**或者：**

```java
//在Application中定义获取全局的context的方法
/**
 * 获取全局的context
 * @return 返回全局context对象
 */
public static Context getContext(){
	return context;
}


public class SingleInstanceClass {

	private static SingleInstanceClass instance;

	private Context mContext;

	private SingleInstanceClass() {
		mContext = MyApplication.getContext;
	}

	public SingleInstanceClass getInstance() {
		if (instance == null) {
			instance = new SingleInstanceClass();
		}
		return instance;
	}
}
```

## 匿名内部类/非静态内部类和异步线程

### 非静态内部类创建静态实例造成的内存泄漏

我们都知道非静态内部类是默认持有外部类的引用的，如果在内部类中定义单例实例，会导致外部类无法释放。

如下面代码：

```java
public class TestActivity extends AppCompatActivity {
	public static InnerClass innerClass = null;

	@Override
	protected void onCreate(@Nullable Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		if (innerClass == null)
			innerClass = new InnerClass();
	}
	private class InnerClass {
		//...
	}
}
```

当**TestActivity**销毁时，因为**innerClass**生命周期等同于应用生命周期，但是它又持有TestActivity的引用，因此导致内存泄漏。

正确做法应将该**内部类设为静态内部类或将该内部类抽取出来封装成一个单例**，如果需要使用Context，请按照上面推荐的使用Application 的 Context。当然，Application 的 context 不是万能的，所以也不能随便乱用，对于有些地方则必须使用 Activity 的 Context，对于Application，Service，Activity三者的Context的应用场景如下：

![](https://justzk.github.io/images/summary-of-common-memory-leaks-in-android/summary-of-common-memory-leaks-in-android-1.png)

### 匿名内部类

android开发经常会继承实现Activity/Fragment/View，此时如果你使用了匿名类，并被异步线程持有了，那要小心了，如果没有任何措施这样一定会导致泄露。

如下代码：

```java
public class TestActivity extends AppCompatActivity {
	//....

	private Runnable runnable=new Runnable() {
		@Override
		public void run() {

		}
	};

	@Override
	protected void onCreate(@Nullable Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		//......
	}

}
```

上面的**runnable**所引用的匿名内部类持有**TestActivity**的引用，当将其传入异步线程中，线程与Activity生命周期不一致就会导致内存泄漏。

### Handler造成的内存泄漏

Handler造成内存泄漏的根本原因是因为，**Handler的生命周期与Activity或者View的生命周期不一致**。Handler属于TLS（Thread Local Storage）生命周期同应用周期一样。看下面的代码：

```java
public class TestActivity extends AppCompatActivity {
	private Handler mHandler = new Handler() {
		@Override
		public void dispatchMessage(Message msg) {
			super.dispatchMessage(msg);
		}
	};

	@Override
	protected void onCreate(@Nullable Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		mHandler.postDelayed(new Runnable() {
			@Override
			public void run() {
				//do your things
			}
		}, 60 * 1000 * 10);

		finish();
	}
}
```

在该**TestActivity**中声明了一个延迟10分钟执行的消息 **Message**，**mHandler**将其 **push** 进了消息队列 **MessageQueue** 里。当该 **Activity** 被**finish**()掉时，延迟执行任务的**Message**还会继续存在于主线程中，它持有该 Activity 的Handler引用，所以此时 finish()掉的 Activity 就不会被回收了从而造成内存泄漏（因 Handler 为非静态内部类，它会持有外部类的引用，在这里就是指**TestActivity**）。

 

修复方法：采用**内部静态类以及弱引用方案**。代码如下：

```java
public class TestActivity extends AppCompatActivity {
	private MyHandler mHandler;

	private static class MyHandler extends Handler {
		private final WeakReference<TestActivity> mActivity;

		public MyHandler(TestActivity activity) {
			mActivity = new WeakReference<>(activity);
		}

		@Override
		public void dispatchMessage(Message msg) {
			super.dispatchMessage(msg);
			TestActivity activity = mActivity.get();
			//do your things
		}
	}

	private static final Runnable mRunnable = new Runnable() {
		@Override
		public void run() {
			//do your things
		}
	};

	@Override
	protected void onCreate(@Nullable Bundle savedInstanceState) {
		super.onCreate(savedInstanceState);
		mHandler = new MyHandler(this);
		mHandler.postAtTime(mRunnable, 1000 * 60 * 10);

		finish();
	}

}
```

需要注意的是：**使用静态内部类 + WeakReference 这种方式，每次使用前注意判空**。

前面提到了 WeakReference，所以这里就简单的说一下 Java 对象的几种引用类型。

Java对引用的分类有 Strong reference, SoftReference, WeakReference, PhatomReference 四种。

![](https://justzk.github.io/images/summary-of-common-memory-leaks-in-android/summary-of-common-memory-leaks-in-android-2.png)

继续回到主题。前面所说的，创建一个静态Handler内部类，然后对 Handler 持有的对象使用弱引用，这样在回收时也可以回收 Handler 持有的对象，但是这样做虽然避免了Activity泄漏，不过Looper 线程的消息队列中还是可能会有待处理的消息，所以我们在**Activity的 Destroy 时或者 Stop 时应该移除消息队列 MessageQueue 中的消息**。

下面几个方法都可以移除 Message：

```java
public final void removeCallbacks(Runnable r);
public final void removeCallbacks(Runnable r, Object token);
public final void removeCallbacksAndMessages(Object token);
public final void removeMessages(int what);
public final void removeMessages(int what, Object object);
```

## 尽量避免使用 staic 成员变量

如果成员变量被声明为 static，那我们都知道其生命周期将与整个app进程生命周期一样。

这会导致一系列问题，如果你的app进程设计上是长驻内存的，那即使app切到后台，这部分内存也不会被释放。按照现在手机app内存管理机制，占内存较大的后台进程将优先回收，意味着如果此app做过进程互保保活，那会造成app在后台频繁重启。就会出现一夜时间手机被消耗空了电量、流量，这样只会被用户弃用。

这里修复的方法是：

不要在类初始时初始化静态成员。可以考虑lazy初始化。

架构设计上要思考是否真的有必要这样做，尽量避免。如果架构需要这么设计，那么此对象的生命周期你有责任管理起来。

### 避免 override finalize()：

1. finalize     方法被执行的时间不确定，不能依赖与它来释放紧缺的资源。时间不确定的原因是： 虚拟机调用GC的时间不确定以及Finalize     daemon线程被调度到的时间不确定。
2. finalize     方法只会被执行一次，即使对象被复活，如果已经执行过了 finalize 方法，再次被 GC 时也不会再执行了，原因是：含有 finalize 方法的     object 是在 new 的时候由虚拟机生成了一个 finalize reference 在来引用到该Object的，而在 finalize     方法执行的时候，该 object 所对应的 finalize Reference 会被释放掉，即使在这个时候把该 object     复活(即用强引用引用住该 object )，再第二次被 GC 的时候由于没有了 finalize reference 与之对应，所以     finalize 方法不会再执行。
3. 含有Finalize方法的object需要至少经过两轮GC才有可能被释放。

