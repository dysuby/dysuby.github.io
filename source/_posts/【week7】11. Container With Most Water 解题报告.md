---
layout: post
title: 【week7】11. Container With Most Water
date: 2018-10-18
categories: algorithm
tags: [homework,algorithm]
---

[11. Container With Most Water](https://leetcode.com/problems/container-with-most-water/description/)

<!--more--> 

## 题目

Given *n* non-negative integers *a1*, *a2*, ..., *an* , where each represents a point at coordinate (*i*, *ai*). *n* vertical lines are drawn such that the two endpoints of line *i* is at (*i*, *ai*) and (*i*, 0). Find two lines, which together with x-axis forms a container, such that the container contains the most water.

**Note:** You may not slant the container and *n* is at least 2.

 

![img](https://s3-lc-upload.s3.amazonaws.com/uploads/2018/07/17/question_11.jpg)

The above vertical lines are represented by array [1,8,6,2,5,4,8,3,7]. In this case, the max area of water (blue section) the container can contain is 49.

 

**Example:**

```
Input: [1,8,6,2,5,4,8,3,7]
Output: 49
```

## 解题报告

题意很简单，看图就可以猜出大概是要找出黑柱能装下的最大容量。

题目提示是使用双指针，我就先搞了个 `begin`，`end`作为两个黑柱，刚开始是想着两个指针同时从头开始遍历，`begin` 只有在 $height[end] > height[begin]$ 时才更新，而 `end` 每次迭代都 `+1`，但是发现光是题图就不能得出答案。

于是想着既然同一方向不行，那就两个指针相向而行。同时我确认了一个原则：**尽可能保留高的柱**。刚开始我写的是：每次迭代都让下一位置较大的指针移动，即

```cpp
if (height[end - 1] > height[begin - 1]) --end; 
else ++begin;
```

发现还是 a 不掉，于是想到，既然我要保留高的柱，我为什么要比较下一个柱子，我应该直接比较当前柱子呀。试了一下，a 掉了。

**为什么是这样做呢**

我认为 `begin`，`end` 始终在靠近对方，而当柱子高度相同时，距离越近，最大容量显然越小，所以应该尽可能保留“远处”的高柱子。

当然，其实当一个指针 `i` 不动时，我们是在找以 `height[i]` 为两个柱子其中之一的最优解，当我们找到另一个更高的柱子 `height[j]` 时，就意味着最高容量为 $maxArea = height[i] * |i - j|$。此时就算 `j` 的下一个柱子更高，也不会改变最大容量，甚至会因为更靠近 `i` 使得容量减少。

更一般的说，当 $min \left \lbrace height[i], height[j] \right \rbrace​$  固定时，即使另一个柱子再高，也不会更新最大容量，反而会逐渐减少，所以每次迭代时都是让较矮的柱子改变。

另外，容易想到，当其中一个柱子 `i` 变高之后，与另外的柱子之前的柱子组合起来也不会使 `i` 的最大容量变得更大，这里不再说明。

## Solution

```cpp
class Solution {
public:
    int maxArea(vector<int>& height) {
        int area = 0;
        for (int begin = 0, end = height.size() - 1; end != begin; ) {
            area = max(area, (end - begin) * min(height[end], height[begin]));
            if (height[end] < height[begin]) --end;
            else ++begin;
        }
        return area;
    }
};
```

