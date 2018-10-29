---
layout: post
title: 【week5】49. Group Anagrams
date: 2018-10-2
categories: algorithm
tags: [homework,algorithm]
---

[49. Group Anagrams](https://leetcode.com/problems/group-anagrams/description/)

<!--more--> 

## 题目

Given an array of strings, group anagrams together.

**Example:**

```
Input: ["eat", "tea", "tan", "ate", "nat", "bat"],
Output:
[
  ["ate","eat","tea"],
  ["nat","tan"],
  ["bat"]
]
```

**Note:**

- All inputs will be in lowercase.
- The order of your output does not matter.

## 解题报告

题意很简单，就是将易位之后可以互相转化的字符串分成各自的一堆。

刚开始想到的是用一个 `int` 作为哈希表，对一个字符串的每个字符进行 `bit &= (1 << (c - 'a'))` 的哈希，然后再放入 `map<int, vector<string>>` 里，但是还没写完就想起字母可以重复，所以要重新想。

既然刚开始已经用了哈希，那接下来自然也在想如何让同类字符串具有一样的哈希值，灵光一闪想到了质数分解（可能是之前在知乎看过这种哈希法），直觉告诉我，一个数的质数分解是唯一的，所以我省略了求证的过程。

首先将 26 个质数写出，然后通过依然是遍历每个字母，但是这次进行的是 `bit *= pn[c - 'a']` ，然后放入 `map` 中。果不其然 a 掉了，打败了 97.6% 的提交。

算法复杂度为 $O(M*N)$.

a 了之后查了下果真有这个定理。

**[唯一分解性定理](https://zh.wikipedia.org/wiki/%E7%AE%97%E6%9C%AF%E5%9F%BA%E6%9C%AC%E5%AE%9A%E7%90%86)**：每个大于1的[自然数](https://zh.wikipedia.org/wiki/%E8%87%AA%E7%84%B6%E6%95%B0)或者本身就是[质数](https://zh.wikipedia.org/wiki/%E8%B4%A8%E6%95%B0)或者可写为质数的[积](https://zh.wikipedia.org/wiki/%E7%A7%AF)，而且这些质因子按大小排列之后，写法仅有一种方式。

## Solution

```cpp
class Solution {
public:
    vector<vector<string>> groupAnagrams(vector<string>& strs) {
        map<int, vector<string>> mp;
        for (auto &s: strs) {
          int bit = 1;  // 这道题的测例没有溢出
          for (auto &c: s)
            bit *= pn[c - 'a'];
          mp[bit].push_back(s);
        }
        vector<vector<string>> ans;
        for (auto &entry: mp)
          ans.push_back(entry.second);
        return ans;
    }
  int pn[26]{2, 3, 5, 7, 11, 13, 17, 19, 23, 29, 31, 37, 41, 43, 47, 53, 59, 61, 67, 71,
            73, 79, 83, 89, 97, 101};
};
```

