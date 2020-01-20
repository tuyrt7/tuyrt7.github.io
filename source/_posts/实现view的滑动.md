---
title: View的滑动
date: 2019/9/8 0:19:53 
categories: android
tags:
     - blog
     - 基础
     - view
---


# 实现View滑动的方式  

  
```

1. layout()方法  
2. offsetLeftAndRight()和offsetTopAndBotton()  
3. LayoutParams改变其属性，最后setLayoutParams()  
4. 动画平移(view动画setAnimations() ，属性动画ObjescAnimator.ofLoad())  
5. scrollTo(x,y)移动到坐标点 和 scrollBy(x,y) 移动x,y的负增量  
6. scroller配合computeScroll()平滑移动 

```

<!--more--> 

# View的dispatche过程
  
view结构
![图](https://raw.githubusercontent.com/tuyrt7/tuyrt7.github.io/master/uploads/img/view_1.png)  
  
dispatch结构  
  
![图](https://raw.githubusercontent.com/tuyrt7/tuyrt7.github.io/master/uploads/img/view_2.jpg)


# 常见滑动冲突场景

> 场景1 —— 外部滑动方向与内部滑动方向不一致，比如ViewPager中包含ListView;  
> 场景2 —— 外部滑动方向与内部滑动方向一致，比如ScrollView中包含ListView;  
> 场景3 —— 上面两种情况的嵌套  


# 滑动冲突处理规则？ 

 
通过判断是水平滑动还是竖直滑动来判断到底应该谁来拦截事件；可以根据水平和竖直两个方向的距离差或速度差来做判断


# 滑动冲突解决方式？  

### 外部拦截法  

即点击事件先经过父容器的拦截处理，如果父容器需要此事件就拦截，不需要就不拦截，需要重写父容器的 onInterceptTouchEvent 方法；在 onInterceptTouchEvent 方法中，首先 ACTION_DOWN 这个事件，父容器必须返回 false ,即不拦截 ACTION_DOWN 事件，因为一旦父容器拦截了 ACTION_DOWN ,那么后续的 ACTION_MOVE/ACTION_UP 都会直接交给父容器处理；其次是 ACTION_MOVE ,根据需求来决定是否要拦截;最后 ACTION_UP 事件,这里必须要返回false,在这里没有多大意义。  

### 内部拦截法  

所有事件都传递给子元素,如果子元素需要就消耗掉,不需要就交给父元素处理,需要子元素配合 requestDisallowInterceptTouchEvent 方法才能正常工作;父元素需要默认拦截除 ACTION_DOWN 以外的事件,这样子元素调用 parent.requestDisallowInterceptTouchEvent(false) 方法时，父元素才能继续拦截需要的事件。 ACTION_DOWN 事件不受   requestDisallowInterceptTouchEvent 方法影响,所以一旦父元素拦截 ACTION_DOWN 事件,那么所有元素都无法传递到子元素去）。

# requestDisallowInterceptTouchEvent 可以在子元素中干扰父元素的事件分发吗？如果可以，是全部都可以干扰吗？  

可以，但是 down 事件干扰不了。