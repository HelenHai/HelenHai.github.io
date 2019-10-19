---
layout: pages
title: 【Leetcode】Two Sum II - Input array is sorted
date: 2017-07-05 10:01:03
tags:
      - leetcode
categories: leetcode
---
## 题目 ##
Given an array of integers that is already sorted in ascending order, find two numbers such that they add up to a specific target number.

The function twoSum should return indices of the two numbers such that they add up to the target, where index1 must be less than index2. Please note that your returned answers (both index1 and index2) are not zero-based.

You may assume that each input would have exactly one solution and you may not use the same element twice.
>- Input: numbers={2, 7, 11, 15}, target=9
>- Output: index1=1, index2=2

## 关键点 ##
`1. index1 < index2`
`2. index不是从0开始的`

## 解题思路 ##
这是 `Two Sum` 的衍生题,这道题比较简单的地方就在于排序是升序的设定两个指针，一个指向开头，一个指向末尾，然后在中间遍历。
若sum = target,返回两个指针位置
若sum < target,左指针右移一位
若sum > target,右指针左移一位
以此类推...直至两个指针相遇  时间复杂度为 O(n)

```c++
class Solution {
public:
    vector<int> twoSum(vector<int>& numbers, int target) {
        int left=0, right=numbers.size()-1;
        int sum=0;     
        
        while(left<right){
            sum=numbers[left]+numbers[right];
            
            if(sum==target){
                return {left+1,right+1};
            }          
            else if(sum<target)  ++left;                
            else --right;          
        }
        
    }
};
```
