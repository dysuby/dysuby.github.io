---
layout: post
title: Canny 边缘检测
date: 2018-10-17
categories: algorithm
tags: [homework,computer vision,note,canny]

---

计算机视觉的作业，虽然完成的不好，但是还是记录下8。

<!--more--> 

## Canny 边缘检测

### 转灰度图

当拿到一张彩色图时，为了方便之后的处理，首先要进行的是灰度图的转化，这里直接使用了 **BT. 709** 的灰度转化公式：$$Y′ = 0.2126 R′ + 0.7152 G′ + 0.0722 B'$$ 

### 高斯模糊

既然拿到了灰度图，接下来就要进行**去噪处理**，不然噪点会影响后面对边缘的检测，*Canny* 使用的是**高斯模糊**，看了算法之后感觉还是很好理解的，就是通过**二维的正态分布函数**计算出一个滤波器，并与原图进行**卷积**，刚开始没接触过卷积，觉得很高大上，但在这里卷积其实就是将**邻域的点加权平均**而已。

大小为 $k$ 的滤波器函数：$$G(u,v)= \frac{1}{2\pi\sigma^2}\exp(-((u-k)^2-(v-k)^2)/(2\sigma^2))$$

卷积：$$img(x, y)=\sum_{i=-1}^1 \sum_{j=-1}^1 img(x-i,y-j)*G(i,j)$$，此处 $G(0,0)$ 对应 $img(x,y)$

### 计算梯度

在我的理解中，一个点的梯度可以告诉我们这个点的**变化方向和大小**，如果变化足够大，便有理由认为这个点为边缘点。这里使用的是 *Sobel* 算子计算梯度，其公式为：

$x$ 方向上：$$G_x=\begin{bmatrix}+1&0&-1\\\ +2&0&-2\\\ +1&0&-1\end{bmatrix} *A$$

$y$ 方向上：$$G_y=\begin{bmatrix}+1&+2&+1\\\ 0&0&0\\\ -1&-2&-1\end{bmatrix} *A$$

总的：$G=\sqrt{G_x^2+G_y^2}$

### 非极大值抑制

这个部分其实我也不算太理解，但是根据网上的一些资料，我认为在上一步中有一些边缘被重复检测了，而这里则要对这些边缘进行筛选，比如说一个边缘点 $E_i=(x,y)$，现在要找的 $E_{i+1}$有好几个点都满足条件，这时就要找一个最符合条件的点。***NMS* 将梯度方向上的极大值点保留，而将非极大值点抑制**。这里没有什么公式，只是**根据梯度的方向计算出两个线性插值**，以这两个插值作为比较对象进行极大值判断。这里也不好讲清楚，具体可以去看看代码。*（注：梯度方向垂直于边缘方向）*

权重的计算公式：$$weight=\left\vert \frac{min\{gradx(x, y), grady(x,y)\}}{max\{gradx(x, y), grady(x, y)\}}\right\vert$$

```pseudocode
if (nms(x, y) > p1 && nms(x, y) > p2) nms(x, y) = grad(x, y);
else nms(x, y) = 0;
```

### 滞后阈值

滞后阈值使用高低两个阈值（高阈值记为`th`，低阈值记为`tl`）来区分边缘像素，这里直接用伪代码表示出来更直观。

```pseudocode
for point in image
	if point > th
		point = EDGE
	else if point < tl
		point = NOEDGE
	else if there is any EDGE in 8-neighbourhood // 不知道八邻域怎么说
		point = EDGE
```

这里对强边缘点和非边缘点的处理都可以理解，而对弱边缘点的处理则有点难理解，我认为强边缘点终究不是所有的边缘点，而弱边缘点便是用来*补充*强边缘点的，**如果一个点的八邻域内有强边缘点，则这个点就可以被认为是一个边缘点**，就好像连线一样。



## 反思

这次实验作业要求我们改写并封装代码，但是由于个人水平有限，原代码的许多地方我无法理解，所以不得不参考了网上的资料以及提供的其他原代码，虽然写出来都是 *Canny* 算法，但是内容已经与我需要改写的那部分原代码差之甚远，没有满足作业的要求。

在查资料和阅读原代码的过程中，我也收获了很多，例如我刚开始并不知道高斯模糊为什么叫高斯模糊，它究竟是个什么原理，但是自己写了一遍之后便发现，原来使用的是正太分布，原理也如此简单。直到现在，我感觉我对这个算法也有了一定的了解，但却也谈不上掌握，因为我并不知道 *Canny* 相较于其他算法有何差别。

Canny 算法在我看来并不难理解，因为它的每一步的目的都很清晰，从去噪到求梯度再到区分筛选，都有理可循，但是我可能是学识尚浅，并不能在细节上区分实现不同导致的效果好坏，看同学的实现效果似乎也与自己有些差别，深知自己需要学习的还有很多。



## 参考资料

<del>明明看的都是中文 wiki 却要放英文 wiki</del>

[Grayscale](https://en.wikipedia.org/wiki/Grayscale)

[Canny edge detector](https://en.wikipedia.org/wiki/Canny_edge_detector)

[Sobel operator](https://en.wikipedia.org/wiki/Sobel_operator)

[Gaussian blur](https://en.wikipedia.org/wiki/Gaussian_blur)

[Canny算子中的非极大值抑制（Non-Maximum Suppression）分析](https://blog.csdn.net/kezunhai/article/details/11620357)

以及其他一些博客。