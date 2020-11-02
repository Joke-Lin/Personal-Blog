---
title: 【STL】next_permutation 算法原理
date: 2020-09-24
mathjax: true
tags:
- C/C++
- STL
categories:
- Computer Science
- DSAA
---

> 参考：
>
> - [【博客园】 STL next_permutation 算法原理和自行实现](https://www.cnblogs.com/luruiyuan/p/5914909.html)
> - [【CSDN】next_permutation和pre_permutation源码解析](https://blog.csdn.net/Czyaun/article/details/104420329)

## 基本思路

【STL】 `next_permutation` 函数就是返回当前序列的下一个字典序，已经为最大字典序则返回 False，否则为返回 True

基本思想如下：

1. 从尾端开始依次比较两个相邻元素直到存在 $a_i,a_{i+1}$ 满足 $a_i < a_{i+1}$，如果未找到返回 False
2. 从尾端开始向前检验，找出第一个大于 $a_i$ 的元素 $a_j$，交换 $a_i,a_j$
3. 将 $a_i$ 之后（不包括 $a_i$）的序列做反序处理

<!-- more -->

可能的代码实现：

```cpp
template<class BidirIt>
bool next_permutation(BidirIt first, BidirIt last)
{
    if (first == last) return false;
    BidirIt i = last;
    if (first == --i) return false;
 
    while (true) {
        BidirIt i1, i2;
 
        i1 = i;
        if (*--i < *i1) {
            i2 = last;
            while (!(*i < *--i2))
                ;
            std::iter_swap(i, i2);
            std::reverse(i1, last);
            return true;
        }
        if (i == first) {
            std::reverse(first, last);
            return false;
        }
    }
}
```

## 简单证明

上面给出了思路，这里做一个简单证明

对一个序列 $a_0,a_1,\cdots,a_i,a_{i+1},\cdots,a_{n-1}$，必定存在 $i$，使得 $a_0 \sim a_i$，为正序，$a_{i+1}\sim a_{n-1}$ 为逆序（这里正序指的是从小到大的顺序，逆序为从大到小），这里对应了上面的步骤 1

现在需要求此序列的下一个字典序，容易得到，$a_{i+1}\sim a_{n-1}$ 已经为逆序（也为递减数列），不存在更大的字典序，但 $a_{i}\sim a_{n-1}$，并不是逆序，即存在更大的字典序

显然以 $a_i$ 作为 $a_{i}\sim a_{n-1}$ 的首元素已经达到最大，所以需要在 $a_{i+1}\sim a_{n-1}$ 中找到一个元素替换 $a_i$，显然此元素为大于 $a_i$ 的最小元素，因为数列 $a_{i+1}\sim a_{n-1}$ 为递减数列，所以只需要从末端开始向前寻找第一个大于 $a_i$ 的元素即可，记此元素为 $a_j$，即步骤 2

因为 $a_j > a_i$，所以 $a_j$ 作为首元素的最小排列为 $a_{i+1} \sim a_{n-1}$ 的顺序表示即可。交换后的序列仍然为逆序的，所以只需要反序处理即可，对应步骤 3