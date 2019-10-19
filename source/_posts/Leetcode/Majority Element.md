---
layout: pages
title: 【Leetcode】 Majority Element
date: 2017-07-05 16:08:03
tags:
      - leetcode
categories: leetcode
---
## 题目 ##
Given an array of size n, find the majority element. The majority element is the element that appears more than ⌊ n/2 ⌋ times.

You may assume that the array is non-empty and the majority element always exist in the array.

## 关键点 ##
`题目求的是众数，且重复次数大于 n/2 。而大于 n/2 的element有且只有一组。可通过数学知识证明`


## 解题思路 ##
### 思路一 排序 ###
由于majorityElement在数组中出现次数大于n/2,所以排序后**位于最中间的数**一定是该组的众数majorityElement。
此时时间复杂度取决于排序算法的效率，这里不再赘述。

### 思路二 摩尔投票法 ###
何谓摩尔投票法？
就是在每一轮投票过程中，从数组中找出一对不同的元素，将其从数组中删除。以此类推不断删除直到无法投票为止。如果数组元素为空，则没有任何一个元素超过数组长度的二分之一。如果只存在一个元素，那么这个元素就是目标元素。
 
在短发执行过程中，我们使用`常量空间`实时记录一个候选元素elem及其出现的次数Counts(elem)。在此，我们把数组中的第一个元素设为候选元素,计数器count=1,比较下一个数和候选元素elem。
>- 如果相等则计数器加一
>- 如果不相等计数器减一

**若此时计数器的值为0,则将该数设置为候选元素,若不为零则继续遍历比较**

```c++
class Solution{
public:
     int majorityElement(vector<int>& nums) {
         //设置候选元素下标和计数器。计数器开始一定为1
         int majorityIndex=0,count=1; 
         for(int i=0;i<nums.size();i++){
             nums.[majorityIndex]==nums[i] ? count++ : count-- ;

             //如果计数器为零，将该数设置为候选元素,并还原计数器的值
             if(count==0){
                 majorityIndex = i;
                 count = 1;
             }
         }
         return nums[majorityIndex];
     }
};

```
