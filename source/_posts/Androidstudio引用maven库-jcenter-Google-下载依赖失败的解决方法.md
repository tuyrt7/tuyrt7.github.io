---
title: 'Androidstudio引用maven库(jcenter,Google)下载依赖失败的解决方法'
date: 2019-11-04 10:59:26
tags:
	- blog
	- 开发问题
	
---


# Androidstudio引用maven库(jcenter,Google)下载依赖失败的解决方法

# 最后解决: 
在jecnter()前或后加上mavenCentral()

## 网上关于这块有两种推荐方式
加载阿里云仓库服务、华为云服务

使用华为云服务

```
// Top-level build file where you can add configuration options common to all sub-projects/modules.
apply from: "buildsystem/dependences.gradle"
buildscript {
    repositories {
        maven { url 'https://mirrors.huaweicloud.com/repository/maven/' }
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.0.1'
    }
}

allprojects {
    repositories {
        maven { url 'https://mirrors.huaweicloud.com/repository/maven/' }
        maven { url "https://jitpack.io" }
    }
}

```



使用阿里云服务

```
// Top-level build file where you can add configuration options common to all sub-projects/modules.
apply from:"buildsystem/dependences.gradle"
buildscript {
    repositories {
        maven { url 'http://maven.aliyun.com/nexus/content/groups/public/' }
        maven { url 'http://maven.aliyun.com/nexus/content/repositories/jcenter' }
        google()
    }
    dependencies {
        classpath 'com.android.tools.build:gradle:3.0.1'
    }
}

allprojects {
    repositories {
        maven { url 'http://maven.aliyun.com/nexus/content/groups/public/' }
        maven { url 'http://maven.aliyun.com/nexus/content/repositories/jcenter' }
        maven { url "https://jitpack.io" }
        google()
    }
}
```



