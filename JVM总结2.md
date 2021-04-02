---
title: JVM总结2
date: 2021-03-15 15:51:45
tags:
 - JVM
categories: Java面经

---

[欢迎查看我对JVM总结的思维导图](https://www.processon.com/view/link/6050a729637689019de0e175)

<!-- toc -->

## 1. 类文件

### 1.1 字节码

在 Java 中，JVM 可以理解的代码就叫做`字节码`（即扩展名为 `.class` 的文件），它不面向任何特定的处理器，只面向虚拟机。Java 语言通过字节码的方式，在一定程度上解决了传统解释型语言执行效率低的问题，同时又保留了解释型语言可移植的特点。所以 Java 程序运行时比较高效，而且，由于字节码并不针对一种特定的机器，因此，Java 程序无须重新编译便可在多种不同操作系统的计算机上运行。

![](desktop类文件结构概览.png)

### 1.2 类文件结构

class文件是以8个字节为基础单位的二进制流，大数据项高位在前分割处理。

```java
ClassFile {
    u4             magic; //Class 文件的标志
    u2             minor_version;//Class 的小版本号
    u2             major_version;//Class 的大版本号
    u2             constant_pool_count;//常量池的数量
    cp_info        constant_pool[constant_pool_count-1];//常量池
    u2             access_flags;//Class 的访问标记
    u2             this_class;//当前类
    u2             super_class;//父类
    u2             interfaces_count;//接口
    u2             interfaces[interfaces_count];//一个类可以实现多个接口
    u2             fields_count;//Class 文件的字段属性
    field_info     fields[fields_count];//一个类会可以有多个字段
    u2             methods_count;//Class 文件的方法数量
    method_info    methods[methods_count];//一个类可以有个多个方法
    u2             attributes_count;//此类的属性表中的属性数
    attribute_info attributes[attributes_count];//属性表集合
}
```

- 无符号数属于基本的数据类型，u1,u2,u4,u8分别代表1个字节，2个字节，4个字节和8个字节的无符号数。
- 表是由多个无符号数或者其它表作为数据项构成的复合数据类型，习惯以“_info”结尾。

**Class文件字节码结构组织示意图** 

![](类文件字节码结构组织示意图.png)

#### 1.2.1 魔数

```java
    u4             magic; //Class 文件的标志
```

每个 Class 文件的头四个字节称为魔数（Magic Number）,它的唯一作用是**确定这个文件是否为一个能被虚拟机接收的 Class 文件**。

#### 1.2.2 版本号

紧接着魔数的四个字节存储的是 Class 文件的版本号：第五和第六是**次版本号**，第七和第八是**主版本号**。

```java
    u2             minor_version;//Class 的小版本号
    u2             major_version;//Class 的大版本号
```

#### 1.2.3 常量池

```java
    u2             constant_pool_count;//常量池的数量
    cp_info        constant_pool[constant_pool_count-1];//常量池
```

紧接着主次版本号之后的是常量池，常量池的数量是 constant_pool_count-1（**常量池计数器是从1开始计数的，将第0项常量空出来是有特殊考虑的，索引值为0代表“不引用任何一个常量池项”**）。

常量池主要存放两大常量：<font color='orange'>字面量</font>（Literal）和<font color='orange'>符号引用</font>(Symbolic Reference)。

**字面量**比较接近于 Java 语言层面的的常量概念，如<font color='cornflowerblue'>文本字符串、声明为 final 的常量值</font>等。

而**符号引用**则属于编译原理方面的概念。包括下面几类常量：

- 被模块导出或者开放的包（Package）
- 类和接口的全限定名(Fully Qualified Name)
- 字段的名称和描述符(Descriptor)
- 方法的名称和描述符
- 方法句柄和方法类型(Method Handle、Method Type、Invoke Dynamic)
- 动态调用点和动态常量

常量池中每一项常量都是一个表，这14种表有一个共同的特点：**开始的第一位是一个 u1 类型的标志位 -tag 来标识常量的类型，代表当前这个常量属于哪种常量类型．**

| 类型                             | 标志（tag） | 描述                   |
| -------------------------------- | ----------- | ---------------------- |
| CONSTANT_utf8_info               | 1           | UTF-8编码的字符串      |
| CONSTANT_Integer_info            | 3           | 整形字面量             |
| CONSTANT_Float_info              | 4           | 浮点型字面量           |
| CONSTANT_Long_info               | ５          | 长整型字面量           |
| CONSTANT_Double_info             | ６          | 双精度浮点型字面量     |
| CONSTANT_Class_info              | ７          | 类或接口的符号引用     |
| CONSTANT_String_info             | ８          | 字符串类型字面量       |
| CONSTANT_Fieldref_info           | ９          | 字段的符号引用         |
| CONSTANT_Methodref_info          | 10          | 类中方法的符号引用     |
| CONSTANT_InterfaceMethodref_info | 11          | 接口中方法的符号引用   |
| CONSTANT_NameAndType_info        | 12          | 字段或方法的符号引用   |
| CONSTANT_MothodType_info         | 16          | 标志方法类型           |
| CONSTANT_MethodHandle_info       | 15          | 表示方法句柄           |
| CONSTANT_InvokeDynamic_info      | 18          | 表示一个动态方法调用点 |

`.class` 文件可以通过`javap -v class类名` 指令来看一下其常量池中的信息(`javap -v  class类名-> temp.txt` ：将结果输出到 temp.txt 文件)。

#### 1.2.4 访问标志 

在常量池结束之后，紧接着的**两个字节**代表**访问标志**（access_flags），这个标志用于识别一些类或者接口层次的访问信息，包括：这个 Class 是类还是接口，是否为 public 或者 abstract 类型，如果是类的话是否声明为 final 等等。

![](访问标志.jpg)

定义一个类文件经过编译可以通过`javap -v class类名` 指令来看一下类的访问标志。

#### 1.2.5 当前类索引,父类索引与接口索引集合

```java
    u2             this_class;//当前类
    u2             super_class;//父类
    u2             interfaces_count;//接口
    u2             interfaces[interfaces_count];//一个类可以实现多个接口
```

**类索引用于确定这个类的全限定名，父类索引用于确定这个类的父类的全限定名，由于 Java 语言的单继承，所以父类索引只有一个，除了 `java.lang.Object` 之外，所有的 java 类都有父类，因此除了 `java.lang.Object` 外，所有 Java 类的父类索引都不为 0。**

**接口索引集合用来描述这个类实现了那些接口，这些被实现的接口将按 `implements` (如果这个类本身是接口的话则是`extends`) 后的接口顺序从左到右排列在接口索引集合中。**

#### 1.2.6 字段表集合

```java
    u2             fields_count;//Class 文件的字段的个数
    field_info     fields[fields_count];//一个类会可以有个字段
```

**字段表**（field info）用于<font color='orange'>描述接口或类中声明的变量</font>。字段包括**类级变量**以及**实例级变量**，但<font color='red'>不包括在方法内部声明的局部变量</font>。

**field info(字段表) 的结构:**

![](字段表的结构.png)

- **access_flags:** 字段的作用域（`public` ,`private`,`protected`修饰符），是实例变量还是类变量（`static`修饰符）,可否被序列化（transient 修饰符）,可变性（final）,可见性（volatile 修饰符，是否强制从主内存读写）。
- **name_index:** 对常量池的引用，表示的字段的名称；
- **descriptor_index:** 对常量池的引用，表示字段和方法的描述符；
- **attributes_count:** 一个字段还会拥有一些额外的属性，attributes_count 存放属性的个数；
- **attributes[attributes_count]:** 存放具体属性具体内容。

#### 1.2.7 方法表集合

```java
    u2             methods_count;//Class 文件的方法的数量
    method_info    methods[methods_count];//一个类可以有个多个方法
```

methods_count 表示方法的数量，而 method_info 表示方法表。

Class 文件存储格式中对方法的描述与对字段的描述几乎采用了完全一致的方式。方法表的结构如同字段表一样，依次包括了访问标志、名称索引、描述符索引、属性表集合几项。

**method_info(方法表的) 结构:**

![](方法表的结构.png)

<font color='red'>注意</font>：因为`volatile`修饰符和`transient`修饰符不可以修饰方法，所以方法表的访问标志中没有这两个对应的标志，但是增加了`synchronized`、`native`、`abstract`等关键字修饰方法，所以也就多了这些关键字对应的标志。

#### 1.2.8 属性表集合

```java
   u2             attributes_count;//此类的属性表中的属性数
   attribute_info attributes[attributes_count];//属性表集合
```

在 Class 文件，字段表，方法表中都可以携带自己的属性表集合，以用于描述某些场景专有的信息。与 Class 文件中其它的数据项目要求的顺序、长度和内容不同，属性表集合的限制稍微宽松一些，不再要求各个属性表具有严格的顺序，并且只要不与已有的属性名重复，任何人实现的编译器都可以向属性表中写 入自己定义的属性信息，Java 虚拟机运行时会忽略掉它不认识的属性。

## 2. 类加载过程

### 2.1 类的生命周期

![](类的生命周期.jpg)

### 2.2 类的加载过程

Class 文件需要加载到虚拟机中之后才能运行和使用，系统加载 Class 类型的文件主要三步:**加载->连接->初始化**。连接过程又可分为三步:**验证->准备->解析**。

#### 2.2.1 加载

在加载阶段，虚拟机要做三件事：

1. 通过全类名获取定义此类的二进制字节流
2. 将字节流所代表的静态存储结构转换为方法区的运行时数据结构
3. 在内存中生成一个代表该类的 `java.lang.Class` 对象,作为方法区这些数据的访问入口

"通过全类名获取定义此类的二进制字节流" 并没有指明具体从哪里获取、怎样获取。比如：比较常见的就是从 ZIP 包中读取（日后出现的JAR、EAR、WAR格式的基础）、其他文件生成（典型应用就是JSP）等等。

**一个非数组类的加载阶段（加载阶段获取类的二进制字节流的动作）是可控性最强的阶段，这一步我们可以去完成还可以自定义类加载器去控制字节流的获取方式（重写一个类加载器的 `loadClass()` 方法）。数组类型不通过类加载器创建，它由 Java 虚拟机直接创建。**

#### 2.2.2 验证

验证是连接阶段的第一步，目的就是为了<font color='orange'>确保Class文件中的字节流包含的信息符合《java虚拟机的规范》的全部约束条件，保证这些信息被当做代码运行后不会危害虚拟机的安全。</font>

包含四个阶段的检验动作,如图

![](验证流程.png)

#### 2.2.4 准备

**准备阶段是正式为类中定义的变量（静态变量，static修饰）分配内存并设置类变量初始值的阶段**，这些内存都将在方法区中分配。

对于该阶段有以下几点需要注意:

- 这时候进行内存分配的<font color='red'>仅包括类变量（static）</font>，而不包括实例变量，实例变量会在对象实例化时随着对象一块分配在 Java 堆中。
- 这里所设置的<font color='red'>初始值</font>"通常情况"下<font color='red'>是数据类型默认的零值</font>（如0、0L、null、false等），比如我们定义了`public static int value=111` ，那么 value 变量在准备阶段的初始值就是 0 而不是111（初始化阶段才会赋值）。特殊情况：比如给 value 变量加上了 fianl 关键字`public static final int value=111` ，那么准备阶段 value 的值就被赋值为 111。

**基本数据类型的零值：**

![](基本数据类型的零值.png)

#### 2.2.5 解析

<font color='orange'>解析阶段是java虚拟机将常量池内的符号引用替换为直接引用的过程。</font>解析动作主要针对类或接口、字段、类方法、接口方法、方法类型、方法句柄和调用限定符7类符号引用进行。<font color='orange'>也就是得到类或者字段、方法在内存中的指针或者偏移量。</font>

> 符号引用就是一组符号来描述目标，可以是任何字面量。<u>**直接引用**就是直接指向目标的指针、相对偏移量或一个间接定位到目标的句柄</u>。在程序实际运行时，只有符号引用是不够的，举个例子：在程序执行方法时，系统需要明确知道这个方法所在的位置。Java 虚拟机为每个类都准备了一张方法表来存放类中所有的方法。当需要调用一个类的方法的时候，只要知道这个方法在方法表中的偏移量就可以直接调用该方法了。通过解析操作符号引用就可以直接转变为目标方法在类中方法表的位置，从而使得方法可以被调用。

#### 2.2.6 初始化

初始化是类加载的最后一步，也是真正执行类中定义的 Java 程序代码(字节码)，<font color='orange'>初始化阶段是执行初始化方法 </font>` <clinit>()`<font color='orange'>方法的过程</font>，该方法是Javac编译器的自动生成物。

` <clinit>()`方法的特点如下：

- ` <clinit>()`由编译器自动收集类中的所有类变量的赋值动作和静态语句块中的语句合并产生的，编译器收集的顺序和源程序中写的顺序一致，静态语句块中只能访问到定义在静态语句之前的变量，在前面的静态语句可以赋值，但是不能访问.

  ```java
  public class Test{
      static {
          i = 0;  //  给变量赋值可以正常通过
          System.out.println(i);   //编译会提示非法前向引用
      }
      static int i = 1;
  }
  ```

- ` <clinit>()`方法与类的构造函数（虚拟机视角中的<init>()方法）不同，不需要显示的调用父类构造器，java虚拟机会保证在执行子类的` <clinit>()`方法前执行完父类的` <clinit>()`方法，因此java中首先执行Object类的` <clinit>()`方法

- 父类的` <clinit>()`会先执行，父类中的静态语句要优于子类的变量赋值操作

- ` <clinit>()`对于类或接口不是必需的，若类中没有静态语句块，也没有变量的赋值操作，就可不产生` <clinit>()`方法

- 接口中不能用静态语句块，当但仍然有变量初始化的赋值操作，因此接口与 类都会生成` <clinit>()`方法。但对于接口来说不需要先执行父接口的` <clinit>()`方法，只有当父接口中定义的变量被使用时，父接口才会被初始化。

- java虚拟机必须保证一个类的` <clinit>()`方法在多线程环境下被真确地加锁同步。

<font color='cornflowerblue'>何时开始类的初始化？</font>

1. 遇到`new`、`getstatic`、`putstatic`或`invokestatic`这四条指令时，如果类型没有初始化，则需要出发初始化阶段。

   生成这四条指令的场景有：

   - 使用`new`关键字实例化对象的时候；
   - 读取或设置一个类型的静态的字段（被`final`修饰，已在编译期把结果放入常量池的静态字段除外）时；
   - 调用一个类型的静态方法时。

2. 使用`java.lang.reflect`包的方法对类型进行反射调用的时候，若类型没有初始化，就需要对其进行初始化；

3. 当初始化类的时候，若其父类未进行初始化，先触发其父类的初始化；

4. 当虚拟机启动时，用户需要指定一个要执行的主类（包含main方法的那个类），虚拟机会先初始化这个主类；

5. 当使用JDK7新加入的动态语言支持时，

6. 当一个接口中定义了JDK8新加入的默认方法（default修饰接口方法）时，如果这个接口的实现类发生了初始化，那该接口要在其之前被初始化。

#### 2.2.7 卸载

卸载类即该类的Class对象被GC。

卸载类需要满足3个要求:

1. 该类的所有的实例对象都已被GC，也就是说堆不存在该类的实例对象。
2. 该类没有在其他任何地方被引用
3. 该类的类加载器的实例已被GC

所以，在JVM生命周期内，由jvm自带的类加载器加载的类是不会被卸载的。但是由我们自定义的类加载器加载的类是可能被卸载的。

只要想通一点就好了，jdk自带的BootstrapClassLoader,ExtClassLoader,AppClassLoader负责加载jdk提供的类，所以它们(类加载器的实例)肯定不会被回收。而我们自定义的类加载器的实例是可以被回收的，所以使用我们自定义加载器加载的类是可以被卸载掉的。

### 2.3 类加载器

在类的加载阶段中“通过一个类的全限定名来获取描述该类的二进制字节流”放到java虚拟机外部去实现，应用程序自己去获取锁需要的类，实现该动作的代码就是“类加载器”(Class Loader)。

<font color='orange'>对于任何一个类，都必须由加载它的类加载器和这个类本身一起共同确立其在Java虚拟机中的唯一性</font>，也就是说只有在这两个类是由同一个类加载器加载的前提下才有意义，否则即使他们来源于同一个Class文件，被同一个java虚拟机加载，但使用的类加载器不同，那么这两个类就必不相等。

> 相等的概念：包括了Class对象的`equals()`方法，isAssignableFrom(),isInstance()方法 的返回结果，以及使用instanceof关键字做对象所属关系判断等。

绝大多数java程序使用以下三类系统提供的类加载器来进行加载：

- **启动类加载器**（Bootstrap Class Loader）：最顶层的加载类，由C++实现，负责加载 `%JAVA_HOME%/lib`目录下的jar包和类或者或被 `-Xbootclasspath`参数指定的路径中的所有类。
- **扩展类加载器**(Extension Class Loader)：主要负责加载目录 `%JRE_HOME%/lib/ext` 目录下的jar包和类，或被 `java.ext.dirs` 系统变量所指定的路径下的jar包。
- **应用程序类加载器**(Application Class Loader)，也称系统类加载器：面向我们用户的加载器，负责加载当前应用classpath下的所有jar包和类。

#### 2.3.1 双亲委派模型

![](双亲委派模型.png)

该模型的类加载流程如下：<font color='orange'>当一个类加载器收到了类加载的请求时，首先会将这个请求委派给父一级的类加载器来完成，每一个层级的类加载器都这样操作，直到把该类加载的请求送到最上级的类加载器来完成，只有当上一级的类加载器反馈无法完成本类加载时，本级的类加载器此后尝试去加载这个类。</font>

使用该模型加载的<font color='red'>好处</font>：

- 保证了保证了java中的类和他的类加载器之间具备了带有优先级的层次关系，最<font color='orange'>重要的是它保证了java程序运行的稳定性，可以避免类的重复加载</font>，也保证了java的核心API不被篡改。

源码分析：

逻辑非常清晰，都集中在 `java.lang.ClassLoader` 的 `loadClass()` 中，

```java
private final ClassLoader parent; 
protected Class<?> loadClass(String name, boolean resolve)
        throws ClassNotFoundException
    {
        synchronized (getClassLoadingLock(name)) {
            // 首先，检查请求的类是否已经被加载过
            Class<?> c = findLoadedClass(name);
            if (c == null) {
                long t0 = System.nanoTime();
                try {
                    if (parent != null) {//父加载器不为空，调用父加载器loadClass()方法处理
                        c = parent.loadClass(name, false);
                    } else {//父加载器为空，使用启动类加载器 BootstrapClassLoader 加载
                        c = findBootstrapClassOrNull(name);
                    }
                } catch (ClassNotFoundException e) {
                   //抛出异常说明父类加载器无法完成加载请求
                }
                
                if (c == null) {
                    long t1 = System.nanoTime();
                    //自己尝试加载
                    c = findClass(name);

                    // this is the defining class loader; record the stats
                    sun.misc.PerfCounter.getParentDelegationTime().addTime(t1 - t0);
                    sun.misc.PerfCounter.getFindClassTime().addElapsedTimeFrom(t1);
                    sun.misc.PerfCounter.getFindClasses().increment();
                }
            }
            if (resolve) {
                resolveClass(c);
            }
            return c;
        }
    }
```

<font color='cornflowerblue'>怎么破坏双亲委派模型？</font>

**自定义加载器的话，需要继承 `ClassLoader` 。如果我们不想打破双亲委派模型，就重写 `ClassLoader` 类中的 `findClass()` 方法即可，无法被父类加载器加载的类最终会通过这个方法被加载。但是，如果想打破双亲委派模型则需要重写 `loadClass()` 方法**

#### 2.3. 2自定义类加载器

除了 `BootstrapClassLoader` 其他类加载器均由 Java 实现且全部继承自`java.lang.ClassLoader`。如果我们要自定义自己的类加载器，很明显需要继承 `ClassLoader`。

##   参考



- [深入理解Java虚拟机 第三版](https://book.douban.com/subject/34907497/)