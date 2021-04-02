---
title: Java中的IO
date: 2021-03-06 19:52:14
tags:
 - 基础知识点
categories: Java基础
---

<!-- toc -->

## 1.什么是流？

**概念**：<font color='orange'>流是内存与存储设备之间传输数据的通道</font>。

## 2.流的分类

​	<font color='cornflowerblue'>按方向分</font>可以分为输入流和输出流。

- 输入流：以内存为基准，将存储设备中的内容读到内存中的流。
- 输出流：以内存为基准，将内存中的内容写到存储设备中去的流。

<font color='cornflowerblue'>按照流的内容</font>分：

- 字节流：流中的数据的最小单位是一个个的字节的流。
- 字符流：流中的数据的最小单位是一个个的字符的流。(<font color='cornflowerblue'>文本内容！</font>)

## 3.IO流的顶级父类

|            |           **输入流**            |              输出流              |
| :--------: | :-----------------------------: | :------------------------------: |
| **字节流** | 字节输入流<br />**InputStream** | 字节输出流<br />**OutputStream** |
| **字符流** |   字符输入流<br />**Reader**    |          字符输出流<br           |

## 4.FileInputStream类（字节输入流）

`java.io.FileInputStream `类是文件输入流，从文件中读取字节。

**构造方法**

- `FileInputStream(File file)`： 通过打开与实际文件的连接来创建一个 FileInputStream ，该文件由文件系统中的 File对象 file命名。 
- `FileInputStream(String name)`： 通过打开与实际文件的连接来创建一个 FileInputStream ，该文件由文件系统中的路径名 name命名。  

```java
public class FileInputStreamDemo {
    public static void main(String[] args) throws Exception {
        //1.创建文件对象
        File file = new File("src/字节流的使用/demo.txt");
        //2.创建一个字节输入流管道与源文件接通
        InputStream inputStream = new FileInputStream(file);
        //3.读取一个字节的编号返回,读取完毕则返回-1
        int re = 0;
        while ((re = inputStream.read())!=-1){
            System.out.print((char)re);
        }
    }
}

```

<font color='cornflowerblue'>一个一个字节读性能很差，读取英文和数字没问题，但读取中文有问题(一个中文字符为三个字节)，因此不推荐,推荐使用桶</font>。

**使用字节数组读取**：`read(byte[] b)`，每次读取b的长度个字节到数组中，返回读取到的有效字节个数，读取到末尾时，返回`-1` ，

桶可以重复使用

```java
public class FileInputStreamDemo02 {
    public static void main(String[] args) throws Exception {
        InputStream inputStream = new FileInputStream("src/字节流的使用/demo.txt");
        //定义一个字节数组读取数据,定义一个桶
        byte[] buffer = new byte[3];
        //读取桶大小的数据
        int len = inputStream.read(buffer);
        String rs = new String(buffer);
        System.out.println(rs);
    }
}
```

这样写一次读入的数据永远是你桶的大小，之后从把桶里的数据全部倒出，容易出问题，可能最后一次文本的内容大小不足桶的大小，最后倒的时候还是全部倒出来了，这就出现了BUG。应该遵循，<font color='orange'>读多少就倒多少</font>。

```java
public static void main(String[] args) throws Exception {
        InputStream inputStream = new FileInputStream("src/字节流的使用/demo.txt");
        //定义一个字节数组读取数据,定义一个桶
        byte[] buffer = new byte[3];
        //读取桶大小的数据
        int len = 0;
        while ((len=inputStream.read(buffer))!=-1){
            //控制你所倒出的数据大小为你成功读入的大小
            String rs = new String(buffer,0,len);
            System.out.print(rs);
        }

    }
```

**小结**：使用字节数组来读取有一定的效率提升，但也无法避免读取中文出现乱码的现象。（<font color='cornflowerblue'>连续的中文可能被拆开读取，进而导致出错</font>）

解决思路：

1. 使用字符流（最好）
2. 定义一个与文件大小一致的字节数组。(<font color='cornflowerblue'>只适合读取小文件</font>)

