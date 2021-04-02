---
title: 多态、包、权限修饰符、内部类、Date类总结
date: 2021-02-28 16:22:44
tags:
  - 基础知识点
categories: Java基础
---

# Date类

## 1.概述

jar包：`java.util.Date`

- `public Date()`：从运行程序的<font color='red'>此时此刻到时间原点</font>经历的毫秒值,转换成<font color='red'>Date对象</font>，分配Date对象并初始化此对象，以表示分配它的时间（精确到毫秒）。
- `public Date(long date)`：将指定参数的毫秒值date,转换成Date对象，分配Date对象并初始化此对象，以表示自从标准基准时间（称为“历元（epoch）”，即1970年1月1日00:00:00 GMT）以来的指定毫秒数。

使用无参构造，可以自动设置当前系统时间的毫秒时刻；指定long类型的构造参数，可以自定义毫秒时刻。例如：

```java
import java.util.Date;

public class Demo01Date {
    public static void main(String[] args) {
        // 创建日期对象，把当前的时间
        System.out.println(new Date()); // Tue Jan 16 14:37:35 CST 2020
        // 创建日期对象，把当前的毫秒值转成日期对象
        System.out.println(new Date(0L)); // Thu Jan 01 08:00:00 CST 1970
    }
}
```

## 2.Date常用方法

- `public long getTime()` 把日期对象转换成对应的**<font color='red'>时间毫秒值</font>**。
- `public void setTime(long time)` 把方法参数给定的**<font color='red'>毫秒值设置给日期对象</font>**

示例代码：

```java
    public class DateDemo02 {
    public static void main(String[] args) {
        //创建日期对象
        Date d = new Date();
        
        //public long getTime():获取的是日期对象从1970年1月1日 00:00:00到现在的毫秒值
        //System.out.println(d.getTime());
        //System.out.println(d.getTime() * 1.0 / 1000 / 60 / 60 / 24 / 365 + "年");

        //public void setTime(long time):设置时间，给的是毫秒值
        //long time = 1000*60*60;
        long time = System.currentTimeMillis();
        d.setTime(time);

        System.out.println(d);
    }
}
```
> 小结：Date表示特定的时间瞬间，我们可以使用Date对象对时间进行操作。

