---
layout: post
title: 【week1】55. Jump Game 解题报告
date: 2018-09-07
categories: algorithm
tags: [homework,algorithm]
---

[55. Jump Game](https://leetcode.com/problems/jump-game/description/)

<!--more--> 

## 题目

Given an array of non-negative integers, you are initially positioned at the first index of the array.

Each element in the array represents your maximum jump length at that position.

Determine if you are able to reach the last index.

**Example 1:**

```
Input: [2,3,1,1,4]
Output: true
Explanation: Jump 1 step from index 0 to 1, then 3 steps to the last index.
```

**Example 2:**

```
Input: [3,2,1,0,4]
Output: false
Explanation: You will always arrive at index 3 no matter what. Its maximum
             jump length is 0, which makes it impossible to reach the last index.
```

## 解题报告

题意很简单，从数组的开始跳，判断能否跳到终点，数组元素为该处能跳的最远距离。

从题目的 tag 可知可以用贪心解，容易想到，每次都要跳最远。所以每一次都从起点 $begin$ 能跳到的最远的地方 $begin + len$ 开始往前找能跳的最远的落点。

a了之后看了看别人的解法发现自己的还是过于冗余，有重复计算。实际上从数组开始一步一步遍历，每轮都判断该落点能否跳得更远，如果该落点大于此时能跳到的最远落点时，则跳不到终点，最终时间代价只需要 $O(n)$。

## Solution

```cpp
// My Solution
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int begin = 0, next = nums[0];
        while (nums[begin] && next < nums.size() - 1) {
          for (int i = next; i != begin; --i)
            next = nums[i] + i > nums[next] + next ? i : next;
          begin = next;
          next = begin + nums[begin];
        }
        return next >= nums.size() - 1;
    }
};

// Others' Solution
class Solution {
public:
    bool canJump(vector<int>& nums) {
        int maxind = 0;
        for(int i=0;i<nums.size();i++){
            if(maxind<i) return false;
            if(i+nums[i]>maxind) maxind = nums[i]+i;
        }
        return true;
    }
};
```

