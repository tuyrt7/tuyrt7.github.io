---
title: EventBus源码初探
date: 2019/9/8 0:24:59 
categories: 源码
tags:
     - blog
     - eventbus
     - 解读
---


## EventBus构造方法  

当我们使用 EventBus 时，一般都是通过`EventBus.getDefault()`获取它的实例对象。函数内的实现如下  

```
public static EventBus getDefault() {
	//单例模式，采用双重检查
    if (defaultInstance == null) {
            synchronized (EventBus.class) {
                if (defaultInstance == null) {
                    defaultInstance = new EventBus();
                }
            }
        }
    return defaultInstance;
}  
  
```
  

<!--more-->  

在此进入其构造函数看看：  
  
```  
public EventBus() {
    this(DEFAULT_BUILDER);
}  
```


这里`DEFAULT_BUILDER`默认时`EventBusBuilder`,用来构造`EventBus`:  

```
private static final EventBusBuilder DEFAULT_BUILDER = new EventBusBuilder();  ```  

`this`调用了`EventBus`的另一个有参构造方法：  
  
```  
EventBus(EventBusBuilder builder) {
        logger = builder.getLogger();
        subscriptionsByEventType = new HashMap<>();
        typesBySubscriber = new HashMap<>();
        stickyEvents = new ConcurrentHashMap<>();
        mainThreadSupport = builder.getMainThreadSupport();
        mainThreadPoster = mainThreadSupport != null ? mainThreadSupport.createPoster(this) : null;
        backgroundPoster = new BackgroundPoster(this);
        asyncPoster = new AsyncPoster(this);
        indexCount = builder.subscriberInfoIndexes != null ? builder.subscriberInfoIndexes.size() : 0;
        subscriberMethodFinder = new SubscriberMethodFinder(builder.subscriberInfoIndexes,
                builder.strictMethodVerification, builder.ignoreGeneratedIndex);
        logSubscriberExceptions = builder.logSubscriberExceptions;
        logNoSubscriberMessages = builder.logNoSubscriberMessages;
        sendSubscriberExceptionEvent = builder.sendSubscriberExceptionEvent;
        sendNoSubscriberEvent = builder.sendNoSubscriberEvent;
        throwSubscriberException = builder.throwSubscriberException;
        eventInheritance = builder.eventInheritance;
 }  ```


可以看到我们时通过`EventBusBuilder`来对`EventBus`进行配置，并初始化一些参数，通过建造者模式。


## 订阅者注册 

获取到`EventBus`的实例后，便可以将订阅者注册到`EventBus`中，看下`register`方法：   

```  
public void register(Object subscriber) {
        Class<?> subscriberClass = subscriber.getClass();
        List<SubscriberMethod> subscriberMethods = subscriberMethodFinder.findSubscriberMethods(subscriberClass);//1
        synchronized (this) {
            for (SubscriberMethod subscriberMethod : subscriberMethods) {
                subscribe(subscriber, subscriberMethod);//2
            }
        }
    }
```  


### 查找订阅者的订阅方法


注释1处的`findSubscriberMethods` 方法找出一个 `SubscriberMethod `的集合，也就是传进来的订阅者的所有订阅方法，接下来就是遍历集合来完成所有订阅者的注册操作。  
从此可以看出，`register`方法完成了**两件事**：  
一是查找订阅者的订阅方法，  
二是订阅者的注册。  

`SubscriberMethod`类中主要用来保存的`Method`对象、线程模式、事件类型、优先级、是否是粘性事件等属性。  
  
下面看下`findSubscriberMethods`方法：  

```  

List<SubscriberMethod> findSubscriberMethods(Class<?> subscriberClass) {
        List<SubscriberMethod> subscriberMethods = METHOD_CACHE.get(subscriberClass); //1
        if (subscriberMethods != null) {
            return subscriberMethods;
        }

        if (ignoreGeneratedIndex) {//这个值是EventBusBuilder的属性传递的
            subscriberMethods = findUsingReflection(subscriberClass);
        } else {
            subscriberMethods = findUsingInfo(subscriberClass); //3
        }
        if (subscriberMethods.isEmpty()) {
            throw new EventBusException("Subscriber " + subscriberClass
                    + " and its super classes have no public methods with the @Subscribe annotation");
        } else {
            METHOD_CACHE.put(subscriberClass, subscriberMethods);//2
            return subscriberMethods;
        }
    }
   
```  
  

注释1处从缓存中查找订阅方法的集合，找到了就马上返回。如果缓存没有根据`ignoreGeneratedIndex` 属性的值来选择采用何种方法来查找订阅方法的集合。`ignoreGeneratedIndex` 的默认值是`false`,可以通过`EventBusBuilder` 来设置。  

