---
layout: post
title:  LaTeX简单使用
date:   2017-10-05
categories: LaTex
tags: [LaTeX]
---

## 前言
  因为离散要求写一篇算法的小文章，实在无法忍受word的数学公式输入之后，我自（`ctrl`）学（`c`）了一下LaTeX的写法，这里简单介绍我写文章时用到的代码，时隔多日，若有偏差欢迎指出

<!--more--> 

---

## 中文LaTeX
  百度上输出中文的方法各种各样，在尝试n多种方法无果后（CJK什么的），使用了**XeLaTeX**，只需使用如下文档类型，中括号内为可选参数，UTF8表示使用utf8编码，花括号内为文档类型，ctexart表示其文档类型为中文article

```tex
\documentclass[UTF8]{ctexart}
```

## 正文区
形如`\begin{环境名}...\end{环境名}`称为**环境**，而`\begin{document}...\end{document}`则是正文区，里面的内容就是文章的内容，而这之前的部分则是用来导入各种**宏包**

## 标题摘要
下面代码较为易懂，`\maketitle`也即打印标题
```tex
\title{Notes On LaTeX Typesetting}
\author{Some One}
\date{November 13, 2011}
\maketitle
```

当然还可以写摘要

```tex
\begin{abstract}
some abstract...
\end{abstract}
```

## 章节目录
代码如下，其中`\chapter`在article中不能使用

```tex
\part{部分标题}
\chapter{章标题}这一章我们介绍这些内容。
\section{节标题}这一节我们介绍这些内容。
\subsection{小节标题}这一小节我们介绍这些内容。
\subsubsection{子节标题}这一子节我们介绍这些内容。
\paragraph{段标题}这一段我们介绍这些内容。
\subparagraph{小段标题}这一小段我们介绍这些内容。
```

使用`\tableofcontents` 可以生成目录，而输入形如

```tex
\renewcommand\thesection{§\arabic{section}}
```
则可以自定义某一类章节标题的格式，在此不作示范

## 列表环境
这里列举三种列表，分别为**无序列表，有序列表，描述列表**

```tex
\begin{itemize} %无序列表
\item 无编号的列表
\item 带编号的列表
\item 带标签的列表
\end{itemize}
\begin{enumerate} %有序列表
\item 无编号的列表
\item 带编号的列表
\item 带标签的列表
\end{enumerate}
\begin{description} %描述列表
\item[无序列表] 无编号的列表。%中括号内为描述内容（相当于一种“序号”）
\item[有序列表] 带编号的列表。
\item[描述列表] 带标签的列表。
\end{description}
```

## 插入图片
插入已有图片需要导入宏包**graphicx**，即`\usepackage{graphicx}`，使用如下

```tex
\includegraphics[选项]{图形文件} %与文字并行的图片

%将图片单独居中插入
\begin{center}
\includegraphics[选项]{图形文件}
\end{center}

%插入浮动图片
\begin{figure}[htbp!]
\centering
\includegraphics[选项]{图形文件}
\caption{图片} %可以在图片下方加标注并自动编号
\end{figure}
```

## 输入数学公式
此处即makedown写法即可，`$a=1$`为**行内公式**，`$$a=1$$`为**块级公式**，具体写法参考[LaTeX/Mathematics](https://en.wikibooks.org/wiki/LaTeX/Mathematics)

## 插入伪代码
这里需要导入宏包

```tex
\usepackage{caption}
\usepackage{algorithm}
\usepackage{algorithmicx}  
\usepackage{algpseudocode}
```
插入时则

```tex
\begin{algorithm}  
\caption{算法名字}
\begin{algorithmic}
\Require 输入
\Ensure 输出
\end{algorithmic}
\State ...
\For{...}
	\State ...
\EndFor
\If
...
\EndIf
\While
...
\EndWhile
\end{algorithm}
```
其中还可以对格式进行定制

```tex
\floatname{algorithm}{算法}  
\renewcommand{\algorithmicrequire}{\textbf{输入:}}  
\renewcommand{\algorithmicensure}{\textbf{输出:}}  
```

## 插入代码
这里需要导入宏包

```tex
\usepackage{listings}
```
还可以对代码进行定制

```tex
\usepackage{xcolor}
\lstset{
    frame=none,                                          % 不显示背景边框
    backgroundcolor=\color[RGB]{245,245,244},            % 设定背景颜色
    keywordstyle=\color[RGB]{116,0,0},                   % 设定关键字颜色
    commentstyle=\it\color[RGB]{0,96,96},                % 设置代码注释的格式
    stringstyle=\rmfamily\slshape\color[RGB]{255,153,18},   % 设置字符串格式
    showstringspaces=false,                              % 不显示字符串中的空格
    language=c++,                                        % 设置语言
}
```
插入代码时使用lstlisting环境即可

```tex
\begin{lstlisting}

#include <iostream>
using namespace std;
int main() {
  cout << "hello" << endl;
  return 0;
}

\end{lstlisting}
```

## 参考文献

```tex
\begin{thebibliography}{123456}
\bibitem[Knuth1]{DK1} D. Knuth, T.A.O.C.P. , Vol. 1, Addison-Wesley, 1997.
\bibitem[Knuth2]{DK2} D. Knuth, T.A.O.C.P. , Vol. 2, Addison-Wesley, 1997.
\bibitem[Knuth3]{DK3} D. Knuth, T.A.O.C.P. , Vol. 3, Addison-Wesley, 1998.
\end{thebibliography}
```
中括号内为文献记号，花括号内为引用名称，至于那个123456表示我也看不懂。

## 字体环境

```tex
\textrm{Roman Family}
\textsf{Sans Serif Family}
\texttt{Typewriter Family}
```
花括号内为字体内容

---

## 感谢
本文内容及示例参考[LaTeX 排版学习笔记](http://zoho.is-programmer.com/posts/30662.html)以及[ LaTeX实战经验：如何插入程序代码](http://blog.csdn.net/simple_the_best/article/details/52710830)，写文章时也参考诸多博客，感谢这些博客