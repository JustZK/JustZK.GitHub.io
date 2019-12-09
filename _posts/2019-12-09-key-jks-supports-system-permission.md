---
layout: post
title: 让Key.jks支持系统签名(证书)
date: 2019-12-09
Author: Zhu Kun
tags: [System Permission]
toc: true
comments: true
---

## 首先下载keytool工具，地址

[keytool-importkeypair](https://github.com/getfatday/keytool-importkeypair)

## 相关文件

1、keytool-importkeypair

2、platform.x509.pem、platform.pk8（需要系统商提供）

3、demo.jks

## 执行指令

```javascript
# 转换系统签名命令
./keytool-importkeypair -k FaceCabinet.jks -p cetc52 -pk8 platform.pk8 -cert platform.x509.pem -alias debug
# FaceCabinet.jks : 签名文件
# cetc52 : 签名文件密码
# platform.pk8、platform.x509.pem : 系统签名文件
# debug : 签名文件别名
```

![](https://justzk.github.io/images/key-jks-supports-system-signing/key-jks-supports-system-signing.png)

**执行完后FaceCabinet.jks文件内就有debug的带有系统签名的key**