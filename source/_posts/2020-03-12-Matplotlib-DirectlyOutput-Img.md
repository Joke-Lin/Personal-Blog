---
title: 'Matplotlib 图像直接导出为 ndarray'
date: 2020-03-12
categories:
- Coding
- ELSE
tags:
- Python
- Matplotlib
- PyQT
updated: 2020-03-14
---

## 导出为ndarray格式图片

*matplotlib* 绘制的图线有自己的显示窗口，有时候希望在其他的*UI*设计中使用其绘制的图，比如*PyQt*，官方有一个支持QT的显示窗口类，但配置很麻烦，在这里记录一种简便的导出方式

主要思路为使用*matplotlib*的`print_png`函数将其图片数据导出到二进制流中，然后*numpy*从此二进制流中取出数据即可

<!-- more -->

代码如下：

```python
import matplotlib.pyplot as plt
import numpy as np
import io
import cv2

# matplotlib 绘制区
x = [1, 2, 3, 4]
y = [1.2, 2.5, 4.5, 7.3]

fig = plt.figure("Image", frameon=False)
plt.plot(x,y)
canvas = fig.canvas

# 获取二进制流
buffer = io.BytesIO()
canvas.print_png(buffer)
data = buffer.getvalue()
buffer.write(data)
buffer.seek(0)

# numpy 获取数据
file_bytes = np.asarray(bytearray(buffer.read()), dtype=np.uint8)
# opencv 读取
img = cv2.imdecode(file_bytes, cv2.IMREAD_COLOR)

buffer.close()
```

## 设置matplotlib borders便于鼠标信息处理（坐标）

有时候仅导出为图片还不够，还需要实现用户的交互操作，在原生*matplotlib*中可以绑定事件以实现用户交互，但导出为图片时，就不得行了，但只需要获取坐标和图片宽高之间的关系，就可以简单的坐标转换一下就可以实现了

```python
# 设置显示的图像（不包括坐标轴，仅绘图区）显示在figure的位置
left, bottom, right, top = 0.1, 0.1, 0.9, 0,9
plt.subplots_adjust(left=left, bottom=bottom, right=right, top=top)
```

### 坐标转换

因为知道绘图区在整个图的相对位置，所以可以很好的处理

下面例子为*PyQt5*使用*widget*显示图片，*widget*到曲线坐标系的转换（曲线坐标系x,y均在[0,1]之间）

**即左上角为原点的屏幕坐标系到[0,1]坐标范围的图表坐标系的转换**

```python
w, h = width(), height() # 即为显示图片的容器大小
plot_w, plot_h = (right - left)*w, (top - bottom)*h # 绘图区大小
# 图标坐标到全图的像素坐标转换
coord2bk = lambda coord: [coord[0]*plot_w + w*left, h*(1-bottom) - coord[1]*plot_h]
# 全图坐标到像素坐标的转换
bk2coord = lambda coord: [(coord[0] - w*left) / plot_w ,1 - (coord[1] - h*(1-top)) / plot_h]
```

