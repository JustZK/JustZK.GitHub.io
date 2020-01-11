---
layout: post
title: HashMap 和 HashTable 以及 CurrentHashMap 的区别
date: 2020-01-11
Author: Zhu Kun
tags: [Iterable, Map, HashMap, HashTable, CurrentHashMap, Synchronized]
toc: true
comments: true
---

## 前题

HashMap 和 HashTable 以及 CurrentHashMap 的区别。

 一般来说，这三个东西基本在面试中 70% 会被问到，

而问的方向也不太一样：

比如初级的问法是讲讲它们之前的区别，

这个我想没什么难度，大多数人还是知道主要核心区别是并发上的处理。此外，内部数据结构的实现、扩容、存取操作这些问题应该是很老生常谈了，这并没有什么好说的，大多数人也都知道。

稍微问的深一点的可能会在下面这些点上出问题：

**哈希碰撞，**

**哈希计算，**

**哈希映射，**

**为什么是头插法，**

**扩容为什么是 2 的幂次等这样的问题。**

## HashMap和HashTable

HashMap和HashTable的区别一种比较简单的回答是：

1. Hashtable和HashMap都实现了Map接口，但是Hashtable的实现是基于Dictionary抽象类
2. HashMap是非线程安全的，HashTable是线程安全的。
3. HashMap的键和值都允许有null存在，而HashTable则都不行。
4. 因为线程安全、哈希效率的问题，HashMap效率比HashTable的要高。

## HashMap和ConcurrentHashMap

1. ConcurrentHashMap是线程安全的和在并发环境下不需要加额外的同步。虽然它不像Hashtable那样需要同样的同步等级(全表锁)，但也有很多实际的用途。
2. 你可以使用Collections.synchronizedMap(HashMap)来包装HashMap作为同步容器，这时它的作用几乎与Hashtable一样,当每次对Map做修改操作的时候都会锁住这个Map对象，而ConcurrentHashMap会基于并发的等级来划分整个Map来达到线程安全，它只会锁操作的那一段数据而不是整个Map都上锁。
3. ConcurrentHashMap有很好的扩展性，在多线程环境下性能方面比做了同步的HashMap要好，但是在单线程环境下，HashMap会比ConcurrentHashMap好一点。

**总结一下**以上两者的区别，它们在**线程安全**、**扩展性**、**同步之间**的区别。

如果是用于缓存的话，ConcurrentHashMap是一个更好的选择，在Java应用中会经常用到。ConcurrentHashMap在读操作线程数多于写操作线程数的情况下更胜一筹。

## ConcurrentHashMap 和 Hashtable 和 Synchronized Map

1. 虽然三个集合类在多线程并发应用中都是线程安全的，但是他们有一个重大的差别，就是他们各自实现线程安全的方式。Hashtable是jdk1的一个遗弃的类，它把所有方法都加上synchronized关键字来实现线程安全。所有的方法都同步这样造成多个线程访问效率特别低。Synchronized     Map与HashTable差别不大，也是在并发中作类似的操作，两者的唯一区别就是Synchronized     Map没被遗弃，它可以通过使用Collections.synchronizedMap()来包装Map作为同步容器使用。
2. 另一方面，ConcurrentHashMap的设计有点特别，表现在多个线程操作上。它不用做外的同步的情况下默认同时允许16个线程读和写这个Map容器。因为其内部的实现剥夺了锁，使它有很好的扩展性。不像HashTable和Synchronized     Map，ConcurrentHashMap不需要锁整个Map，相反它划分了多个段(segments)，要操作哪一段才上锁那段数据。
3. 坦白说，集合类是一个最重要的Java     API，我觉得恰当的使用它们是一种艺术。依我个人经验，我会使用ArrayList这些容器来提高自己的Java程序的性能，而不会去用一些遗弃的容器比如Vector等等，在Java     5之前，Java集合容器有一个很致命的缺陷就是缺乏可扩展性。
4. 同步集合类比如Hashtable和Vector在多线程Java应用里面逐渐成为障碍物；在jdk5后出现一些很好的并发集合，对大容量、低延迟的电子交易系统有很大影响，是快速存取数据的支柱。