---
layout: post
title: TCP发送带摘要认证(Digest Authentication)的HTTP请求
date: 2021-06-15
Author: Zhu Kun
tags: [TCP, HTTP]
toc: true
comments: true
---

## 知识点：

1. http请求是封装好的tcp；

2. 封装好的http请求，如：volley、OKhttp：

3. - 一次http请求会发起判断本地是否有建立该tcp的链路，若有则复用，没有则新建；
   - 一个http请求新建的tcp链路，会产生多次心跳；
   - 若一次http请求结束后几次心跳内再无http请求则关闭tcp链路；

4. 若自己从tcp层写http请求，一般每次都新建一个tcp链路，在完成一次http请求后主动close     tcp链路；

5. 带摘要的http请求（即头文件有验证的请求）是产生了2次通信，先请求一次我要通信，服务端会回复加密的key，客户端根据多个key，加密成新的value进行第二次验证；

## 流程图：

![](http://justzk.github.io/images/tcp-sends_http_request_with_digest_authentication/tcp-sends_http_request_with_digest_authentication-1.png)

## 摘要计算：

在说明如何计算摘要之前，先说明参加摘要计算的信息块。信息块主要有两种: 

### 1：表示与安全相关的数据的 A1；

A1中的数据时密码和受保护信息的产物，它包括用户名，密码，保护域和随机数等内容，A1 只涉 及安全信息，与底层报文自身无关。 

若算法是:MD5

则 A1=*<*user>:*<*realm>:*<*password>

 

若算法是:MD5-sess 

A1=MD5(*<*use>:*<*realm>:*<*password>):*<*nonce>:*<*cnonce>

 

### 2：表示与报文相关的数据的 A2；

A2 表示是与报文自身相关的信息，比如 URL，请求反复和报文实体的主体部分，A2 加入摘要计算 主要目的是有助于防止反复，资源或者报文被篡改。 

若 qop 未定义或者 auth: 

A2=*<*request-method>:*<*uri-directive-value>

 

若 qop 为 auth:-int

A2=*<*request-method>:*<*uri-directive-value>:MD5(*<*request-entity-body>)

 

### 下面定义摘要的计算规则:

若 qop 没有定义:

摘要 response=MD5(MD5(A1):*<*nonce>:MD5(A2)) 

 

若 qop 为 auth:

摘要 response=MD5(MD5(A1):*<*nonce>:*<*nc>:*<*cnonce>:*<*qop>:MD5(A2))

 

若 qop 为 auth-int:

摘要 response= MD5(MD5(A1):*<*nonce>:*<*nc>:*<*cnonce>:*<*qop>:MD5(A2)) 

 

## 随机数(nonce)的生成

本文建议这个假想的随机数公式: 

nonce = BASE64(time-stamp MD5(time-stamp ”:” ETag “:” private-key)) 

 

其中:

time-stamp 是服务器产生的时间戳或者其他不会重复的序列号，ETag 是与所请求实体有关的 HTTP ETag 首部的值，priviate-key 是只有服务器知道的数据。 

这样，服务器就可以收到客户端的认证首部之后重新计算散列部分，如果结果与那个首部的随机数 不符，或者是时间戳的值不够新，就可以拒绝请求，服务器可以通过这种方式来限制随机数的有效 持续时间。 

包括了 ETag 可以防止对已经更新资源版本的重放请求。注意:在随机数中包含客户端 IP，服务器好 像就可以限制原来获取此随机数的客户端重用这个随机数了，但这会破坏代理集群的工作，使用代 理集群时候，来自单个用户的多条请求通常会经过不同的代理进行传输，而且 IP 地址欺骗实现起来 也不复杂。 

 

## 实例：

![](http://justzk.github.io/images/tcp-sends_http_request_with_digest_authentication/tcp-sends_http_request_with_digest_authentication-2.png)

```kotlin
//案例算法是：MD5
vala1:String="$username:$realm:$password"

//案例qop是：auth
//request-method：必须是大写，案例是GET
//uri-directive-value：即uri；请求地址
vala2:String="GET:$uri"

//案例qop是：auth
valresponse=md5("${md5(a1)}:$nonce:$nc:$cnonce:$qop:${md5(a2)}")
```

