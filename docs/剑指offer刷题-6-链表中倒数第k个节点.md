---
title: 剑指offer刷题-6-链表中倒数第k个节点
date: 2020-08-14 18:36:48
tags:
  - Java
categories: 刷题
---

**题目及地址：**

[输入一个链表，输出该链表中倒数第k个节点。为了符合大多数人的习惯，本题从1开始计数，即链表的尾节点是倒数第1个节点。例如，一个链表有6个节点，从头节点开始，它们的值依次是1、2、3、4、5、6。这个链表的倒数第3个节点是值为4的节点。](https://leetcode-cn.com/problems/lian-biao-zhong-dao-shu-di-kge-jie-dian-lcof/)

示例：

```java
给定一个链表: 1->2->3->4->5, 和 k = 2.

返回链表 4->5
```

限制：

null

**Java**

**方法一：**

```java
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {
        int count = 0;
        ListNode co = head;
        while(co!=null){
            co = co.next;
            count++;
        }
        co = head;
        for(int i=0;i<count-k;i++) co = co.next;
        return co;
    }
}
```

**思路：**首先遍历一下整个链表得到链表的长度，然后根据题目给定的k找到题目要求的节点。

**方法二：**

```java
class Solution {
    public ListNode getKthFromEnd(ListNode head, int k) {
        ListNode n1 = head, n2 = head;
        for(int i=0;i<k;i++) n1 = n1.next;
        while(n1!=null){
            n1 = n1.next;
            n2 = n2.next;
        }
        return n2;
    }
}
```

**思路：** **推荐使用双指针的思想！**首先使用右指针往下走`k`个节点，之后再使左指针开始与右指针同时移动，两指针之间的间隙为k个节点，当右指正走向链表末尾时，左指针刚好指向倒数第k个节点。

