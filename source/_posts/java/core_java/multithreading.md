---
layout: post
title: 多线程
date: 2014-11-20
category: java
tags: [core_java]
toc: true
---
## 使用线程给其他任务提供机会
如果需要执行一个比较耗时的任务，应该使用独立的线程。下面是在一个单独的线程中执行一个任务的简单过程：
1. 将任务代码移到实现了`Runnable`接口的类的`run`方法中。这个接口非常简单，只有一个方法：<!--more-->
```java
public interface Runnable
{
    void run();
}
```
可以如下所示实现一个类：
```java
class MyRunnable implements Runnable
{
    public void run()
    {
        task code;
    }
}
```
2. 创建一个类对象:
```java
Runnable r = new MyRunnable();
```
3. 由`Runnable`创建一个`Thread`对象:
```java
Thread t = new Thread(r);
```
4. 启动线程：
```java
t.start();
```

**注释**:也可以通过构建一个`Thread`类的子类定义一个线程，如下所示:
```java
class MyThread extends Thread
{
    public void run()
    {
        task code;
    }
}
```
然后，构造一个子类的对象，并调用`start`方法。目前，这种方法已经不再推荐，应该从运行机制上减少需要并运行的任务数量。如果有很多任务，要为每个任务创建一个独立的线程所付出的代价太大了。可以使用线程池来解决这个问题。
**警告**:不要调用`Thread`类或`Runnable`对象的`run`方法。直接调用`run`方法，只会执行同一个线程中的任务，而不会启动新线程。应该调用`Thread.start`方法。这个方法将会创建一个执行`run`方法的新任务。

**线程状态**
线程可以有如下6种状态：
  * New(新生)
  * Runnable(可运行)
  * Blocked(被阻塞)
  * Waiting(等待)
  * Timed waiting(计时等待)
  * Terminated(被终止)
![线程状态]({{BASE_PATH}}/image/java/core_java/imag1.png)
## 线程的属性
其中包括：线程的优先级，守护线程，线程组以及处理为捕获异常的处理器。
### 线程优先级
在Java程序设计语言中，每一个线程有一个*优先级*。默认情况下，一个线程继承它的父线程父的优先级。
**`java.lang.Thread`**
* `void setPriority(int newPriority)`
设置线程的优先级。优先级必须在`Thread.MIN_PRIORITY`与`Thread.MAX_PRIORITY`之间。一般使用`Thread.NORM_PRIORITY`优先级。
* `static int MIN_PRIORITY` 最小优先级值为1
* `static int NORM_PRIORITY` 默认优先级为5
* `static int MAX_PRIORITY` 最高优先级为10
* `static void yield()`
导致当前执行线程处于让步状态。如果有其他的可运行线程具有至少与此线程同样高的优先级，那么这些线程接下来会被调度。注意这是一个静态方法。
### 守护线程`Daemon Threads`
调用方法：`t.setDaemon(true);`可以把进程转化成守护线程。
守护线程的唯一用途是为其他线程提供服务。计时器就是一个例子，它定时地发送”时间滴答`timer ticks`”信号给其他线程或者清空过时的高速缓存项的线程。当只剩下守护线程时，虚拟机就退出了，由于如果只剩下守护线程，就没必要继续运行程序了。
**`java.lang.Thread`**
* `void setDaemon(boolean isDaemon)`
标识该线程为守护线程或用户线程。这一方法必须在线程启动之前调用。


