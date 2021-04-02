---
title: 剑指offer刷题(1)找出数组中出现次数超过数组长度一半的值
date: 2020-07-25 14:22:34
tags: 
  - Java
  - Python
categories: 刷题
---
**题目地址：**[数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字](https://leetcode-cn.com/problems/shu-zu-zhong-chu-xian-ci-shu-chao-guo-yi-ban-de-shu-zi-lcof/)
**数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。** **你可以假设数组是非空的，并且给定的数组总是存在多数元素。**

示例：

```
输入: [1, 2, 3, 2, 2, 2, 5, 4, 2]
输出: 2
```

###### python:

思路：要找出这个数字需要统计数组中每个元素出现的次数，在Python中这个数组是个列表，因此可以使用List自带的方法count来统计列表中每个元素出现的次数。使用set方法得到数组列表中出现的不重复元素，依次比对所有元素的出现次数是否大于数组长度的一半。由于题中给定数组中只有一个元素满足题目要求，故使用for循环对比发现某个元素出现的次数大于数组长度的一半时，返回即可。

```python
class Solution(object):
    def majorityElement(self, nums):
        """
        :type nums: List[int]
        :rtype: int
        """
        result = {}
        max_num = -1
        max_coount = 0
        for i in set(nums):
            if nums.count(i) > int(len(nums)/2):
                max_coount = nums.count(i)
                max_num = i
        return max_num
```

**Java**

可涉及的知识点如下：

> ArrayList
> LinkedList
> Stack
> HashMap || HashSet
> Queue

考虑使用HashMap，其中[Java中map集合方法](https://www.runoob.com/java/java-map-interface.html)需要熟记。

**方法一：**

```java
class Solution {
    public int majorityElement(int[] nums) {
        Map<Integer, Integer> map = new HashMap<>();
        for(int i=0;i<nums.length;i++){
            if(map.containsKey(nums[i])){
                map.put(nums[i],map.get(nums[i])+1);
            }
            else{
                map.put(nums[i],1);
            }
        }
        int leng = nums.length >> 1;
        for(int key : map.keySet()){
            if (map.get(key)>leng){
                return key;
            }
        }
        return 0;
    }
}
```

**思路：**可以想到用`hashmap`来存储数组中出现的元素以及其出现的次数，`map`中`key`是不重复的，因此需要用到map集合的方法`containsKey`来判定原`map`中是否存在这个`key`，存在则在原来的键值`value`上+1（调用`put`方法），不存在就put键值为1，最后找出键值大小超过数组长度一半的key即可。

**方法二：**

```java
class Solution {
    public int majorityElement(int[] nums) {
        for(int i=0;i<nums.length;i++){
            int count = 0;
             for(int j=0;j<nums.length;j++){
                 if (nums[i] == nums[j]){
                     count++;
                 }
            }
            if (count>nums.length>>1){
                return nums[i];
            }
        }
        return 0;
    }
}
```

**思路：**此方法执行时间花销较大，内存比前一方法略小，直接通过两个`for循环`依次统计所有元素出现次数，用`count`变量存储，每统计完一个元素就判定该元素的出现次数是否大于一半数组长度，大于则直接返回该元素，不再进行循环。