---
title: Studio常用快捷键
date: 2019/9/8 0:23:47
categories: 快捷键
tags:
	 - blog
     - studio
     - 工具
	 
---

# Android Studio常用快捷键  

Android Studio 是谷歌基于IntelliJ IDEA开发的安卓开发工具，有点类似 Eclipse ADT，Android Studio 提供了集成的 Android 开发工具用于开发和调试，基于Gradle的构建支持。  
  
Android Studio常用快捷键   

- Ctrl+D： 集合了复制和粘贴两个操作，如果有选中的部分就复制选中的部分，并在选中部分的后面 粘贴出来，如果没有选中的部分，就复制光标所在的行，并在此行的下面粘贴出来。 

- Ctrl+空格： 输入代码时按此组合键会列出与之相匹配的类、方法名、成员变量等，起智能提示的作用。 在编辑XML文件一样有用。

- Ctrl+向下箭头 或Ctrl+向上箭头：在有自动匹配下拉列表时，此快捷键会自动关掉下拉列表， 光标移动到下/上一行。 

<!--more--> 

- 自动匹配下拉列表的排列方式的切换：在自动匹配下拉列表的右下角有个“π”图标，点击后可选 是按:实用性、字母两种排列方式。 
- Ctrl+斜杠 、Ctrl+shift+斜杠： Ctrl+斜杠：注释或取消注释当前行或选中的代码块，以双斜杠的方式即“//” Ctrl+shift+斜杠:注释或取消注释选中的代码块，以“/……/”方式注释， 
- Ctrl+shift+Enter：自动匹配相对应的语法结构，比如if，do-while，try-catch等结构。 
- Ctrl+F：搜索 
- Ctrl+句点：在自动匹配下拉列表中，选中第一个item 
- 感叹号：在自动匹配下拉列表中，上下键选中一个返回结果为boolean的item，按感叹号会自动取反： 
- Ctrl+Enter :在自动匹配下拉列表中，在没有选中的item时，默认选中第一个item。 
- Ctrl+shift+A：快速查找android studio中的菜单。 
- Ctrl+N：快速查找类名、文件 
- Ctrl+B：直接跳转到类、方法、成员变量定义的地方。与Ctrl+鼠标左键效果一样 
- Ctrl+Alt+B：查询有哪些类实现了光标所在的接口。 
- Ctrl+Alt+shift+I：检测代码,比如检测一些定义了，但没有使用过的变量或方法。检测的目的是为了提高代码效率。 
- Ctrl+Alt+shift+N ：快速打开输入的方法或变量。 
- Ctrl+shift+F7：以高亮的形式标志处一些相关的东西，这里主要由三种情况：
　　- 光标的位置在implement时，会把类实现了接口的方法名给标志出来；
　　- 光标的位置在return时，会标志出方法的所有退出的地方；
　　- 光标在try或者throws关键字处时，会标志出会产生异常的语句。 
- shift+鼠标滚动：可实现编辑界面的横向滚动。 
- Ctrl+Alt+V：调用方法时传入的参数是比较复杂的表达式时，可用此组合键重构变量，以简化代码的复杂度。
　　
在组合键之前要先选中表达式。  

