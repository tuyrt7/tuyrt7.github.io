---
title: Android通过参数动态设置ImageView
date: 2019-09-24 09:40:11
tags:
	- blog
	- ui
---


我在学习的过程中有这样一个情景。比如天气预报。我会根据请求回来的数据进行解析，其中是根据每天或者每小时的不同对天气图标进行更换
在这个过程中，我想到得是if语句进行判断，但我觉得这个不现实，也太累赘。我发现我请求回来的数据有可能会有30来个图标，也就是说如果我用if语句判读
的话，我要写30个if。。。。。。。。。。。
但我得知一个办法，我分享给大家一起学习！！！！

在drawable 文件夹中创建levellist.xml

如下图：  

![图1](https://raw.githubusercontent.com/tuyrt7/tuyrt7.github.io/master/uploads/img/iv-1.png)

<!--more-->

在layout中把image view的src设置成已创建好的xml文件 

![图2](https://raw.githubusercontent.com/tuyrt7/tuyrt7.github.io/master/uploads/img/iv-2.png)


在java代码中设置 

> Imagetianqi_tv.getDrawable().setLevel(0);
> 这个0代表的是evellist.xml中的
> android:maxLevel="0"