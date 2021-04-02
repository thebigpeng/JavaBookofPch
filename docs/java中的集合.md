---
title: java中的collection
date: 2021-03-09 16:16:08
tags:
  - 基础知识点
categories: Java基础
---

<!-- toc -->

## 1. 什么是集合

`collection`集合就是一个<font color='cornflowerblue'>大小可变的容器</font>， 容器中的每个数据称为一个元素。

<font color='orange'>集合的特点</font>：

- 类型可以不固定；
- 大小可以不固定。

java中集合的代表是：`Collection`，是所有集合的祖宗类。

<font color='red'>这个图记死！</font>

![alt](collection.png)

![alt](集合.png)

## 2.各系列集合的特点

### 2.1 Set

**Set**集合添加的元素是<font color='orange'>无序</font>、<font color='orange'>不重复</font>、<font color='orange'>无索引</font>的！

- **HashSet**：添加的元素是<font color='cornflowerblue'>无序</font>，不重复，无索引。
  - 底层数据结构是哈希表（无序唯一）。依赖两个方法来<font color='orange'>保证元素唯一性:hashCode()和equals()</font>。
- **LinkedHashSet**：(HashSet的子类)添加的元素是<font color='cornflowerblue'>有序</font>，不重复，无索引。
  - 底层数据结构是链表和哈希表(FIFO插入有序，唯一)，链表的额结构保证了元素有序，哈希表则保证了元素唯一。
- **TreeSet**：不重复，无索引，按大小默认排序。
  - 其底层数据结构是**红黑树**。
  - 如何保证元素排序？
    - 自然比较
    - 比较器来排序
  - 如何保证元素的唯一性？
    - 根据比较的返回值是否为0来决定。

### 2.2 List

**List**集合添加的元素是<font color='red'>有序，可重复，有索引</font>的。

- **ArrayList**：添加的元素有序，可重复，有索引，
  - 底层数据结构是数组，因此查询快，增删慢；
  - 线程不安全，效率高。
- **LinkedList**：添加的元素有序，可重复，有索引；
  - 底层数据结构是链表，查询慢，增删快。
  - 线程不安全，效率高。
- **Vector**:底层数据结构是数组，查询快，增删慢。
  - 线程安全，效率低。



> 如果你知道是Collection集合，但是不知道使用谁，就用ArrayList。
> 如果你知道用集合，就用ArrayList。

## 3. Collection的遍历方式

遍历就是把容器中的各个元素都访问一遍。

> toArray和toArray(T[ ] a)返回的都是当前所有元素的数组。 toArray返回的是一个Object[]数组，类型不能改变。 toArray(T[ ] a)返回的是当前传入的类型T的数组，更方便用户操作,比如需要获取一个String类型的数组：toArray(new String[0])。

## 


作者：knock_小新
链接：https://juejin.cn/post/6844903521092009997
来源：掘金
著作权归作者所有。商业转载请联系作者获得授权，非商业转载请注明出处。

### 3.1 迭代器

 集合中获取迭代器的方法：

<font color='red'>问一次取一次</font>

```java
public class collectionDemo {
    public static void main(String[] args) {
        Collection<String> collection = new ArrayList<>();
        collection.add("小莫");
        collection.add("小明");
        collection.add("小队");

        Iterator<String> it = collection.iterator();
        //System.out.println(it.next());  //Exception in thread "main" java.util.NoSuchElementException
        while (it.hasNext()){
            System.out.println(it.next());
        }

    }
}

```

流程：

1. 先获取当前集合的迭代器（调用iterator()方法得到）
2. 定义一个while循环，问一次取一次

### 3.2foreach

简单就不写了，直接看代码 

```java
public class collectionDemo {
    public static void main(String[] args) {
        Collection<String> collection = new ArrayList<>();
        collection.add("小莫");
        collection.add("小明");
        collection.add("小队");

        Iterator<String> it = collection.iterator();
        for (String str:collection){
            System.out.println(str);
        }

    }
}
```

特点：

- 操作方便，从头到尾遍历，但不知道索引
-  无索引有时候不方便

### 3.3Lambda表达式

......之后补充

## 4. java中常见的数据结构

### 4.1基本数据结构介绍