注释2处，把找到的订阅方法的集合放入缓存中，防止下次再次查找。  
我们一般都是用`EventBus`的单例模式来获取实例，所以`ignoreGeneratedIndex`值为`false`,执行`if`语句的`findUsingInfo `方法。   
   
```  
  
private List<SubscriberMethod> findUsingInfo(Class<?> subscriberClass) {
        FindState findState = prepareFindState();
        findState.initForSubscriber(subscriberClass);
        while (findState.clazz != null) {
            findState.subscriberInfo = getSubscriberInfo(findState);//1
            if (findState.subscriberInfo != null) {
                SubscriberMethod[] array = findState.subscriberInfo.getSubscriberMethods();//2
                for (SubscriberMethod subscriberMethod : array) {
                    if (findState.checkAdd(subscriberMethod.method, subscriberMethod.eventType)) {
                        findState.subscriberMethods.add(subscriberMethod);
                    }
                }
            } else {
                findUsingReflectionInSingleClass(findState);//3
            }
            findState.moveToSuperclass();
        }
        return getMethodsAndRelease(findState);
    }   

```  
  
  
注释1处通过`getSubscriberInfo`获取订阅者信息,如果有就通过订阅者信息去获取所有订阅方法的信息。在我们开始查找订阅方法的时候并没有忽略注解器为我们生成的索引` MyEventBusIndex`,如果通过`EventBusBuilder`配置了`MyEventBusIndex`，  
执行注释2处的方法，便会获取订阅方法的相关信息；如果没有配置` MyEventBusIndex`，便会执行注释3处的`findUsingReflectionInSingleClass`方法，将订阅方法保存到保存到`findState`中。最后通过`getMethodsAndRelease`方法对`findState`进行回收处理并返回订阅方法的`list`集合。默认是没有配置`MyEventBusIndex`,所以可以进去`findUsingReflectionInSingleClass`方法看看执行过程：
  
  
```  
  
private void findUsingReflectionInSingleClass(FindState findState) {
       Method[] methods;
       try {
           methods = findState.clazz.getDeclaredMethods();//1
       } catch (Throwable th) {
           methods = findState.clazz.getMethods();
           findState.skipSuperClasses = true;
       }
       for (Method method : methods) {
           int modifiers = method.getModifiers();
           if ((modifiers & Modifier.PUBLIC) != 0 && (modifiers & MODIFIERS_IGNORE) == 0) {
               Class<?>[] parameterTypes = method.getParameterTypes();
               if (parameterTypes.length == 1) {
                   Subscribe subscribeAnnotation = method.getAnnotation(Subscribe.class);
                   if (subscribeAnnotation != null) {
                       Class<?> eventType = parameterTypes[0];
                       if (findState.checkAdd(method, eventType)) {
                           ThreadMode threadMode = subscribeAnnotation.threadMode();
                           findState.subscriberMethods.add(new SubscriberMethod(method, eventType, threadMode,
                                   subscribeAnnotation.priority(), subscribeAnnotation.sticky()));
                       }
                   }
               } 

		......
       }
   }   
  
```  


注释1处通过反射来获取订阅者中的所有订阅方法，并根据方法的类型、参数、注解来找到订阅方法。找到后把订阅方法的相关信息存储到`findState`中。  

### 订阅者的注册


```   

// Must be called in synchronized block
   private void subscribe(Object subscriber, SubscriberMethod subscriberMethod) {
       Class<?> eventType = subscriberMethod.eventType;
       Subscription newSubscription = new Subscription(subscriber, subscriberMethod);//1
       CopyOnWriteArrayList<Subscription> subscriptions = subscriptionsByEventType.get(eventType);//2
       if (subscriptions == null) {
           subscriptions = new CopyOnWriteArrayList<>();
           subscriptionsByEventType.put(eventType, subscriptions);
       } else {
		//判断订阅者是否已经被注册
           if (subscriptions.contains(newSubscription)) {
               throw new EventBusException("Subscriber " + subscriber.getClass() + " already registered to event "
                       + eventType);
           }
       }

       int size = subscriptions.size();
       for (int i = 0; i <= size; i++) {
           if (i == size || subscriberMethod.priority > subscriptions.get(i).subscriberMethod.priority) {
               subscriptions.add(i, newSubscription);//3
               break;
           }
       }

       List<Class<?>> subscribedEvents = typesBySubscriber.get(subscriber);//4
       if (subscribedEvents == null) {
           subscribedEvents = new ArrayList<>();
           typesBySubscriber.put(subscriber, subscribedEvents);
       }
       subscribedEvents.add(eventType);

	//粘性事件的处理
       if (subscriberMethod.sticky) {
           if (eventInheritance) {
               Set<Map.Entry<Class<?>, Object>> entries = stickyEvents.entrySet();
               for (Map.Entry<Class<?>, Object> entry : entries) {
                   Class<?> candidateEventType = entry.getKey();
                   if (eventType.isAssignableFrom(candidateEventType)) {
                       Object stickyEvent = entry.getValue();
                       checkPostStickyEventToSubscription(newSubscription, stickyEvent);
                   }
               }
           } else {
               Object stickyEvent = stickyEvents.get(eventType);
               checkPostStickyEventToSubscription(newSubscription, stickyEvent);
           }
       }
   }

```  



