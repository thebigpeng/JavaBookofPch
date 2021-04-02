---
title: Java中的伪共享和各种锁
date: 2021-03-05 11:23:47
tags:
  - 多线程
categories: Java基础

---

<!-- toc -->

## 1.伪共享

### 1.1CPU内存的存储结构

计算机通过在CPU与主内存之间添加一级或者多级**高速缓冲存储器(Cache)**来解决主内存与CPU之间运行速度差较大的问题，这个Cache一般是集成到CPU的内部。

两级Cache结构如下：

![alt](二级Cache.png)

Cache内部是用行来储存的，每一行称作一个**Cache行**，**<font color='red'>Cache行</font>**是<font color='red'>Cache与主内存进行数据交换的单位</font>，Cache行的大小一般为2的幂次数字节。

![alt](Cache行结构.png)

### 1.2什么是伪共享

当CPU去访问某个变量的时候，首先检查CPU的Cache中是否有该变量，有则直接获取，否则就去主内存中去获取该变量，然后直接把改变量所在内存区域的<font color='cornflowerblue'>一个Cache行大小</font>的内存复制到Cache中，这一步可能会把多个变量放到一个cache行中。当<font color='cornflowerblue'>多个线程同时修改一个缓存行里面的多个变量时，由于同时只有一个线程操作缓存行</font>，与将每个变量放到一个缓存行中相比性能会下降，这就是<font color='cornflowerblue'>**伪共享**</font>。

> 当我们创建一个数组时，数组里面的多个元素就会被放入同一个缓存行，这样的操作对单线程的性能是有利的。



### 1.3如何避免多线程的伪共享

<font color='orange'>JDK8之前</font>一般都是通过**字节填充**的方式来避免该问题，也就是创建一个变量时使用填充字段来填充该变量所在的缓存行。

示例代码如下：

```java
public final static class FiledDong{
    public volatile long value = 0L;
    public volatile p1, p2, p3, p4, p5, p6;
}
```

<font color='cornflowerblue'>原理如下</font>：

假如缓存行的大小时64字节，那么`FiledDong`类里面填充了6个long型的变量，每个long型变量占用8字节，加上value变量的8字节共56字节， 另外再由于`FiledDong`是类对象，类对象的字节码的对象头占用8字节，因此这个类刚好可以放入一个缓存行。

**<font color='red'>JDK8</font>**则提供了一个`sun.misc.Contended`的注解来解决伪共享问题，不仅可以用来修饰类，也能直接修饰变量。

用法示例：

```java
@sun.misc.Contended
public final static class FiledDong{
    public volatile long value = 0L;
    
}
```

## 2.多线程的各种锁

### 2.1悲观锁与乐观锁

这你两个名字都是数据库中引入的名词。

**<font color='cornflowerblue'>悲观锁</font>**是指对数据被外界修改持保守态度，认为数据很容易就被其它线程修改，所以在数据被处理前首先对数据进行加锁，并且整个过程数据都出于锁定状态。

在数据库中，操作数据记录时首先给记录加排它锁，只有当线程获取锁成功后才能进行操作，获取失败说明其它线程正在对该记录进行操作。

**<font color='cornflowerblue'>乐观锁</font>**则相反，认为数据在一般情况下不会造成冲突，因此在访问记录前不会加排他锁，而是在数据提交更新的时候才会正式对数据冲突与否进行检查。乐观锁直到提交时才锁定，因此不会产生任何死锁。

### 2.2公平锁和非公平锁

根据<font color='cornflowerblue'>线程获取锁的抢占机制</font>，可以将锁分为公平锁和非公平锁。**公平锁**表示线程获取锁的顺序是按照请求锁的时间早晚来决定的，早请求早获得。**非公平锁**则是在运行时闯入，先来不一定先得到。

`ReentrantLock`提供了这两种锁的实现

- 公平锁：`ReentrantLock pairLock = new ReentrantLock(true);`
- 非公平锁：`ReentrantLock unPairLock = new ReentrantLock(false);`

### 2.3独占锁和共享锁

根据线程是否能被单个线程持有还是能被多个线程共同持有分为独占锁和非独占锁。

**<font color='cornflowerblue'>独占锁</font>**保证在任何情况下都只有一个线程能得到锁，`ReentrantLock`就是以独占方式实现的。独占锁也是一种悲观锁，每次访问资源都会先加上互斥锁，限制了并发性，其它线程值能等工作线程释放锁后才能进行读取。

**<font color='cornflowerblue'>共享锁</font>**允许被多个线程同时持有，如`ReadWriteLock`读写锁，允许多个线程同时操作。它是一种乐观锁。

### 2.4自旋锁

当一个线程获取锁失败后，会被切换到<font color='orange'>内核状态</font>后被挂起，等该线程获取到锁时需要再切换到内核状态来唤醒它。从用户态切换到内核状态开销很大，对并发性能有一定的影响。

**<font color='cornflowerblue'>自旋锁</font>**让线程在获取该锁时，若获取失败也不会马上阻塞自己，在不放弃CPU使用权的情况下多次尝试，若尝试到指定次数后仍没获取成功才会被阻塞挂起。

> 该锁用CPU的使用时间换线程阻塞与调度的开销，很可能浪费这个CPU时间。