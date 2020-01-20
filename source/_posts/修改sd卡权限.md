---
title: 修改sd卡权限 
date: 2019/9/8 0:19:02 
categories: android定制
tags:
     - blog
     - 定制rom
---


# 修改sd卡权限  

- 前提是手机须Root。
- 用具有Root读写权限的文件管理器 编辑`root/system/etc/permissions/platform.xml`文件。增加一行代码：  
查找代码：  

<!--more--> 

```
  
<permission name="android.permission.WRITE_EXTERNAL_STORAGE" >
  <group gid="sdcard_r" />
  <group gid="sdcard_rw" /> 
</permission>  
```  

将之改为：  
  
```  

<permission name="android.permission.WRITE_EXTERNAL_STORAGE" >
  <group gid="sdcard_r" />
  <group gid="sdcard_rw" /> 
 <group gid="media_rw" /> 
</permission>  
```  

重启ok