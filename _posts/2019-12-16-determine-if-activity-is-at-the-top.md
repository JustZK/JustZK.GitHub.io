---
layout: post
title: 判断activity是否在最上层
date: 2019-12-15
Author: Zhu Kun
tags: [Activity]
toc: true
comments: true
---

```java
/**
 * @param context       //getApplicationContext()
 * @param activityName  //包名
 * @return              //结果
 */
public static boolean isTopActivity(Context context, String activityName) {

    LogUtil.getInstance().d(TAG, "isTopActivity", false);

    boolean result = false;

    try {
        ActivityManager am = (ActivityManager) (context.getSystemService(ACTIVITY_SERVICE));

        if (am != null) {
            ComponentName cn = am.getRunningTasks(1).get(0).topActivity;
            LogUtil.getInstance().d(TAG, "para:" + activityName, false);
            LogUtil.getInstance().d(TAG, "TopActivity:" + cn.getClassName(), false);

            result = cn.getClassName().equals(activityName);
        } else {
            LogUtil.getInstance().d(TAG, "context.getSystemService(ACTIVITY_SERVICE) == null", false);
        }
    } catch (Exception e) {
        e.printStackTrace();
        LogUtil.getInstance().d(TAG, e.getMessage(), false);
    }

    return result;
}
```

**此方法兼容性不好 建议使用生命周期** 