- **队列**（queue）：先进先出，后进后出。如排队、叫号等系统。
- **栈**（stack）：先进后出，后进先出。压栈即入栈，弹栈即出栈。
- **线性表**（数组）：数组是内存中的连续存储区域，分成若干个等分的小区域（大小一样），<font color='orange'>其元素存在索引</font>，<font color='cornflowerblue'>查询元素快，增删元素慢。</font>
- **链表**：元素不是内存中的连续存储区域；<font color='orange'>元素是游离存储的，每个元素会记录下个元素的地址</font>。<font color='cornflowerblue'>查询元素慢，增删元素快。</font>

### 4.2 树的基本结构

树具有的特点：

1. 每一个节点有零个或者多个子节点
2. 没有父节点的节点称之为根节点，**一个树最多有一个根节点。**
3. 每一个非根节点有且只有一个父节点

**二叉树**：
如果树中的每个节点的子节点的个数不超过2，那么该树就是一个二叉树。

**二叉查找树/二叉排序树（**BST**）**：

二叉查找树的特点：

1. 左子树上所有的节点的值<font color='red'>均小于等于</font>他的根节点的值
2. 右子树上所有的节点值<font color='red'>均大于或者等于</font>他的根节点的值
3. 每一个子节点最多有两个子树

**二叉查找树增删改查的性能都很高！！！**

遍历获取元素的时候可以按照"左中右"的顺序进行遍历；

<font color='cornflowerblue'>二叉查找树存在的问题</font>：会出现<font color='orange'>"瘸子"</font>的现象，影响查询效率。

”瘸子现象“就是指：当存储的元素为依次递增时，查询速率非常低下。（相当于链表，所有查询都要冲根节点开始。）此时树最高。

### 4.3**平衡二叉树**：

基于查找二叉树，但是让树不要太高，尽量让树的元素均衡分布。这样综合性能就高了

规则：**<font color='orange'>它的左右两个子树的高度差的绝对值不超过1，并且左右两个子树都是一棵平衡二叉树</font>**

如果发现不平衡，底层可以使用<font color='orange'>旋转算法来调整</font>。

**旋转算法**：

在构建一棵平衡二叉树的过程中，当有新的节点要插入时，检查是否因插入后而破坏了树的平衡，如果是，则需要做旋转去改变树的结构。

有可能会有导致树不平衡，这时候就需要进行调整，而可能出现的情况就有4种，分别称作**左左，左右，右左，右右**。

- **左左**：即为在原来平衡的二叉树上，在<font color='cornflowerblue'>节点的左子树的左子树下，有新节点插入，</font>导致节点的左右子树的高度差为2；

  - 只需要<font color='orange'>对节点进行右旋即可</font>

- **左右**：为在原来平衡的二叉树上，在节点的<font color='cornflowerblue'>左子树的右子树下</font>，有新节点插入，导致节点的左右子树的高度差为2；

  - ​	**调整方式**:将左右进行第一次左旋，将左右先调整成左左，然后再对左左进行右旋，从而使得二叉树平衡。

    ![alt](左右.png)

- **右左**：在原来平衡的二叉树上，在节点的<font color='cornflowerblue'>右子树的左子树下</font>，有新节点插入，导致节点的左右子树的高度差为2；

  - ​	**调整方式**:先对右左进行右旋，使得二叉树变成右右，之后再对"11"节点进行左旋。

    ![alt](右左.png)

- **右右**：即为在原来平衡的二叉树上，在节点的右子树的右子树下，有新节点插入，导致节点的左右子树的高度差为2;

  - **只需对节点进行一次:**<font color='cornflowerblue'>左旋</font>即可调整平衡

**总结**：<font color='orange'>左边高就做右旋，右边高就做左旋，如果一次操作后仍不成功，则反着做一次再按照规律做。</font>

### 4.4**红黑树**

<font color='cornflowerblue'>就是平衡的二叉查找树！</font>红黑树是一种自平衡的二叉查找树，是计算机科学中用到的一种数据结构，它的平衡是通过"红黑树的特性"进行实现的；<font color='red'>其增删改查性能都最高</font>。

红黑树的<font color='orange'>特性</font>：

1. 每一个节点或是红色的，或者是黑色的。
2. 根节点必须是黑色
3. 每个叶节点(Nil)是黑色的；（如果一个节点没有子节点或者父节点，则该节点相应的指针属性值为Nil，这些Nil视为叶节点）
4. 如果某一个节点是红色，那么它的子节点必须是黑色(不能出现两个红色节点相连的情况)
5. 对每一个节点，从该节点到其所有后代叶节点的简单路径上，均包含相同数目的黑色节点；

![alt](红黑树.png)