注释1处根据` subscriber`（订阅者）和`subscriberMethod`（订阅方法）创建了一个`Subscription`(订阅对象)。  
注释2处根据`eventTpye`(事件类型)获取所有的`Subscription`，判断如果为 `null`则重新创建，并将订阅对象和事件类型保存在` subscriptionsByEventType`（Map集合）。  
注释3处按照订阅方法的优先级插入到订阅对象集合中，完成订阅方法的注册。  
注释4处通过`subscriber`，获取subscribedEvents`(事件类型集合),如果为`null`则重新创建，并将`eventType`添加`subscribedEventslist`集合中，并根据`subscriber`,将`subscribedEvents`放进`typesBySubscriber`（Map集合）  
如果是粘性事件，则从`stickyEvents`事件保存队列中取出该事件类型发送给当前订阅者。



### 总结  

`subscribe()`主要完成了两件事。  


> 1 把订阅对象Subscription根据事件类型eventTpye放进了subscriptionsByEventTypeMap集合，将订阅事件subscribedEvents根据subscriber封装到typesBySubscriberMap集合  
> 2 粘性事件的处理  


## 事件的发送  
  

在获取到EventBus对象时，通过post()把事件提交  

```  

public void post(Object event) {

		//PostingThreadState 保存着事件队列和线程状态信息
       PostingThreadState postingState = currentPostingThreadState.get();

	//获取事件队列，并将当前事件插入事件队列
       List<Object> eventQueue = postingState.eventQueue;
       eventQueue.add(event);

       if (!postingState.isPosting) {
           postingState.isMainThread = isMainThread();
           postingState.isPosting = true;
           if (postingState.canceled) {
               throw new EventBusException("Internal error. Abort state was not reset");
           }
           try {
			//处理事件队列中的所有事件
               while (!eventQueue.isEmpty()) {
                   postSingleEvent(eventQueue.remove(0), postingState);
               }
           } finally {
               postingState.isPosting = false;
               postingState.isMainThread = false;
           }
       }
   }
```  


首先从`PostingThreadState`对象中取出事件队列，然后将当前事件插入进去，最后将队列中所有事件依次交给`postSingleEvent()`方法处理，并移除该事件。  


```  

private void postSingleEvent(Object event, PostingThreadState postingState) throws Error {
        Class<?> eventClass = event.getClass();
        boolean subscriptionFound = false;

		//eventInheritance表示是否向上查找事件的父类，默认为true
        if (eventInheritance) {
            List<Class<?>> eventTypes = lookupAllEventTypes(eventClass);
            int countTypes = eventTypes.size();
            for (int h = 0; h < countTypes; h++) {
                Class<?> clazz = eventTypes.get(h);
                subscriptionFound |= postSingleEventForEventType(event, postingState, clazz);
            }
        } else {
            subscriptionFound = postSingleEventForEventType(event, postingState, eventClass);
        }

		//表示找不到事件时的异常处理
        if (!subscriptionFound) {
            if (logNoSubscriberMessages) {
                logger.log(Level.FINE, "No subscribers registered for event " + eventClass);
            }
            if (sendNoSubscriberEvent && eventClass != NoSubscriberEvent.class &&
                    eventClass != SubscriberExceptionEvent.class) {
                post(new NoSubscriberEvent(this, event));
            }
        }
    }
```  




`eventInheritance `表示是否向上查找事件的父类，默认为 `true` ，可以 `EventBusBuilder` 的构造方法配置。当 `eventInheritance` 为 `true` 时，通过 `lookupAllEventTypes()` 找到所有父类事件并存在`list`,然后通过` postSingleEventForEventType()` 对事件逐一处理。  

`postSingleEventForEventType `方法源码：   


```  

