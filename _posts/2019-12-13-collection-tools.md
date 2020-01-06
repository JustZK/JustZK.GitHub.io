---
layout: post
title: Collections工具类
date: 2019-12-13
Author: Zhu Kun
tags: [Iterable, Collections]
toc: true
comments: true
---

## 排序操作

**只针对List集合元素进行排序**

- reverse(List list)：反转指定List集合中元素的顺序
- shuffle(List list)：对List中的元素进行随机排序（洗牌）
- sort(List list)：对List里的元素根据自然升序排序
- sort(List list, Comparator c)：自定义比较器进行排序
- swap(List list, int i, int     j)：将指定List集合中i处元素和j出元素进行交换
- rotate(List list, int     distance)：将所有元素向右移位指定长度，如果distance等于size那么结果不变

## 查找、替换操作

**只针对Collection接口相关**

- int binarySearch(List list,Object     key)：使用二分搜索法搜索指定的List集合，以获得指定对象在List集合中的索引（位置）。如果要使该方法可以正常工作，则必须保证List中的元素已经处于有序状态
- Object max(Collection     coll)：根据元素的自然顺序，返回指定集合中的最大元素
- Object max(Collection coll,Comparator     comp)：根据Comparator指定的顺序，返回给定集合的最大元素
- Object min(Collection     coll)：根据元素的自然顺序，返回指定集合中的最小元素
- Object min(Collection coll,Comparator     comp)：根据Comparator指定的顺序，返回给定集合的最小元素
- void fill(List list,Object     obj)：使用指定元素obj替换指定List集合中的所有元素
- int frequency(Collection coll,Object     obj)：返回指定集合中指定元素的出现次数
- int indexOfSubList(List source,List     target)：返回子List对象在父List对象中第一次出现的位置索引，如果父List中没有出现这样的子List，则返回-1
- int lastIndexOfSubList(List source,List     target)：返回子List对象在父List对象中最后一次出现的位置索引，如果父List中没有出现这样的子List，则返回-1
- boolean replaceAll(List list,Object oldVal,Object     newVal)    ：使用一个新值newVal替换List对象的所有旧值oldVal

```java
ArrayListnumbers=newArrayList();
numbers.add(2);
numbers.add(-5);
numbers.add(3);
numbers.add(0);

//原始序列，输出[2,-5,3,0]
System.out.println(numbers);

//输出最大元素，3
System.out.println(Collections.max(numbers));

//输出最大元素，-5
System.out.println(Collections.min(numbers));

//将numbers中的0用1来替代
//输出[2,-5,3,1]
System.out.println(Collections.replaceAll(numbers,0,1));

//判断-5在List集合出现的次数，返回1
System.out.println(Collections.frequency(numbers,-5));

//排序
//输出[-5,1,2,3]
Collections.sort(numbers);
System.out.println(numbers);

//只有排序后的List集合才可以用二分法查询，输出3
System.out.println(Collections.binarySearch(numbers,3));
```

## 同步控制

**Collections**类中提供了多个**synchronizedXxx()**方法，该方法可以将指定集合包装成线程同步的集合，从而可以解决多线程并发访问集合时的线程安全问题。

 

Java中常用的集合框架中的实现类**HashSet**、**TreeSet**、**ArrayList**、**ArrayDeque**、**LinkedList**、**HashMap**和**TreeMap**都是线程不安全的。如果有多个线程访问它们，而且有超过一个的线程试图修改它们，则存在线程安全的问题。**Collections**提供了多个类方法可以把它们包装成线程同步的集合。

```java
public class SynchronizedTest {

    public static void main(String[] args) {
        Collection c = Collections.synchronizedCollection(new ArrayList<>());
        List list = Collections.synchronizedList(new ArrayList<>());
        Set s = Collections.synchronizedSet(new HashSet<>());
        Map m = Collections.synchronizedMap(new HashMap<>());
    }
}
```

上面的例子中，直接将新创建的集合对象传给了**Collections**的**synchronizedXxx()**方法，这样就可以直接获取**List**、**Set**和**Map**的线程安全的实现版本。

## **设置不可变集合**

Collections有三类方法可返回一个**不可变集合**：

1. emptyXxx()：返回一个空的不可变的集合对象
2. singletonXxx()：返回一个只包含指定对象的，不可变的集合对象。
3. unmodifiableXxx()：返回指定集合对象的不可变视图

```java
public void testUnmodifiable() {
  System.out.println("给定的list：" + list);
  List<String> unmodList = Collections.unmodifiableList(list);

  unmodList.add("再加个试试！"); // 抛出：java.lang.UnsupportedOperationException

  // 这一行不会执行了
  System.out.println("新的unmodList：" + unmodList);
}
```

