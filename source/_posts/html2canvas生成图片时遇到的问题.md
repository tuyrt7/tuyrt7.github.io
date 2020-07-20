---
title: '微信web使用html2canvas遇到的坑'
date: 2020-07-20 13:34:26
tags:
	- blog
	- web 
	- html
	
---

**解释：**

在微信浏览器使用html2canvas遇到的坑。标签结构在正常显示，用html2canvas生成图片时出现问题如下：

**bug：**

1. input输入框value下移
![input_value_bug.jpg](https://raw.githubusercontent.com/tuyrt7/tuyrt7.github.io/master/uploads/img/input_value_bug.jpg)

2. transform属性引起css生成的箭头位置变化
![arrow_bug.jpg](https://raw.githubusercontent.com/tuyrt7/tuyrt7.github.io/master/uploads/img/arrow_bug.jpg)

3. android端-微信浏览器：边框丢失
![arrow_border.png](https://raw.githubusercontent.com/tuyrt7/tuyrt7.github.io/master/uploads/img/arrow_border.jpg)

![input_bug.jpg](https://raw.githubusercontent.com/tuyrt7/tuyrt7.github.io/master/uploads/img/input_bug.jpg)


**解决：**

1.  input的padding值过大不会改变标签显示位置，生成图片时会影响，尤其事lineheight.
2. 不用transform属性定位，用其他属性代替。或用图片标签样式
3. 暂无良好解决方法，不用border画边框线，用其他效果代替