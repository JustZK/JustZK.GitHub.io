---
layout: post
title: Collection集合基础（简介）
date: 2020-01-06
Author: Zhu Kun
tags: [Iterable, Collection]
toc: true
comments: true
---

## **简介**

**Collection**接口是**Set**,**Queue**,**List**的父接口。Collection接口中定义了多种方法可供其子类进行实现，以实现数据操作。由于方法比较多，就偷个懒，直接把JDK文档上的内容搬过来。

## **接口中定义的方法**

![](http://justzk.github.io/images/collection-basics-1.png)

可以看出Collection用法有：添加元素，删除元素，返回Collection集合的个数以及清空集合等。

其中重点介绍iterator()方法，该方法的返回值是Iterator<E>。

## **使用Iterator遍历集合元素**

Iterator接口经常被称作迭代器，它是Collection接口的父接口。但Iterator主要用于遍历集合中的元素。

Iterator接口中主要定义了2个方法：

![](http://justzk.github.io/images/collection-basics-2.png)

下面程序简单示范了通过Iterator对象逐个获取元素的逻辑。

```java
public class IteratorExample {
    public static void main(String[] args){
        //创建集合，添加元素  
        Collection<Day> days = new ArrayList<Day>();
        for(int i =0;i<10;i++){
            Day day = new Day(i,i*60,i*3600);
            days.add(day);
        }
        //获取days集合的迭代器
        Iterator<Day> iterator = days.iterator();
        while(iterator.hasNext()){//判断是否有下一个元素
            Day next = iterator.next();//取出该元素
            //逐个遍历，取得元素后进行后续操作
            .....
        }
    }
}
```

**注意：**当使用Iterator对集合元素进行迭代时，Iterator并不是把集合元素本身传给了迭代变量，而是把集合元素的值传给了迭代变量（就如同参数传递是值传递，基本数据类型传递的是值，引用类型传递的仅仅是对象的引用变量），所以修改迭代变量的值对集合元素本身没有任何影响。

下面的程序演示了这一点：

```java
public class IteratorExample {
    public static void main(String[] args){
        List<String> list =Arrays.asList("java语言","C语言","C++语言");
        Iterator<String> iterator = list.iterator();
        while(iterator.hasNext()){
            String next = iterator.next();//集合元素的值传给了迭代变量，仅仅传递了对象引用。保存的仅仅是指向对象内存空间的地址
            next ="修改后的";
            System.out.println(next);
            
        }
        System.out.println(list);
    }
}
```

输出结果如下：

```
修改后的
修改后的
修改后的
[java语言, C语言, C++语言]
```

## 具体介绍Collection接口的三个子接口：

[Set](onenote:#Set集合&section-id={CCC50C86-DE00-7B49-B6F1-F039EA88C06A}&page-id={FFE1B0CA-8B7A-8546-AD96-D4234DACC269}&end&base-path=https://d.docs.live.net/f634f5cd1abdf5be/文档/ZhuKun/Android/Java/集合/Collection.one)，

[List](onenote:#List集合&section-id={CCC50C86-DE00-7B49-B6F1-F039EA88C06A}&page-id={CDD0BD08-550E-0840-992A-DF6B023DC93B}&end&base-path=https://d.docs.live.net/f634f5cd1abdf5be/文档/ZhuKun/Android/Java/集合/Collection.one)，

[Queue](onenote:#Queue集合&section-id={CCC50C86-DE00-7B49-B6F1-F039EA88C06A}&page-id={DA0F075E-5228-B548-BFFC-0F306299FA1C}&end&base-path=https://d.docs.live.net/f634f5cd1abdf5be/文档/ZhuKun/Android/Java/集合/Collection.one)。