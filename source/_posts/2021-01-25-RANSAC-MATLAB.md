---
title: 'Matlab ransac 库函数介绍'
date: 2021-01-25
mathjax: false
categories:
- Computer Science
- CV&CG&DIP
- CV
tags:
- matlab
- RANSAC
---

matlab 自带了 ransac 函数，这里总结一下用法。matlab 自带的这个函数其实是 MSAC（M-estimator sample consensus）算法，但其实差不多，它对给定数据集，以及自定的模型计算函数和误差计算函数返回具有最大内点的模型。

<!-- more -->

## 函数声明

从 ransac 函数的源文件介绍中截取一段：

```matlab
%RANSAC Fit a model to noisy data. 
%   [model, inlierIdx] = RANSAC(data, fitFcn, distFcn, sampleSize, maxDistance) 
%   fits a model to noisy data using the M-estimator SAmple Consensus 
%   (MSAC) algorithm, a version of RAndom SAmple Consensus algorithm.
%   
%   Inputs      Description 
%   ------      -----------
%   data        An M-by-N matrix, whose rows are data points to be modeled.
%               For example, for fitting a line to 2-D points, data would be an 
%               M-by-2 matrix of [x,y] coordinates. For fitting a geometric 
%               transformation between two sets of matched 2-D points, 
%               the coordinates can be concatenated into an M-by-4 matrix.
%   
%   fitFcn      A handle to a function, which fits the model to a minimal
%               subset of data. The function must be of the form 
%                 model = fitFcn(data)
%               model returned by fitFcn can be a cell array, if it is
%               possible to fit multiple models to the data. 
%  
%   distFcn     A handle to a function, which computes the distances from the
%               model to the data. The function must be of the form
%                 distances = distFcn(model, data)
%               If model is an N-element cell array, then distances must be an 
%               M-by-N matrix. Otherwise, distances must be an M-by-1 vector.
%  
%   sampleSize  Positive numeric scalar containing the minimum size of a 
%               sample from data required by fitFcn to fit a model.
%
%   maxDistance Positive numeric scalar specifying the distance threshold 
%               for finding outliers. Increasing this value will make the 
%               algorithm converge faster, but may adversely affect the 
%               accuracy of the result.
%  
%   Outputs     Description
%   -------     -----------
%   model       The model, which best fits the data.
% 
%   inlierIdx   An M-by-1 logical array, specifying which data points are 
%               inliers.
```

Matlab 官网介绍：[Fit model to noisy data - MATLAB ransac - MathWorks 中国](https://ww2.mathworks.cn/help/vision/ref/ransac.html)

这里已经讲的比较清楚了，函数接收五个参数，两个返回值

### 参数

- *data*：一行为一个数据的数据集
- *fitFcn*：计算模型的**函数句柄**，比如匿名函数或者一般函数前面加 @ 符号
- *distFcn*：同样是函数句柄，它返回每一个数据对模型的误差，接收参数为数据以及 *fitFcn* 计算出的模型
- *sampleSize*：随机选取数据点的个数，通过这几个数据作为 *fitFcn* 的输入参数
- *maxDistance*：认为是模型到数据的可接受误差，小于这个的都认为是内点，越大函数执行的速度越快

### 返回值

- *model*：得出的模型
- *inlierIdx*：一个和 *data* 相同行数的逻辑矩阵，代表最后符合此模型的内点

## 使用例子

官网给出了个例子，用的是匿名函数和一些库函数计算直线，这里用自己的函数实现官网的例子。

```matlab
clear;clc;
% 自带的数据 
load pointsForLineFitting.mat
data = points;
figure;
plot(data(:,1), data(:,2), 'o');
axis equal
hold on

sampleSize = 2;
maxDistance = 1;
[model, inlierIdx] = ransac(data, @fitFcn, @distFcn, sampleSize, maxDistance);
% 绘制内点
inlier = data(inlierIdx, :);
plot(inlier(:,1), inlier(:,2), 'o');
x = [min(data(:,1)), max(data(:, 1))];
y = model(1)*x + model(2);
plot(x,y,'g--');
hold off

% 这里的模型就是算直线的 k 和 b ，y = kx+b
function model = fitFcn(data)
    x = data(:,1);
    y = data(:,2);
    k = (y(1) - y(2))/(x(1) - x(2));
    b = -k*x(2) + y(2);
    model = [k, b];
end

% 就是算 (y - y')^2
function distances  = distFcn(model, data)
    k = model(1);
    b = model(2);
    distances  = (data(:,2) - (k*data(:,1) + b)).^2;
end
```

效果图如下：

![](/assets/ArticleImg/2021/ransac-matlab.png)

其中圆点表示数据，橘色代表最后的内点，直线为最后得到的模型。

