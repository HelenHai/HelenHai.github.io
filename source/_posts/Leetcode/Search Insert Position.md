---
layout: pages
title: 【Leetcode】Search Insert Position
date: 2017-07-013 22:21:03
tags:
      - leetcode
categories: leetcode
---
## 题目 ##
Given a sorted array and a target value, return the index if the target is found. If not, return the index where it would be if it were inserted in order.

You may assume no duplicates in the array.

Here are few examples.
[1,3,5,6], 5 → 2
[1,3,5,6], 2 → 1
[1,3,5,6], 7 → 4
[1,3,5,6], 0 → 0

## 关键点 ##
查找

## 解题思路 ##
### 思路一 ###
遍历整个数组，查找到`（>=target）` 的数组元素，并返回数组下标。因为数组是有序的，所以遍历完成后还没有查找到说 `target` 是最大的，直接返回数组长度。

```c++
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
       for(int i=0;i<nums.size();i++){
           if(target <= nums[i]) return i;
       }
       return nums.size();
    }
};

```
### 思路二 ###
可以选择使用二分折半查找来提升查找效率

```c++
class Solution {
public:
    int searchInsert(vector<int>& nums, int target) {
        int left=0,right=nums.size()-1;
        
        //边界条件判定，比如数组长度为1时
        if(target>nums.back()) return nums.size();

        while(left<right){
            int mid=(left+right)/2;
            if(target==mid) return mid;
            if(target<mid) right=mid;
            else left=mid+1;
        }
        //当在数组中没有查询到target值时返回右指针的位置
        return right;
    }
};
```