- Ctrl+D：比较两个jar文件，在同一工程中，选中两个jar文件，按此组合键
- Ctrl+O：子类想重写父类的方法时，按此组合键可显示所有父类的方法。接口对应的组合键时Ctrl+I。
- Ctrl+shift+I:快速查看方法体，想查看一个方法是如何实现的，可把光标移至方法处，按此组合键。
- Ctrl+Q：把光标移至方法处，按此组合键可快速查看方法的说明文档。
- Ctrl+~:切换编辑界面的风格，快捷键设计
- Alt+shift+C：查看工程的最近修改。
- Ctrl+E：快速查看最近打开打开过的文件。
- shift+F6：可对类、方法、变量进行重命名，使用的地方会自动更新名字。
- Alt+F1：快速打开project view、structure view 等查看相应的元素。
- Ctrl+Alt+F7:查看一个类、方法、成员变量在整个工程中的使用情况。
- Ctrl+shift+空格：在赋值或者是在方法中要传入参数时，列出类型匹配的方法名、成员变量名。
- Ctrl+Alt+T：选中一块代码，按此组合键，可快速添加if 、for、try/catch等语句。
- Ctrl+Tab：打开界面切换窗口，保持按住Ctrl键，选中相应的要打开的窗口。
- Ctrl+W：选中光标所在的所在的单词（一个成员变量或者是一个方法名），多按一次会选中所在的语句， 再多按一次会选中所在的代码块。。。依次类推，每增加一次会扩大一级选中的范围。
- Alt+回车 导入包,自动修正
- Ctrl+N 查找类
- Ctrl+Shift+N 查找文件
- Ctrl+Alt+L 格式化代码
- Ctrl+Alt+O 优化导入的类和包
- Alt+Insert 生成代码(如get,set方法,构造函数等)
- Ctrl+E或者Alt+Shift+C 最近更改的代码
- Ctrl+R 替换文本
- Ctrl+F 查找文本
- Ctrl+Shift+Space 自动补全代码
- Ctrl+空格 代码提示
- Ctrl+Alt+Space 类名或接口名提示
- Ctrl+P 方法参数提示
- Ctrl+Shift+Alt+N 查找类中的方法或变量
- Alt+Shift+C 对比最近修改的代码
- Shift+F6 重构-重命名
- Ctrl+Shift+先上键
- Ctrl+X 删除行
- Ctrl+D 复制行
- Ctrl+/ 或 Ctrl+Shift+/ 注释（// 或者/…/ ）
- Ctrl+J 自动代码
- Ctrl+E 最近打开的文件
- Ctrl+H 显示类结构图
- Ctrl+Q 显示注释文档
- Alt+F1 查找代码所在位置
- Alt+1 快速打开或隐藏工程面板
- Ctrl+Alt+ left/right 返回至上次浏览的位置
- Alt+ left/right 切换代码视图
- Alt+ Up/Down 在方法间快速移动定位
- Ctrl+Shift+Up/Down 代码向上/下移动。
- F2 或Shift+F2 高亮错误或警告快速定位
- 代码标签输入完成后，按Tab，生成代码。
- 选中文本，按Ctrl+Shift+F7 ，高亮显示所有该文本，按Esc高亮消失。
- Ctrl+W 选中代码，连续按会有其他效果
- 选中文本，按Alt+F3 ，逐个往下查找相同文本，并高亮显示。
- Ctrl+Up/Down 光标跳转到第一行或最后一行下
- Ctrl+B 快速打开光标处的类或方法 


## 最常用快捷键  

- Ctrl＋E，可以显示最近编辑的文件列表 
- Shift＋Click可以关闭文件 
- Ctrl＋[或]可以跳到大括号的开头结尾 
- Ctrl＋Shift＋Backspace可以跳转到上次编辑的地方 
- Ctrl＋F12，可以显示当前文件的结构
- Ctrl＋F7可以查询当前元素在当前文件中的引用，然后按F3可以选择 
- Ctrl＋N，可以快速打开类 
- Ctrl＋Shift＋N，可以快速打开文件
- Alt＋Q可以看到当前方法的声明 
- Ctrl＋W可以选择单词继而语句继而行继而函数 
- Alt＋F1可以将正在编辑的元素在各个面板中定位 
- Ctrl＋P，可以显示参数信息 
- Ctrl＋Shift＋Insert可以选择剪贴板内容并插入 
- Alt＋Insert可以生成构造器/Getter/Setter等 
- Ctrl＋Alt＋V 可以引入变量。例如把括号内的SQL赋成一个变量
- Ctrl＋Alt＋T可以把代码包在一块内，例如try/catch 
- Alt＋Up and Alt＋Down可在方法间快速移动


下面的不是很有用

- 在一些地方按Alt＋Enter可以得到一些Intention Action，例如将”==”改为”equals()” 
- Ctrl＋Shift＋Alt＋N可以快速打开符号
- Ctrl＋Shift＋Space在很多时候都能够给出Smart提示 
- Alt＋F3可以快速寻找 
- Ctrl＋/和Ctrl＋Shift＋/可以注释代码 
- Ctrl＋Alt＋B可以跳转到抽象方法的实现
- Ctrl＋O可以选择父类的方法进行重写 
- Ctrl＋Q可以看JavaDoc
- Ctrl＋Alt＋Space是类名自动完成 
- 快速打开类/文件/符号时，可以使用通配符，也可以使用缩写
- Live Templates! Ctrl＋J
- Ctrl＋Shift＋F7可以高亮当前元素在当前文件中的使用 
- Ctrl＋Alt＋Up /Ctrl＋Alt＋Down可以快速跳转搜索结果
- Ctrl＋Shift＋J可以整合两行 
- Alt＋F8是计算变量值  


在独处的过程中激发思考的力量……