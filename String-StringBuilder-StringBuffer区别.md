---
title: String、StringBuilder和StringBuffer区别
date: 2021-03-07 15:29:22
tags:
 - 基础知识点
categories: Java面经基础
---

<!-- toc -->

## 1.简单区别

- String是不可改变长度的，StringBuilder和StringBuffer是可变长度的。
- 从运行速度来说，StringBuilder > StringBuffer > String。
- 从线程安全来说，<font color='cornflowerblue'>StringBuilder是线程不安全的</font>，而<font color='orange'>StringBuffer是线程安全</font>的。

`String`适用于操作少量字符串的场合，`StringBuilder`适用于单线程下字符缓冲区进行大量操作的场合；`StringBuffer`适用在多线程下字符缓存区进行大量操作的场合。

## 2.为什么String是不可变长度的？

查看`String`类源码发现其使用`final`关键字来修饰字符数组保存字符串`private final byte[] value`，因此是不可变长的。而`StringBuilder` 与 `StringBuffer` 都继承自 `AbstractStringBuilder` 类，在 `AbstractStringBuilder` 中也是使用字符数组保存字符串`char[]value` 但是没有用 `final` 关键字修饰，所以这两种对象都是可变的。

```java
public final class String
    implements java.io.Serializable, Comparable<String>, CharSequence {

    /**
     * The value is used for character storage.
     *
     * @implNote This field is trusted by the VM, and is a subject to
     * constant folding if String instance is constant. Overwriting this
     * field after construction will cause problems.
     *
     * Additionally, it is marked with {@link Stable} to trust the contents
     * of the array. No other facility in JDK provides this functionality (yet).
     * {@link Stable} is safe here, because value is never null.
     */
    @Stable
    private final byte[] value;
```

**final**： 不可改变，最终的含义。可以用于修饰类、方法和变量。

- 类：被修饰的类，不能被继承。
- 方法：被修饰的方法，不能被重写。
- 变量：被修饰的变量，有且仅能被赋值一次。

对于如下代码中的String对象长度发生变化如何解释：

```java
public class demo {
    public static void main(String[] args) {
        String str = "我是";
        str = str + "程序猿！";
        System.out.println(str);
    }
}

```

分析:通过编译与反编译可以发现，再使用`+`进行字符串的拼接操作时，实际上JVM初始了一个`StringBuilder`来参与拼接，拼接完后的`StringBuilder`对象通过调用其`toString()`方法来返回最终的字符串。查看`toString()`方法的源码如下，可以发现最终是<font color='orange'>new了一个新的String对象而不是改变了原来String对象的内容和长度来返回</font>，相当于用旧String对象的引用指向了新对象。

```java
@Override
    @HotSpotIntrinsicCandidate
    public String toString() {
        // Create a copy, don't share the array
        return isLatin1() ? StringLatin1.newString(value, 0, count)
                          : StringUTF16.newString(value, 0, count);
    }

```

由于`String`类是被`final`修饰的，该类不能不继承！另外

StringBuilder和StringBuffer都被final修饰，都不可继承。

<font color='cornflowerblue'>要是问用final修饰的好处是啥？</font>

[参考我写的另一篇总结。](https://jamesforlife.top/2021/03/07/final%E6%80%BB%E7%BB%93/)

## 3.为什么`StringBuffer`是线程安全的？

通过查看`StringBuffer`的源码，可以发现`StringBuffer`类的<font color='orange'>常用方法基本都使用了</font>`synchronized`<font color='orange'>来进行同步</font>，故`StringBuffer`是线程安全带的。然而`StringBuilder`并没有，这也是`StringBuffer`运行速度比`StringBuilder`慢的原因。

[`synchronized`关键字总结如下。]()

## 4.面试题解析

[面试题转自掘金](https://juejin.cn/post/6867158692018520072#heading-3)

```
public class Demo {

    public static void main(String[] args) {
        String str = null;
        str = str + "";
        System.out.println(str);
    }

}

```

问输出是什么？

输出是<font color='red'>null</font>，因为使用`+`进行拼接实际上是会转换为`StringBuilder`使用`append`方法进行拼接。

通过查看源码发现，`StringBuilder`是调用了其父类`AbstractStringBuilder`中的`append`方法来增添字符，

```java
public AbstractStringBuilder append(String str) {
        if (str == null) {
            return appendNull();
        }
        int len = str.length();
        ensureCapacityInternal(count + len);
        putStringAt(count, str);
        count += len;
        return this;
    }
```

```java
private AbstractStringBuilder appendNull() {
        ensureCapacityInternal(count + 4);
        int count = this.count;
        byte[] val = this.value;
        if (isLatin1()) {
            val[count++] = 'n';
            val[count++] = 'u';
            val[count++] = 'l';
            val[count++] = 'l';
        } else {
            count = StringUTF16.putCharsAt(val, count, 'n', 'u', 'l', 'l');
        }
        this.count = count;
        return this;
    }

```

这样一看答案也就清晰了。