---
title: lifecycle
date: 2019-11-21 08:58:39
tags:
	- android
	- jetpack
---


### Lifecycle学习笔记（未完）


Lifecycle管理生命周期也是观察者模式，其中主要涉及到以下几个类：LifecycleObserver、LifecycleOwner、Lifecycle、State、Event  

- LifecycleObserver：Lifecycle观察者。我们需要自定义类实现LifecycleObserver，通过注解的方式可观察生命周期方法。 
- LifecycleOwner：Lifecycle持有者。让Activity或者fragment实现该接口，当生命周期改变是事件会被LifecycleObserver接收到。 
- Lifecycle：生命周期抽象类。持有添加和移除监听方法。定义State和Event枚举。
- LifecycleRegistry：Lifecycle的实现类。 
- State：当前生命周期所处状态。Lifecycle 将 Activity 的生命周期函数对应成 State . 
- Event：当前生命周期变化所对应的事件。State 变化触发 Event 事件，事件会被注册的观察者LifecycleObserver 接收处理。 
 
Event枚举：  

```
public enum Event {
        ON_CREATE,
        ON_START,
        ON_RESUME,
        ON_PAUSE,
        ON_STOP,
        ON_DESTROY,
        ON_ANY
    }

```

State枚举：  

```
public enum State {
        DESTROYED,
        INITIALIZED,
        CREATED,
        STARTED,
        RESUMED;
        public boolean isAtLeast(@NonNull State state) {
            return compareTo(state) >= 0;
        }
    }

```  
<!--more--> 

State和Event的对应关系：   

![对应关系](https://raw.githubusercontent.com/tuyrt7/tuyrt7.github.io/master/uploads/img/lifecycle.jpg) 
