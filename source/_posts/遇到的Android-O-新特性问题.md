---
title: 遇到的Android O 新特性问题
date: 2019-09-10 08:02:30
tags:
	- blog
	- 新特性
	- android
---


# Notification 在android O 引起的bug

**现象**:  
`notification`在`lollipop上`能显示，在`Android Oreo and above` 不能显示  
**原因**：  
`notitcation`在通过`new NotificationCompat.Builder().build()`这一步中的`Builder()`方法时，在8及以上版本是必须指定一个`channels`，在发送通知前，最好在`Activity`活动`oncreate()`初始化`channel`对象  

**解决**：  
指定`channels`渠道，对`smallIcon` 和 `largeIcon` 有一定的要求， 
各种手机标准不定。     

<!--more--> 

小图制作方法：   

1. In Android Studio, go to File > New > Image Asset.
2. From the Icon Type drop-down list, select Notification Icons.
3. Click the icon next to the Clip Art item to select a Material Design icon for your notification. For this app, use the Android icon.
4. Rename the resource ic_android and click Next and Finish. This creates drawable files with different resolutions for different API levels.   


[Notification使用的官方意见](https://codelabs.developers.google.com/codelabs/android-training-notifications/index.html?index=..%2F..android-training#2)   


# FileProvider在android O 引起的bug  


**现象**:
`android8.1`以上进行程序间文件共享使用`FileProvider`时出现的构造 SD 卡异常错误  

使用这段代码去共享手机照片时：   
 

```if (Build.VERSION.SDK_INT >= 24) {
         mImageUri = FileProvider.getUriForFile(this, "com.example.cameraalbumtest.fileprovider", outputImage);
     } else {
         mImageUri = Uri.fromFile(outputImage);
     }  
  ```
  

_Error_:   

```  
java.lang.IllegalArgumentException: Failed to find configured root  
```

__解决__：  
在`file_paths.xml`文件中添加`root-path` 标签  

```   

<path>
 <root-path name="root_path" path="."/>
</path>  
```