---
title: 求解图的连通分量
date: 2020-04-06
mathjax: true
tags:
- C/C++
- Graph Theory
categories:
- Coding
- DSAA
---

## 简单定义

有向图中称为，强连通分量。连通图和连通分量都是针对无向图。

在有向图G中，如果两个顶点间至少存在一条路径，称两个顶点**强连通**(strongly connected)。如果有向图G的每两个顶点都强连通，称G是一个**强连通图**。非强连通图有向图的极大强连通子图，称为**强连通分量**(strongly connected components)。

<!-- more -->

## 无向图

对于无向图而言，只要从一个点开始使用DFS或者BFS遍历所有可以遍历的边，这些遍历到的点集就构成了一个连通分量。然后寻找下一个之前没有遍历的点作为下一个连通分量的根结点，继续进行遍历。

```cpp
vector<int> G[maxn];    // 邻接表
bool visited[maxn];     // 检查是否遍历完

// DFS 遍历连接图 获得连通分量
void dfs(int root) {
    visited[root] = true;
    for(int i = 0;i < G[root].size();i++) {
        if(!visited[G[root][i]]) {
            dfs(G[root][i]);
        }
    }
}
int count = 0;	// 连通分量的个数
for(int i = 1;i <= num_nodes;i++) {
    if(!visited[i]) {
        dfs(i);
        count++;
    }
}
```

## 有向图

下面两个算法都是$O(N+E)$的复杂度

### Tarjan算法

可参考：[有向图强连通分量的Tarjan算法](https://www.byvoid.com/zhs/blog/scc-tarjan)

算法的基本思想如下：任选一节点开始进行深度优先搜索（**若深度优先搜索结束后仍有未访问的节点，则再从中任选一点再次进行**）。搜索过程中已访问的节点不再访问。搜索树的若干子树构成了图的强连通分量。

节点按照被访问的顺序存入堆栈中。从搜索树的子树返回至一个节点时，检查该节点是否是某一强连通分量的根节点（见下）并将其从堆栈中删除。如果某节点是强连通分量的根，则在它之前出堆栈且还不属于其他强连通分量的节点构成了该节点所在的强连通分量。

算法代码为：

```cpp
tarjan(u)
{
    DFN[u]=Low[u]=++Index                      // 为节点u设定次序编号和Low初值
    Stack.push(u)                              // 将节点u压入栈中 其实就是保存拓扑序
    for each (u, v) in E                       // 枚举每一条边
        if (v is not visted)                   // 如果节点v未被访问过
            tarjan(v)                          // 继续向下找
            Low[u] = min(Low[u], Low[v])
        else if (v in S)                       // 如果节点v还在栈内
            Low[u] = min(Low[u], DFN[v])
    if (DFN[u] == Low[u])                      // 如果节点u是强连通分量的根
        repeat
            v = S.pop                          // 将v退栈，为该强连通分量中一个顶点
            print v
        until (u== v)
}
```

其中：

- DFN[u]：为结点u的搜索编号（时间次序）
- Low[u]：为结点u和与结点u相连的子树的最小搜索序（最早出现时间）

此算法其中判断强连通分量的思路就是，在以u为根节点进行DFS的过程中如果出现一个结点v指向了之前遍历过的点t，即$u \rightarrow t \rightarrow v \rightarrow t$也就表明出现了一个环，其中$t,v,t$就构成了环，即连通分量。

其中的DFN就保存每个结点的访问顺序，Low保存相关子树的最早访问时间，在算法回溯的时候更新，可保证一个强连通分量的Low都是一致的，栈保存的是拓扑序，即遍历的次序，不断出栈获取连通分量。

### Kosaraju算法

可参考：[Kosaraju算法解析: 求解图的强连通分量](https://www.cnblogs.com/nullzx/p/6437926.html)

Kosaraju算法比Tarjan算法看似要简单一些，但效率没有Tarjan算法高，Kosaraju算法依靠DFS遍历获取极大连通子图。但存在一点问题。

![](\assets\ArticleImg\2020\connected-components-1.png)

对于上图，从A0开始遍历和B4开始遍历是不一样的结果。如果从B开始遍历，需要2次DFS便可以遍历完整个图，而A0只需要一次。**所以Kosaraju算法的第一个DFS需要获取正确的遍历顺序，**然后第二次DFS的次数便是连通分量的个数了。

上图的反图为：

![](\assets\ArticleImg\2020\connected-components-2.jpg)

参考代码：

```cpp
// g 是原图，g2 是反图

void dfs1(int u) {
  vis[u] = true;
  for (int v : g[u])
    if (!vis[v]) dfs1(v);
  s.push_back(u);
}

void dfs2(int u) {
  color[u] = sccCnt;
  for (int v : g2[u])
    if (!color[v]) dfs2(v);
}

void kosaraju() {
  sccCnt = 0;
  for (int i = 1; i <= n; ++i)
    if (!vis[i]) dfs1(i);
  for (int i = n; i >= 1; --i)
    if (!color[s[i]]) {
      ++sccCnt;
      dfs2(s[i])
    }
}
```

主要就是两点：

1. 后序遍历获取拓扑序，保证BA的相对顺序不会改变
2. 反向原图根据上面获取的遍历顺序，重新DFS

#### 反图的作用

其中的反图很有意思，对于强连通子图，反图和原图并无区别。但对于非强连通的则会有很大的影响。例如$A \rightarrow B \rightarrow C$反图为$A \leftarrow B \leftarrow C$ 从A开始遍历一次便遍历完，但反图后从A开始便需要3次，也就是强连通分量的个数（单个结点也是强连通）。而且反图保证了图的收缩，可参考：[Kosaraju算法为什么是用图G的反向图的逆后序，而不能是图G的后序？](https://www.zhihu.com/question/265266923/answer/912239192)

**所以通过后续遍历获取遍历结点的顺序，然后通过反图获取强连通分量的个数。**

