---
title: '图像的前向和后向映射'
date: 2021-02-16
mathjax: true
categories:
- Computer Science
- CV&CG&DIP
- CV
tags:
- matltb
---

图像变换中总是伴随着映射，原图中的点到新图中的点之间的映射。从原图映射到新图上，由于坐标的不连续，就有新图上某点的像素确定的问题。也就是像素坐标是整数，但是映射之后的坐标不一定是整数，就需要确定它周围的坐标的像素值。图像像素的映射有前向映射和后向映射。

后向映射比较好理解，新图映射到原图上某点，然后通过它周围的像素确定这个点的像素值，一般可以有最邻近，双线性，双三次插值等。双线性比较简单且好用。

前向映射到新图中的浮点数位置，直接取整会有空洞产生，一种合适的方法就是按双线性的思路，把这个点的像素按权值分配到四个周围整数点。直接这样累加会导致像素重叠，所以还需要最后对分配到某个像素的所有像素值及其对应权值归一化。

<!-- more -->

详细可参考：[图像变换——向前映射和向后映射_薇洛的打火机-CSDN博客](https://blog.csdn.net/glorydream2015/article/details/44873703)

## 代码实现

这里给出了完整的 matlab 代码实现，根据实验结果可以知道，这里的前向映射耗费的时间比后向大，计算过程比较复杂，但在某些场景下，前向仍然会比后向方便，所以在此记录：

```matlab
filename = './test.JPG';
img = double(imread(filename));
% 旋转的角度
theta = 0.67*pi;
% 绕原点旋转矩阵
T1 = [cos(theta) sin(theta) 0;
      -sin(theta) cos(theta) 0;
      0 0 1];
imgsize = size(img);
w = imgsize(2);
h = imgsize(1);
% matlab 坐标从 1 开始有点烦
bound = [1 1+w 1+w 1;
         1 1 1+h 1+h;
         1 1 1 1];
%  获取变换后图片的大小和左上角顶点在原坐标系的坐标
boundWarp = T1 * bound;
% 正无穷方向取整,避免越界,这里加1稳妥一点
newW = ceil(max(boundWarp(1,:)) - min(boundWarp(1,:))) + 1;
newH = ceil(max(boundWarp(2,:)) - min(boundWarp(2,:))) + 1;
vex = [min(boundWarp(1,:)) min(boundWarp(2,:))];
% 平移到新图坐标系的矩阵为
T2 = [1 0 -vex(1)+1;0 1 -vex(2)+1;0 0 1];
% 最后的变换为
T = T2*T1;
% 变换后的图片矩阵
imgsize2 = imgsize;
imgsize2([1 2]) = [newH newW];
% 上面这样利于单通道/多通道的情况
img2 = zeros(imgsize2);
img3 = img2;

% 前向映射
tic
% tempTrans(i,j) 为矩阵,存储分配到这个像素的原图像素值以及其权值
% 用于记录每一个点所分配的权重
tempTrans = cell(imgsize2([1 2]));
for i = 1:imgsize(1)
    for j = 1:imgsize(2)
        coord = T*[j;i;1];
        x = coord(1); y = coord(2);
        s = fix(coord(1)); t = fix(coord(2));
        k = [s+1-x x-s t+1-y y-t];
        p = img(i,j,:); p = p(:);
        % 双线性插值的方法分配
        tempTrans{t, s}(:, end+1) = [p;k(1)*k(3)];
        tempTrans{t, s+1}(:, end+1) = [p;k(2)*k(3)];
        tempTrans{t+1, s}(:, end+1) = [p;k(1)*k(4)];
        tempTrans{t+1, s+1}(:, end+1) = [p;k(2)*k(4)];
    end
end
% 归一化,否则会造成某些地方很突兀(叠加)
for i = 1:imgsize2(1)
    for j = 1:imgsize2(2)
        temp = tempTrans{i,j};
        len = size(temp,2);
        if len == 0
            continue;
        end
        % 归一化数据,也就是重新分配权值
        sum_w = sum(temp(end,:));
        for k = 1:len
            img2(i,j,:) = img2(i,j,:) + reshape(temp(end, k)/sum_w * temp(1:end-1,k), 1, 1, 3);
        end
    end
end
fprintf("前向映射耗费的总时间:");
toc

figure;
subplot(1,2,1);
imshow(uint8(img2));
title("Forward Mapping");

% 后向映射
tic
for i = 1:imgsize2(1)
    for j = 1:imgsize2(2)
        % 逆映射回去,得到坐标
        coord = T\[j;i;1];
        img3(i, j, :) = backward(img, coord);
    end
end
fprintf("后向映射耗费的总时间:");
toc

subplot(1,2,2);
imshow(uint8(img3));
title("Backward Mapping");

% 后向映射到原图,获取对应的值
function pixel_value = backward(srcimg, srccoord)
    x = srccoord(1);
    y = srccoord(2);
    % 不再原图上的点返回黑色
    pixel_value = 0;
    [r, c, ~] = size(srcimg);
    % 坐标在图像外的返回 0 
    if x > c || y > r || x < 1 || y < 1
        return;
    end
    % 现在坐标已经在图像内部、或者边界，然后处理边界（图像右和下）避免插值时数组边界访问异常
    if x == c || y == r
        pixel_value = srcimg(x, y, :);
        return;
    end
    % 此时依据四个点做双线性插值 首先对坐标向零取整
    s = fix(srccoord(1));
    t = fix(srccoord(2));
    k = [s+1-x x-s t+1-y y-t];
    pixel_value = k(1)*k(3)*srcimg(t,s,:) + k(2)*k(3)*srcimg(t, s+1,:) +  ...
                  k(1)*k(4)*srcimg(t+1,s,:) + k(2)*k(4)*srcimg(t+1,s+1,:);
end
```

原图为：

![](/assets/ArticleImg/2021/forward-backward-maping.jpg)

实现结果如下：

![](/assets/ArticleImg/2021/forward-backward-maping2.jpg)

代码输出：

前向映射耗费的总时间:历时 12.063921 秒。
后向映射耗费的总时间:历时 4.419853 秒。

## 【补】存在的问题

这样子看起来效果比较好，但是当加入缩放因子时，前向映射仍然会产生空隙，因为这里双线性只将映射到浮点位置的像素分配到四个周围像素。但是，当缩放加入后，会有的像素映射不到，解决办法就是增加分配的核大小，由原来的 2*2 改为更大，但不能有一个通用的方法。所以，一般还是使用后向映射。