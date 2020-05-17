---
title: 画个简陋樱花树（简单递归）
date: 2020-03-31
mathjax: true
tags:
- C/C++
categories:
- Coding
- ELSE
---

> 看到网上很多代码绘制的樱花树，参见[CSDN](https://blog.csdn.net/weixin_43943977/article/details/102691392)，便想自己试试，但是画的有点丑

<!-- more -->

## 基本思路

其实就是一个“二叉树的遍历”的思路，使用递归不断的二叉，就可以了，这也算是分形图案了。但是简单的递归二叉，不掌握好长度、角度、粗细以及主干和枝干的变化就会很规整。所以慢慢调参吧。

这里采用的是给出每个参数的最大取值范围，然后设置一个缩小的函数，随着递归层数的提高，对应的参数越来越小，这个小的程度和范围就要自己把握了。

缩小函数可以参考：$\frac{1}{n},\frac{1}{\sqrt{n}}$

## 依赖

这里的樱花树使用的是一个简单的[C++图形库 easyx](https://easyx.cn/)，简单容易上手。

## 演示

{% video /assets/ArticleVideo/2020/draw-cherrytree.mp4 %}

## 基础代码

```c++
#include <easyx.h>
#include <conio.h>
#include <vector>
#include <ctime>
#include <cstdlib>
#include <cmath>
#include <iostream>

#define SEE(x) cout << #x << ":" << x << endl;
#define RED (RGB(240, 128, 128))
#define WHITE (RGB(255, 255, 255))
#define BROWN (RGB(160, 82, 45))

// 与主干角度的偏移角度
const int angle_min = 18;
const int angle_max = 28;
// 长度
const int length_min = 60;
const int length_max = 90;
// 宽度
const int thick = 10;

// 绘制区大小
int width = 800, height = 600;

using namespace std;

typedef pair<int, int> Point;

// 绘制一条线
void drawline(Point st, Point ed) {
    line(st.first, st.second, ed.first, ed.second);
}

// 产生范围内的随机数
int rand_range(int st, int ed) {
    return st + rand() % (ed - st);
}

// 给点原点 相对于水平的角度 长度 返回处理后的节点
Point getPointFromAngle(Point src, float angle, int length) {
    angle = 3.14/180.0 * angle;
    return { src.first+length*cos(angle), src.second-length*sin(angle) };
}

// 递归画叉
void draw_bifurcation(Point p, float angle, int layer) {
    // 结束层
    if (layer == 13) {
        return;
    }
    srand(time(NULL));
    // 偏移角
    float delta = rand_range(angle_min, angle_max);
    // 收缩 使用根号
    float shrink = pow(layer, 0.5);
    // 便宜的角度越来越小
    float left_angle = angle + delta/shrink;
    // 右分支的角度确定
    float right_angle = left_angle - 2.4*delta/shrink;
    // 随机长度
    int length_left = rand_range(length_min / shrink, length_max / shrink);
    int length_right = rand_range(length_min / shrink, length_max / shrink);
    // 获取下一个分支点
    Point left = getPointFromAngle(p, left_angle, length_left);
    Point right = getPointFromAngle(p, right_angle, length_right);
    // 末端绘制红白相间的花瓣
    int type = rand() % 2;
    if (layer > 8) {
        if (type == 0) {
            setlinecolor(RED);
        }
        else {
            setlinecolor(WHITE);
        }
    }
    else {
        setlinecolor(BROWN);
    }
    // 设置厚度
    setlinestyle(PS_SOLID , thick/layer);
    drawline(p, left);
    // 递归左分支
    draw_bifurcation(left, left_angle, layer + 1);
    setlinestyle(PS_SOLID, thick / layer);
    if (layer > 8) {
        if (type == 0) {
            setlinecolor(WHITE);
        }
        else {
            setlinecolor(RED);
        }
    }
    else {
        setlinecolor(BROWN);
    }
    // 动态效果
    Sleep(1);
    drawline(p, right);
    // 递归右分支
    draw_bifurcation(right, right_angle, layer + 1);
}

// 随机绘制地面的花瓣
void draw_ground() {
    int left = width * 0;
    int right = width * 1;
    int up = height - 60;
    int down = height;
    int length = 4;
    setlinecolor(RED);
    setlinestyle(PS_SOLID, 3);
    int x, y, angle;
    for (int i = 0; i < 300; i++) {
        x = rand_range(left, right);
        y = rand_range(up, down);
        angle = rand_range(0, 360);
        Point ed = getPointFromAngle({ x, y }, angle, length);
        Sleep(5);
        drawline({ x, y }, ed);
    }
}

int main() {
    initgraph(width, height);
    // 设置背景色
    setbkcolor(RGB(241, 215, 118));
    cleardevice();
    // 绘制主干
    setlinecolor(BROWN);
    setlinestyle(PS_SOLID | PS_ENDCAP_SQUARE, 10);
    line(width/2, height-110, width/2, height-20);
    // 递归绘制
    draw_bifurcation({ width/2, height-110 }, 90.0, 1);
    // 绘制掉落花瓣的地面
    draw_ground();
    _getch();
    closegraph();
    return 0;
}
```

