---
title: TextView里面设置字体粗体
date: 2019-12-20 11:00:39
tags:
	- android
	- ui
	- blog  

---

## TextView里面设置字体粗体  

# 动态：

###### 不建议这种方式，这种方式比较操蛋，结果效果显示除了被选中的Item的字体被加粗外，其他未被选中的两个Item（随机的）也被加粗。而且加粗效果还不明显！！！

> ps:所以最好不要用这种。  


```
    //android中为textview动态设置字体为粗体
     TextView textView = (TextView)findViewById(R.id.textView);
     TextPaint tp = textView .getPaint();
     tp.setFakeBoldText(true);  ```   
``` 

2. 推荐这种方式，这种方式完美的解决的我需要选中的Item字体加粗。  
 

```//android中为textview动态设置字体为粗体
 TextView textView = (TextView)findViewById(R.id.textView);
 textView .setTypeface(Typeface.defaultFromStyle(Typeface.BOLD));
//设置不为加粗
 textView.setTypeface(Typeface.defaultFromStyle(Typeface.NORMAL));
 
```
 

<!--more-->
# 静态：  

这种是最直接的方式，在xml文件里面直接添加一个TextView的属性值。但这不能解决我的需求，在这里只是给大家介绍一下！  
    ```
    android:textStyle="bold"   
    ``` 

网上提供了这两种方式，是英文加粗的，我没试过，大家可以试一下！  
    英文设置加粗可以在xml里面设置:   
    
```
   <SPAN style="FONT-SIZE: 18px">android:textStyle="bold"</SPAN>   
```
    
英文还可以直接在String文件里面直接这样填写:  
代码如下:  
```
<string name="styled_text">Plain, <b>bold</b>, <i>italic</i>, <b><i>bold-italic</i></b></string>
```
b代码加粗,i代表倾斜 