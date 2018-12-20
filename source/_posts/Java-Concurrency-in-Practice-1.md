---
title: JAVA并发编程实践笔录（一）
categories:
  - java
copyright: true
date: 2018-12-19 09:53:06
tags: [java,multiThread]
top: true
password:
---

## 1. volatile变量

例子：数绵羊

```java
volatile boolean asleep;
……
	while (!asleep)
		countSomeSheep();
```

​	上面的例子示范了一种volatile变量的典型应用：检查状态标记，以确定是否退出一个循环。为了让该程序正常工作，asleep必须标记为volatile，	否则执行检查的线程不会注意到asleep已被其他线程修改。

​	当然，我们也可以用锁来代替volatile，同样能保证对asleep变量修改的可见性。但是锁会使代码变得复杂。

> 加锁可以保证可见性与原子性；volatile变量只能保证可见性。

​	只有满足了下面所有标准后，你才能使用volatile变量：

- 写入变量时并不依赖变量的当前值；或者能够确保只有单一的线程修改变量的值；
- 变量不需要与其他的状态变量共同参与不变约束；
- 访问变量时，没有其他的原因需要加锁。

