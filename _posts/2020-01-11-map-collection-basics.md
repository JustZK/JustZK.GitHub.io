---
layout: post
title: Map集合基础
date: 2020-01-11
Author: Zhu Kun
tags: [Iterable, Map]
toc: true
comments: true
---

## 简介

Map用户保存具有映射关系的数据，因此Map集合里保存着两组数，一组值用户保存Map里的key,另一组值用户保存Map里的value，key和value都可以是任何引用类型的数据。Map的key不允许重复，即同一个Map对象的任何两个key通过equals方法比较总是返回false。

如下图所描述，key和value之间存在单向一对一关系，即通过指定的key,总能找到唯一的、确定的value。从Map中取出数据时，只要给出指定的key，就可以取出对应的value。

![](http://justzk.github.io/images/map-collection-basics/map-collection-basics-2.jpg)

## Map的继承关系

![](http://justzk.github.io/images/map-collection-basics/map-collection-basics-1.png)

## Map集合与Set集合、List集合的关系

**1.与Set集合的关系**

如果 把Map里的所有key放在一起看，它们就组成了一个Set集合（所有的key没有顺序，key与key之间不能重复），实际上Map确实包含了一个keySet()方法，用户返回Map里所有key组成的Set集合。

**2.与List集合的关系**

如果把Map里的所有value放在一起来看，它们又非常类似于一个List：元素与元素之间可以重复，每个元素可以根据索引来查找，只是Map中索引不再使用整数值，而是以另外一个对象作为索引。

**接口中定义的方法**

![](http://justzk.github.io/images/map-collection-basics/map-collection-basics-3.png)

Map中还包括一个内部类Entry，该类封装了一个key-value对。Entry包含如下三个方法：

![](http://justzk.github.io/images/map-collection-basics/map-collection-basics-4.png)

Map集合最典型的用法就是成对地添加、删除key-value对，然后就是判断该Map中是否包含指定key，是否包含指定value，也可以通过Map提供的keySet()方法获取所有key组成的集合，然后使用foreach循环来遍历Map的所有key，根据key即可遍历所有的value。下面程序代码示范Map的一些基本功能：

```java
public class MapTest {
    public static void main(String[] args){
        Day day1 = new Day(1, 2, 3);
        Day day2 = new Day(2, 3, 4);
        Map<String,Day> map = new HashMap<String,Day>();
        //成对放入key-value对
        map.put("第一个", day1);
        map.put("第二个", day2);
        //判断是否包含指定的key
        System.out.println(map.containsKey("第一个"));
        //判断是否包含指定的value
        System.out.println(map.containsValue(day1));
        //循环遍历
        //1.获得Map中所有key组成的set集合
        Set<String> keySet = map.keySet();
        //2.使用foreach进行遍历
        for (String key : keySet) {
            //根据key获得指定的value
            System.out.println(map.get(key));
        }
        //根据key来移除key-value对
        map.remove("第一个");
        System.out.println(map);
    }
}
```

**输出结果：**

```java
true
true
Day [hour=2, minute=3, second=4]
Day [hour=1, minute=2, second=3]
{第二个=Day [hour=2, minute=3, second=4]}
```

[HashMap](onenote:#HashMap&section-id={2E43351A-C506-3B47-8E98-04EEDBD78EAA}&page-id={B28C4B37-65DA-0D40-A2A5-FB00C9EF7757}&end&base-path=https://d.docs.live.net/f634f5cd1abdf5be/文档/ZhuKun/Android/Java/集合/Map.one)

[TreeMap](onenote:#TreeMap&section-id={2E43351A-C506-3B47-8E98-04EEDBD78EAA}&page-id={C6458A0E-2157-2B46-AAD5-4523C992D8A8}&end&base-path=https://d.docs.live.net/f634f5cd1abdf5be/文档/ZhuKun/Android/Java/集合/Map.one)

## Map实现类的性能分析及适用场景

HashMap与Hashtable实现机制几乎一样，

但是HashMap比Hashtable性能更好些。

LinkedHashMap比HashMap慢一点，因为它需要维护一个双向链表。

TreeMap比HashMap与Hashtable慢（尤其在插入、删除key-value时更慢），因为TreeMap底层采用红黑树来管理键值对。

## 适用场景：

一般的应用场景，尽可能多考虑使用HashMap，因为其为快速查询设计的。

如果需要特定的排序时，考虑使用TreeMap。

如果仅仅需要插入的顺序时，考虑使用LinkedHashMap。