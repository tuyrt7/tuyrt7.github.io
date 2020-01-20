---
title: Navigation-BottomNavigationView使用踩坑
date: 2019-10-16 20:47:53
tags:
	- blog
	- jetpack
	
---

# 使用Navigation-BottomNavigationView做App导航

项目中使用Navigation管理Fragment跳转，页面层级非常清晰，通过ActionId跳转，暂时未用到launchSingleTop，deeplink的用法

好处是大大的，坑也多，也许是我太low了，毕竟才刚刚用起来...

使用BottomNavigaionView+Navigation的导航页就是在顶级页面，不需要去监听状态然后跳转，然而没有状态可监听就意味着我们无法通过状态判断去完成我们所需要的特定功能

<!--more-->

首先：
bug1: 导航item对应的fragment不在栈中，当前页（只要不是主页）回退都会到startDestination对应的fragment,只有在主页回退才会直接退出
你可能会说可以监听activity的回退onBackPressed()方法。嗯，哈哈告诉你，我试过了，确实可以直接退出...
但是我想来句MMP...退出时会先闪屏到主页然后退出activity。
未解决 我还未找到方法解决这个问题，等之后在细细查看下源码再说

bug2: 每次点击导航都会创建新的fragment，看起来似乎没毛病...但是当前页点击-当前页的item也会这样，我就.......
解决办法：继承NavHostFragment,重写关于...未验证

bug3: CoordinatorLayout（郭神说的加强版的fragment）如果使用属性 fitsSystemWindows=true 的会出现导航item 文字不显示，导航图标被压缩
详情如图：

![图1](https://raw.githubusercontent.com/tuyrt7/tuyrt7.github.io/master/uploads/img/nav_4.jpg)