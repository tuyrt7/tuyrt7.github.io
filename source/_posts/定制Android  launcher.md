---
title: 为定制Android launcher应用提供一种思路
date: 2021-04-26 11:35:17
categories: android定制
tags:
     - blog
     - android定制
---

# 为定制Android launcher应用提供一种思路 
  
### 1、可以通过修改 packages\apps\Launcher3 的清单文件，为其主页过滤器设置其优先级 priority 为 -1，并添加一个 Category ，然后在开发的桌面应用的首 Activity 按照添加过滤器类别 HOME和HOME.TEST 
```
如：  
<intent-filter android:priority="-1">
    <action android:name="android.intent.action.MAIN" />  
    <category android:name="android.intent.category.HOME" />  
    <category android:name="android.intent.category.HOME.TEST" /> //添加特殊的类别  
    <category android:name="android.intent.category.DEFAULT" />  
    <category android:name="android.intent.category.MONKEY"/>  
</intent-filter>  ```
 

### 2、在编译的镜像中，删除原系统的 launcher3.apk，内置自己开发的 launcher应用  
  

#### 注意
内置的应用如果包含静态so文件，需要把so文件打包放到指定的位置(/system/lib/ or /vendor/lib/ or ...) 