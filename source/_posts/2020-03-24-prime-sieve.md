---
title: '埃氏筛 & 欧拉筛'
date: 2020-03-24
tags:
- C/C++
categories:
- Coding
- DSAA
---


> 素数的定义：指在大于1的自然数中，除了1和该数自身外，无法被其他自然数整除的数。大于1的自然数若不是素数，则称之为合数。

<!-- more -->

## 试除法

直接使用定义解决的方案。

```c++
bool isPrime(int n) {
    for(int i = 2;i <= sqrt(n);i++) {
        if(n % i == 0) {
            return false;
        }
    }
    return true;
}
```

这里从2遍历到$\sqrt n$ 因为之后的就没有必要了，一个和数拆分成两个因子必定是在$\sqrt n$的两侧，是对称的，所以只需要遍历一边就可以了。


## 埃氏筛

上面的算法是判断一个数是否是素数，但是对获取某个范围的素数开销非常大。埃氏筛（素数筛）就是求某个范围素数的算法。

原理很简单，合数必定可以拆分为一系列素数的积，即**某个素数的任意倍数都是合数**。

代码也很简单：

```c++
// maxn 为范围的上届
bool is_prime[maxn] = {true}; // 初始化为全部 true
is_prime[0] = is_prime[1] = false;
int prime_numbers[maxn];
int cnt = 0;
for(int i = 2;i < maxn;i++) {
    if(is_prime[i]) {
        prime_numbers[cnt++] = i;
        for(int j = i*i;j < maxn;j += i) {
            is_prime[j] = false;
        }
    }
}
```

复杂度为：$n\log \log n$

## 欧拉筛

埃氏筛很明显的一个缺点就是一个合数会被重复筛掉（被每一个素数因子筛一次），增加复杂度。欧拉筛就是在其上的改进，使每一个合数只被它的最小因子筛掉。

```cpp
bool is_prime[maxn] = {true}; // 初始化为全部 true
is_prime[0] = is_prime[1] = false;
int prime_numbers[maxn];
int cnt = 0;
for(int i = 2;i < maxn;i++) {
    if(is_prime[i]) {
        prime_numbers[cnt++] = i;
    }
    // 循环中<maxn没有也没关系
    for(int j = 0;i*prime_numbers[j] < maxn;j++) {
        is_prime[i*prime_numbers[j]] = false;
        if(i % prime_numbers[j] == 0) {
            break;
        }
    }
}
```

欧拉筛复杂度为n（将空循环不视作开销），每个合数仅被筛一次。

核心就在于`i % prime_numbers[j] == 0`，当这个条件成立的时候跳出循环，不继续往下筛，下面的都是已经或者未来要被筛掉的。

条件成立时意味着 $i = prime\_numbers[j] \times K$，而继续循环下去可以得到$i*prime\_numbers[j+1]$也就是,$prime\_numbers[j]\times prime\_numbers[j+1] \times K = prime\_numbers[j] \times Q$，也就是说它会被$prime\_numbers[j]$乘以另外一个数给筛掉，之后的循环也就没必要进行了。也保证了，每个合数都只被它的最小因子筛掉。

令$N = K \times Q \times ...$  K Q为两个素数且$Q \gt K$，假设N可以被Q筛掉，也就是$N = Q \times M， M= K\times... $这里的M就是上面循环中的i，它是不可能使用QM筛掉的，因为`M%K == 0`，就跳出循环之外了。