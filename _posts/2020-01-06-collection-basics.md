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

### Set集合

Set集合与Collection集合基本相同，没有提供任何额外的方法。实际上Set就是Collection，只是行为略有不同（Set不允许包含重复元素）。

Set集合不允许包含相同的元素，如果试图把两个相同的元素加入同一个Set集合中，则添加操作失败，add()方法返回false，且新元素不会被加入。

**具体详见**：[由浅入深理解java集合(二)——集合 Set](https://www.jianshu.com/p/9081017a2d67)

### List集合

#### 简介

1. List集合代表一个元素有序、可重复的集合，集合中每个元素都有其对应的顺序索引。
2. List集合允许使用重复元素，可以通过索引来访问指定位置的集合元素 。
3. List集合默认按元素的添加顺序设置元素的索引，例如第一个添加的元素索引为0，第二个添加的元素索引为1......
4. List作为Collection接口的子接口，可以使用Collection接口里的全部方法。
5. 而且由于List是有序集合，因此List集合里增加了一些根据索引来操作集合元素的方法。

#### 接口中定义的方法

| **void add(int index,  Object element)**      | 在列表的指定位置插入指定元素（可选操作）。                   |
| --------------------------------------------- | ------------------------------------------------------------ |
| **boolean addAll(int  index, Collection c)**  | 将集合c 中的所有元素都插入到列表中的指定位置index处          |
| **Object get(index)**                         | 返回列表中指定位置的元素。                                   |
| **int indexOf(Object  o)**                    | 返回此列表中第一次出现的指定元素的索引；如果此列表不包含该元素，则返回 -1。 |
| **Object remove(int  index)**                 | 移除列表中指定位置的元素。                                   |
| **Object set(int  index, Object element)**    | 用指定元素替换列表中指定位置的元素。                         |
| **List subList(int  fromIndex, int toIndex)** | 返回列表中指定的 fromIndex（包括 ）和  toIndex（不包括）之间的所有集合元素组成的子集。 |
| **Object[] toArray()**                        | 返回按适当顺序包含列表中的所有元素的数组（从第一个元素到最后一个元素）。 |

除此之外，Java 8还为List接口添加了如下两个默认方法。

| **void  replaceAll(UnaryOperator operator)** | 根据operator指定的计算规则重新设置List集合的所有元素。 |
| -------------------------------------------- | ------------------------------------------------------ |
| **void sort(Comparator  c)**                 | 根据Comparator参数对List集合的元素排序。               |

**具体详见**：[由浅入深理解java集合(三)——集合 List](https://www.jianshu.com/p/d436b4cf6b94)

### Queue集合

#### 简介

Queue用户模拟队列这种数据结构，队列通常是指“先进先出”(FIFO，first-in-first-out)的容器。队列的头部是在队列中存放时间最长的元素，队列的尾部是保存在队列中存放时间最短的元素。新元素插入（offer）到队列的尾部，访问元素（poll）操作会返回队列头部的元素。通常，队列不允许随机访问队列中的元素。

#### 接口中定义的方法

![](http://justzk.github.io/images/collection-basics-3.png)

**具体详见**：[由浅入深理解java集合(四)——集合 Queue](https://www.jianshu.com/p/35760d7bac0d)