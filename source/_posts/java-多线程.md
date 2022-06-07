---
title: java 多线程
date: 2022-06-04 22:23:53
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

[****JVM程序计数器 VS OS程序计数器****](https://dwdw666.github.io/2022/02/10/JVM%E7%A8%8B%E5%BA%8F%E8%AE%A1%E6%95%B0%E5%99%A8-VS-OS%E7%A8%8B%E5%BA%8F%E8%AE%A1%E6%95%B0%E5%99%A8/)
