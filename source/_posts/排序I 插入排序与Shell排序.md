---
layout: post
title:  排序I 插入排序与Shell排序
date: 2017-11-11
categories: ds
tags: [sort,algorithm]
---

## 前言
插入排序是一种典型的基于相邻元素交换的简单算法（还有冒泡排序和选择排序等），而Shell排序则在某种程度上与插入算法类似，故而放在一起说。（至于冒泡和选择？一个插入不就够了吗）

<!--more--> 

---

## 插入排序
插入排序的思想很简单，假设有一个序列`[x1, x2...xn]`，当对第k个元素进行排序时，把这个元素插入前面k-1个元素（已经有序）中合适的位置中（依然保持有序），也就是说，**若保证前k-1个元素有序，插入第k个元素后前k个元素也将保持有序，那么经过n次插入后，整个序列也将有序**。

### C++实现
实现中避免了显式的交换，而是将比`nk`大的元素都往后移了一位。
```cpp
    void insertsort(vector<int> &ele) {
      int temp = 0, j = 0;
      for (int index = 1; index < ele.size(); ++index) {
        temp = ele[index];
        for (j = index; j >= 1 && temp < ele[j - 1]; --j)
          ele[j] = ele[j - 1];
        ele[j] = temp;
      }
    }
```

### 时间复杂度
#### 插入排序
由代码可见，最坏情形下（如果反序输入），那么计算总数将为

$$
\sum_{i=2}^{n}i = 2 + 3 + 4 + ... + N = \Theta (N^2) 
$$

实际上，插入排序的平均情形也是$\Theta (N^2)$，而如果事先排好序，那么时间复杂度将为 $O(N)$ 。
### 简单排序算法的下界
首先，定义**逆序**的概念：在数组中具有 $i<j$ 且 $A[i] > A[j]$ 的有序对 $(A[i], A[j])$，而这正是简单排序算法的执行交换次数，每交换一次（非直接）不按顺序排列的两个元素将会消除一个逆序。当数组中不存在逆序时，则数组有序。

**定理一：N个互异数的数组平均逆序数是 $N(N-1)/4$**

证明：若一个数组有N个数，则将有$N(N-1)$个有序对$(A[i], A[j])$，而其中$i<j$的有$N(N-1)/2$，那么其中为逆序的平均便有$N(N-1)/4$个

**定理二：通过交换相邻元素进行排序的任何算法平均需要 $\Omega(N^2)$ 的时间**

证明：初始的平均逆序数为$N(N-1)/4 = \Omega(N^2)$，而每一次交换只减少一个逆序，因此需要$\Omega(N^2)$次交换。

---

## Shell排序
**Shell排序**也称**缩小增量排序(*diminishing increment sort*)**，由*Donald Shell*发明。

希尔排序使用一个增量序列 $h_1, h_2, ..., h_t$ ，只要$h_1=1$，那么任意增量序列都是可以的，因为此时排序等同于插入排序，当然不同序列的效率也有所不同。在使用$h_k$进行增量排序后，对于任意的$i$都有$A[i]<A[i+k]$(假设存在)，即所有相隔为$h_k$的元素都被排序，此时称数组为$h_k$-排序的（*$h_k$-sorted*）。而希尔排序的一个重要性质是，一个*$h_k$-sorted*的数组在之后的$h_{k+1}$排序中也将保持其$h_k$-排序的性质。

### C++实现
实际上是对任意的$i$对应的序列$A[i], A[i+h_k], A[i+2h_k]...$进行插入排序。
```cpp
    void shellsort(vector<int> &ele, string choice = "Shell") {
      vector<int> *seq;
      if (choice == "Sedgewick") {
        seq = &Sedgewick;
      } else if (choice == "Hibbard") {
        seq = &Hibbard;
      } else{
        seq = new vector<int>;
        for (int i = ele.size() / 2; i > 0; i /= 2)
          seq -> insert(seq -> begin(), i);
      }
      int temp = 0, j = 0, k = seq -> size() - 1;
      while ((*seq)[k] > ele.size() / 2)
        --k;
      for (int inc = (*seq)[k]; true; inc = (*seq)[--k]) {
        for (int index = inc; index < ele.size(); ++index) {
          temp = ele[index];
          for (j = index; j >= inc; j -= inc)
            if (ele[j - inc] > temp)
              ele[j] = ele[j - inc];
            else
              break;
          ele[j] = temp;
        }
        if (inc == 1) break;
      }
    }
```
#### 类实现
选择哪一种增量排序视情况我给了用户（不存在的）自己选择，故而有了`string`参数，并将增量序列封装成了`private`，这样写看上去有点low，但比起现场生成个人认为通过`while`判断从哪里开始更有效率，而如果选择了*Shell*序列，则构建一个序列也与之前的没差多少，具体可见[vegchic/learn-ds/sort/shellsort(insert)](https://github.com/vegchic/learn-ds/blob/master/sort/shellsort(insert).cpp)

---

### 时间复杂度
由于有不同的序列，这里只列出几种不同序列的时间复杂度。具体可见[wikipedia](https://en.wikipedia.org/wiki/Shellsort#Gap_sequences)

|  序列    | 计算公式 | 具体 | 最坏情形 |
| ----         | ---     | ---  | ---     |
| *Shell*     |$\left\lfloor\frac{N}{2^k}\right\rfloor\quad$ | $\left\lfloor\frac{N}{2}\right\rfloor,\left\lfloor\frac{N}{4}\right\rfloor,...,1$| $\Theta(N^2)$|
| *Hibbard*   | $2^k-1$  | $1,3,7,15...$ | $\Theta(N^{\frac{3}{2}})$ |

|	*Sedgewick*  | $\begin{cases}9(4^{k-1} - 2^{\frac{k}{2}}) + 1\text{ }k\text{ is even} \\\\ 4^{k+1} - 6\cdot2^{(k+1)/2} + 1\text{ }k\text{ is odd}\end{cases}$  | $1,5,19,41,109,...$  | $\Theta(N^{\frac{4}{3}})$ |



---

## 测试
以后的排序测试大概都是这个套路+valgrind了，就不贴了
```cpp
#include <random>
#define TEST 999
void test() {
  sort solution;
  vector<int> test;
  default_random_engine e(time(0));
  uniform_int_distribution<unsigned> u(0, TEST);
  for (int i = 0; i < TEST; ++i) {
    test.push_back(u(e));
  }
  solution.shellsort(test, "Shell");
  int prev = 0;
  for (int index = 0; index < TEST; ++index) {
    if (prev > test[index])
      cout << "error" << endl;
    prev = test[index];
  }
  vector<int> test2;
  for (int i = 0; i < TEST; ++i) {
    test2.push_back(u(e));
  }
  solution.insertsort(test2);
  prev = 0;
  for (int index = 0; index < TEST; ++index) {
    if (prev > test2[index])
      cout << "error" << endl;
    prev = test2[index];
  }
}
```

---

## 结语
最近学到了排序，虽然以前也已经开始用了，但是终归系统的学习了一下，故而写两篇笔记记录下自己学的。
### Reference
[Shellsort](https://en.wikipedia.org/wiki/Shellsort#Gap_sequences)

*《数据结构与算法分析——C语言描述》,Mark Allen Weiss*