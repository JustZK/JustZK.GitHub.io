---
layout: post
title: Thread, Looper和Handler机制(附带HandlerThread与AsyncTask)
date: 2019-12-10
Author: Zhu Kun
tags: [Handler]
toc: true
comments: true
---

## Thread，Looper和Handler的关系

与Windows系统一样，Android也是消息驱动型的系统。引用一下消息驱动机制的四要素：

- 接收消息的“消息队列”
- 阻塞式地从消息队列中接收消息并进行处理的“线程”
- 可发送的“消息的格式”
- “消息发送函数”

与之对应，Android中的实现对应了

- 接收消息的“消息队列” ——【MessageQueue】
- 阻塞式地从消息队列中接收消息并进行处理的“线程” ——【Thread+Looper】
- 可发送的“消息的格式” ——【Message】
- “消息发送函数”——【Handler的post和sendMessage】

**一个Looper类似一个消息泵**：它本身是一个死循环，不断地从MessageQueue中提取Message或者Runnable。

**而Handler可以看做是一个Looper的暴露接口**：向外部暴露一些事件，并暴露sendMessage()和post()函数。

在安卓中，除了UI线程/主线程以外，普通的线程(先不提HandlerThread)是不自带Looper的。想要通过UI线程与子线程通信需要在子线程内自己实现一个Looper。开启Looper分**三步走**：

1. 判定是否已有Looper并Looper.prepare()
2. 做一些准备工作(如暴露handler等)
3. 调用Looper.loop()，线程进入阻塞态

由于每一个线程内最多只可以有一个Looper，所以一定要在Looper.prepare()之前做好判定，否则会抛出java.lang.RuntimeException: Only one Looper may be created per thread。为了获取Looper的信息可以使用两个方法：

- Looper.myLooper() 
- Looper.getMainLooper()

**Looper.myLooper()**：获取当前线程绑定的Looper，如果没有返回null。

**Looper.getMainLooper()**：返回主线程的Looper,这样就可以方便的与主线程通信。

注意：**在Thread的构造函数中调用Looper.myLooper只会得到主线程的Looper**，因为此时新线程还未构造好

下面给一段代码，通过Thread，Looper和Handler实现线程通信：

```java
public class MainActivity extends Activity {
    public static final String TAG = "Main Acticity";
    Button btn = null;
    Button btn2 = null;
    Handler handler = null;
    MyHandlerThread mHandlerThread = null;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        btn = (Button) findViewById(R.id.button);
        btn2 = (Button) findViewById(R.id.button2);
        Log.d("MainActivity.myLooper()", Looper.myLooper().toString());
        Log.d("MainActivity.MainLooper", Looper.getMainLooper().toString());


        btn.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                mHandlerThread = new MyHandlerThread("onStartHandlerThread");
                Log.d(TAG, "创建myHandlerThread对象");
                mHandlerThread.start();
                Log.d(TAG, "start一个Thread");
            }
        });

        btn2.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View view) {
                if (mHandlerThread.mHandler != null) {
                    Message msg = new Message();
                    msg.what = 1;
                    mHandlerThread.mHandler.sendMessage(msg);
                }

            }
        });
    }
}
```

```java
public class MyHandlerThread extends Thread {
    public static final String TAG = "MyHT";

    public Handler mHandler = null;

    @Override
    public void run() {
        Log.d(TAG, "进入Thread的run");
        Looper.prepare();
        mHandler = new Handler(Looper.myLooper()) {
            @Override
            public void handleMessage(Message msg) {
                Log.d(TAG, "获得了message");
                super.handleMessage(msg);
            }
        };
        Looper.loop();
    }
}
```

## HandlerThread 和 AsyncTask

### HandlerThread

Android为了方便对Thread和Handler进行封装，也就是HandlerThread。文档中对HandlerThread的定义是：

> Handy class for starting a new thread that has a looper. The looper can then be used to create handler classes. Note that start() must still be called.

HandlerThread继承自Thread，说白了就是Thread加上一个一个Looper。分析下面的代码:

```java
public class MyHandlerThread extends HandlerThread {
    @Override
    public void run() {
        if (Looper.myLooper == null) {
            Looper.prepare();
        }
        super.run();
    }
}
```

会抛出

> java.lang.RuntimeException: Only one Looper may be created per thread

错误。如果我们把super.run()注释掉就不会有这样的错误。显然在super.run()中进行了Looper的绑定。

### AsyncTask

AsyncTask是谷歌对Thread和Handler的进一步封装，完全隐藏起了这两个概念，而用doInBackground(Params... params)取而代之。但需要注意的是AsyncTask的效率不是很高而且资源代价也比较重，只有当进行一些小型操作时为了方便起见使用。这一点在官方文档写的很清楚:

> AsyncTask is designed to be a helper class around Thread and Handler and does not constitute a generic threading framework. AsyncTasks should ideally be used for short operations (a few seconds at the most.) If you need to keep threads running for long periods of time, it is highly recommended you use the various APIs provided by the java.util.concurrent package such as Executor, ThreadPoolExecutor and FutureTask.
>
> AsyncTask被设计为围绕Thread和Handler的助手类，并不构成通用的线程框架。 理想情况下，AsyncTasks应该用于短操作（最多几秒钟。）如果需要保持线程长时间运行，强烈建议您使用java.util.concurrent包提供的各种API，例如 Executor，ThreadPoolExecutor和FutureTask。