private boolean postSingleEventForEventType(Object event, PostingThreadState postingState, Class<?> eventClass) {
        CopyOnWriteArrayList<Subscription> subscriptions;
        synchronized (this) {
            subscriptions = subscriptionsByEventType.get(eventClass);//1
        }
        if (subscriptions != null && !subscriptions.isEmpty()) {
            for (Subscription subscription : subscriptions) {//2
                postingState.event = event;
                postingState.subscription = subscription;
                boolean aborted = false;
                try {
                    postToSubscription(subscription, event, postingState.isMainThread);
                    aborted = postingState.canceled;
                } finally {
                    postingState.event = null;
                    postingState.subscription = null;
                    postingState.canceled = false;
                }
                if (aborted) {
                    break;
                }
            }
            return true;
        }
        return false;
    }

```  



注释1处同步取出所有` subscriptions `订阅对象集合，然后遍历 将 `event `事件和 `subscription` 订阅对象传递给 `postingState`，并调用 `postToSubscription()` 方法对事件进行处理。  

`postToSubscription` 方法代码：  


```  

private void postToSubscription(Subscription subscription, Object event, boolean isMainThread) {
        switch (subscription.subscriberMethod.threadMode) {
            case POSTING:
                invokeSubscriber(subscription, event);
                break;
            case MAIN:
                if (isMainThread) {
                    invokeSubscriber(subscription, event);
                } else {
                    mainThreadPoster.enqueue(subscription, event);
                }
                break;
            case MAIN_ORDERED:
                if (mainThreadPoster != null) {
                    mainThreadPoster.enqueue(subscription, event);
                } else {
                    // temporary: technically not correct as poster not decoupled from subscriber
                    invokeSubscriber(subscription, event);
                }
                break;
            case BACKGROUND:
                if (isMainThread) {
                    backgroundPoster.enqueue(subscription, event);
                } else {
                    invokeSubscriber(subscription, event);
                }
                break;
            case ASYNC:
                asyncPoster.enqueue(subscription, event);
                break;
            default:
                throw new IllegalStateException("Unknown thread mode: " + subscription.subscriberMethod.threadMode);
        }
    }
```  


取出订阅方法的 `threadMode` 线程模式，根据 `threadMode` 来分别处理，如果时 `MAIN` ,则直接通过反射直接运行订阅的方法，如果不是主线程，则通过` mainThreadPoster` 把我们的订阅事件添加到主线程队列中。`mainThreadPoster` 是 `HandlerPoster` 类型，继承在` Handler `,通过 `Handler` 将订阅方法切换到主线程执行。




## 订阅者取消注册  


取消注册调用` unregister() `方法  

```  

public synchronized void unregister(Object subscriber) {
        List<Class<?>> subscribedTypes = typesBySubscriber.get(subscriber);//1
        if (subscribedTypes != null) {
            for (Class<?> eventType : subscribedTypes) {
                unsubscribeByEventType(subscriber, eventType);//2
            }
            typesBySubscriber.remove(subscriber);//3
        } else {
            logger.log(Level.WARNING, "Subscriber to unregister was not registered before: " + subscriber.getClass());
        }
    }
```  


在订阅者注册过程中看到过 typesBySubscriber ，它是一个Map集合。  
注释1处，通过 subscriber 找到 subscribedTypes 事件类型集合  
注释3处，把 subscriber 从 subscribedTypes 移除  
注释2处，遍历 subscribedTypes ，并调用 unsubscribeByEventType()方法   

```  

private void unsubscribeByEventType(Object subscriber, Class<?> eventType) {
        List<Subscription> subscriptions = subscriptionsByEventType.get(eventType);//1
        if (subscriptions != null) {
            int size = subscriptions.size();
            for (int i = 0; i < size; i++) {
                Subscription subscription = subscriptions.get(i);
                if (subscription.subscriber == subscriber) {
                    subscription.active = false;
                    subscriptions.remove(i);
                    i--;
                    size--;
                }
            }
        }
}
```  


注释1处通过 `eventType` 来得到对应 `subscriptions` 订阅对象集合，并在 `for` 循环中判断如果订阅对象 `Subscription` 的属性 `subscriber `等于传进来的 `subscriber`,则从`subscriptions`中移除该订阅对象。`


![图](https://raw.githubusercontent.com/tuyrt7/tuyrt7.github.io/master/uploads/img/eventbus.png)  


终于写完了，跟着别人的思路分析源码只能对该部分有一个大概的了解，自己在经过这个跟进与记录的过程中，能对源码有更深一步的了解。  
最终的目的是学习别人的编程思想来形成自己一套编写规范，从而影响自己编程人生。   

fighting!!!  
