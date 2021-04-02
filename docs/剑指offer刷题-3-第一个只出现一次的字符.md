---
title: 剑指offer刷题(3)在字符串 s 中找出第一个只出现一次的字符
date: 2020-07-27 21:11:34
tags: 
  - Java
  - Python
categories: 刷题
---
**题目及地址：**

[在字符串 s 中找出第一个只出现一次的字符。如果没有，返回一个单空格。 s 只包含小写字母。](https://leetcode-cn.com/problems/di-yi-ge-zhi-chu-xian-yi-ci-de-zi-fu-lcof/)

示例：

```java
s = "abaccdeff"
返回 "b"

s = "" 
返回 " "
```

限制：

0 <= s 的长度 <= 50000

**Java**

**方法一：**

```java
class Solution {
    public char firstUniqChar(String s) {
        char a = ' ';
        Map <Character, Boolean> map = new HashMap<>();
        for(int i=0;i<s.length();i++){
            if(map.containsKey(s.charAt(i))){
                map.put(s.charAt(i),false);
            }else{
                map.put(s.charAt(i),true);
            }
        }
        for(char key:s.toCharArray()){
            if(map.get(key)){
                return key;
            }
        }
        return a;
    }
    
}
```

**思路：**

**方法二：**

```java
class Solution {
    public char firstUniqChar(String s) {
        int[] counts = new int[26];
        for (int i=0; i<s.length(); i++){
            counts[s.charAt(i)-'a']++;
        }
        for(int i=0; i<s.length(); i++){
            if(counts[s.charAt(i)-'a']==1){
                return s.charAt(i);
            }
        }
        return ' ';
    }
}
```

**思路：**