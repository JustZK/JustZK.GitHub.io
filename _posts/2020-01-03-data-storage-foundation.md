---
layout: post
title: 数据存储基础
date: 2020-01-03
Author: Zhu Kun
tags: [Data Storage]
toc: true
comments: true
---

## 描述一下Android数据持久存储方式？

- **SharedPreferences**存储：一种轻型的数据存储方式，本质是基于XML文件存储的key-value键值对数据，通常用来存储一些简单的配置信息（如应用程序的各种配置信息）；
- **SQLite**数据库存储：一种轻量级嵌入式数据库引擎，它的运算速度非常快，占用资源很少，常用来存储大量复杂的关系数据；
- **ContentProvider**：四大组件之一，用于数据的存储和共享，不仅可以让不同应用程序之间进行数据共享，还可以选择只对哪一部分数据进行共享，可保证程序中的隐私数据不会有泄漏风险；
- **File**文件存储：写入和读取文件的方法和     Java中实现I/O的程序一样；
- **网络**存储：主要在远程的服务器中存储相关数据，用户操作的相关数据可以同步到服务器上；

## SharedPreferences的应用场景？注意事项？

- SharedPreferences是一种轻型的数据存储方式，本质是基于XML文件存储的key-value键值对数据，通常用来存储一些简单的配置信息，如int，String，boolean、float和long；

- 注意事项： 

- 1. 勿存储大型复杂数据，这会引起内存GC、阻塞主线程使页面卡顿产生ANR
  2. 勿在多进程模式下，操作Sp
  3. 不要多次edit和apply，尽量批量修改一次提交
  4. 建议apply，少用commit

**推荐文章**：[史上最全面，清晰的SharedPreferences解析](https://link.juejin.im/?target=https%3A%2F%2Fblog.csdn.net%2Fgeekerhw%2Farticle%2Fdetails%2F79713068) [SharedPreferences在多进程中的使用及注意事项](https://link.juejin.im/?target=http%3A%2F%2Fzmywly8866.github.io%2F2015%2F09%2F09%2Fsharedpreferences-in-multiprocess.html)

## 了解SQLite中的事务操作吗？是如何做的

SQLite在做CRDU操作时都默认开启了事务，然后把SQL语句翻译成对应的SQLiteStatement并调用其相应的CRUD方法，此时整个操作还是在rollback journal这个临时文件上进行，只有操作顺利完成才会更新db数据库，否则会被回滚；

## 使用SQLite做批量操作有什么好的方法吗？

使用SQLiteDatabase的beginTransaction方法开启一个事务，将批量操作SQL语句转化为SQLiteStatement并进行批量操作，结束后endTransaction()

## 如何删除SQLite中表的个别字段？

SQLite数据库只允许增加字段而不允许修改和删除表字段，只能创建新表保留原有字段，删除原表

## 使用SQLite时会有哪些优化操作？

- 使用事务做批量操作
- 及时关闭Cursor，避免内存泄露
- 耗时操作异步化：数据库的操作属于本地IO耗时操作，建议放入异步线程中处理
- ContentValues的容量调整：ContentValues内部采用HashMap来存储Key-Value数据，ContentValues初始容量为8，扩容时翻倍。因此建议对ContentValues填入的内容进行估量，设置合理的初始化容量，减少不必要的内部扩容操作
- 使用索引加快检索速度：对于查询操作量级较大、业务对查询要求较高的推荐使用索引