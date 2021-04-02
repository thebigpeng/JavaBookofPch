---
title: synchronized关键字
date: 2021-03-08 10:43:51
tags: 
 - 多线程高级
categories: Java面经
---

<!-- toc -->

## 1.三种应用方式

### 1.1修饰实例方法

作用于当前实例加锁，进入同步代码前要获得当前实例的锁。

### 1.2修饰静态方法

修饰`static`方法时，作用于当前类对象(Class对象，每个类都有一个Class对象)，进入同步代码前要获得当前类对象（Class对象）的锁。

<font color='orange'>修饰不同的方法只要他们的锁不一样就不会发生互斥。</font>

### 1.3修饰代码块

即同步代码块，指定加锁对象，对给定对象加锁，进入同步代码库前要获得给定对象的锁。

```java
public class AccountingSync implements Runnable{
    static AccountingSync instance=new AccountingSync();
    static int i=0;
    @Override
    public void run() {
        //省略其他耗时操作....
        //使用同步代码块对变量i进行同步操作,锁对象为instance
        synchronized(instance){
            for(int j=0;j<1000000;j++){
                    i++;
              }
        }
    }
    public static void main(String[] args) throws InterruptedException {
        Thread t1=new Thread(instance);
        Thread t2=new Thread(instance);
        t1.start();t2.start();
        t1.join();t2.join();
        System.out.println(i);
    }
}

作者：foofoo
链接：https://juejin.cn/post/6844903670933356551
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。
```

将synchronized作用于一个给定的括号里的实例对象instance，即当前实例对象就是锁对象，每次当线程进入synchronized包裹的代码块时就会要求当前线程持有instance实例对象锁，如果当前有其他线程正持有该对象锁，那么新到的线程就必须等待，这样也就保证了每次只有一个线程执行i++;操作。

