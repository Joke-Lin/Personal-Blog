---
title: '【译】图像特征检测，描述和匹配'
date: 2020-12-03
mathjax: true
categories:
- Computer Science
- CV&CG&DIP
- CV
tags:
- 特征检测

---

> Hassaballah, M. , A. A. Ali , and H. A. Alshazly . "Image Features Detection, Description and Matching." (2016).a
>
> 原文地址：[Image Features Detection, Description and Matching](https://link.springer.com/chapter/10.1007%2F978-3-319-28854-3_2)



**摘要** 特征检测，描述和匹配是计算机视觉应用中重要的组成部分，因此它们在前几十年中受到了极大的注意。几种特征检测器和描述符已经在文献在中被提出，且对图像中那些具有潜在吸引力（即，具有一种独特的属性）的点做了许多定义。这篇文章为检测和描述图像特征介绍了基本符号和数学概念。然后文章还讨论了完美特征所具有的性质并且概述了现存的多种检测和描述方法。更深地，文章还解释了一些特征匹配方法。最后，文章讨论了最常用的检测和描述算法性能评估技术。

**关键词** 兴趣点 特征检测器 特征描述符 特征提取 特征匹配

<!-- more -->

## 1 介绍

在前几十年中，图像特征检测器和描述符已经称为计算机视觉社区中最流行的工具并且被广泛应用在大量的应用中。图像表示 【1】，图像分类和图像检索 【2-5】，目标识别和匹配 【6-10】，三维场景重建 【11】，动作追踪 【12-14】，纹理分类 【15-16】，机器人定位 【17-19】，以及生物识别系统 【20-22】，所有都依赖于图像中稳定且有代表性的特征。因此，对于这些应用，检测和提取图像特征是十分重要的步骤。

为了建立图像之间的对应关系，其中两个或多个图像的对应关系是需要的，确定每一幅图像中显著的点集是重要的 【8，23】。在分类任务中，目标图像的特征描述符和训练完成的图像特征相匹配，并且已训练图像中具有最大一致对应关系的就是最佳匹配。在这种情况下，特征描述符匹配可以建立在距离测量上，例如欧几里得距离或者马氏距离。对于图像配准，同一场景不同设备不同时间获取的两幅或者多幅图像的空间对齐是十分重要的。图像配准或对齐任务中主要的步骤为：特征检测，特征匹配，基于图像中一致特征的变换函数的推导，以及基于导出的变换函数的图像重建【24】。任何匹配、识别系统的首要步骤是检测以及描述图像中的兴趣位置。一旦计算出描述符，比较这些描述符可以得到图像间的关系以完成匹配、识别任务。另外，对于线上街道级别虚拟导航应用，我们需要特征检测和特征描述符从一些平面图像（全景图像）中取提取特征【25】。

基本思想是首先检测对一类转换协变的兴趣区域。然后，对于每个检测到的区域，在检测到的关键点周围构建图像数据的不变特征向量表示（即描述符）。<u>从图像中提取特征描述符可以基于二阶统计数据、参数模型、从图像变换中获取的参数，甚至是这些方法的组合。</u>两种图像特征可以从图像内容表示中提取出来：<u>全局特征和局部特征</u>。全局特征（例如颜色和纹理）目的是将图像作为整体考虑，可以解释为从图像所有像素中获取的一种特别的属性。然而，局部特征是用于发现和描述图像中的关键点或兴趣区域。在这方面，如果局部特征算法在图像中检测到 n 个关键点，就有 n 个向量描述每一个关键点的形状，颜色，方向，纹理等。全局颜色和纹理特征被证实在数据库中寻找相似图像十分有用，而面向局部结构的特征适合目标分类或者寻找同一对象或场景的其它事件【26】。同时，全局特征不能区分图像的前景和背景，以及两个部分的混合信息【27】。

另一方面，因为实时应用需要处理更多数据或者需要在计算能力有限的移动设备中运行，对可以快速计算，快速匹配，有效利用内存并且还要保持好的准确度的局部特征描述符的需求在不断增长。另外，对移动平台上的图像匹配来说，局部特征描述符被证实是一个很好的选择，在这里可以处理遮挡和丢失的对象【18】。<u>对于相机标定，图像分类，图像检索和目标追踪、重建这一类具体应用，特征检测器和描述符对亮度、视角改变和图像扭曲（例如噪声，模糊，光照）的鲁棒性是十分重要的</u>【28】。 而其他特定的视觉识别任务，如人脸检测或识别，则需要使用特定的检测器和描述符【29】。

在文献中，大量的特征提取方法被提出来计算可靠的描述符。有些描述符是专门为特定的应用场景设计例如形状匹配【29，30】。在这些描述符中，<u>尺度不变特征变换（SIFT）</u>描述符【31】利用了高斯函数中一系列差分（DoG）中的局部极值来提取鲁棒性特征，并且<u>加速稳健特征（SURF）</u>描述符【32】受到 SIFT 的部分启发，用于快速计算独特不变的局部特征，SURF 十分流行且广泛应用于多种应用。这些描述符使用预设的过滤器和非线性操作来表示突出的图像区域。在文章的剩余部分， 我们概述了这些方法和算法以及开发人员提出的改进。更深地，我们也将介绍检测和描述图像特征的基础符号和数学概念。我们还在细节上探索检测器和描述符之间的量化关系以及如何评估它们的性能。

## 2 定义和原理

### 2.1 全局和局部特征

在图像处理和计算机视觉任务中，我们需要表示图像通过其特征。原始图像对人眼是抑郁提取特征的，但是对于计算机算法并非如此。总的来说，有两种表示图像的方法，全局特征和局部特征。在全局特征表达中，图像通过一个多维特征向量表示，用来描述整幅图像中的信息。换句话说，全局表示方法会生成一个单一向量，这个向量包含图像的多个方面的可测量信息，例如颜色，纹理或者形状。特别地，每幅图像中提取的单一向量可以用来作比较。例如，当一个人想要区分两幅图像，分别是关于海（蓝色）和森林（绿色）的，一个关于颜色的全局描述符可以为每个类生成完全不同的向量。在这样的背景下，全局特征可以被解释为图像中涉及所有像素的一个特别的属性。这种属性可以是颜色直方图，纹理，边缘，甚至是将图像滤波后的描述符【33】。在另一方面，局部特征表示的主要目标是根据一些显著区域区别表示图像，同时保持视角和光照不变性。因此，<u>图像是基于它的局部结构，通过从一组被称为兴趣区域（即关键点）的图像区域中提取的局部特征描述符集合来描述的</u>。 ***Fig. 1*** 描述了这些兴趣区域。大部分局部特征表达了图像块中的纹理。

<img src="\assets\Note\Image Features Detection Description and Matching.assets\image-20201111213719530.png" alt="image-20201111213719530" style="zoom:60%;" />

总的来说，使用哪一种特征一般上很大程度上会依赖于手头上的应用，开发者偏向于最具有区分度的。例如，一个有着大鼻子和小眼睛的人和另一个小鼻子和大眼睛的人在释放图或者灰度分布上会有相似的面部照片。然后，从局部特征簇提取的局部特征或者全局模式看起来会根据有区分度。然而，<u>对于在 WEB 尺度级图像索引应用中的大型数据库，考虑全局特征识是合适的</u>。再者，<u>全局特征对那些可以接受粗略地兴趣目标分割应用是有用的。全局特征的优势在于它们更快，更紧凑同时易于计算且内存友好</u>。不过，全局表示也受到一些众所周知的限制，尤其是它们<u>对一些重要变换不能保持不变，并且对杂物和遮挡敏感</u>。在一些应用中，例如副本检测，许多非法拷贝和原件是十分相似的；它们仅收到压缩，缩放或者有限的裁剪【34】。同时，使用为大尺度图像搜索局部特征可以提供更好的效果【35】。另外因为局部结构比其它平滑区域结构更易区分且稳定，所以局部特征在图像匹配和目标识别中更有用。然而，它们经常需要大量的内存，因为图像可能包含成百上千个局部图像描述符。研究者建议将局部图像描述符聚集为一个紧凑向量并且降维优化【35】。

### 2.2 特征检测器的特征

*Tuytelaars* 和 *Mikolajczyk*【27】将局部特征定义为“是一个与其邻居区分开的图像模式”。因此，它们认为局部不变特征的目标是提供一种可以有效匹配图像间局部结构的表示。也就是说，我们希望获取一组稀疏的局部度量，这些度量可以捕获输入图像的本质并对它们的兴趣区域进行编码。为了这个目标，特征检测器和提取器必须有某些性质，同时需要记住这些性质的重要性依赖于实际的应用设置以及做出的妥协。下面列举的性质对在计算机视觉应用中运用特征是十分重要的：

- **鲁棒性**，特征检测算法需要检测出同一个特征位置，不依赖缩放，选择，平移，光度变形，人为压缩和噪声。
- **可重用性**，特征检测算法可以在不同观测条件下获取的同一场景图像反复检测出相同的特征。
- **准确性**，特征检测算法应该准确地定位图像特征（相同像素位置），尤其是在需要精确对应关系来估计对极几何[^1]（the epipolar geometry）的图像匹配任务中。
- **通用性**，特征检测算法需要可以在不同应用中检测特征。
- **高效性**，特征检测算法应该能够在新图像中快速检测特征以支持实时应用。
- **保量性**，图像检测算法需要检测所有的或者大量的图像特征。其中，检测出的特征密度应该反映图像的信息内容以提供紧凑的图像表示。

### 2.3 尺度和仿射不变性

事实上，基于矩形或者圆形这类固定形状的区域匹配，在存在一些几何和光度畸变下，寻找对应关系是不可靠的，因为它们会影响区域形状。数字图形中的目标在不同的观察尺度下会有不同的外表。因此，在分析图像内容时，尺度改变会产生重要的影响。已经提出了不同的技术来解决在这种条件下检测和提取不变图像特征。一些是处理尺度变化，还有一些则更深层次地处理仿射变换。为了解决尺度变化，这些技术假设尺度的变化在所有方向上都是相同的(即均匀的)，并使用尺度空间的连续核函数在所有可能的尺度上搜索稳定的特征。其中，<u>图像的大小是不同的，并且滤波器（例如，高斯滤波器）重复地用来平滑图像，也可以保持原始图像大小，改变滤波器的大小</u>，如 ***Fig. 2*** 所示。更多关于在尺度改变下的特征检测细节可以在【36】中找到，在这之中提出了一个用于生成图像兴趣尺度级的假设，通过在高斯拉普拉斯（LoG）归一化后的尺度中寻找尺度空间极值。

<img src="\assets\Note\Image Features Detection Description and Matching.assets\image-20201114203537215.png" alt="image-20201114203537215" style="zoom:60%;" />

在另一个方面，<u>在仿射变换中每一个方向的缩放可以是不同的</u>[^2]。不均匀的缩放对定位，尺度，局部结构形状都有影响。因此，尺度不变检测器在这种显著仿射变换中会失效。因此，在这种均匀缩放下的特征检测器需要拓展成仿射不变检测器，以检测局部图像结构的仿射形状。因此，这些放射不变检测器可以看作是尺度不变检测器的一般推广。

总的来说，仿射变换是一系列变换的组合：平移，缩放，翻转，旋转，和剪切[^3]。一个仿射变换（affinity）是任一个保持共线性和距离比的线性映射。在这个角度上看，仿射意味着是射影变换的一个特殊情况，他不会把任何对象从仿射空间 $\mathbb{R}^3$ 移至无穷远平面，反之亦然。简单来说，$\mathbb{R}^n$ 仿射变换是一个映射 $f:\mathbb{R}^n \rightarrow \mathbb{R}^n$，具体形式如下：
$$
f(Y) = \mathbb{A} Y + \mathbb{B}
$$
对所有的 $Y\in \mathbb{R}^n$ ，其中 $\mathbb{A}$ 为 $\mathbb{R}^n$ 上的线性变换。在一些特殊的情况，如果 $det(\mathbb{A}) \gt 0$，这样的变换被称为保向，然而如果 $det(\mathbb{A}) \lt 0$，则被称为方向反转。众所周知的是，在特定变换族下的函数是不变的，当它的值不随其参数上的变换族改变而改变。二阶矩矩阵为估计局部图像特征的仿射形状提供了理论依据。<u>仿射不变检测器有以下几种：Harris-affifine，Hessian-affifine，以及 maximally stable extremal regions (MSER)</u>。还需要记住的是这些检测器检测的特征会从圆形变换为椭圆形。

## 3 图像特征检测器

<u>特征检测器可以大概地分为三类：单尺度检测器，多尺度检测器以及仿射不变检测器。</u>概括地说，单尺度意味着使用检测器内部参数对特征或目标轮廓仅有一个表示。单尺度检测器对诸如旋转，平移，光照改变和附加噪声的变换是不变的。然而，它们不适用于处理缩放问题。给定同一场景的但尺度不同的两幅图像，我们想要确定是否可以检测到相同的兴趣点。因此，在尺度改变下，建立能够可靠地提取显著特征的多尺度检测器是有必要的。接下来我们会讨论单尺度和多尺度检测器以及仿射不变检测器的细节。

### 3.1 单尺度检测器

#### 3.1.1 Moravec’s 检测器

Moravec’s 检测器【37】对寻找图像中完全不同的区域感兴趣，这可以用于记录连续图像帧。它被用于角点检测算法，其中角点是那些低自相似性的点。 这个检测器测试给定图像的每一个像素来确定是否是一个角点。它考虑以某个像素为中心的局部图像块，然后确定它与相邻图像块的相似度。这个相似度是使用中心图像块核其它图像块的平方差之和（SSD）来确定的。基于 SSD 的值，我们需要考虑以下几种情况：

- 如果像素在均匀强度的区域内，那么相邻的块会看起来相似或者发生很小的变化。
- 如果像素在边上，那么在平行方向的相邻块只有很小的改变，与边垂直的方向会有很大的变化。
- 如果像素在一个任意方向都会发生很大变化的位置，那么没有相邻块会与其相似，当任意平移都会发生很大改变时，也就检测到了一个角点。

块与其邻居（水平，垂直和两个对角线）之间最小的 SSD 被用于角的一个度量。一个角或者兴趣点当 SSD 达到一个局部最大值时被检测到。下面的步骤可以用于实现 Moravec’s 检测器：

1. 输入：灰度图像，窗口大小，阈值 $T$

2. 对图像中的每一个像素 $(x,y)$，以及平移量 $(u,v)$，计算强度变化 $V$，如下
    $$
    V_{u,v}(x,y) = \sum_{\forall a,b \in window } [I(x+u+a,y+v+b) - I(x+a,y+b)]^2
    $$

3. 通过计算角测度 $C(x,y)$ 为每一个像素 $(x,y)$ 构建对应的角图
    $$
    C(x,y) = min(V_{u,v}(x,y))
    $$

4. 对所有角图中所有小于 T 的值归 0

5. 使用非最大值抑制寻找局部最大值。剩下所有的非零值为角点。

<img src="\assets\Note\Image Features Detection Description and Matching.assets\image-20201115201625703.png" alt="image-20201115201625703" style="zoom:60%;" />

为了执行非最大值抑制，需要按梯度方向扫描一幅图像，这个方向是与边方向是垂直的。所有不是局部最大值的像素都会被抑制为 0。正如 ***Fig.3*** 所描述的，p 和 r 是在 q 梯度方向上的两个相邻点。如果像素 q 的值比 p，q 任一个小，则抑制。Moravec’s 检测器的一个优势为可以检测出大量的角点。然而，<u>它不是各向同性的。强度改变值仅仅在一些离散的位移上（例如，8 个理论方向）并且不再这 8 个相邻方向上的边会被分配到一个相对大的角测度。</u>因此，它不是旋转不变的[^4]，这也导致了可重用率低。

#### 3.1.2 Harris 检测器

Harris 和 Stephens【38】发展了一种结合角和边检测器的方法以解决 Moravec’s 检测器中的限制。通过获取所有方向上的自相关改变量（例如，强度改变），这使得检测器在检测和重用率上变得可取。这个基于自相关矩阵的检测器是应用最广泛的技术。这个用于检测图像特征以及描述局部结构的 2*2 对称自相关矩阵如下表示[^5]：
$$
M(x,y) = \sum_{x,y} w(u,v) \times 
\left[
\begin{matrix}
I_x^2(u,v) & I_xI_y(u,v) \\
I_xI_y(u,v) & I_y^2(u,v)
\end{matrix}
\right]
$$

其中 $I_x,I_y$ 分别是在 x 和 y 方向上的局部图像微分，$w(u,v)$ 表示以 $(x,y)$ 为中心的区域 $(u,v)$ 中的加权窗口。如果一个圆形窗口，例如高斯函数，那么这个响应将是各向同性的，并且中心部分会有更高的权重。为了寻找兴趣点，将为每一个像素计算矩阵 M 的特征值。如果两个特征值都很大就代表这里存在着角点。特征值和检测点的分类关系描述在 ***Fig.4*** 中。

<img src="\assets\Note\Image Features Detection Description and Matching.assets\image-20201115224916303.png" alt="image-20201115224916303" style="zoom:67%;" />

通过为每一个像素 $(x,y)$ 计算角测度 $C(x,y)$ 构建响应图，$C(x,y)$ 计算公式如下：
$$
C(x,y)=det(M) - K(trace(M))^2
$$
其中
$$
\begin{align}
det(M) &= \lambda_1 \times \lambda_2 \\
trace(M) &= \lambda_1 + \lambda_2
\end{align}
$$
K 是一个调节参数，$\lambda_1,\lambda_2$ 是自相关矩阵的特征值。特征值计算的开销是昂贵的，因为涉及到平方根的计算。因此，Harris 建议使用这个角测度，它将两个特征值合并在一个计算中。非最大值抑制用于寻找局部最大值，最后角测度图中保留的非零点就是寻找到的角点。[^6]

#### 3.1.3 SUSAN 检测器

Simith 和 Brady 【39】介绍了一中不使用图像微分的角点计算方法，是一种通用的低层次的图像处理技术，称为 SUSAN（Smallest Univalue Segment Assimilating Nucleus）[^8]。它不仅是一个角检测器还可以用于边缘检测核图像降噪。<u>通过在每一个像素上放置一个固定半径的圆形掩膜来检测角点</u>。中心像素就代表着核，掩膜下的其它像素会与核相比较来检查是否与核有相似的强度值。<u>与核亮度十分相似的像素归为一组，被称为 USAN （Univalve Segment Assimilating Nucleus）。当一个位置的 USAN 中的像素个数达到一个局部最小值并且低于特别的阈值 T，这个位置就是角点</u>。为了检测角点，同一掩膜下的两个像素的相似比较函数 $C(r,r_0)$ 如下表示：
$$
C(r,r_0) = 
\left\{
\begin{align}
1&, &\text{if　}\vert I(r)-I(r_0) \vert \le T \\
0&, & otherwise
\end{align}
\right.
$$
并且 USAN 的区域大小为：
$$
n(r_0) = \sum_{r \in c(r_0)} C(r,r_0)
$$
其中 $r_0$ 和 $r$  分别是掩膜下核与其它点的坐标。SUSAN 角点检测器的性能主要依赖于相似比较函数 $C(r,r_0)$，它不能免疫某些影响图像的因素（例如，强亮度波动和噪声）。

SUSAN 检测器有如下几个优势：

1. 不需要使用微分，因此不需要额外的降噪或者任何昂贵的计算
2. 高可重用率
3. 平移以及旋转不变

不幸的是，<u>它对尺度和其它变换不是不变的，并且固定的阈值不使用于一般情况</u>。这个检测器需要一个可适应的阈值，并且掩膜的形状需要改变。

#### 3.1.4 FAST 检测器[^7]

FAST（Features from Accelerated Segment Test）最初是由 Rosten 和 Drummondn 【40，41】提出的角点检测器。在这个检测方案中，通过对每个像素使用分段检测来确定候选点，该方法将围绕候选点的 16 个像素作为计算基础。如果在以 r 为半径的 Bresenham 圆上有一组包含 n 个连续像素，且这些像素全部比候选像素（表示为 $I_p$ ）与阈值 t 相加还亮，或者比与阈值相减还暗，那么 p 就被归为角点。有一种高速的检测方法来排除大量的非角点，这种快速检测方法仅检查 1，5，9，13 四个像素。一个角点必须满足至少有三个测试像素比 $I_p+t$ 亮或者比 $I_p - t$ 暗，快速检测方法剩下的点则使用原来的检测方法。***Fig.5*** 描述了这个过程，其中放大的方块为角点检测使用的像素。像素 p 为候选角的中心。使用虚线描绘的弧中有 12 个连续像素比 p 亮了一个阈值。使用 $r=3,n=9$ 的圆可以带来最好的结果。

<img src="\assets\Note\Image Features Detection Description and Matching.assets\image-20201116000143758.png" alt="image-20201116000143758" style="zoom:65%;" />

尽管高速测试可以有很好的性能，但还是受到了在【41】中提及的几个限制和不足。使用机器学习方法可以改善这些限制和不足。决策树算法（ID3）是一种著名的用于学习像素分类的方法，可以对上述算法有很大的加速。因为第一阶段测试会在兴趣点周围产生很多响应，所以需要额外执行非最大值抑制。这使得特征定位更加准确。这个步骤的角测度公式如下：
$$
C(x,y) = max(
\sum_{j\in S_{bright}}\vert I_{p\rightarrow j} - I_p \vert - t,
\sum_{j\in S_{dark}}\vert I_p - I_{p\rightarrow j} \vert - t
)
$$
其中 $I_{p\rightarrow j}$ 表示位于 Bresenham 圆上的像素。用这个方法，处理时间依然是短，因为第二个阶段测试仅仅用于通过第一阶段测试的那一小部分像素。

换句话说，整个处理分为两个阶段。<u>首先，对给定 n 以及合适的阈值的分段测试的角点检测在一组图像（最好是取自目标应用域）上执行。圆上的 16 个像素被分为比中心暗，相似或者亮。然后，在这 16 个位置使用 ID3 算法来得到最大信息增益</u>[^9]。 非最大值抑制应用于连续弧中像素与中心像素之间的绝对差值之和。注意到使用 ID3 算法执行的角点检测与使用分段检测器会有轻微的不同，因为决策树模型依赖于训练数据，这些模型不能覆盖所有的角点。<u>与现存的许多检测器相比，FAST 角点检测器的高速表现使其十分适合实时视频处理应用</u>。然而，<u>它不是尺度不变的，并且对噪声敏感</u>，它还依赖于阈值，选择一个合适的阈值是一个相当重要的工作。

#### 3.1.5 Hessian 检测器[^10]

Hessian 斑点检测器【42，43】是以一个关于图像强度 $I(x,y)$ 的 $2\times 2$ 的二阶微分矩阵，这个矩阵称为 Hesian 矩阵。这个矩阵可以用于局部图像结构，并且其形式如下：
$$
H(x,y,\sigma) = 
\left[
\begin{matrix}
I_{xx}(x,y,\sigma) & I_{xy}(x,y,\sigma) \\
I_{xy}(x,y,\sigma) & I_{yy}(x,y,\sigma)
\end{matrix}
\right]
$$

其中 $I_{xx},I_{xy},I_{yy}$ 是二阶图像微分使用标准差为 $\sigma$ 的高斯函数计算，它寻找在两个正交方向上高响应的点。也就是说，检测器寻找那些满足 Hessian 矩阵行列式达到局部最大值的点：
$$
\text{det}(H) = I_{xx}I_{yy}-I_{xy}^2
$$
通过选择具有最大行列式的带你，这个方法长结构不友好，因为长结构在一个方向上只有很小的二阶微分（即，信号改变）。使用 $3\times 3$ 的窗口对图像执行非最大值抑制，保持那些比周围 8 个像素都大的像素不变，否则置为 0。然后，检测器返回那些保留下来的像素中大于预设阈值 T 的像素。最后保留下来的主要为角点和纹理改变强烈的区域。虽然 Hessian 矩阵用于描述点周围 8 个相邻点的局部结构，但它的行列式可以用于检测在两个方向上表现出信号变化的图像结构。与其它检测器（例如，拉普拉斯）相比，Hessian 矩阵的行列式仅在两个正交方向显著改变的局部图像模式上响应【44】。然而，在检测器中使用二阶微分使得对噪声敏感。另外，局部最大值经常存在于轮廓或者直边上，在这些位置信号仅在一个方向上发生改变【45】。因此，这些局部最大值是不稳定的，因为噪声和相邻模式的轻微改变都会产生影响。

### 3.2 多尺度检测器

#### 3.2.1 高斯拉普拉斯（LoG）

高斯拉普拉斯（Laplacian-of-Gaussian，LoG）是一个二阶微分的线性组合且是一个常用的斑点检测器。给定一副图像 $I(x.y)$ 作为输入，图像的尺度空间表示为 $L(x,y,\sigma)$，通过图像和一系列不同高斯核卷积 $G(x,y,\sigma)$ 生成，其中
$$
L(x,y,\sigma) = G(x,y,\sigma)\bigotimes I(x,y) \\
G(x,y,\sigma) = \frac{1}{2\pi \sigma^2} e^{\frac{-(x^2+y^2)}{2\sigma^2}}
$$
通过下式计算拉普拉斯操作
$$
\nabla^2L(x,y,\sigma) = L_{xx}(x,y,\sigma) + L_{yy}(x,y,\sigma)
$$


拉普拉斯对暗斑点呈现正响应，对亮斑点则是负响应，斑点大小为 $\sqrt{2\sigma}$ 时响应最强。然而，响应十分依赖于斑点结构的大小以及高斯平滑核的大小。高斯标准差用于控制模糊尺度。为了在图像域自动捕获不同大小的斑点，一种带有自动尺度选择的多尺度方法在【36】中提出，这种方法通过寻找如下式所示的<u>尺度归一化拉普拉斯</u>的尺度空间极值。
$$
\nabla^2_{norm} L(x,y,\sigma) = \sigma^2(L_{xx}(x,y,\sigma) + L_{yy}(x,y,\sigma))
$$
它还可以检测那些在空间和尺度上同时是归一化拉普拉斯极值的点。LoG 操作符是循环对称的，因此它很自然地具有旋转不变性。LoG 很适合斑点检测，因为它的循环对称性质，但它也为例如角，边，脊，多节（multi-junctions）这类局部结构提供了很好的<u>特征尺度</u>[^11]估计。在这种情况下，LoG 可用于为给定的图像位置寻找特征尺度或直接检测尺度不变区域，通过寻找 LoG 函数的 3D（位置和尺度[^12]） 极值，如 ***Fig.6*** 所述。拉普拉斯的尺度选择性质可以在【46】中获取到细节。

<img src="\assets\Note\Image Features Detection Description and Matching.assets\image-20201124171703461.png" alt="image-20201124171703461" style="zoom:80%;" />

#### 3.2.2 高斯差分（DoG）

实际上，LoG 操作是十分耗时的。为了加速计算，Lowe【31】提出了一种基于局部三维极值的高效算法，极值是在使用高斯差分（Difference-of-Gaussian，DoG）构建的尺度空间金字塔中寻找的。这种方法应用于*尺度不变特征变换（SIFT）*算法。在这个背景下，DoG 给出了 LoG 的一种近似，并且用于从尺度空间极值中高效检测稳定特征。DoG 函数 $D(x,y,\sigma)$ 可以不使用卷积操作得出，通过高斯金字塔中由因子 k 分割的相邻尺度相减得到。
$$
\begin{align}
D(x,y,\sigma) =& (G(x,y,k\sigma)-G(x,y,\sigma)) \bigotimes I(x,y) \\
=& L(x,y,k\sigma) - L(x,y,\sigma)
\end{align}
$$
通过 DoG 提取的特征类型与 LoG 有一致的分类。DoG 区域检测器寻找 3D 尺度空间极值的过程如 ***Fig.7*** 所示。LoG 和 DoG 共有的<u>缺点为局部极值可以在相邻的直边轮廓中检测出，这种轮廓仅在一个方向上发生信号改变，使得它们稳定性下降，并对噪声和微小的改变敏感</u>【45】。

![image-20201124174117634](\assets\Note\Image Features Detection Description and Matching.assets\image-20201124174117634.png)

#### 3.2.3 Harris-Laplace[^13]

Harris-Laplace 是由  Mikolajczyk 和 Schmid【45】提出的尺度不变的角点检测器。<u>它依赖于 Harris 角点检测器和高斯尺度空间的组合</u>。尽管 Harris 角点具有旋转不变性和光照不变性，但不是尺度不变的因此，对该检测器中使用的二阶矩矩阵进行了修正，使其与图像分辨率无关。Harris-Laplace 中的尺度适应的二阶矩矩阵表示如下：
$$
M(x,y,\sigma_1,\sigma_D) = \sigma^2_D g(\sigma_1)
\left[
\begin{matrix}
I^2_{x}(x,y,\sigma_D) & I_{x}I_y(x,y,\sigma_D) \\
I_{x}I_y(x,y,\sigma_D) & I^2_{y}(x,y,\sigma_D)
\end{matrix}
\right]
$$
其中 $I_x,I_y$ 为使用尺度为 $\sigma_D$ 的高斯核在分别在它们方向上做的图像微分。参数 $\sigma_1$ 决定了当前尺度，Harris 角点在这个尺度下的高斯尺度空间内检测。换句话说，微分尺度 $\sigma_D$ 决定了用于计算微分的高斯核大小。然而，积分尺度 $\sigma_1$ 用于控制某一相邻区域内的微分加权平均。多尺度 Harris 角点度量是使用上式矩阵的行列式和迹计算的，如下式：
$$
C(x,y,\sigma_1,\sigma_D) = \text{det} [M(x,y,\sigma_1,\sigma_D)]-\alpha \cdot \text{trace}^2[M(x,y,\sigma_1,\sigma_D)]
$$
常量 $\alpha$ 的值在 0.04 和 0.06 之间。在每一层尺度空间表示中，通过检测点 $(x,y)$ 8 邻域的局部最大值来提取兴趣点。然后，使用阈值来过滤那些极值比较小的角点，因为它们在任一观测条件下都不稳定
$$
C(x,y.\sigma_1,\sigma_D) > Threshold_{Harris}
$$
另外，LoG 也可以用于寻找所有尺度上的极大值。其中，只有那些达到拉普拉斯最大值或者其响应大于阈值的点才可以接受[^14]。
$$
\sigma_1^2\vert L_{xx}(x,y,\sigma_1) + L_{yy}(x,y,\sigma_1) \vert > Threshold_{Laplacian}
$$
Harris-Laplace 提供了一组具有代表性的点，这些点在图像以及尺度维度都是具有特征的。与多尺度 Harris 相比，Harris-Laplace 减少了很多不必要的兴趣点。这些兴趣点在尺度，旋转，光照以及附加噪声具有不变性。 此外，兴趣点是高可重用的。然而，Harris-Laplace 检测器与 LoG 和 DoG 检测器相比，返回更少的点。它也不适用于仿射变换。

#### 3.2.4 Hessian-Laplace

和 Harris-Laplace 相似，基于 Hessian 矩阵的检测器也可以用相同的方法构造一个具有尺度不变性的检测器，名为 Hessian-Laplace。首先，我们使用 Laplacian 或者它的近似表示 DoG 来构造尺度空间。然后使用 Hessian 矩阵的行列式提取尺度不变类斑点特征。Hessian-Laplace 检测器获取的大量特征覆盖了整幅图像，和 Harris-Laplace 相比可重用性略低。此外，由于空间和尺度定位中使用的滤波器的相似性，提取的位置更适合于基于二阶高斯微分的尺度估计。Bay et al. 【32】宣称基于 Hessian 的检测器比基于 Harris 的更稳定。类似得，使用 DoG 代替 LoG 来加速计算，Hessian 行列式使用积分图像技术来近似【29】以达到 Fast Hessian 检测器【32】。

#### 3.2.5 Gabor-Wavelet 检测器

最近，Yussof 和 Hitam【47】提出一种基于 Gabor 小波[^15]的多尺度兴趣点检测器。Gabor 小波是受高斯包络函数限制的平面波形状的生物激励卷积核，其核类似于哺乳动物简单皮层细胞的二维感受场谱的响应。Gabor 小波使用经过高斯包络函数调制的复平面波。

<img src="\assets\Note\Image Features Detection Description and Matching.assets\image-20201125112807047.png" alt="image-20201125112807047" style="zoom:39%;" />

其中 $K_{u,v} = K_ve^{i\Phi u},z=(x,y)$，u 和 v 决定了Gabor 小波的方向和尺度，$K_v = K_{max}/f^v$ 并且 $\Phi_u = \pi u/8$，$K_{max}$ 是最大频率，$f = \sqrt{2}$ 为频率域内核间距因子。通过下面的卷积计算图像 $I$ 与 $\psi$ 之间的响应
$$
G = I \bigotimes \psi
$$
这个卷积的系数表示局部图像区域的信息，这应该比独立的像素更有效。Gabor 小波的优势是可以同时在空间和空间频率域上提供最佳分辨率。另外，Gabor 小波可以增强低层次的特征，例如峰，谷，脊。因此，通过组合图像多方向的响应，它可以从不同尺度的图像中提取特征点。兴趣点在多个尺度上提取，并结合均匀间隔的方向。作者证明了 Gabor 小波检测器提取的特征点对不同几何变换都具有高准确度以及高适应性。

### 3.3 仿射不变检测器

到现在为止讨论的特征检测器展示了平移、旋转和均匀尺度的不变性，并认为局部图像结构的位置和尺度不受仿射变换的影响。因此，它们可以处理一部分富含挑战性的反射不变性问题，同时记住尺度在每个方向上是可以不同的。这反过来使得尺度不变检测器不能处理显著的仿射变换。因此，建立一个对透视变换具有鲁棒性的检测器需要仿射变换不变性。一个仿射不变检测器可以视作是尺度不变检测器的一般版本。最近，一些特征检测器拓展为可以处理仿射变换不变特征。举个例子，Schaffalitzky 和 Zisserman 【48】通过仿射归一化拓展 Harris-Laplace 检测器。Mikolajczyk 和 Schmid 【45】介绍了一种尺度以及仿射不变兴趣点检测方法。他们的算法同时适应了点邻域的位置，尺度，形状来获取仿射不变点。其中，Harris 检测器使用适用于仿射变换并基于二阶矩矩阵估计点邻域的形状。这是通过如下由 Lindberg 和 Garding【49】得出的迭代估计方案：

1. 使用尺度不变 Harris-Laplace 检测器明确初始区域点。
2. 对每一个初始点，使用仿射形状自适应将区域归一化以满足仿射不变。
3. 迭代估计仿射区域，选择合适的积分尺度，微分尺度和空间定位兴趣点。
4. 使用这些尺度以及空间定位来更新仿射区域。
5. 重复第三步直到满足停止条件。

类似于 Harris-affine，同样的想法可以用于基于 Hessian 的检测器，以实现名为 Hessian-affine 的仿射不变检测器。对于单幅图像。Hessian-affine 通常比 Harris-affine 得到更多可靠的区域。性能的改变依赖于分析的场景类型。再者，Hessian-affine 对纹理场景有很好的响应，纹理场景中有很多类似角的部分。然而，对一些类似建筑的结构化场景，Hessian-affine 处理得很好。Mikolajczyk 和 Schmid 对几种最先进的仿射检测器进行了深入的分析【50】。

由于篇幅限制，还有几种没有被讨论过的特征检测器，例如，Fast Hessian 或 the Determinant of Hessian (DoH) 【32】 ，MSER【51，52】。关于这些检测器更详细的讨论见【44，45，53】。

## 4 图像特征描述

一旦在位置 $p(x,y)$，尺度 $s$，以及角度 $\theta$ 检测到一组兴趣点，它们在 $p$ 邻域的内容或者图像结构就需要编码为合适的描述符以达到有识别度的匹配并对局部图像变形不敏感。这种描述符需要和 $\theta$ 对齐并和尺度 $s$ 成比例。文献中有大量的图像特征描述符，最常用的是在接下来的章节中要讨论的几种。

### 4.1 尺度不变特征变换（SIFT）

lowe【31】提出了尺度不变特征变换算法（scale-invariant feature transform ，SIFT），其使用 DoG 操作在图像中检测到大量的兴趣点。这些点通过 DoG 函数的局部极值选择。在每一个兴趣点可以提取一个特征向量。在多个尺度上，在兴趣点周围的邻域上，利用局部图像属性来估计图像的局部方向，以提供相对于旋转的不变性。接下来，为每一个检测到的点基于特定特征尺度的局部图像信息计算一个描述符。通过建立兴趣点周围区域的梯度方向直方图，SIFT 描述符寻找最高的方向值并且其它大于最值 80% 的值，并使用这些方向作为这个关键点的主方向。

SIFT 算法的描述阶段从采样关键点周围 $16\times 16$ 区域的梯度值与方向开始，使用它的尺度作为高斯模糊的等级。然后，将原区域划分为 $4\times 4$ 的子区域，并为每个子区域创建一组包含 8 个方向的方向直方图。$\sigma$ 大小为区域一半的高斯加权函数为每一个采样点赋予一个权值，位于区域中心的梯度有较高的权值，这些梯度受位置变化的影响较小。描述符由所有方向直方图组成的向量构成。因为有 $4\times 4$ 个直方图，每一个都包含 8 个 bin，所以每个关键点的特征向量有 $4\times 4\times 8 = 128$ 个元素。最后，特征向量归一化为单位长度以获取光照仿射变化的不变性。然而，相机饱和度或其它类似的影响造成非的线性光照改变会使得某些梯度发生大的改变。这些改变可以通过将特征向量最大值 0.2 以上的值阈值化[^16]处理减少，并再次归一化。***Fig.8*** 为 SIFT 算法的图形表示，其中梯度方向和大小在每一个像素处计算并使用高斯分布加权（如图中的覆盖圆）。然后为每一个子区域计算加权的梯度方向直方图。

<img src="\assets\Note\Image Features Detection Description and Matching.assets\image-20201126082806529.png" alt="image-20201126082806529" style="zoom:70%;" />

标准 SIFT 描述符表示在几个方面是值得注意的：<u>这种表示是精心设计的，以避免由于边界效应和位置的平滑变化而产生的问题，方向和尺度不会引起特征向量的根本变化；它十分紧凑，使用 128 元素的向量表示像素块；但对仿射变换不是显著不变的，这种表示对由透视效果引起的变形具有惊人的弹性</u>。这些特征在不同尺度，选择和光照下与其它竞争算法的匹配性能中得到了证明。在另一个方面，标准 SIFT 特征向量的组成是复杂的并且 SIFT 具体设计背后的选择不明确导致 SIFT  公共的高维问题，这影响了计算描述符所需的时间（十分慢）。作为 SIFT 的拓展，Ke 和 Sukthankar【54】提出了 PCA-SIFT 来减少原始 SIFT 描述符的高维性。**PCA-SIFT** 使用主成分分析（Principal Components Analysis，PCA）技术对关键点周围的梯度图像块归一化。它在给定的尺度下提取 $41\times 41$ 的块并计算竖直和水平两个方向的图像梯度，然后将两个方向的梯度连接以创建特征向量。因此，它的特征向量长度为 $2\times 39 \times 39 = 3042$。梯度图像向量投影到事先计算的特征空间中，最后得到 36 维的特征向量。然后将向量归一化为单位大小以减少光照改变的影响。此外，Morel 和 Yu【55】证明了 SIFT 对除了六参数的仿射变换外的四参数的缩放，选择和平移是完全不变的。因此，它们介绍了 **affine-SIFT（ASIFT）**通过不同的相机轴方向参数，也就是 SIFT 遗留下来的经纬度角，模拟可获得的所有图像视图。

### 4.2 梯度位置方向直方图（GLOH）

梯度位置方向直方图（Gradient location-orientation histogram，GLOH）由 Mikolajczyk 和 Schmid 【50】提出，也是 SIFT 描述符的拓展。GLOH 与 SIFT 描述符非常相似，它只将 SIFT 使用的笛卡尔位置网格替换为对数-极坐标网格，并应用 PCA 来减小描述符的大小。GLOH 使用对数-极坐标位置网格将径向分为三个（半径设置为 6，11，15），角度方向分为 8 个，如 ***Fig.9*** 所示最后有 17 个子块。GLOH 描述符将梯度方向分为 16 个条目并以此建立了一组直方图，所以每个兴趣点的特征向量有 $17\times 16 = 272$ 个元素。通过计算 PCA 协方差矩阵将这个 272 维描述符降为 128 维，并选择最高的 128 维特征用于描述。基于在【50】中的实验评估，<u>GLOH 优于原始 SIFT 描述符，并给出了最佳的性能，尤其是在光照改变的情况下。此外，和 SIFT 描述符相比，GLOH 获取的特征更具有区分度，但也需要更昂贵的计算开销</u>。

<img src="\assets\Note\Image Features Detection Description and Matching.assets\image-20201126112550328.png" alt="image-20201126112550328" style="zoom:67%;" />

### 4.3 加速鲁棒特征描述符（SURF）

加速鲁棒特征（Speeded-Up Robust Features，SURF）检测-描述符由 Bay 等人发展【32】以作为 SIFT 的有效替代。与SIFT 相比它更加快且更具鲁棒性。对于兴趣点检测阶段，并不依赖理想高斯微分，而是基于简单的 2D 核滤波器。其中，它使用基于 Hessian 矩阵行列式的尺度不变斑点检测器来进行尺度选择和定位。<u>它基础的思想为用一个高效的方式来近似高斯二阶微分，通过一组盒滤波器创建的积分图像</u>。***Fig.10*** 描绘了这个 $9\times 9$ 的盒滤波器，它是 $\sigma = 1.2$ 的高斯函数的近似，并且代表了斑点响应映射的最低尺度。这些近似表示为 $D_{xx},D_{yy},D_{xy}$。因此，Hessian 行列式的近似表示为：
$$
\text{det}(H_{approx}) = D_{xx}D_{yy} - (wD_{xy})^2
$$
其中 $w$ 是滤波器响应的相对权重，用于平衡 Hessian 行列式表达式。近似的 Hessian 行列式表示了图像中的斑点响应大小。这些响应存储在斑点响应映射图中，使用二次插值寻找并细化局部最大值，如 DoG。最后，在 $3\times 3\times 3$ 的邻域内做非极大值抑制来获取稳定的兴趣点和尺度值。

<img src="\assets\Note\Image Features Detection Description and Matching.assets\image-20201127130822757.png" alt="image-20201127130822757" style="zoom:67%;" />

SURF 描述符首先以检测到的兴趣点为中心建立一个方形区域，并确定其主方向。这个窗口的大小为 $20s$，其中 s 为其兴趣点对应的尺度。然后，这个兴趣区域进一步分为 $4\times 4$ 个更小的子区域并为每一个子区域在如图 ***Fig.11***  $5\times 5$ 大小的采样点上计算竖直和水平（分别表示为 $d_x,d_y$ ）两个方向上的 Harr 小波响应。这些响应使用以兴趣点为中心的高斯窗口加权以增加对几何畸变和位置错误的鲁棒性。对每一个子区域小波响应 $d_x,d_y$ 求和并添加到特征向量 $v$ ，其中
$$
v = (\sum d_x,\sum \vert d_x\vert,\sum d_y,\sum \vert d_y \vert)
$$
为所有 $4\times 4$ 的子区域计算这个，就得到一个 $4\times 4\times 4 = 64$ 维的特征描述符。最后，对这个特征描述符归一化以减少光照影响。

<img src="\assets\Note\Image Features Detection Description and Matching.assets\image-20201127131158056.png" alt="image-20201127131158056" style="zoom:60%;" />

和 SIFT 相比 SURF 描述符主要的优势在于处理速度，因为它使用 64 维特征向量来描述局部特征，而 SIFT 使用的是 128 维。然而，<u>SIFT 描述符更适于描述受平移，选择，缩放和其它光照变化影响的图像</u>。尽管 SURF 展示了它在很多计算机视觉应用上的潜力，但它也有许多缺陷。<u>在比较 2D 和 3D 目标时，当旋转幅度大或视角差距大时它将失效。此外，如【56】解释的那样，SURF 并不是完全仿射不变的</u>。

### 4.4 Local Binary Pattern (LBP)

LBP 【57，58】表征了纹理的空间结构并展示了灰度单一变换的不变性特点。它通过将相邻像素和中心像素比较以编码排序关系，也就是说通过将每一个像素值和其相邻像素比较以创建一个基于顺序的特征。特别地，将那些比中心像素具有更大特征响应的相邻像素标记为 “1”，否则标记为 “0”。这些同时进行的比较的结果由一个二进制串记录。然后，从公共比率为 2 的几何序列得到的权重，根据串的索引分配给每一个比特。因此，带有权值的二进制串就可以转换为一个十进制值索引（即，LBP 特征响应）。也就是说，这个描述符将邻域上的结果描述了为一个二进制数（二元模式）。其标准模式下，强度为 $g(c)$ 的像素 $c$ 的标记按下式确定
$$
S(g_p - g_c) = 
\left \{
\begin{align}
1,& &\text{if　} g_p \ge g_c \\
0,& &\text{if　} g_p < g_c
\end{align}
\right.
$$
其中像素 $p$ 属于 $3\times 3$ 大小的邻域，灰度为 $g_p(p=0,1,\dots,7)$ 。然后，像素邻域的 LBP 通过对阈值 $S(g_p-g_c)$ 按 $2^k$ 加权相加获得，如下：
$$
LBP = \sum_{k=0}^7 S(g_p-g_c).2^k
$$
在为每一个像素计算其标记之后，就可以生成一个 256 bin 的直方图，作为这个纹理的特征描述符。***Fig.12*** 展示了在 $3\times 3$ 邻域内计算 LBP 以及基础区域的方向描述符的例子。此外，LBP 描述符的一般形式为：

<img src="\assets\Note\Image Features Detection Description and Matching.assets\image-20201128003446585.png" alt="image-20201128003446585" style="zoom:55%;" />

其中 $n_c$ 对应着局部邻域中心像素的灰度级，$n_i$ 是半径为 R  的圆上的 N 等分像素的灰度级。

<img src="\assets\Note\Image Features Detection Description and Matching.assets\image-20201128003803784.png" alt="image-20201128003803784" style="zoom:60%;" />

因为像素之间的关系随距离增加而减少，所以许多的纹理信息可以从局部邻域中获得。因此，半径 R 一般都比较小。在实际应用中，邻域间的差分符号被解释为 N 位二进制数，使得有如 ***Fig.13*** 所示的 $2^N$ 个不同的二元模式。二元模式被称为均匀模式，其中每位最多包含两个从 “0” 到 “1” 的转换。例如，“11000011” 和 “00001110” 是两个二元模式，但 “00100100” 和 “01001110” 就不是二元模式。

<img src="\assets\Note\Image Features Detection Description and Matching.assets\image-20201129111111162.png" alt="image-20201129111111162" style="zoom:60%;" />

已经提出了几种不同的 LBP，包括中心对称 LBP（the center-symmetric local binary patterns，CS-LBP），局部三元模式（the local ternary pattern，LTP），基于 CS-LBP 的中心对称 LTP（CS-LTP），以及正交对称 LTP（orthogonal symmetric local ternary pattern，OS-LTP）【60】。不像 LBP，CS-LBP 描述符比较中心对称对之间的灰度差分（见 Fig.13）。实际上，LBP 有容忍光照改变和计算简单的优势。不幸的是，LBP 特征是离散模式的索引而不是数值特征，因此它很难与其它区分度高的特征结合在一个紧凑的描述符中【61】。此外，它产生更高维的特征并对平坦区域的高斯噪声敏感。

### 4.5 Binary Robust Independent Elementary Features (BRIEF)

BRIEF 是一个低比特率描述符，用于随机森林和随机蕨类分类器的图像匹配【62】。它属于 LBP 和 BRISK 那样的二元描述族，它仅执行简单的二元比较测试和使用汉明距离[^17]而不是欧几里得距离或者马氏距离。简单来说，为了建立二元描述符，有必要的仅仅是比较被检测兴趣点周围的两个像素的强度。这使得以一个非常小的计算开销便可以获得具有代表形的描述符。此外，二元描述符匹配仅需要计算汉明距离，而汉明距离通过现代架构的 XOR 原语实现快速计算。

BRIEF 算法依赖于相对小的图像强度差分测试来将图像块表示为二进制串。更特别的是，对大小为 $S \times S$ 的图像块的二元描述符通过连接以下测试结果获得：
$$
\tau = 
\left\{
\begin{align}
1,& &\text{if　} I(P_j) > I(P_i) \\
0,& &\text{otherwise}
\end{align}
\right.
$$
其中 $I(p_i)$ 表示为 $p_i$ 处的（平滑）像素强度，并且所有 $p_i$ 位置的选择唯一定义了一组二进制测试。采样点来自于均值为 0 ，方差为 $\frac{1}{25} S^2$ 的各向同性的高斯分布。为了增加这个描述子的鲁棒性，图像块使用方差为 2，大小为 $9\times 9$ 的高斯核进行预平滑。BRIEF 描述符需要设置两个参数：二元像素对数量和二进制阈值。

作者的实验显示了仅仅 256 位，甚至 128 位便足以获取十分好的匹配结果。因此，BRIEF 在计算以及内存存储方面都是高效的。不幸的是 BRIEF 描述符对大于 35° 的旋转不具鲁棒性，因此，它并不提供旋转不变性。

### 4.6 其它特征描述符

大量的描述符在文献中提出，并且之中的许多已经被证明在计算机视觉应用中是有效的。举个例子，基于颜色的局部特征是由 Weijer 和 Schmidt 【63】提出的基于颜色信息的四个颜色描述符。Gabor 表示或者其变化【64，65】在某种意义上是最优的，在最小化空间和频率的二维联合不确定性上。Zernike 矩【66，67】和 Steerable 滤波器【68】也用于特征提取和描述。

受到 Weber’s Law 的启发，根据局部强度变化和中心像素强度值为每个像素计算一个密集的描述符，这个方法为在【28】中提出的 Weber Local Descriptor（WLD）。WLD 描述符采用了 SIFT 使用梯度和其方向直方图的优势，以及 LBP 的计算效率和较小支持区域的优势。和 LBP 描述符相比，WLD 首先计算显著微模式（即，差分激励），然后在这些显著模式以及当前点的梯度方向上建立统计数据。

两种基于测量图像可视实体相似度从兴趣区域提取特征的方法在【69】中提出。这些方法的思路为将两个著名的方法结合起来，即 SIFT 描述符和 Local Self-Similarities（LSS）。基于笛卡尔位置网格提取了两个名为 Local Self-Similarities（LSS，C）和 Fast Local Self-Similarities（FLSS，C）的特征，这两个特征是基于对数-极坐标位置网格（LSS，LP）的 Local Self-Similarities 的改进版本。LSS 和 FLSS 作为局部特征应用于 SIFT 算法。LSS和FLSS描述符在每个单元中使用基于分布的直方图表示而不是而不是在自然（LSS，LP）描述符中的对数-极坐标位置网格中选择每个桶中的最大相关值。因此，它们得到更具鲁棒性的几何变换不变性和好的光度变换不变性。一个基于二阶梯度直方图的局部图像描述符在【70】中提出，名为 HSOG，用于捕捉神经邻域的曲率相关几何特性。Dalal 和 Triggs【71】展示了梯度方向直方图（HOG）描述符，结合了 SIFT 的性质以及 GLOH 描述符。HOG 和 SIFT 的主要差别为 HOG描述符是在具有重叠局部对比归一化的均匀间隔单元的密集网格上计算的。

Fan 等人在另一个方向提出了一种兴趣区域描述方法，这种方法在多个可使用区域中基于强度顺序汇聚局部特征。通过强度顺序汇聚不仅对选择和单调强度改变不变，而且将序数信息编码为描述符。通过汇聚两种不同的局部特征，可以得到一种基于梯度，一种基于强度的描述符，即 Multisupport Region Order-Based Gradient Histogram (MROGH) 和 the Multisupport Region Rotation and Intensity Monotonic Invariant Descriptor (MRRID)。前一种结合了强度顺序信息和梯度，然而后一种完全基于强度顺序，这使得它对大的光照改变部分不变。

尽管最近提出了大量的图像特征描述符，其中的几个是专门为某些场景应用设计的，如目标识别，形状检索或者 LADAR 数据处理【73】。此外，这些描述符的作者在为某些特别任务收集的有限数量基准数据集上评估描述符性能。因此，为特别的应用邻域选择一个合适的描述符是困难的。在这方面，最近一些研究对几种描述符做了比较：兴趣区域描述符【50】，二进制描述符【74】，局部颜色描述符【75】，和 3D 描述符【76，77】。事实上，声称描述图像特征是一个已经被解决的问题是过于大胆和乐观的。另一方面，鉴于上述描述符在几种应用程序中的成功，声称为一般现实情况设计描述符几乎是不可能的说法太过悲观了。最后，为了将描述符算法设计为可以通用还有大量的工作要做。我们主张进一步研究如何使用由三维数据和颜色信息捕获的新模式。对于实时应用，未来研究的进一步是在如 GPU 的并行处理单元上实现算法。

## 5 特征匹配

特征匹配或者一般而言的图像匹配，作为很多计算机视觉应用的一部分，如图像配准，相机标定和目标识别，建立同一场景/目标的两幅图像之间的对应关系。一种常见的图像匹配方法包括从图像数据中检测一组与图像描述符相联系的兴趣点。一旦特征和它的描述符从两幅或多幅图像中提取出来，下一步便是如 ***Fig.14*** 那样在这些图像之间建立基础的特征匹配。

<img src="\assets\Note\Image Features Detection Description and Matching.assets\image-20201202112550367.png" alt="image-20201202112550367" style="zoom:67%;" />

为了不失一般性，图像匹配的问题可以表述如下，假设 $p$  是由检测器检测到的点与一个描述符相联系[^18]

<img src="\assets\Note\Image Features Detection Description and Matching.assets\image-20201202000243428.png" alt="image-20201202000243428" style="zoom:60%;" />

其中，对于所有的 $K$，第 $k$ 个描述符的特征向量为：

<img src="\assets\Note\Image Features Detection Description and Matching.assets\image-20201202000306500.png" alt="image-20201202000306500" style="zoom:60%;" />

兴趣点 $(p,q)$ 匹配仅当 $(\text{i})$ $p$ 为 $q$ 与第一幅图像中所有点的最佳匹配并且反过来 $(\text{ii})$  $q$ 是 $p$ 与第二幅图像所有点的最佳匹配。在这个条件下，设计一个高效算法来尽可能快的执行匹配是非常重要的。欧氏范数中图像描述符特征空间中的最近邻匹配可用于基于向量的特征匹配。然而，实际上最优最近邻匹配算法及其参数依赖于数据集的特征。此外，为了抑制那些被认为模棱两可的一致关系的候选匹配，图像描述符之间的最近距离和次近距离之间的比值需要小于一个阈值。特别的是，在高维特征匹配中，这两个算法是最高效的：随机 K-d 森林和 the fast library for approximate nearest neighbors (FLANN)【78】。

在另一方面，这些算法并不适用与二进制特征（例如，FREAK 或者 BRISK）。二进制特征匹配使用的是汉明距离，通过 按位 XOR 操作计算，并对结果进行位计数。这里涉及到的操作都可以很快的执行。匹配大型数据库的经典方案为使用比线性搜索快几个数量级的近似匹配来提到原先的线性搜索。也就是说，以返回最近邻的近似点作为代价，但是经常与真实的最近邻点十分相近。为了执行二进制特征匹配，需要使用其它的方法如【80-82】。

一般地，基于兴趣点的匹配方法的性能依赖于潜在兴趣点的性质以及与之相联系的图像描述符【83】。因此，适合图像内容的检测器和描述符应该被用于应用中。举个例子，如果一副包含细菌细胞的图像，那么就需要斑点检测器而不是角点检测器，但对于城市的鸟瞰图，角点检测器更适于人造结构。此外，选择一个可以解决图像退化问题的检测器和描述符是十分重要的。例如，如果没有尺度改变，则不处理尺度变换的检测器更受推崇；反之，如果一副包含很大变形，例如尺度和旋转，那么具有更高计算力的 SURF 特征检测器和描述符更适用于这个情况。至于更高的准确度，建议同时使用几种检测器和描述符。在特征匹配邻域，必须注意到二进制描述符（例如，FREAK 或 BRISK）一般具有更快的速度且一般用于寻找图像间点的一致性关系，但是它们和基于向量的描述符相比准确度更低【74】。数据分析显示，类似 RANSAC 的鲁棒性方法可以用于在估计几何变换或者基础矩阵时，筛选匹配特征中的外点，这对图像配置和目标识别应用中的特征匹配十分有用。

## 6 性能评估

### 6.1 基准数据集

在互联网上有多种多样的数据集可以作为研究者的基准。一个流行并广泛用于检测器和描述符性能评估的为标准 Oxford 数据集【84】。这个数据集包含了不同几何和光度变换（视角改变，尺度改变，图像旋转，图像模糊，光照变换以及 JPEG 压缩）以及不同场景类型的（结构和纹理场景）数据集。在光照改变的情况下，通过改变相机孔径来改变光照。然而对于旋转，尺度改变，视角改变和模糊，有两种不同的场景类型。一种为结构场景，包含独特边缘边界的同类区域（例如涂鸦，建筑）。另一种则包含不同形式的重复纹理。因此，图像变换和场景类型造成的影响可以单独分析。每一个数据集包含 6 个几何和光度变形图像，其中第一张图像和其余 5 张相比是渐变的。***Fig.15*** 为从 Oxford 数据集中抽取的几张图像。

<img src="\assets\Note\Image Features Detection Description and Matching.assets\image-20201202224948702.png" alt="image-20201202224948702" style="zoom:67%;" />

### 6.2 评估标准

为了判断两张图像特征是否匹配（即，属于是否属于同一特征），Mikolajczyk 等人【44】提出基于重复率标准的评估方法，通过比较检测到的区域和真实区域之间的重叠大小。重复率可以被认为是最重要的评估标准，在评估特征检测器的稳定性上。它测量探测器在图像中提取相同特征点的能力，而不考虑成像条件。重复率标准表示了检测器某个检测器对于某一场景是否足够好。在这个评估方法中，两个兴趣区域 A 和 B 是一致的，当重叠错误率 $\epsilon$ 足够小时，如 **Fig.16** 所示。这个重复错误率表示了在一个单应性变换 H 下两个区域一致性的好坏。它由两个区域的交集和并集的比率定义：

<img src="\assets\Note\Image Features Detection Description and Matching.assets\image-20201202230218642.png" alt="image-20201202230218642" style="zoom:67%;" />

<img src="\assets\Note\Image Features Detection Description and Matching.assets\image-20201202225919982.png" alt="image-20201202225919982" style="zoom:67%;" />

这个方法计算了两个区域的并集和交集的全部像素数量。此外，一个匹配是正确的，当图像上两个对应区域的错误率小于 区域并的 50%，也就是 $\epsilon < 0.5$ 。重叠错误率的计算是基于 H 的，这个单应性变换矩阵是由区域定义的。因此，为了评估特征检测器的性能，对给定一对图像的重复率分数计算为区域与区域的对应数量和这对图像中较少的区域数量的比值。

另一方面，区域描述符的性能由匹配标准度量，即，描述符表示一个场景区域的好坏。这是基于由图像对得到的正确和错误匹配的数量决定的。这通过比较真实的对应区域数量和正确匹配的区域数量计算。匹配为描述符空间中最邻近的【50】。在这个情况下，两个兴趣区域成匹配对，如果它们描述符 $D_A,D_B$ 之间的欧几里得距离小于一个阈值 $\tau$ 。最后的结果使用召回率（recall）和 1-准确度（1-precision） 表示。每一个参考图像的描述符与变换之后的描述符相比较并计算正确和错误匹配的数量。

<img src="\assets\Note\Image Features Detection Description and Matching.assets\image-20201202232054828.png" alt="image-20201202232054828" style="zoom:60%;" />

其中，No.correspondences 为两幅图像的匹配区域的数量。然而，召回率是同一场景的两幅图像之间正确匹配的区域数量对于一致区域数量。一个理想描述符的对任意准确率其召回率为 1。为了获取其曲线，需要改变阈值 $\tau$。特别地，召回率随着阈值的增长而增长，因为图像变换引入的噪声和区域检测器中相似描述符间距离的增加。一个慢增长曲线表明描述符会受到图像噪声的影响。如果不同描述符的曲线的斜率差异很大，然后，对于所研究的图像变换或场景类型，这些描述符的显著性和鲁棒性是不同的【50】。

## 7 总结

这篇文章的目标是为新研究者在图像特征检测和提取研究领域提供一简洁明了的，简短的介绍。介绍了检测和提取图像特征的基本符号和数学概念，描述了完美特征检测器的特性。之前介绍了各种现存的检测兴趣点的算法。也讨论了使用最频繁的描述算法 SIFT，SURF，LBP，WLD，……并且它们的优缺点也着重强调了。此外，它解释了一些特征匹配方法。最后，文章还介绍了它们算法中用于评估性能的技术。

---

## 参考文献

1. Yap, T., Jiang, X., Kot, A.C.: Two-dimensional polar harmonic transforms for invariant image representation. IEEE Trans. Pattern Anal. Mach. Intell. 32(7), 1259–1270 (2010)
2. Liu, S., Bai, X.: Discriminative features for image classification and retrieval. Pattern Recogn. Lett. 33(6), 744–751 (2012)
3. Rahmani, R., Goldman, S., Zhang, H., Cholleti, S., Fritts, J.: Localized content-based image retrieval. IEEE Trans. Pattern Anal. Mach. Intell. 30(11), 1902–1912 (2008)
4. Stöttinger, J., Hanbury, A., Sebe, N., Gevers, T.: Sparse color interest points for image retrieval and object categorization. IEEE Trans. Image Process. 21(5), 2681–2691 (2012)
5. Wang, J., Li, Y., Zhang, Y., Wang, C., Xie, H., Chen, G., Gao, X.: Bag-of-features based medical image retrieval via multiple assignment and visual words weighting. IEEE Trans. Med. Imaging 30(11), 1996–2011 (2011)
6. Andreopoulos, A., Tsotsos, J.: 50 years of object recognition: directions forward. Comput. Vis. Image Underst. 117(8), 827–891 (2013)
7. Dollár, P., Wojek, C., Schiele, B., Perona, P.: Pedestrian detection: an evaluation of the state of the art. IEEE Trans. Pattern Anal. Mach. Intell. 34(4), 743–761 (2012)
8. Felsberg, M., Larsson, F., Wiklund, J., Wadströmer, N., Ahlberg, J.: Online learning of correspondences between images. IEEE Trans. Pattern Anal. Mach. Intell. 35(1), 118–129 (2013)
9. Miksik, O., Mikolajczyk, K.: Evaluation of local detectors and descriptors for fast feature matching. In: International Conference on Pattern Recognition (ICPR 2012), pp. 2681–2684. Tsukuba, Japan, 11–15 Nov 2012
10. Kim, B., Yoo, H., Sohn, K.: Exact order based feature descriptor for illumination robust image matching. Pattern Recogn. 46(12), 3268–3278 (2013)
11. Moreels, P., Perona, P.: Evaluation of features detectors and descriptors based on 3D objects. Int. J. Comput. Vis. 73(3), 263–284 (2007)
12. Takacs, G., Chandrasekhar, V., Tsai, S., Chen, D., Grzeszczuk, R., Girod, B.: Rotation-invariant fast features for large-scale recognition and real-time tracking. Sign. Process. Image Commun.
28(4), 334–344 (2013)
13. Tang, S., Andriluka, M., Schiele, B.: Detection and tracking of occluded people. Int. J. Comput. Vis. 110(1), 58–69 (2014)
14. Rincón, J.M., Makris, D., Uru ˇnuela, C., Nebel, J.C.: Tracking human position and lower body parts using Kalman and particle filters constrained by human biomechanics. IEEE Trans. Syst. Man Cybern. Part B 41(1), 26–37 (2011)
15. Lazebnik, S., Schmid, C., Ponce, J.: A sparse texture representation using local affine regions. IEEE Trans. Pattern Anal. Mach. Intell. 27(8), 1265–1278 (2005)

16. Liu, L., Fieguth, P.: Texture classification from random features. IEEE Trans. Pattern Anal. Mach. Intell. 34(3), 574–586 (2012)
17. Murillo, A., Guerrero, J., Sagues, C.: SURF features for efficient robot localization with omnidirectional images. In: International Conference on Robotics and Automation, pp. 3901–3907. Rome, Italy, 10–14 Apr, 2007
18. Valgren, C., Lilienthal, A.J.: SIFT, SURF & seasons: appearance-based long-term localization in outdoor environments. Rob. Auton. Syst. 58(2), 149–156 (2010)
19. Campos, F.M., Correia, L., Calado, J.M.F.: Robot visual localization through local feature fusion: an evaluation of multiple classifiers combination approaches. J. Intell. Rob. Syst. 77(2), 377–390 (2015)
20. Farajzadeh, N., Faez, K., Pan, G.: Study on the performance of moments as invariant descriptors for practical face recognition systems. IET Comput. Vis. 4(4), 272–285 (2010)
21. Mian, A., Bennamoun, M., Owens, R.: Keypoint detection and local feature matching for textured 3D face recognition. Int. J. Comput. Vis. 79(1), 1–12 (2008)
22. Jain, A.K., Ross, A.A., Nandakumar, K.: Introduction to Biometrics, 1st edn. Springer (2011)
23. Burghardt, T., Damen, D., Mayol-Cuevas, W., Mirmehdi, M.: Correspondence, matching and recognition. Int. J. Comput. Vis. 113(3), 161–162 (2015)
24. Bouchiha, R., Besbes, K.: Comparison of local descriptors for automatic remote sensing image registration. SIViP 9(2), 463–469 (2015)
25. Zhao, Q., Feng, W., Wan, L., Zhang, J.: SPHORB: a fast and robust binary feature on the sphere. Int. J. Comput. Vis. 113(2), 143–159 (2015)
26. Zhang, S., Tian, Q., Huang, Q., Gao, W., Rui, Y.: USB: ultrashort binary descriptor for fast visual matching and retrieval. IEEE Trans. Image Process. 23(8), 3671–3683 (2014)
27. Tuytelaars, T., Mikolajczyk, K.: Local invariant feature detectors: a survey. Found. Trends Comput. Graph. Vis. 3(3), 177–280 (2007)
28. Chen, J., Shan, S., He, C., Zhao, G., Pietikäinen, M., Chen, X., Gao, W.: WLD: a robust local image descriptor. IEEE Trans. Pattern Anal. Mach. Intell. 32(9), 1705–1720 (2010)
29. Viola, P., Jones, M.J.: Robust real-time face detection. Int. J. Comput. Vis. 57(2), 137–154 (2004)
30. Janan, F., Brady, M.: Shape description and matching using integral invariants on eccentricity transformed images. Int. J. Comput. Vis. 113(2), 92–112 (2015)
31. Lowe, D.G.: Distinctive image features from scale-invariant keypoints. Int. J. Comput. Vis. 60(2), 91–110 (2004)
32. Bay, H., Ess, A., Tuytelaars, T., Gool, L.: Speeded-up robust features (SURF). Comput. Vis. Image Underst. 110(3), 346–359 (2008)
33. Oliva, A., Torralba, A.: Modeling the shape of the scene: a holistic representation of the spatial envelope. Int. J. Comput. Vis. 42(3), 145–175 (2001)
34. Bianco, S., Mazzini, D., Pau, D., Schettini, R.: Local detectors and compact descriptors for visual search: a quantitative comparison. Digital Signal Process. 44, 1–13 (2015)
35. Jégou, H., Perronnin, F., Douze, M., Sánchez, J., Pérez, P., Schmid, C.: Aggregating local descriptors into a compact codes. IEEE Trans. Pattern Anal. Mach. Intell. 34(9), 1704–1716 (2012)
36. Lindeberg, T.: Feature detection with automatic scale selection. Int. J. Comput. Vis. 30(2), 79–116 (1998)
37. Moravec, H.P.: Towards automatic visual obstacle avoidance. In: 5th International Joint Conference on Artificial Intelligence, pp. 584–594 (1977)
38. Harris, C., Stephens, M.: A combined corner and edge detector. In: The Fourth Alvey Vision Conference, pp. 147–151. Manchester, UK (1988)
39. Smith, S.M., Brady, J.M.: A new approach to low level image processing. Int. J. Comput. Vis. 23(1), 45–78 (1997)
40. Rosten, E., Drummond, T.: Fusing points and lines for high performance tracking. In: International Conference on Computer Vision (ICCV’05), pp. 1508–1515. Beijing, China, 17–21 Oct 2005

41. Rosten, E., Drummond, T.: Machine learning for high speed corner detection. In: 9th European Conference on Computer Vision (ECCV’06), pp. 430–443. Graz, Austria, 7–13 May 2006
42. Beaudet, P.R.: Rotationally invariant image operators. In: International Joint Conference on Pattern Recognition, pp. 579–583 (1978)
43. Lakemond, R., Sridharan, S., Fookes, C.: Hessian-based affine adaptation of salient local image features. J. Math. Imaging Vis. 44(2), 150–167 (2012)
44. Mikolajczyk, K., Tuytelaars, T., Schmid, C., Zisserman, A., Matas, J., Schaffalitzky, F., Kadir, T., Gool, L.: A comparison of affine region detectors. Int. J. Comput. Vis. 65(1/2), 43–72 (2005)
45. Mikolajczyk, K., Schmid, C.: Scale & affine invariant interest point detectors. Int. J. Comput. Vis. 60(1), 63–86 (2004)
46. Lindeberg, T.: Scale selection properties of generalized scale-space interest point detectors. J. Math. Imaging Vis. 46(2), 177–210 (2013)
47. Yussof, W., Hitam, M.: Invariant Gabor-based interest points detector under geometric transformation. Digital Signal Process. 25, 190–197 (2014)
48. Schaffalitzky, F., Zisserman, A.: Multi-view matching for unordered image sets. In: European Conference on Computer Vision (ECCV), pp. 414–431. Copenhagen, Denmark, 28–31 May 2002
49. Lindeberg, T., Gårding, J.: Shape-adapted smoothing in estimation of 3-D shape cues from affine deformations of local 2-D brightness structure. Image Vis. Comput. 15(6), 415–434 (1997)
50. Mikolajczyk, K., Schmid, C.: A performance evaluation of local descriptors. IEEE Trans. Pattern Anal. Mach. Intell. 27(10), 1615–1630 (2005)
51. Matas, J., Chum, O., Urban, M., Pajdla, T.: Robust wide baseline stereo from maximally stable extremal regions. In. In British Machine Vision Conference (BMV), pp. 384–393 (2002)
52. Matas, J., Ondrej, C., Urban, M., Pajdla, T.: Robust wide-baseline stereo from maximally stable extremal regions. Image Vis. Comput. 22(10), 761–767 (2004)
53. Li, J., Allinson, N.: A comprehensive review of current local features for computer vision. Neurocomputing 71(10–12), 1771–1787 (2008)
54. Ke, Y., Sukthankar, R.: PCA-SIFT: a more distinctive representation for local image descriptors. In: IEEE Conference on Computer Vision and Pattern Recognition (CVPR’04), pp. 506–513. Washington, DC, USA, 27 June–2 July 2004
55. Morel, J., Yu, G.: ASIFT: a new framework for fully affine invariant image comparison. SIAM J. Imaging Sci. 2(2), 438–469 (2009)
56. Pang, Y., Li, W., Yuan, Y., Pan, J.: Fully affine invariant SURF for image matching. Neurocomputing 85, 6–10 (2012)
57. Ojala, T., Pietikäinen, M., Mäenpää, M.: Multiresolution gray-scale and rotation invariant texture classification with local binary patterns. IEEE Trans. Pattern Anal. Mach. Intell. 24(7), 971–987 (2002)
58. Heikkiläa, M., Pietikäinen, M., Schmid, C.: Description of interest regions with local binary patterns. Pattern Recogn. 42(3), 425–436 (2009)
59. Tian, H., Fang, Y., Zhao, Y., Lin, W., Ni, R., Zhu, Z.: Salient region detection by fusing bottomup and top-down features extracted from a single image. IEEE Trans. Image Process. 23(10), 4389–4398 (2014)
60. Huang, M., Mu, Z., Zeng, H., Huang, S.: Local image region description using orthogonal symmetric local ternary pattern. Pattern Recogn. Lett. 54(1), 56–62 (2015)
61. Hong, X., Zhao, G., Pietikäinen, M., Chen, X.: Combining LBP difference and feature correlation for texture description. IEEE Trans. Image Process. 23(6), 2557–2568 (2014)
62. Calonder, M., Lepetit, V., Özuysal, M., Trzcinski, T., Strecha, C., Fua, P.: BRIEF: computing a local binary descriptor very fast. IEEE Trans. Pattern Anal. Mach. Intell. 34(7), 1281–1298 (2012)
63. Van De Weijer, J., Schmid, C.: Coloring local feature extraction. In: European Conference on Computer Vision (ECCV), pp. 334–348. Graz, Austria, 7–13 May 2006
64. Subrahmanyam, M., Gonde, A.B., Maheshwari, R.P.: Color and texture features for image
indexing and retrieval. In: IEEE International Advance Computing Conference (IACC), pp.1411–1416. Patiala, India, 6–7 Mar 2009
65. Zhang, Y., Tian, T., Tian, J., Gong, J., Ming, D.: A novel biologically inspired local feature descriptor. Biol. Cybern. 108(3), 275–290 (2014)
66. Chen, Z., Sun, S.: A Zernike moment phase-based descriptor for local image representation and matching. IEEE Trans. Image Process. 19(1), 205–219 (2010)
67. Chen, B., Shu, H., Zhang, H., Coatrieux, G., Luo, L., Coatrieux, J.: Combined invariants to similarity transformation and to blur using orthogonal Zernike moments. IEEE Trans. Image
Process. 20(2), 345–360 (2011)
68. Freeman, W., Adelson, E.: The design and use of steerable filters. IEEE Trans. Pattern Anal. Mach. Intell. 13(9), 891–906 (1991)
69. Liu, J., Zeng, G., Fan, J.: Fast local self-similarity for describing interest regions. Pattern Recogn. Lett. 33(9), 1224–1235 (2012)
70. Huang, D., Chao, Z., Yunhong, W., Liming, C.: HSOG: a novel local image descriptor based on histograms of the second-order gradients. IEEE Trans. Image Process. 23(11), 4680–4695 (2014)
71. Dalal, N., Triggs, B.: Histograms of oriented gradients for human detection. In: IEEE Conference on Computer Vision and Pattern Recognition (CVPR’05), pp. 886–893. San Diego, CA, USA, 20–26 June 2005
72. Fan, B., Wu, F., Hu, Z.: Rotationally invariant descriptors using intensity order pooling. IEEE Trans. Pattern Anal. Mach. Intell. 34(10), 2031–2045 (2012)
73. Al-Temeemy, A.A., Spencer, J.W.: Invariant chromatic descriptor for LADAR data processing. Mach. Vis. Appl. 26(5), 649–660 (2015)
74. Figat, J., Kornuta, T., Kasprzak, W.: Performance evaluation of binary descriptors of local features. Lect. Notes Comput. Sci. (LNCS) 8671, 187–194 (2014)
75. Burghouts, G., Geusebroek, J.M.: Performance evaluation of local color invariantss. Comput. Vis. Image Underst. 113(1), 48–62 (2009)
76. Moreels, P., Perona, P.: Evaluation of features detectors and descriptors based on 3D objects. Int. J. Comput. Vis. 73(2), 263–284 (2007)
77. Guo, Y., Bennamoun, M., Sohel, F., Lu, M., Wan, J., Kwok, N.M.: A comprehensive performance evaluation of 3D local feature descriptors. Int. J. Comput. Vis. First online, 1–24 (2015)
78. Muja, M., Lowe, D.G.: Scalable nearest neighbor algorithms for high dimensional data. IEEE Trans. Pattern Anal. Mach. Intell. 36(11), 2227–2240 (2014)
79. Szeliski, R.: Computer Vision: Algorithms and Applications. Springer, USA (2011)
80. Muja, M., David G. Lowe: Fast matching of binary features. In: IEEE Conference on Computer Vision and Pattern Recognition (CVPR’12), pp. 404–410. Toronto, ON, USA, 28–30 May 2012
81. Nister, D., Stewenius, H.: Scalable recognition with a vocabulary tree. In: IEEE Conference on Computer Vision and Pattern Recognition (CVPR’06), pp. 2161–2168. Washington, DC, USA, 17–22 June 2006
82. Yan, C.C., Xie, H., Zhang, B., Ma, Y., Dai, Q., Liu, Y.: Fast approximate matching of binary codes with distinctive bits. Frontiers Comput. Sci. 9(5), 741–750 (2015)
83. Lindeberg, T.: Image matching using generalized scale-space interest points. J. Math. Imaging Vis. 52(1), 3–36 (2015)
84. The oxford data set is available at (last visit, Oct. 2015) [http://www.robots.ox.ac.uk/~vgg/data/affine/](http://www.robots.ox.ac.uk/~vgg/data/affine/)

---

[^1]: 研究在两个相机位置产生的两幅图像的之间存在的特殊几何关系
[^2]: 即仿射不变性包含了尺度不变，仿射会改变不同方向的尺度，即不均匀地缩放，尺度不变是均匀地缩放
[^3]: 四边形变为平行四边形的变换，仿射变换其实就是一个线性变换和一个平移
[^4]: 这里使用8个方向作为改变依据，但旋转会改变这8个离散方向所代表的原有方向
[^5]: 这里原文有点错误（或者他想表达的是卷积操作），可参考http://dept.me.umn.edu/courses/me5286/vision/Notes/2015/ME5286-Lecture8.pdf
[^6]: 具有旋转不变性，部分光照变化不变性（兴趣点数量会发生改变），不具备尺度不变性，因为依然单尺度的
[^7]: 参考 https://www.cnblogs.com/ronny/p/4078710.html
[^8]: 翻译为最小核值相似区或者最小同值分割吸收核
[^9]: 也就是首先对一组测试图像使用完整的 FAST 算法，然后使用 ID3 得到16像素中亮，暗，相似像素数量和是否是角点的关系，之后就可以直接判断 16 像素中这三种像素的数量直接判断角点
[^10]: 海森矩阵参考 https://blog.csdn.net/u013921430/article/details/79770458
[^11]: 具有最大 LoG 响应的尺度
[^12]: 位置代表的是图像空间，尺度代表的是尺度空间，尺度为1维，图像为2维，组成3维
[^13]: 可参考[Harris角点 - ☆Ronny丶 - 博客园 (cnblogs.com)](https://www.cnblogs.com/ronny/p/4009425.html)
[^14]: 这里也就是说需要在尺度空间获得最大值，尺度不变性需要同时满足尺度空间 LoG 最大响应以及图像空间极值条件
[^15]: 这个是真不懂 [小波变换——哈尔小波，Haar - ostartech - 博客园 (cnblogs.com)](https://www.cnblogs.com/wxl845235800/p/10711858.html)，[【小波变换】小波变换入门----haar小波-筱-CSDN博客-haar小波](https://blog.csdn.net/baidu_27643275/article/details/84826773)
[^16]: 即值大于最大值 0.2 的值截断为最大值的 0.2
[^17]: 两个字之间对应位不同的数量
[^18]:  感觉这里是某一个点的一组描述符