```java
File f = new File("src/字节流的使用/demo.txt");
        long fileLength = f.length();
        InputStream inputStream = new FileInputStream(f);
		//byte[] buffer2 = inputStream.readAllBytes();
        byte[] buffer = new byte[(int)fileLength];
        int len = inputStream.read(buffer);
        String rs =  new String(buffer, 0, len);
        System.out.println(rs);
```

## 5.FileOutputStream(字节输出流)

OutputStream`有很多子类，我们从最简单的一个子类开始。

`java.io.FileOutputStream `类是文件输出流，用于将数据写出到文件。

**构造方法**

- `public FileOutputStream(File file)`：创建文件输出流以写入由指定的 File对象表示的文件。 
- `public FileOutputStream(String name)`： 创建文件输出流以指定的名称写入文件。  

当你创建一个流对象时，必须传入一个文件路径。该路径下，如果没有这个文件，会创建该文件。如果有这个文件，会清空这个文件的数据。

```java
public class outPutStreamDemo01 {
    public static void main(String[] args) throws Exception {
        //创建目标文件对象(写)
//        File f = new File("src/字节流的使用/demo2.txt");
        //创建字节输出流管道与文件对接，，默认是数据覆盖管道，新建管道会从文件头开始写入数据，并覆盖旧数据
        OutputStream os = new FileOutputStream("src/字节流的使用/demo2.txt");
        //创建追加数据管道，只需要将第二个参数开启为true即可
        OutputStream os2 = new FileOutputStream("src/字节流的使用/demo2.txt", true);
        //写数据出去
        os.write(97);
        os.write('b');
        os.write('彭'); //只能写中中文字符的第一个字节,写出后乱码
        os.write("\r\n".getBytes());//写换行
        
//        byte[] buffer1 = "我真是爱死写代码了！！GO!".getBytes("GBK");
        byte[] buffer = "我真是爱死写代码了！！GO!".getBytes();
        os.write(buffer);
        os.flush(); //立即刷新数据到文件中去，刷新后管道可以继续使用
        os.close(); //关闭管道资源，包含了刷新，关闭后管道不再可用
    }
}
```

文本内容输出;

```
abm
我真是爱死写代码了！！GO!
```

> - 回车符`\r`和换行符`\n` ：
>   - 回车符：回到一行的开头（return）。
>   - 换行符：下一行（newline）。
> - 系统中的换行：
>   - Windows系统里，每行结尾是 `回车+换行` ，即`\r\n`；
>   - Unix系统里，每行结尾只有 `换行` ，即`\n`；
>   - Mac系统里，每行结尾是 `回车` ，即`\r`。从 Mac OS X开始与Linux统一。

## 6.字节流做文件的复制

<font color='orange'>由于字节是计算机中一切文件的组成</font>，因此字节流适合做一切文件的复制。

```java
public class fileCopyDemo {
    public static void main(String[] args) {
//        //创建字节读取管道
//        File f = new File("src/字节流的使用/demo3.txt");
//        InputStream inputStream = new FileInputStream(f);
//        byte[] buffer = inputStream.readAllBytes();
//        //创建字符输出管道
//        OutputStream os = new FileOutputStream("src/字节流的使用/demo4.txt");
//        os.write(buffer);
//        os.close();
        InputStream inputStream = null;
        OutputStream os = null;
        try {
            inputStream = new FileInputStream("src/字节流的使用/demo3.txt");
            os = new FileOutputStream("src/字节流的使用/demo4.txt");
            byte[] buffer = new byte[1024];
            int len = 0;
            while ((len=inputStream.read(buffer))!=-1){
                os.write(buffer,0, len);
            }
            System.out.println("复制完成！");
         }catch (Exception e){
            e.printStackTrace();
        }finally {
            if (inputStream!=null){
                try {
                    inputStream.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
            if (os!=null){
                try {
                    os.close();
                } catch (IOException e) {
                    e.printStackTrace();
                }
            }
        }
    }
}

```

## 7.资源回收的新方法

JDK1.7之后提供了新的资源回收机制

`try-with-resourses:`

```java
try{
	//这里只能放置资源对象，用完后会自动调用close()关闭
}{

}catch(Exception e){
	e.printStackTrace()
}
```

