---
title: 剑指offer刷题(4)滑动窗口里的最大值
date: 2020-08-07 20:18:01
tags:
  - Java
categories: 刷题
---

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        int [] a = new int [0];
        int index=0;
        if (nums.length==0 || nums==null) return a;
        int [] re = new int [nums.length-k+1]; 
        for(int i=0; i<nums.length-k+1;i++){
            int max=Integer.MIN_VALUE;
            for(int j=i;j<i+k;j++){
                if(nums[j]>max){
                    max=nums[j];
                }
            }
            re[index++]=max;
            System.out.println(max);
        }
         return re;
    }
}
```

