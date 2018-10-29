---
layout: post
title: 【week4】79. Word Search 解题报告
date: 2018-09-25
categories: algorithm
tags: [homework,algorithm]
---

[79. Word Search](https://leetcode.com/problems/word-search/description/)

<!--more--> 

## 题目

Given a 2D board and a word, find if the word exists in the grid.

The word can be constructed from letters of sequentially adjacent cell, where "adjacent" cells are those horizontally or vertically neighboring. The same letter cell may not be used more than once.

**Example:**

```pseudocode
board =
[
  ['A','B','C','E'],
  ['S','F','C','S'],
  ['A','D','E','E']
]

Given word = "ABCCED", return true.
Given word = "SEE", return true.
Given word = "ABCB", return false.
```

## 解题报告

题意大概是在一个矩阵中判断是否存在一条路恰好满足给定字符串。

本来刚开始以为可以用 DP 做，但是无奈水平有限，写不出来。于是乖乖用回溯做。除了一些小 bug 外，主要还是要注意一条路上走过的元素要做标记，并在递归出来时把标记去掉，避免下一条路卡在这里。

算法复杂度是 $O(2^n)$，本来以为挺慢的，没想到跑出来还打败了 99% 的提交。

## Solution

```cpp
class Solution {
public:
    bool exist(vector<vector<char>>& board, string word) {
        for (int i = 0; i < board.size(); ++i)
          for (int j = 0; j < board[0].size(); ++j)
            if (board[i][j] == word[0] && dfs(board, word, 0, i, j))
              return true;
        return false;
    }
    
    bool dfs(vector<vector<char>>& board, string& word, int index, int row, int col) {
      if (row * col < 0 || row >= board.size() || col >= board[0].size()) return false;
      if (index == word.size() - 1) return board[row][col] == word[index];
      else if (board[row][col] == word[index]) {
        board[row][col] = 0;
        if (dfs(board, word, index + 1, row + 1, col) || dfs(board, word, index + 1, row, col + 1) ||
          dfs(board, word, index + 1, row - 1, col) || dfs(board, word, index + 1, row, col - 1))
          return true;
        board[row][col] = word[index];
      }
      return false;
    }
};
```

