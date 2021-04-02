---
title: 剑指offer刷题(2)两数之和
date: 2020-07-26 21:11:34
tags: 
  - Java
  - Python
categories: 刷题
---
**题目及地址：**[给定一个整数数组 nums 和一个目标值 target，请你在该数组中找出和为目标值的那 两个 整数，并返回他们的数组下标。你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。](https://leetcode-cn.com/problems/shu-zu-zhong-chu-xian-ci-shu-chao-guo-yi-ban-de-shu-zi-lcof/)

示例：

```java
给定 nums = [2, 7, 11, 15], target = 9
因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```

**Java**

**方法一：**

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int[] a = {0,0};
        for(int i=0;i<nums.length;i++){
                for(int j=i+1;j<nums.length;j++){
                    int add = nums[i] + nums[j];
                    if(add == target){
                        a[0] = i;
                        a[1] = j; 
                        return a;
                    }
                }
            }
        return a;
    }
}
```

**思路：**`for循环`打天下，依次对比所有数的和是否为`target`值，是则直接返回下标。占用内存较大，费时较多。

**方法二：**

```java

```

**思路：**