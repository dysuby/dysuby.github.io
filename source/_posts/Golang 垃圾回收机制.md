---
layout: post
title: Golang 垃圾回收机制
date: 2019-06-20
categories: tech
tags: [docker-compose,docker]
---



本文简单介绍 golang 的垃圾回收（*Garbage Collection*）

<!--more-->



## 前言

垃圾回收是编程语言中提供的内存管理功能。

在 C/C++ 中，堆变量的内存是由程序员自己维护的，即如果变量不再使用，需要程序员手动释放内存，不然会发生**资源浪费**甚至**内存泄漏**。

```c++
// C++ example
int main() {
    int *a;
    for (size_t i = 0; i < 1000000; ++i) {
        a = new int[1000];
        // delete[] a;		doesn't free memory, result in crashed
    }
    return 0;
}
```



为了解决这个问题，后来开发出来的几乎所有新语言（java，python，php等等）都引入了语言层面的自动内存管理 —— 也就是语言的使用者只用关注内存的申请而不必关心内存的释放，内存释放由 *虚拟机（virtual machine）*或 *运行时（runtime）* 来自动进行管理。而这种对不再使用的内存资源进行自动回收的功能就被称为**垃圾回收**。



## 垃圾回收算法

这里只介绍常用的 引用计数，以及 golang 使用到的 标记-清楚，三色标记法



### 引用计数（Reference counting）

引用计数的思想很简单，就是当有其他变量引用该对象时计数器就加一，当引用该对象的变量不再引用或是被回收时计数器就减一，当计数器为 0 时对象便会被回收。

**优点：**

- 方法简单，回收速度快

**缺点：**

- 无法解决循环引用问题
- 花费额外空间
- 更新计数器影响性能



### 标记 - 清除（Mark and Sweep）

1. 标记从根变量开始迭代遍历所有被引用的对象，对能够通过应用遍历访问到的对象都标记为“被引用”；
2. 标记完成后进行清除操作，对没有标记过的内存进行回收（回收同时可能伴有碎片整理操作）。

用图表示如下：

![](/images/go gc/mark_sweep_1.png)

![](/images/go gc/mark_sweep_2.png)

![](/images/go gc/mark_sweep_3.png)

![](/images/go gc/mark_sweep_4.png)



**优点**：

- 解决了引用计数的问题

**缺点：**

- 垃圾回收时会 *stop the world*，存在性能问题

后续出现了很多 mark&sweep 算法的变种（如三色标记法）优化了这个问题。



### 三色标记算法（Tricolor Mark-and-sweep Algorithm）

三色标记法是对 标记-清除 的改进，在 mark 时，不再简单标记，而是：

1. 将所有对象标为 白色
2. 将可达到的对象标记为 灰色

1. 将 灰色 对象的引用对象标为新的灰色，并将当前对象标为黑色
2. 重复步骤 3，直至不存在灰色对象

清除的时候再将白色对象回收。

用图表示如下：

![](/images/go gc/mark_sweep_1.png)

![](/images/go gc/mark_sweep_2.png)

![](/images/go gc/mark_sweep_3.png)

![](/images/go gc/mark_sweep_4.png)



**优点：**

- 用户程序可以和 mark 并发执行



#### 写屏障（Write Barrier）

为了解决 GC 的同时用户修改了引用关系引入的拦截机制，会在 GC 运行时监视内存修改，并对对象重新标记。

假设有两种情况：

1. 产生了新对象：GC 会把所有新对象标记为 灰色，避免被 sweep
2. 引用了 白色 对象：重新标记为灰色



## Go GC

![](/images/go gc/golang-gc.png)



上图便是 Golang 的 GC 流程，可以看到，STW 只有 GC 开始时和 Re-scan（更新写屏障监视到的修改） 时出现。



### 变化过程

- v1.1 STW
- v1.3 Mark STW, Sweep 并行
- v1.5 三色标记法
- v1.8 hybrid write barrier（*Yuasa-style deletion write barrier [Yuasa ‘90]* 和 *Dijkstra-style insertion write barrier [Dijkstra ‘78]*）



## Reference

[GO GC 垃圾回收机制](https://segmentfault.com/a/1190000018161588)

[图解Golang的GC算法](https://i6448038.github.io/2019/03/04/golang-garbage-collector/)

[Golang 垃圾回收剖析](http://legendtkl.com/2017/04/28/golang-gc/)

