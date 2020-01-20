---
title: onNewIntent疑惑
date: 2019/9/8 0:26:23 
categories: android基础
tags:
     - blog
     - 基础
---
  

一直对`activity`的` **onNewIntent** `有些困惑，开始时只是知道在`activity`被重新`restart`时会进入，后来又发现还不只是这种情况，下面是经过调试后的结论

- 第一种情况：`activity launchMode`为`singleTask`或者`singleInstance`

> 1、activitya start activityb  
> 2、activityb start activitya  

在第二步被执行后，`activitya`就会顺序执行 `onNewIntent onRestart onStart onResume  `  

<!--more--> 

- 第二种情况：activity launchMode为singleTop singleTask singleInstance  

> 1、start activitya  
> 2、activitya start activitya  
 
在第二步被执行后，`activitya`就会顺序执行`onPause onNewIntent onResume  `

第一种情况其实是真正的`activity被restart`，第二种情况是`activity`位于栈顶时被再次`start`就会进入`onNewIntent ` 

其实理解的简单一点，无论什么模式，只有`activity`是同一个实例的情况下，`intent`发生了变化，就会进入`onNewIntent`中，这个方法的作用也是让你来对旧的`intent`进行保存，对新的`intent`进行对应的处理。

