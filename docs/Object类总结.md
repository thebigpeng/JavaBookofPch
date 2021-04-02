---
title: Object类总结
date: 2021-02-28 14:58:26
tags:
  - 基础知识点
categories: Java基础
---

<!-- toc -->

# Object类总结

## 1.什么是Object类？

Object是Java中的祖宗类，java中的所有类要么默认继承Object类，要么间接继承它，因此Object类中的方法所有方法都能使用，比较常见的方法有`public String toString()`、`public boolean equals(Object obj)`等。

## 2.toString()方法

- `public String toString()`：返回该对象的字符串表示

1. 该方法默认是<font color='red'>返回</font>当前<font color='red'>对象在</font><font color='red'>堆内存</font>中的<font color='red'>地址信息</font>，
2. 默认的地址信息格式：`类的全限名(包名)@内存地址`

<font color='cornflowerblue'>问题</font>：开发中直接输出对象，默认输出对象的地址是往往是毫无无意的，我们往往需要输出对象的内容数据信息。在实际开发中我们往往需要<font color='red'>把父类的toString()方法重写</font>来满足开发需求。

例如自定义的Person类：

```java
public class Person {  
    private String name;
    private int age;

    @Override
    public String toString() {
        return "Person{" + "name='" + name + '\'' + ", age=" + age + '}';
    }

    // 省略构造器与Getter Setter
}
```



## 3.equals()方法

1. 该方法<font color='red'>默认是比较两个对象的地址</font>是否相同，相同则返回true，反之返回false。
2. 直接比较两个对象的地址是否相等完全可以使用 `==` 来替代，因此可以<font color='red'>重写该方法来定制自己的比较规则</font>。

如果希望进行对象的内容比较，即所有或指定的部分成员变量相同就判定两个对象相同，则可以覆盖重写equals方法。例如：

```java
import java.util.Objects;

public class Person {	
	private String name;
	private int age;
	
    @Override
    public boolean equals(Object o) {
        // 如果对象地址一样，则认为相同
        if (this == o)
            return true;
        // 如果参数为空，或者类型信息不一样，则认为不同
        if (o == null || getClass() != o.getClass())
            return false;
        // 转换为当前类型
        Person person = (Person) o;
        // 要求基本类型相等，并且将引用类型交给java.util.Objects类的equals静态方法取用结果
        return age == person.age && Objects.equals(name, person.name);
    }
}
```

## 4.hashCode 与 equals

### 4.1什么是hashCode()

hashCode() 的作用是获取哈希码，也称为散列码；它实际上是返回一个 int 整数。这个哈希码的作用是确定该对象在哈希表中的索引位置。

散列表存储的是键值对(key-value)，它的特点是：能根据“键”快速的检索出对应的“值”。这其中就利用到了散列码！（可以快速找到所需要的对象）

### 4.2为什么要有hashCode()

**以“HashSet 如何检查重复”为例子来说明为什么要有 hashCode：**当你把对象加入到HashSet时， HashSet首先会计算该对象的hashCode值来判断对象加入的位置，同时与该位置其它已经加入的对象的hashCode值做比较，如果没有相同的hashCode，则hashSet则假设该对象没有重复出现；如果发现有重复的hashCode值对象，则会调用其`equals()`方法来检查hashCode相等的对象是否真的相同。如果相同则加入hashSet失败，如果不同则重新散列到其他位置（摘自《Head first java》第二版），这样使得`equals`次数大大减少，提高了执行速度。

通过我们可以看出：`hashCode()` 的作用就是**获取哈希码**，也称为散列码；它实际上是返回一个 int 整数。这个**哈希码的作用**是确定该对象在哈希表中的索引位置。**`hashCode()`在散列表中才有用，在其它情况下没用**。在散列表中 hashCode() 的作用是获取对象的散列码，进而确定该对象在散列表中的位置。

### 4.3hashCode（）与 equals（）的相关规定

1. 如果两个对象相等，则 hashcode 一定也是相同的
2. 两个对象相等,对两个对象分别调用 equals 方法都返回 true
3. 两个对象有相同的 hashcode 值，它们也不一定是相等的
4. **因此，equals 方法被覆盖过，则 hashCode 方法也必须被覆盖**
5. hashCode() 的默认行为是对堆上的对象产生独特值。如果没有重写 hashCode()，则该 class 的两个对象无论如何都不会相等（即使这两个对象指向相同的数据）

<font color='cornflowerblue'>为什么重写 equals 时必须重写 hashCode 方法？</font>

# Objects类

Objects类在JDK1.7及以上包含，依旧是<font color='red'>继承了Object类</font>。它提供了一些方法来操作对象，它由一些静态的实用方法组成，这些方法是<font color='red'>null-save（空指针安全的）</font>或<font color='red'>null-tolerant（容忍空指针的）</font>，用于计算对象的hashcode、返回对象的字符串表示形式、比较两个对象。

该类提供了两个方法：

- `public static boolean equals(Object a, Object b)`:判断两个对象是否相等。
- static boolean isNull(Object obj) 判断对象是否为null,如果为null返回true

## 1.equals方法

在比较两个对象的时候，<font color='cornflowerblue'>Object的equals方法容易抛出空指针异常</font>，而Objects类中的equals方法就优化了这个问题。

学习一下源码：

```java
public static boolean equals(Object a, Object b) {  
    return (a == b) || (a != null && a.equals(b));  
```

## 2.isNull

- static boolean isNull(Object obj) 判断对象是否为null，如果为null返回true。

该方法比较鸡肋。

```java
Student s1 = null;
Student s2 = new Student("蔡徐坤", 22);

// static boolean isNull(Object obj) 判断对象是否为null,如果为null返回true
System.out.println(Objects.isNull(s1)); // true
System.out.println(Objects.isNull(s2)); // false
```

