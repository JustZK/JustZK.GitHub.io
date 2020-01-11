---
layout: post
title: TreeMap基础
date: 2020-01-11
Author: Zhu Kun
tags: [Iterable, Map, TreeMap]
toc: true
comments: true
---

TreeMap是SortedMap接口的实现类。

TreeMap 是一个**有序的key-value集合**，它是通过红黑树实现的，每个key-value对即作为红黑树的一个节点。

## TreeMap排序方式

TreeMap有两种排序方式，和TreeSet一样。

**自然排序**：TreeMap的所有key必须实现Comparable接口，而且所有的key应该是同一个类的对象，否则会抛出ClassCastException异常。

**定制排序**：创建TreeMap时，传入一个Comparator对象，该对象负责对TreeMap中的所有key进行排序。

## TreeMap中判断两个元素key、value相等的标准

类似于TreeSet中判断两个元素相等的标准，

TreeMap中判断两个**key相等**的标准是：

两个key通过compareTo()方法返回0，TreeMap即认为这两个key是相等的。

TreeMap中判断两个**value相等**的标准是：

两个value通过equals()方法比较返回true。

**注意：**如果使用自定义类作为TreeMap的key，且想让TreeMap良好地工作，则重写该类的equals()方法和compareTo()方法时应保持一致的返回结果：两个key通过equals()方法比较返回true时，它们通过compareTo()方法比较应该返回0。如果两个方法的返回结果不一致，TreeMap与Map接口的规则就会冲突。

除此之外，与TreeSet类似，TreeMap根据排序特性，也添加了一部分新的方法，与TreeSet中的一致。可以参考前面的文章。

## TreeMap的本质

### 红黑树

R-B Tree，全称是Red-Black Tree，又称为“红黑树”，它一种特殊的二叉查找树。红黑树的每个节点上都有存储位表示节点的颜色，可以是红(Red)或黑(Black)。

**红黑树的特性:**

1. 每个节点或者是黑色，或者是红色。
2. 根节点是黑色。
3. 每个叶子节点（NIL）是黑色。 [注意：这里叶子节点，是指为空(NIL或NULL)的叶子节点！]
4. 如果一个节点是红色的，则它的子节点必须是黑色的。
5. 从一个节点到该节点的子孙节点的所有路径上包含相同数目的黑节点。

**注意：**

1. 特性(3)中的叶子节点，是只为空(NIL或null)的节点。
2. 特性(5)，确保没有一条路径会比其他路径长出俩倍。因而，红黑树是相对是接近平衡的二叉树。

![](http://justzk.github.io/images/tree-map-basics/tree-map-basics-1.png)

**红黑树的时间复杂度为: O(log n)**

更多关于红黑树的增删改查操作，可以参考[这篇文章](https://link.jianshu.com?t=http:/www.cnblogs.com/skywang12345/p/3245399.html)。

**可以说TreeMap的增删改查等操作都是在一颗红黑树的基础上进行操作的。**

## TreeMap遍历方式

**遍历TreeMap的键值对**

第一步：根据entrySet()获取TreeMap的“键值对”的Set集合。

第二步：通过Iterator迭代器遍历“第一步”得到的集合。

**遍历TreeMap的键**

第一步：根据keySet()获取TreeMap的“键”的Set集合。

第二步：通过Iterator迭代器遍历“第一步”得到的集合。

**遍历TreeMap的值**

第一步：根据value()获取TreeMap的“值”的集合。

第二步：通过Iterator迭代器遍历“第一步”得到的集合。