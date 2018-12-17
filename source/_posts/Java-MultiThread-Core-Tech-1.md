---
title: Java多线程编程核心技术笔录（一）
categories:
  - java
copyright: true
date: 2018-12-17 21:19:35
tags: [java,multiThread]
top: true
password:
---

今天分析Thread.currentThread()和This的差异：

创建CountOperate.java文件：

```java
package syswar.cc;

public class CountOperate extends Thread {
	public CountOperate() {
		System.out.println("CountOp-------begin");
		System.out.println("Thread.currentThread.getName()="+ Thread.currentThread().getName());
		System.out.println("Thread.currentThread.isAlive()="+ Thread.currentThread().isAlive());
		System.out.println("this.getname()="+ this.getName());
		System.out.println("this.isAlive()="+ this.isAlive());
		System.out.println("CountOp-------end");
	}
	@Override
	public void run() {
		System.out.println("run------begin");
		System.out.println("Thread.currentThread.getName()="+ Thread.currentThread().getName());
		System.out.println("Thread.currentThread.isAlive()="+ Thread.currentThread().isAlive());
		System.out.println("this.getname()="+ this.getName());
		System.out.println("this.isAlive()="+ this.isAlive());
		System.out.println("run------end");
	}
}
```

创建Run.java文件：

```java
package syswar.cc;

public class Run {

	public static void main(String[] args) {
		CountOperate c= new CountOperate();
		Thread t1 = new Thread(c);
		System.out.println("main begin t1 isAlive=" + t1.isAlive());
		t1.setName("A");
		t1.start();
		System.out.println("main end t1 isAlive=" + t1.isAlive());
	}
}
```

程序运行结果如下：

> CountOp-------begin
> Thread.currentThread().getName()=main
> Thread.currentThread().isAlive()=true
> this.getname()=Thread-0
> this.isAlive()=false
> CountOp-------end
> main begin t1 isAlive=false
> main end t1 isAlive=true
> run------begin
> Thread.currentThread().getName()=A
> Thread.currentThread().isAlive()=true
> this.getname()=Thread-0
> this.isAlive()=false
> Thread.currentThread() == this: false
> run------end

通过结合[CSDN这篇文章](https://blog.csdn.net/yezis/article/details/57513130)的讲解，我们知道：

`CountOp-------begin`和`CountOp-------end`之间为构造函数运行结果，由main函数调用并执行，所以`Thread.currentThread().getName()=main`很好理解。

那这个this是什么？CountOperate的引用，是个线程类，但是这个线程类并没有设置名字，所以Thread默认给了一个Thread-0。

通过源码可以很清楚的知道这点。

```java
public Thread() {
	init(null, null, "Thread-" + nextThreadNum(), 0);
}
```

由于只运行了CountOperate构造函数，并未开始执行，因此`this.isAlive()=false`。

---

将CountOperate作为参数传给Thread对象并执行start()启动线程，我们直接启动的线程实际上是t1，而CountOperate作为参数将属性Target赋值给Thread，之后Thread的run方法中调用Target.run()；所以`Thread.currentThread()`是Thread的引用t1，而this依旧是`CountOperate`的引用，所以不一样，即`Thread.currentThread() == this: false`；此时`this.isAlive()=false`即`CountOperate.isAlive()=false`，它还未真正启动，需要等待t1执行run()方法来启动它。