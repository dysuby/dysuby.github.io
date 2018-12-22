---
layout: post
title: CFLP 两种解题算法
date: 2018-12-22 00:00
categories: algorithm
tags: [homework,algorithm]
---

代码和结果可以在 [vegchic/CFLPsolver](https://github.com/vegchic/CFLPsolver) 上找到

## Capacitated Facility Location Problems

现有客人 $n$ 位，工厂 $m$ 座，每个客人都有自己的服务需求，每个工厂都能提供一定数量的服务，每个客人到每座工厂都需要一定的费用，工厂刚开始是关闭的，开工厂也要付出一定的费用。现在要求： **求能满足所有客户的需求的最小代价分配方案**。

<!--more-->

以上问题可以用数学公式表达：

$$y_i=\begin{cases}1&\text{if facility }i\text{ is opened}\\\ 0&\text{otherwise}\end{cases}$$

$$x_{ij}=\begin{cases}1&\text{if facility }i\text{ serves customer }j\\\ 0&\text{otherwise}\end{cases}$$

$$v^*=\min\sum^m_{i=1}\sum^n_{j=1}c_{ij}x_{ij}+\sum^m_{i=1}f_iy_i$$

$$\text{s.t.}\ \sum_n^{j=1}a_jx_{ij}\le b_iy_i \ \forall i,$$

$$\sum_{i=1}^mx_{ij}=1\ \forall j,$$

$$x_{ij}-y_i\le0\ \forall i,j,$$

$$x_{ij}\in\{0,1\}\ \forall i, j,$$

$$y_i\in\{0,1\}\ \forall i,$$

其中，$a_j$ 为第 $j$ 位顾客的需求，$b_i$ 为第 $i$ 座工厂的容量，$f_i$ 为开第 $i$ 座工厂的费用，$c_{ij}$ 为将第 $j$ 位顾客分配到 $i$

 座工厂的费用。在实际编程中，我直接将 $x_{ij}$ 简化为 ${x_j}$。



### 迭代贪心

可以使用贪心算法求一个近似解。算法如下：

```pseudocode
for i from 0 to T /* T 为迭代次数 */
	shuffle customer order
	for c in customer
		choose the closet avaliable facility f
		if f is closed
			open f
			update solution
		update solution
	update optimal_solution
```



#### 分析

可以看见，该算法并没有考虑开厂的费用，因为大部分开厂费用都比分配的费用要少。通过增加迭代打乱客户顺序，可以让解变得更好，而且写起来也比较简单，该算法优点是速度快，缺点是可能误差会比较大。经试验迭代次数 $T=10$ 最好。



#### 代码

[greedy.py](https://github.com/vegchic/CFLPsolver/blob/master/greedy.py)



#### 结果

[greedy_result.csv](https://github.com/vegchic/CFLPsolver/blob/master/result/greedy_result.csv)

[greedy_details](https://github.com/vegchic/CFLPsolver/tree/master/result/greedy)



### 模拟退火

模拟退火也可以求一个近似解。通过按概率接受差解可以跳出局部最优解。算法如下：

```pseudocode
generate the init solution
optimal = init_solution
while T > tmin:
	for i from 0 to times
		new_solution = localsearch(solution)
		delta = new_solution.cost - optimal.cost
		if new_solution.cost < optimal.cost or random() < exp(-delta / T)
			update optimal
	drop T by t_rate
```



#### 邻域算子

1. 随机交换两个顾客分配到的工厂：
   1. 初始化候选表，里面包含客户两两配对的所有情况。
   2. 随机选择两个顾客。
   3. 如果交换后不能满足约束，从候选表中移除这个组合，如果候选表为空，则返回当前解，否则转到 2。
   4. 交换两个顾客分配到的工厂，更新 $x$ 和总费用。
2. 随机将一个顾客移到另一个工厂：
   1. 初始化工厂的候选表和顾客的候选表。
   2. 随机选择一个顾客。
   3. 随机选择一个工厂。
   4. 如果顾客不能转移到该工厂，则从工厂候选表中移除该工厂，如果工厂候选表不为空，转 3，如果为空，则从客户候选表中移除该客户，如果客户候选表不为空，转 2，否则返回当前解。
   5. 将顾客转移并更新 $x$ 和总费用，如果转移后使顾客原来的工厂没有服务的顾客，则关闭该工厂并更新 $y$ 和总费用。
3. 随机选择一个工厂，将其他工厂的顾客分配到它上直到达到其容量限制：
   1. 初始化工厂的候选表。
   2. 随机选择一个工厂。
   3. 如果工厂的剩余容量不能满足任意一位顾客（本来应该是其他工厂的顾客，这里做了简化）的需求，则从候选表中移除该工厂，如果不为空，转 2，否则返回当前解。
   4. 打乱顾客顺序。并将能放进该工厂的顾客转移到该工厂，如果转移后使原来的工厂不再服务任何顾客，则关闭工厂，更新 $x​$，$y​$，还有总费用。



#### 分析

模拟退火耗时比贪心长，但是可以找到更优解，在实验中参数取 $T=100,\ tmin=5,\ times=3000,\ trate=0.95$



#### 代码

[SA.py](https://github.com/vegchic/CFLPsolver/blob/master/SA.py)



#### 结果

[sa_result.csv](https://github.com/vegchic/CFLPsolver/blob/master/result/sa_result.csv)

[sa_details](https://github.com/vegchic/CFLPsolver/tree/master/result/sa)



### 对比

自己写了个 [compare.py](https://github.com/vegchic/CFLPsolver/blob/master/compare.py)，比较了下模拟退火和贪心（将退火花费的时间和解的费用与贪心作对比）：[compare.csv](https://github.com/vegchic/CFLPsolver/blob/master/result/compare.csv)。从性价比来看，退火用时过长，不太划算，不过这也是因为我的写法太过耗时。

