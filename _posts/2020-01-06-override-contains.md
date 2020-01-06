---
layout: post
title: 重写contains(包含)
date: 2020-01-06
Author: Zhu Kun
tags: [Iterable, Collection, List]
toc: true
comments: true
---

```java
public class BTListInfo {

   private String btmac;

   public BTListInfo(String bt_mac) {
      this.btmac = bt_mac;
   }

   public boolean equals(Object obj) {
      if (this == obj)
         return true;
      if (obj != null && obj.getClass() == BTListInfo.class) {
         BTListInfo r = (BTListInfo) obj;
         return this.btmac == r.btmac;
      }
      return false;
   }

   public String getBTmac() {
      return btmac;
   }

   public void setBTmac(String bt_mac) {
      this.btmac = bt_mac;
   }
}
```

```java
BTListInfo b=new BTListInfo(String btmac);
if(list.contains(b)){

}
```

