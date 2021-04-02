---
title: String与常量池
date: 2021-03-08 19:39:09
tags:
 - 基础知识点
categories: Java面经基础
---

<!-- toc -->

## 1 String常量何时会进入常量池

- **编译期**：<font color='cornflowerblue'>通过双引号声明的字符串常量</font>，在前端编译期将被静态的写入class文件中的“常量池”。该“常量池”会在类加载后被载入“内存中的常量池”，也就是我们平时所说的常量池。
- **运行期间**：**调用`String.intern`()方法**，可能将该String对象动态的写入上述“内存中常量池”。

```java
public native String intern();

    /**
     * Returns a string whose value is the concatenation of this
     * string repeated {@code count} times.
     * <p>
     * If this string is empty or count is zero then the empty
     * string is returned.
     *
     * @param   count number of times to repeat
     *
     * @return  A string composed of this string repeated
     *          {@code count} times or the empty string if this
     *          string is empty or count is zero
     *
     * @throws  IllegalArgumentException if the {@code count} is
     *          negative.
     *
     * @since 11
     */
```

`String.intern()`是一个native方法。根据Javadoc，如果常量池中存在当前字符串, 就会直接返回当前字符串. 如果常量池中没有此字符串, 会将此字符串放入常量池中后, 再返回。

## 2 **jdk6**和**jdk7**下String.intern()的区别

问：`String s = new String("abc");`<font color='red'>这个语句创建了几个对象</font>？

答：<font color='orange'>创建了两个。</font>

- 第一个对象，内容"abc"，存储在常量池中。
- 第二个对象，内容"abc"，存储在堆(java Heap)中。



在**JDK6**中：常量池放在Perm区中，和正常的Heap（指Eden、Surviver、Old区）完全分开。具体来说：<font color='cornflowerblue'>使用引号声明的字符串都是通过编译和类加载直接载入常量池，位于Perm区；</font><font color='orange'>new出来的String对象位于Heap（E、S、O）中。</font>拿一个Perm区的对象地址和Heap中的对象地址进行比较，肯定是不相同的。

在jdk6及之前的版本中，字符串常量池都是放在Perm区的。Perm区的默认大小只有4M，如果多放一些大字符串，

在**JDK7**中：将字符串常量池从Perm区移到正常的Heap（E、S、O）中了。


[具体参考此链接](https://tech.meituan.com/2014/03/06/in-depth-understanding-string-intern.html)