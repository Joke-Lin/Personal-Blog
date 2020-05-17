---
title: Binary Index Tree 树状数组
date: 2020-05-13
mathjax: true
tags:
- C/C++
categories:
- Coding
- DSAA
---

## 树状数组

树状数组是能够完成下述操作的数据结构：

给定一个初始值全为0的数列，$a_1,a_2,\dots,a_n$

- 给定i，计算$a_1+a_2+\dots+a_n$
- 给定i和x，执行$a_i+=x$

即单点修改和区间和计算

<!-- more --->

## 原理介绍

### 树状数组结构：

![](\assets\ArticleImg\2020\bit.png)

使用大节点保存了多个子节点的信息（区间和信息），如图，$a_8$为$a_1 \sim a8$的和，$a_6$则为$a_5,a_6$，十分巧妙

其中每个节点的相关节点和其下标对应的二进制有关，与其二进制末尾0的个数有关。其中求$a_1+a_2+\dots+a_n$，有以下规律：

1. $SUM(2) = a[1]+a[2]$
2. $SUM(3) = a[2] + a[3]$
3. $SUM(4) = a[4]$
4. $SUM(5) = a[4]+a[5]$
5. $SUM(6) = a[4]+a[6]$

要获取$SUM[n]$则必须知道$SUM(n-(2^{0的个数}))$，这个0的个数通过以下函数获取:

```cpp
int lowbit(int x) {
  return x & -x;
}
```

也可以用宏实现，x和-x按位相与便可以获得$2^{num(0)}$

所以获取$SUM(n)$，可以由以下代码获取：

```cpp
int getsum(int n) {
    int res = 0;
    while(x >= 1) {
        res += nums[n];
        n -= lowbit(n);
    }
    return res;
}

```

对于单点更新，从以下结论可以得出方法：

更新点x，则所有包含点x结果的点均要更新，而包含点x的点即为x+lowbit(x)，所以代码如下：

```cpp
// v当然可以是负数
void add(int x, int v) {
    while(x < maxn) {
        nums[x] += v;   
        x += lowbit(x);
    }
}
```

