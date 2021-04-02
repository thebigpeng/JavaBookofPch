---
title: 什么是JDK、jre和jvm
date: 2021-03-06 15:24:34
tags: 
- 知识点
categories: Java面经基础
---

首先看官方的解释：

- **JDK**(<font color='cornflowerblue'>Java Development Kit</font> Java开发工具包)，JDK是提供给Java开发人员使用的，其中包含了java的开发工具，也包括了JRE。所以安装了JDK，就不用在单独安装JRE了。其中的开发工具包括编译工具(javac.exe) 打包工具(jar.exe)等。
- **JRE**(<font color='cornflowerblue'>Java Runtime Environment</font> Java运行环境) 是 JDK 的子集，也就是包括 JRE 所有内容，以及开发应用程序所需的编译器和调试器等工具。JRE 提供了库、Java 虚拟机（JVM）和其他组件，用于运行 Java 编程语言、小程序、应用程序。
- **JVM**(<font color='cornflowerblue'>Java Virtual Machine</font> Java虚拟机)，JVM可以理解为是一个虚拟出来的计算机，具备着计算机的基本运算方式，它主要负责把 Java 程序生成的字节码文件，解释成具体系统平台上的机器指令，让其在各个平台运行。

> 我们可以把Java程序设计语言、Java虚拟机、Java API 类库这三部分统称为 JDK（Java Development Kit），JDK 是用于支持 Java 程序开发的最小环境...另外，可以把 Java API 类库中的 Java SE API 子集和 Java 虚拟机这两部分统称为 JRE（Java Runtime Environment），JRE 是支持 Java 程序运行的标准环境。-《深入理解Java虚拟机：JVM高级特性与最佳实践（第2版）》





## 1.JDK的目录结构：

我们安装好的JDK包含`jdk_XXXXX`文件夹和`jre_XXXXX`文件夹，而`jdk_XXXXX`文件夹中又包含一个相同目录的`jre_XXXXX`文件夹，而`jvm`的目录一般在*C:\Program Files\Java\jdk1.8.0_45\jre\bin\server*。

`jvm`十分重要：

- jvm是整个 Java 实现跨平台的最核心内容，由 **Java 程序编译成的 .class 文件会在虚拟机上执行**。
- 另外在 JVM 解释 class 文件时需要调用类库 lib。在 JRE 目录下有两个文件夹 lib、bin，而 lib 就是 JVM 执行所需要的类库。
- jvm.dll 并不能独立工作，当 jvm.dll 启动后，会使用 explicit 方法来载入辅助动态链接库一起执行。

## 2.什么是JDK？

<font color='cornflowerblue'>JDK 是 JRE 的超集，JDK 包含了 JRE 所有的开发、调试以及监视应用程序的工具。</font>

这些工具如下：

- java – 运行工具，运行 .class 的字节码
- javac– 编译器，将后缀名为.java的源代码编译成后缀名为.class的字节码
- javap – 反编译程序
- javadoc – 文档生成器，从源码注释中提取文档，注释需符合规范
- jar – 打包工具，将相关的类文件打包成一个文件
- jdb – debugger，调试工具
- ..........（等等，不一一列出了）

## 3.什么是JRE？

<font color='cornflowerblue'>JRE 本身也是一个运行在 CPU 上的程序，用于解释执行 Java 代码。</font>

## 4.什么是JVM？

<font color='cornflowerblue'>其实简单说 JVM 就是运行 Java 字节码的虚拟机，JVM 是一种规范，各个供应商都可以实现自己 JVM虚拟机。</font>

## 5.jvm中的Client模式和Server模式

在 JVM 中有两种不同风格的启动模式， Client模式、Server模式。

- Client模式：加载速度较快。可以用于运行GUI交互程序。
- Server模式：加载速度较慢但运行起来较快。可以用于运行服务器后台程序。

## 6.总结

JDK是java开发工具包的缩写，JRE是java运行时环境的缩写，JDK包含了JRE，安装 JDK 完成后已经默认安装了 JRE 。程序员通过JDK写的源代码通过开发工具包中的javac编译器编译成后缀名为`.class`的字节码文件，再由java运行工具来执行。JVM是java虚拟机的缩写，编译好的字节码文件实际是在JVM上运行，JVM执行.class文件需要调用JRE中的库文件，从而与计算机的操作系统进行交互，它是java实现跨平台运行的核心。

![alt](JDK与JRE以及JVM的关系.png)

