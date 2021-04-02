---
title: 剑指offer刷题-5-数组中只出现一次的两个数
date: 2020-08-08 12:09:22
tags:
	- Java
categories:	刷题
---

**题目及地址：**

[一个整型数组 `nums` 里除两个数字之外，其他数字都出现了两次。请写程序找出这两个只出现一次的数字。要求时间复杂度是O(n)，空间复杂度是O(1)。](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-lcof/)

示例：

```java
输入：nums = [4,1,4,6]
输出：[1,6] 或 [6,1]

输入：nums = [1,2,10,4,1,4,3,3]
输出：[2,10] 或 [10,2]
```

限制：

- 2 <= nums.length <= 10000

**Java 解法一：**

```Java
class Solution {
    public int[] singleNumbers(int[] nums) {
        Map<Integer, Integer> map = new HashMap<>();
        int [] re  = new int [2];
        int index = 0;
        for (int i= 0; i<nums.length;i++){
            if(map.containsKey(nums[i])){
                map.remove(nums[i]);
            }else{
                map.put(nums[i],1);
            }
        }
        for(int aa:map.keySet()){
            re[index++]=aa;
            System.out.println(aa);
        }
        return re;
    }
}
```

**思路：**使用`HashMap`来存储数组中所有出现的数字以及对应的频次，若发现一个数字已经出现，则在`map`中删除这个`key`，无的话则`put`这个key，并把`value`设置为1；最后使用`keySet`得到这个map的`key`集合（类型为Integer），调用for-each循环输出即可。该方法不满足题设时间复杂度与空间复杂度要求，`map`使用了额外的空间。

**Java 解法二(强烈推荐！)：**

```Java
class Solution {
    public int[] singleNumbers(int[] nums) {
        int [] re  = {0,0};
        int s1 = 0;
        for (int i= 0; i<nums.length;i++){
            s1 ^=nums[i];
        }
        s1 &=(-s1); //获得分类用的mask！
        for(int test:nums){
            if ((test & s1) == 0) {
                re[0]^=test;
            }else{
                re[1]^=test;
            }
        }
        return re;
    }
}

```

**思路：**根据异或运算的特性：

> a ^ b = b ^ a; 0 ^ a =  a; a ^ b ^ a = b; a ^ a = 0;

​		因此将所有的数组中所有的数组作异或运算后得到的结果就为数组中**只出现一次的两个数的异或值**！得到这两个数的异或值后要怎么分别得到他们？考虑**分组异或**，因为一组数中如果只含有一个出现频次为1的数，那他们所有数的异或结果就是这个数。此题含有两个出现次数为1的数，如何进行把这两个数分到不同的组内进行全组异或运算是关键。

> 设a=2,b=4,那么将他们用二进制展（这里我们假设他们的长度为一个字节8位）：
>
> ​    a: 0000 0010
>
> ​    b: 0000 0100
>
> a^b: 0000 0110 

​		观察发现a与b的异或值的二进制表达式其实反映了他们哪个位是否不相等，相等则为0，不相等则为1，因此可以将这个不相等的位作为一个分组的标志位，当然我们只需要找到a与b不相等的**最低位**来做判定即可。此时可以想到我们常用的奇偶判定方法：&1，1的最低位为1其他位都为0，如果其结,1则该数为奇数 ，否则为偶数。

​		同理我们找到一个只含有1个位为1的二进制数，且为1的这个位刚好是a与b异或的值的二进制表达式中为1的最低位，将这个数作为分组的条件，去异或数组中的所有值，则所有**成对出现的数会被抵消**，只留下单个出现的那两个数，此时判断异或的结果是0还是1就将他们分组了。

