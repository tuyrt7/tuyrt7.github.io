---
title: String-StringBuffer-StringBuilder之间区别2
date: 2019/9/8 0:24:16 
categories: java基础
tags:
     - blog
     - 基础
	 
---

## 源码分析  

[https://www.cnblogs.com/Wilange/p/7570633.html](https://www.cnblogs.com/Wilange/p/7570633.html)   
String,StringBuffer,StringBuilder的区别及其源码分析(一)   
[https://www.cnblogs.com/Wilange/p/7572125.html](https://www.cnblogs.com/Wilange/p/7572125.html)     
String,StringBuffer,StringBuilder的区别及其源码分析(二)    

<!--more-->  

类别|效率|线程安全
String|效率慢|  
StringBuffer|效率快|线程安全  
StringBuilder|效率快|线程不安全  

```  
  
String str =new String("Hello");  
str=str+"nihao";  
```


首先，创建值为 ”Hello” 的 str对象 ，后来对 str 对象追加字符串 “nihao” ,形成新的 str对象 ，咦？不是说字符串一旦创建，不能更改吗？   

其实，第一行代码是我们程序员自己创建了 str对象，但当执行到第二行代码时，因为 str值一旦创建不能更改，而我们代码中仍然使用 str这个变量名，系统内部又重新创建新的 str对象，（用 str=”值” 方式进行赋值，在编译前就会在常量池中创建好）对之前的 str对象 进行覆盖，而之前的 str对象 将由GC进行回收。
那试想，如果进行频繁的字符串更改,对内存是无比的浪费啊，而 `StringBuffer`和`StringBuilder`正好解决此问题，创建一个此类对象，可以在原内存中对对象进行更改,类似于普通类对于属性的操作。   
  
## 借助jvm内存分析来深入理解String  


jvm为了节省字符串重复创建而造成内存浪费，特别维互一个叫常量池的区域，当使用 `String str =”值”`形式创建字符串对象时，就会去查找常量池中有无这个的值，如有，直接引用返回，如果没有系统会在常量池创建，然后引用返回。当使用`String str =new String(“值”)`时，既然有new关键词，势必会在进行堆中进行new对象3步骤（1.在堆中开辟内存空间 2，调用构造器初始化对象 3.返回引用地址值），每次new都会产生新的对象。  

  
Sring对象的更改（主要指new方式创建），会频繁的在堆中开垦内存，造成内存浪费，效率也会大大在降低，并且会有很多脏屁股让GC去帮忙擦。 StingBuffer和StingBuilder不同，一个对象只需要开辟一块内存空间，那效率就会高很多。   
但要细说StringBuffer和StringBuilder，它们在效率上应该不会有大的区别，但因为StringBuffer内中的方法，很多用了synchronized锁，所有在多线程下，会稍慢于StringBuilder.    


也正因为 StringBuffer类中方法中多代有synchronized，线程安全，所有在多线程环境下，我们要舍StringBuilder而用StringBuffer    

__使用总结__：  

>（1）如果要操作少量的数据用 String；  
>（2）多线程操作字符串缓冲区下操作大量数据 StringBuffer；  
>（3）单线程操作字符串缓冲区下操作大量数据 StringBuilder。  
