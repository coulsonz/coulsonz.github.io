---
title: 问与答
categories:
  - java
copyright: true
date: 2018-12-19 10:30:06
tags: [java]
top: true
password:
---

## Q1. 以下代码有逸出，求解？

书中说道：当ThisEscape发布EventListener时，它也无条件地发布了封装ThisEscape的实例，因为内引类的实例包含了对封装实例隐含的引用。

```java
public class ThisEscape {
    public ThisEscape(EventSource source) {
        source.registerListener {
            new EvenetListener() {
                public void onEvent(Event e) {
                    doSomething(e);
                }
            }
        }
    }
}
```

<!-- more -->

使用工厂方法防止this引用在构造期间逸出

```java
public class SafeListener {
    private final EvenetListener listener;
    
    private SafeListener() {
        listener = new EventListener() {
            public void onEvent(Event e) {
                doSomething(e);
            }
        }
    }
    
    public static SafeListener newInstance(EventSource source) {
        SafeListener safe = new SafeListener();
        source.registerListener(safe.listener);
        return safe;
    }
}
```

