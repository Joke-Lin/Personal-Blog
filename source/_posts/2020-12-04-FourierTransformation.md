---
title: '傅里叶变换推导'
date: 2020-12-04
mathjax: true
categories:
- Computer Science
- CV&CG&DIP
- CV
tags:
- 傅里叶变换
- 信号处理
---

主要介绍从傅里叶级数出发推导傅里叶变换，知乎有很多优秀的傅里叶变换的推导，这里给出一个从傅里叶级数出发，化离散为连续推导出傅里叶变换（也参考了网上的一些思路）

本文的傅里叶形式主要为冈萨雷斯中文版《数字图形处理 第三版》第四章给出的形式，下文用《DIP》替代。

<!-- more -->

## 傅里叶级数

复数形式的傅里叶级数：
$$
\begin{align}
f(t) &= \sum_{n=-\infty}^{\infty} C_n e^{j\frac{2\pi n}{T}t} \\
C_n &= \frac{1}{T} \int_{-\frac{T}{2}}^{\frac{T}{2}} f(t) e^{-j\frac{2\pi n}{T}t} \text{d}t \\
n &= 0,\pm 1,\pm 2,\dots
\end{align}
$$
等式摘自《DIP》 4.2.2 傅里叶级数。

傅里叶级数的复数形式可参考网上，注意指数表达有角频率和频率两种，这里为角频率。傅里叶变换推导中将指出。

## 傅里叶变换

《DIP》4.2.4 给出了傅里叶变换对
$$
\begin{align}
F(\mu) &= \int_{-\infty}^{\infty} f(t) e^{-j2\pi \mu t} \text{d}t \\
f(t) &= \int_{-\infty}^{\infty} F(\mu) e^{-j2\pi \mu t} \text{d}\mu
\end{align}
$$
其实我们发现傅里叶变换和傅里叶级数的形式是十分相似的，且**级数与积分之间的关系可以用离散和连续来表示**，那么可以试试将傅里叶级数连续化，看看是否可以得出傅里叶变换。傅里叶级数是适用于周期函数的，对于一般函数可认为 $T\rightarrow \infty$

重写一般函数的傅里叶级数如下：
$$
\begin{align}
f(t) &= \lim_{T\rightarrow \infty} \sum_{n = -\infty}^{\infty} C_n e^{j\frac{2\pi n}{T}t} \\
&=\lim_{T\rightarrow \infty} \sum_{n = -\infty}^{\infty} 
\{\frac{1}{T} \int_{-\frac{T}{2}}^{\frac{T}{2}} f(t) e^{-j\frac{2\pi n}{T}t} \text{d}t\}
e^{j\frac{2\pi n}{T}t} \\
\end{align}
$$
令 $\Delta w = \frac{2\pi}{T}$（角频率）， $T\rightarrow \infty$ 所以 $\Delta w \rightarrow 0$，将 $\Delta w$ 带入上式可得
$$
f(t) = \lim_{\Delta w\rightarrow 0} \sum_{n = -\infty}^{\infty} 
\{\frac{\Delta w}{2\pi} \int_{-\infty}^{\infty} f(t) e^{-j\Delta w\cdot nt} \text{d}t\}
e^{j\Delta w\cdot nt}
$$
令 $w = \Delta w\cdot n$，带入可得
$$
f(t) = \lim_{\Delta w\rightarrow 0} \sum_{n = -\infty}^{\infty} 
\{\frac{\Delta w}{2\pi} \int_{-\infty}^{\infty} f(t) e^{-jwt} \text{d}t\}
e^{jwt}
$$
这里可以发现大括号内的就是傅里叶变换的形式。所以将其单独提取出来即：
$$
F(w) = \int_{-\infty}^{\infty} f(t) e^{-jwt} \text{d}t
$$
所以函数又可化为：
$$
\begin{align}
f(t) &= \lim_{\Delta w\rightarrow 0} \sum_{n = -\infty}^{\infty} 
\frac{\Delta w}{2\pi} F(w)
e^{jwt} \\
&= \frac{1}{2\pi} \lim_{\Delta w\rightarrow 0} \sum_{n = -\infty}^{\infty} 
\Delta w F(w)
e^{jwt}
\end{align}
$$
注意到 $w = \Delta w\cdot n$ ，因为 $n \in (-\infty,\infty)$，所以 w 为$(-\infty,\infty)$ 上以 $\Delta w$ 为间隔的点集。且有定义可知 w 是 n 的函数，对于确定的 $\Delta w$ ，有 $\text{d}w = \Delta w$，现在，令 $\Delta w\rightarrow 0$ ，即将 w 连续化，所以上式的**本质为函数 $F(w)e^{jwt}$ 自变量 w 在 $(-\infty,\infty)$ 上的积分**。最终可得：
$$
\begin{align}
f(t) &= \frac{1}{2\pi} \int_{-\infty}^{\infty} 
F(w) e^{jwt} \text{d} w \tag{1}\\
F(w)& = \int_{-\infty}^{\infty} f(t) e^{-jwt} \text{d}t \tag{2} \\
w &= \Delta w \cdot n \\
\Delta w &= \frac{2\pi}{T}
\end{align}
$$
式子 $(1),(2)$ 构成了傅里叶变换对，$(1)$ 为傅里叶逆变换，$(2)$ 为傅里叶变换

其中并没有涉及到 $\Delta w$ ，只涉及到自变量 $t,w$。但傅里叶变换对是时域到频率域的转换。现在也很好理解为什么连接了频率域，**因为推导过程涉及到了 $\Delta w$，它的物理意义为角频率，而 w 是数轴上以 $\Delta w$ 分割的点集，也就覆盖了全部的频率，换句话说，w 是在频率域中的变量**。

这个变换对定义式和《DIP》以及网上一些不同，主要在于系数 $\frac{1}{2\pi}$ 以及指数部分，本质上都是相同的，不过是**自变量取角频率还是频率的区别**。

频率和角频率的关系为：$w = 2 \pi \mu$，带入角频率傅里叶变换对可得：

1. 傅里叶变换
    $$
    F(w) = F(2\pi \mu) = \int_{-\infty}^{\infty} f(t) e^{-j2\pi \mu t} \text{d}t = F'(\mu)
    $$

2. 傅里叶逆变换
    $$
    \begin{align}
    f(t) &= \frac{1}{2\pi} \int_{-\infty}^{\infty} 
    F(w) e^{jwt} \text{d} w \\
    &= \frac{1}{2\pi} \int_{-\infty}^{\infty} 
    F(2\pi \mu) e^{j2\pi \mu t} \text{d} (2\pi \mu) \\
    &= \int_{-\infty}^{\infty} 
    F'(\mu) e^{j2\pi \mu t} \text{d} \mu
    \end{align}
    $$
    

即：
$$
\begin{align}
F(\mu) &= \int_{-\infty}^{\infty} f(t) e^{-j2\pi \mu t} \text{d}t \\
f(t) &= \int_{-\infty}^{\infty} 
F(\mu) e^{j2\pi \mu t} \text{d} \mu

\end{align}
$$

## 总结

本文给出了傅里叶变换对的频率与角频率形式，以及从傅里叶级数出发推导傅里叶变换的方法，但不是很严谨，只能是说给出了一个思路，傅里叶变换其实为傅里叶级数拓展到无穷周期的表示。



