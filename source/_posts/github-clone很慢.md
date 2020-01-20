---
title: github clone很慢
date: 2019-09-19 07:51:23
tags:
	- github
	- blog
	- answer
---


在天朝，经常会出现访问Github 异常慢，尤其在clone远程代码的时候，几KiB/s的速度在遭遇几十上百M的代码后，让大家苦不堪言。

下面就介绍一种方法，绝对包治百病，亲测速度可以达到接近1M。

1. 获取Github相关网站的ip

访问 [ipaddress](https://www.ipaddress.com) ，找到页面中下方的“IP Address Tools - Quick Links”，分别输入github.global.ssl.fastly.net和github.com，查询ip地址。

2. 修改本地host文件

以windows为例，打开host文件，c/windows/System32/drivers/etc/hosts

3. 增加host映射  

```
199.232.5.194 github.global.ssl.fastly.net
140.82.113.4 github.com 
```

4. 更新DNS缓存

命令行输入：ipconfig /flushdns，使增加的映射生效。

5. 大功告成

接下来就可以随意访问Github和clone代码了。