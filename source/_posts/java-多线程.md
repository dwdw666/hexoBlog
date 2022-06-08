---
title: java 多线程
date: 2022-05-04 22:23:53
tags:
categories: Java
---
### java实现多线程的方式

- 继承thred接口
- 实现runnable接口
- 实现Callable接口

### 创建线程的方式

- 通过thread类直接创建线程
- 利用线程池内部类创建线程

### 启动线程的方式

- 调用线程的start()方法
- 利用线程池内部创建线程  那啥是线程池呢

### 上下文切换

回忆起了操作系统中的pcb，tcb。操作系统在切换线程的时候也会保留下pcb和tcb的信息

cpu执行线程的任务时，会为线程分配时间片，以下几种情况会发生上下文切换。

1. 线程的cpu时间片用完
2. 垃圾回收
3. 线程自己调用了 sleep、yield、wait、join、park、synchronized、lock 等方法

当发生上下文切换时，操作系统会保存当前线程的状态，并恢复另一个线程的状态,jvm中有块内存地址叫程序计数器，用于记录线程执行到哪一行代码,是线程私有的。

[****JVM程序计数器 VS OS程序计数器****](https://www.notion.so/JVM-VS-OS-ae6a6d35cd1645acbb8a4b25d712586b)

### 守护线程

java进程需要等待所有线程都运行结束，才会结束，有一种特殊线程叫守护线程，当所有的非守护线程都结束后，即使它没有执行完，也会强制结束。

垃圾回收线程就是典型的守护线程

### 线程的阻塞

 join是指调用该方法的线程进入阻塞状态，等待某线程执行完成后恢复运行

### 线程的打断

时间片的切换

### 线程的状态

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/8678ca5e-e2f0-42d2-94e0-44a35d06ce01/Untitled.png)

线程的五种状态

![93f311262656412c83a99b9c03b4815e_tplv-k3u1fbpfcp-zoom-1.png](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/728f1c95-878a-441c-91f3-4a500b478b89/93f311262656412c83a99b9c03b4815e_tplv-k3u1fbpfcp-zoom-1.png)

### 线程通信

线程间通信可以通过共享变量+wait()&notify()来实现

1. Thread-0先获取到对象的锁，关联到monitor的owner，同步代码块内调用了锁对象的wait()方法，调用后会进入waitSet等待，Thread-1同样如此，此时Thread-0的状态为Waitting
2. Thread2、3、4、5同时竞争，2获取到锁后，关联了monitor的owner，3、4、5只能进入EntryList中等待，此时2线程状态为 Runnable，3、4、5状态为Blocked
3. 2执行后，唤醒entryList中的线程，3、4、5进行竞争锁，获取到的线程即会关联monitor的owner
4. 3、4、5线程在执行过程中，调用了锁对象的notify()或notifyAll()时，会唤醒waitSet的线程，唤醒的线程进入entryList等待重新竞争锁

注意:

1. Blocked状态和Waitting状态都是阻塞状态
2. Blocked线程会在owner线程释放锁时唤醒
3. wait和notify使用场景是必须要有同步，且必须获得对象的锁才能调用,使用锁对象去调用,否则会抛异常
- wait() 释放锁 进入 waitSet 可传入时间，如果指定时间内未被唤醒 则自动唤醒
- notify()随机唤醒一个waitSet里的线程
- notifyAll()唤醒waitSet中所有的线程

![Untitled](https://s3-us-west-2.amazonaws.com/secure.notion-static.com/2485cb7d-fd88-489e-94d8-7e21590e4939/Untitled.png)

```java
public class Threaded_communication {
    static final Objectlock= new Object();
    public static void main(String ...arg){
       new Thread(()->{
           synchronized (lock){
               System.out.println("开始执行1");
               try{
lock.wait();
               }catch (InterruptedException e){
                   e.printStackTrace();
               }
               System.out.println("继续执行核心逻辑1");
           }
       },"t1").start();

       new Thread(()->{
           synchronized (lock){
               System.out.println("开始执行2");
               try{
lock.wait();
               }catch (InterruptedException e){
                   e.printStackTrace();
               }
               System.out.println("继续执行核心逻辑2");
           }
       },"t2").start();

       try{
           Thread.sleep(2000);
       }catch(InterruptedException e){
           e.printStackTrace();
       }
       System.out.println("开始唤醒");
       synchronized (lock){
lock.notifyAll();
       }
    }
}

```

wait 和 sleep的区别

wait是Object的方法，sleep是Thread的方法

wait会立即释放锁，sleep不会释放锁

### 生产者消费者模型

包含一点java内部类的概念

[https://cloud.tencent.com/developer/article/1675402](https://cloud.tencent.com/developer/article/1675402)

```java
import java.util.LinkedList;

public class Productor_consummer {
  public static void main(String... args) throws InterruptedException {
    MessageQueue queue = new MessageQueue(2);

    for (int i = 0; i < 3; i++) {
      int id = i;
      new Thread(
              () -> {
                queue.put(new Message(id, "值" + id));
              },
              "生产者" + i)
          .start();
    }

    new Thread(
            () -> {
              while (true) {
                queue.take();
              }
            },
            "消费者")
        .start();
  }

  // 消息队列被生产者和消费者持有
  static class MessageQueue {
    private LinkedList<Message> list = new LinkedList<>();

    // 容量
    private int capacity;

    public MessageQueue(int capacity) {
      this.capacity = capacity;
    }
    // 生产
    public void put(Message message) {
      synchronized (list) {
        while (list.size() == capacity) {
          System.out.println("队列已满");
          try {
            list.wait();
          } catch (InterruptedException e) {
            e.printStackTrace();
          }
        }
        list.addLast(message);
        System.out.println("生产消息：{}");
        list.notifyAll();
      }
    }

    public Message take() {
      synchronized (list) {
        while (list.isEmpty()) {
          System.out.println("队列空了等待消费者");
          try {
            list.wait();
          } catch (InterruptedException e) {
            e.printStackTrace();
          }
        }
        Message message = list.removeFirst();
        System.out.println("消费者消息：{}");
        // 消费后通知生产者
        list.notifyAll();
        return message;
      }
    }
  }

  static class Message {
    private int id;
    private Object value;

    public Message(int id, Object value) {
      this.id = id;
      this.value = value;
    }
  }
}

```

### ****ReentrantLock 可重入锁****

实例:

一个线程输出a，一个线程输出b，一个线程输出c，abc按照顺序输出，连续输出5次

可重入锁的原理：

重入锁实现可重入性原理或机制是：每一个锁关联一个线程持有者和计数器，当计数器为 0 时表示该锁没有被任何线程持有，那么任何线程都可能获得该锁而调用相应的方法；当某一线程请求成功后，JVM会记下锁的持有线程，并且将计数器置为 1；此时其它线程请求该锁，则必须等待；而该持有锁的线程如果再次请求这个锁，就可以再次拿到这个锁，同时计数器会递增；当线程退出同步代码块时，计数器会递减，如果计数器为 0，则释放该锁。

```java
import java.util.concurrent.locks.Condition;
import java.util.concurrent.locks.ReentrantLock;

public class demo_reentrantLock_2 {
  public static void main(String... arg) {
    AwaitSignal awaitSignal = new AwaitSignal(5);
    // 构建三个条件变量
    Condition a = awaitSignal.newCondition();
    Condition b = awaitSignal.newCondition();
    Condition c = awaitSignal.newCondition();

    // 开启三个线程
    new Thread(
            () -> {
              awaitSignal.print("a", a, b);
            },
            "a")
        .start();

    new Thread(
            () -> {
              awaitSignal.print("b", b, c);
            },
            "b")
        .start();

    new Thread(
            () -> {
              awaitSignal.print("c", c, a);
            },
            "c")
        .start();
    try {
      Thread.sleep(5000);
    } catch (InterruptedException e) {
      e.printStackTrace();
    }
    awaitSignal.lock();
    try {
      a.signal();
    } finally {
      awaitSignal.unlock();
    }
  }

  static class AwaitSignal extends ReentrantLock {
    private int loopNumber;

    public AwaitSignal(int loopNumber) {
      this.loopNumber = loopNumber;
    }

/**
*@paramprint输出的字符
*@paramcurrent当前条件变量
*@paramnext下一个条件变量
*/
public void print(String print, Condition current, Condition next) {
      for (int i = 0; i < loopNumber; i++) {
        lock();
        try {
          try {
            current.await();
            System.out.println(print);
          } catch (InterruptedException e) {
            System.out.println(e);
          }
          next.signal();
        } finally {
          unlock();
        }
      }
    }
  }
}

```

### condition

### 死锁

```java
public class DeadLock {
  public static void main(String... arg) {
    Beer beer = new Beer();
    Story story = new Story();
    new Thread(
            () -> {
              synchronized (beer) {
                System.out.println("我有酒，给我故事");
                try {
                  Thread.sleep(1000);
                } catch (InterruptedException e) {
                  e.printStackTrace();
                }
                synchronized (story) {
                  System.out.println("小王开始喝酒讲故事");
                }
              }
            })
        .start();
    new Thread(
            () -> {
              synchronized (story) {
                System.out.println("我有故事，给我酒");
                try {
                  Thread.sleep(1000);
                } catch (InterruptedException e) {
                  System.out.println(e);
                }
                synchronized (beer) {
                  System.out.println("老王开始喝酒讲故事");
                }
              }
            })
        .start();
  }

  static class Beer {}

  static class Story {}
}

```

****java内存模型(JMM)****

volital 关键字

### 线程池

线程的资源很宝贵，不可能无限的创建，必须要有管理线程的工具，线程池就是一种管理线程的工具，java开发中经常有池化的思想，如 数据库连接池、Redis连接池等。

预先创建好一些线程，任务提交时直接执行，既可以节约创建线程的时间，又可以控制线程的数量。