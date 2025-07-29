---
title: ImageNet and ILSVRC
date: 2025-04-14 10:21:32
index_img: /img/cover/AlexNet-1.jpg
math: true
excerpt: A detailed explanation of the mathematical principles of convolutional neural networks.
categories:
  - Artificial Intelligence
  - Deep Learning
tags:
  - Convolutional Neural Networks
  - Artificial Intelligence
  - Finished
  - AlexNet
  - Image Clssification
  - Deep Learning
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>
# ImageNet Classification and ILSVRC Competition

> My **final assignment** for **Introduction to artificial intelligence**.

# Tracing the Evolution of ILSVRC Winners and Their Impact on Image Classifications

## Abstract

The past decade witnessed the transformation from Fully Connected Neural Network(FCNN) to Convolutional Neural Network(CNN) , which significantly addressed various challenges in the field of image recognition. This review traces how deep learning had passed a decade of breakthrough, introducing the evolution of winners from the **ImageNet Large Scale Visual Recognition Challenge (ILSVRC)**, highlighting key advancements such as AlexNet, GoogLeNet, ResNet and ResNeXt. By analyzing the architectural innovations and methodological breakthroughs of these models such as ReLU, dropout, LRN, Inception, Residual Network and cardinality, the review then provides insights into the trends in neural network research, regarding how they implemented the optimization by adding the depth of the layers without introducing a huge amount of extra parameters and computational complexity. Finally, we discussed the outlook of Deep Neural Networks in the field of image classification, including striving for higher quality datasets, moving from object recognition to human-level understanding and finding alternative models outperforming traditional CNNs.

## Introduction

再看卷积神经网络！在笔者大一上学期，曾经写过一篇有关**ImageNet 和 ILSVRC**相关的论文，当时属实是**有一种掌握梯度下降就可以优化一切**的傲气，如今回看这篇文章，颇觉得有许多需要改进的地方。因此，笔者决定**重构**这篇文章，并且从这篇文章出发，力求在数学上、工程上和思想上都对卷积神经网络有更深的理解。

回看当年**炼丹**的日子，或许在历史的长河中，我们总是**能够找到新的灵感**。

本博客的相关代码和文件将会存储在 [这个仓库](https://github.com/xiyuanyang-code/ImageNet-and-CNN-learning-materials) 中。坚持用中文，因为希望**力求获得更加深刻的理解**。

## 从“卷积”谈起...

给定两个函数$f(t)$和$g(t)$：

$$(f*g)(t)=\int_{-\infty}^{+\infty}f(τ)g(t−\tau)\mathrm{d}τ$$

这个定义非常的抽象，我们不妨举一个**形象化的例子**：

### 音频处理

给定一个波形函数$f(t)$代表一段音频，现在，我们希望对这个函数做数学运算，来**模拟回声**的效果。

> 这里其实暗含一个**线性系统**（线性时不变系统**LTI**）的假设，具体指的是：
>
> - **线性**：系统的输入和输出可以进行线性叠加。
> 	- 在这里，很显然两个波形函数叠加就可以得到新的函数。
> - **时不变性**：系统的输入输出关系不随时间变化。换句话说，如果输入信号的时间发生平移（延迟或提前），那么输出信号也会相应地平移相同的时间量，而不会改变其形状或性质。
> 	- $ y(t)=T[x(t)] \text{ then, }T[x(t−t_0)]=y(t−t_0)$. 其中 $T$ 代表系统的变化关系， $x(t)$ 和 $y(t)$ 分别代表系统的输入和输出。
> 	- 在这里，我们可以通过平移等手段实现这一点。（不深究）

从直觉上，我们知道，如果在时间$t_0$处连续函数$f(t)$的函数值为$s_0 = f(t_0)$，那么我们希望某种施加变换得到新函数$F(t)$，满足$F(t_0 + \delta) = f(t_0 + \delta) + \epsilon f(t_0) $.

> 这个公式直观意思：$t_0 + \delta$处的声音波形函数收到回声的影响（$t = t_0$），使其分贝数（函数值）发生了变化。

如何定量的表示这个函数？我们可以定义**核函数**$g(t)$，定义如下：
$$
g(t) = 
\begin{cases} 
1, & t = 0 \\\\ 
\epsilon, & t = \delta \\\\ 
0, & \text{otherwise}
\end{cases}
$$
这样我们就可以写出新的函数$F(t) = (f*g)(t)=\int_{0}^{t}f(τ)g(t−\tau)\mathrm{d}τ$。

> 这个函数可以解释为：加入**回声**考虑之后，在$t$时刻的函数值（声音的分贝）可以解释为**在过去每一个时刻发出声音**的回声的**线性叠加**。因此，在实际我们可以将$g(t)$定义为更加复杂的衰减函数。

这就是卷积！我们可以推广至更一般的情况，**它的核心思想是通过一个滑动窗口（核函数）对输入数据进行加权求和，从而生成一个新的表示。**例如在之前音频处理的例子中，核函数就是$g$，滑动窗口就是$\int_{0}^{t}$，我们对原始的输入数据$f(t)$进行加权求和，最终得到新的表示。

### 卷积的可视化

```python
"""
Author: Xiyuan Yang   xiyuan_yang@outlook.com
Date: 2025-04-14 19:23:55
LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
LastEditTime: 2025-04-14 19:24:01
FilePath: /CNN-tutorial/src/convolution.py
Description:
Do you code and make progress today?
Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved.
"""

import numpy as np
import matplotlib.pyplot as plt
import matplotlib as mlp
from scipy.integrate import quad

mlp.use("Agg")


# Original Function f(t)
def original_signal(t):
    return np.sin(2 * np.pi * t)


def echo_kernel2(t, alpha):
    if t >= 0:
        return np.exp(-alpha * t)
    else:
        return 0.0


# Normalize the kernel to ensure its integral is 1
def normalized_echo_kernel(t, alpha):
    # Compute the normalization factor (integral of the kernel from 0 to infinity)
    normalization_factor, _ = quad(lambda tau: echo_kernel2(tau, alpha), 0, np.inf)
    # Return the normalized kernel value
    return echo_kernel2(t, alpha) / normalization_factor


# Convolution with normalized kernel
def convolution(f, g, t_values, alpha):
    h = []
    for t in t_values:
        integral, _ = quad(lambda tau: f(tau) * g(t - tau, alpha), 0, t)
        h.append(integral)
    return np.array(h)


# Plot figure for two pictures
def plotfig(t, f, h, alpha):
    plt.figure(figsize=(10, 6))
    
    # Original Signal
    plt.subplot(2, 1, 1)
    plt.plot(t, f, label="Original Signal", color="blue")
    plt.title("Original Signal")
    plt.xlabel("Time (s)")
    plt.ylabel("Amplitude")
    plt.legend()
    plt.grid()

    # Signal with Continuous Echo Effect
    plt.subplot(2, 1, 2)
    plt.plot(t, h, label="Signal with Continuous Echo", color="red")
    plt.title("Signal with Continuous Echo Effect")
    plt.xlabel("Time (s)")
    plt.ylabel("Amplitude")
    plt.legend()
    plt.grid()
    plt.tight_layout()
    plt.savefig(f"img/Signal_with_Continuous_Effect_alpha={alpha:.2f}.png")
    plt.close()


def main():
    t = np.linspace(0, 5, 500)  # Time range from 0 to 5 seconds
    f = original_signal(t)      # Original signal
    
    # Test different values of alpha
    for i in [0.01, 0.1, 0.3, 0.5, 0.6, 0.8, 1, 1.1, 1.4, 1.5, 2, 2.5, 5]:
        h = convolution(original_signal, normalized_echo_kernel, t, alpha=i)
        plotfig(t, f, h, i)


if __name__ == "__main__":
    main()
```

随便选择一个$alpha$值，可以看到带着回声卷积处理之后的函数：

![卷积示例](https://s1.imagehub.cc/images/2025/04/15/2c9b576d8bde3d09dbd4c4ee58040077.png)

## 卷积与图像

我们知道，对于一张jpg图像可以把它转化为一个高维矩阵（每一个元素的值代表对应点的灰度大小）。但是对于**自然世界中有意义的图像**，矩阵在相邻位置的值往往具有**一致性**，比如色块和轮廓等。我们把这一特征抽象为**图像中的关联性**，相邻像素通常具有相似的灰度值或颜色（现实世界中物体表面的连续性和光照的平滑变化）。

![随机矩阵对应的雪花图](https://s1.imagehub.cc/images/2025/04/15/984fb8180a3715627a8214b8bbf62499.png)

并且，我们很明显知道，**图像像素点之间的关联性**很大程度上会随着距离的增加而衰减，我们可以使用常见的衰减模型来刻画这种衰减的状态：

指数衰减：$y = e^{-kx}$ 或者 幂函数衰减：$y = \frac{1}{x^k}$

同时，图像还满足多种**不变性**：平移不变性，旋转不变性，视点不变性，大小伸缩不变性...

因此，我们可以联想到，**选择合适的核函数**进行卷积运算是否可以实现图像特征的提取？答案是肯定的，我们不妨来看下面的例子：**边缘检测**。

### 边缘检测

![image](https://s1.imagehub.cc/images/2025/04/15/339343f50534cbc97d20c228d803e8fc.png)

我们继续从**音频识别**的例子出发，卷积的本质就是**加权平均**，同时，图像相邻位置的像素点的值之间具有**关联性**，因此，我们可以使用**$3 \times 3$**的卷积核，作为上文的卷积核函数。（这里是**离散卷积**）

我们知道**边缘**区域代表着**更加显著**的亮度变化，因此，如果我们施加这样的卷积核：

$$\begin{bmatrix}-1 & 0  & 1 \\\\ -1 & 0  & 1 \\\\ -1 & 0  & 1 \end{bmatrix}$$

**负值区域**（左侧）会对图像的亮度降低贡献较大，而**正值区域**（右侧）则会对亮度增加贡献较大。因此，就可以把对应的梯度特征给卷积出来，同理，我们也可以有对纵向的卷积核。

```python
"""
Author: Xiyuan Yang   xiyuan_yang@outlook.com
Date: 2025-04-15 00:28:15
LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
LastEditTime: 2025-04-15 00:29:55
FilePath: /CNN-tutorial/src/convolution_demo.py
Description:
Do you code and make progress today?
Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved.
"""

import torch
import torch.nn.functional as F
from PIL import Image
import torchvision.transforms as transforms
import matplotlib.pyplot as plt
import matplotlib as mlp
import numpy as np

mlp.use("Agg")


def apply_convolution(image_path, kernel, name):
    # 打开图片并转换为灰度图
    image = Image.open(image_path).convert("L")

    # 转换为张量
    transform_to_tensor = transforms.ToTensor()
    image_tensor = transform_to_tensor(image).unsqueeze(0)  # 增加批次维度 (1, 1, H, W)

    # 卷积操作
    kernel_tensor = (
        torch.tensor(kernel, dtype=torch.float32).unsqueeze(0).unsqueeze(0)
    )  # (1, 1, 3, 3)
    convolved_image = F.conv2d(image_tensor, kernel_tensor, padding=1)  # 保持尺寸

    # 转换回 NumPy 数组以便显示
    convolved_image_np = convolved_image.squeeze().detach().numpy()

    # 显示原图和卷积后的图像
    plt.subplot(1, 2, 1)
    plt.title("Original Image")
    plt.imshow(image, cmap="gray")

    plt.subplot(1, 2, 2)
    plt.title("Convolved Image")
    plt.imshow(convolved_image_np, cmap="gray")
    plt.savefig(f"img/Convolved_img {name}.png")
    plt.close()


# 示例调用
if __name__ == "__main__":
    # 定义 3x3 卷积核（例如边缘检测）
    kernel_1 = [[-1, -1, -1], [-1, 8, -1], [-1, -1, -1]]
    kernel_2 = [[0, -1, 0], [-1, 5, -1], [0, -1, 0]]
    kernel_3 = [[1 / 9, 1 / 9, 1 / 9], [1 / 9, 1 / 9, 1 / 9], [1 / 9, 1 / 9, 1 / 9]]
    kernel_4 = [[-1, 0, 1], [-1, 0, 1], [-1, 0, 1]]
    kernel_5 = np.random.randn(3, 3)
    kernel_5 = kernel_5 / kernel_5.sum()
    kernel_6 = np.identity(3) / 3
    kernel_7 = [[-2, -3, -2], [-3, 21, -3], [-2, -3, -2]]

    # 替换为您本地的图片路径
    image_path = "img/demo_cat.jpg"  # 请确保路径正确
    apply_convolution(image_path, kernel_1, "edge_detection")
    apply_convolution(image_path, kernel_2, "sharpen")
    apply_convolution(image_path, kernel_3, "normalize")
    apply_convolution(image_path, kernel_4, "edge_detect2")
    apply_convolution(image_path, kernel_5, "just for fun")
    apply_convolution(image_path, kernel_6, "kernel6")
    apply_convolution(image_path, kernel_7, "kernel_7")

    # the usage of surbo kernel
    kernel_surbo_1 = [[-1, 0, 1], [-2, 0, 2], [-1, 0, 1]]
    kernel_surbo_2 = [[1, 2, 1], [0, 0, 0], [-1, -2, -2]]
    apply_convolution(image_path, kernel_surbo_1, "surbo_1")
    apply_convolution(image_path, kernel_surbo_2, "surbo_2")
```

### 卷积的数学运算

在图像卷积中，我们会使用**卷积函数的二维离散形式**：

$$F(x,y) = \sum_{u,v} f(x-u,y-v)g(u,v)$$

$$F(x,y) = \int f(x-u,y-v)g(u,v) \mathrm{d}u \mathrm{d}v$$

这里$g(u,v)$代表我们之前使用的$3 \times 3$的卷积核。

![图像卷积的数学运算](https://s1.imagehub.cc/images/2025/04/15/852f706ca8313ad8d5d81a1d0ed3c2b0.png)

> 这里就是一个非常经典的**二维图像**的滑动窗口！

在卷积扫一遍之后，就会得到一张新的图像，这也就是我们上文展示的**图像过卷积**。

{% note primary %}

**Zero Padding**：解决图像过卷积的问题

对于**边缘**的像素块，为了防止其在卷积之后被剪裁，我们需要**适当扩展图像的size**，例如这样：

![Zero Padding](https://s1.imagehub.cc/images/2025/04/15/facae81e4da98908c65b8cb1ee318691.png)

{% endnote %}

在定义好卷积核函数之后（一个**可训练的矩阵**），我们终于可以**移动滑动窗口**了，这一个过程叫**跨步（Stride）**。$stride(i,j)$表示单次操作中横向移动$i$步，纵向移动$j$步。

同时，对于彩色图像，我们需要对RGB三个通道进行**多通道卷积**，不过为了简单起见，我们这里只考虑黑白单通道图像。

## 卷积神经网络

终于，我们迎来了今天的主角，**卷积神经网络**！

#### 生物学灵感

**局部感受野**：生物视觉系统中的神经元通常只对视野中的小区域敏感，这被称为局部感受野。CNN 中的卷积层通过卷积操作模拟了这一特性，允许网络在局部区域内提取特征。

因此，我们可以尝试**将卷积结构带入到神经网络中**，因为单纯的全连接网络在小数据量的情况下很容易过拟合，无法**识别图像的真正特征**。

#### 网络结构

一个标准的卷积神经网络（Convolutional Neural Network, CNN）通常由以下几个模块构成：

- 卷积层（Convolutional Layer） —— 简单细胞
- 池化层（Pooling Layer）
- 全连接层（Fully-Connected Layer）—— 复杂细胞
- 输出层（Output Layer）

在接下来的部分，我们将具体介绍每一个网络层的应用。

## 网络结构详解

在这个部分，我们将会**AlexNet**为基础，介绍最基本的卷积神经网络的**搭建**和**训练**的过程。

先上代码（网络结构）：

```python
"""
Author: Xiyuan Yang   xiyuan_yang@outlook.com
Date: 2025-04-15 14:40:20
LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
LastEditTime: 2025-04-15 14:41:31
FilePath: /CNN-tutorial/src/AlexNet.py
Description:
Do you code and make progress today?
Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved.
"""

import torch
import torch.nn as nn
import torch.nn.functional as F


class AlexNet(nn.Module):
    def __init__(self, num_classes=1000):
        super(AlexNet, self).__init__()

        # Define the convolutional layers
        self.features = nn.Sequential(
            # First convolutional layer: 3 input channels, 64 output channels, kernel size 11x11, stride 4, padding 2
            nn.Conv2d(3, 64, kernel_size=11, stride=4, padding=2),
            nn.ReLU(inplace=True),
            nn.MaxPool2d(kernel_size=3, stride=2),
            # Second convolutional layer: 64 input channels, 192 output channels, kernel size 5x5, padding 2
            nn.Conv2d(64, 192, kernel_size=5, padding=2),
            nn.ReLU(inplace=True),
            nn.MaxPool2d(kernel_size=3, stride=2),
            # Third convolutional layer: 192 input channels, 384 output channels, kernel size 3x3, padding 1
            nn.Conv2d(192, 384, kernel_size=3, padding=1),
            nn.ReLU(inplace=True),
            # Fourth convolutional layer: 384 input channels, 256 output channels, kernel size 3x3, padding 1
            nn.Conv2d(384, 256, kernel_size=3, padding=1),
            nn.ReLU(inplace=True),
            # Fifth convolutional layer: 256 input channels, 256 output channels, kernel size 3x3, padding 1
            nn.Conv2d(256, 256, kernel_size=3, padding=1),
            nn.ReLU(inplace=True),
            nn.MaxPool2d(kernel_size=3, stride=2),
        )

        # Define the fully connected layers
        self.classifier = nn.Sequential(
            nn.Dropout(),
            nn.Linear(256 * 6 * 6, 4096),  # Flattened feature map size is 256 * 6 * 6
            nn.ReLU(inplace=True),
            nn.Dropout(),
            nn.Linear(4096, 4096),
            nn.ReLU(inplace=True),
            nn.Linear(4096, num_classes),
        )

    def forward(self, x):
        # Pass input through the convolutional layers
        x = self.features(x)

        # Flatten the output for the fully connected layers
        x = torch.flatten(x, 1)

        # Pass through the fully connected layers
        x = self.classifier(x)
        return x


# Example usage
if __name__ == "__main__":
    # Create an instance of AlexNet with 1000 output classes (default for ImageNet)
    model = AlexNet(num_classes=1000)

    # Print the model architecture
    print(model)

    # Test with a random input tensor (batch size 1, 3 channels, 224x224 image)
    input_tensor = torch.randn(1, 3, 224, 224)
    output = model(input_tensor)
    print(output.shape)  # Should output torch.Size([1, 1000])

```

```
AlexNet(
  (features): Sequential(
    (0): Conv2d(3, 64, kernel_size=(11, 11), stride=(4, 4), padding=(2, 2))
    (1): ReLU(inplace=True)
    (2): MaxPool2d(kernel_size=3, stride=2, padding=0, dilation=1, ceil_mode=False)
    (3): Conv2d(64, 192, kernel_size=(5, 5), stride=(1, 1), padding=(2, 2))
    (4): ReLU(inplace=True)
    (5): MaxPool2d(kernel_size=3, stride=2, padding=0, dilation=1, ceil_mode=False)
    (6): Conv2d(192, 384, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1))
    (7): ReLU(inplace=True)
    (8): Conv2d(384, 256, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1))
    (9): ReLU(inplace=True)
    (10): Conv2d(256, 256, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1))
    (11): ReLU(inplace=True)
    (12): MaxPool2d(kernel_size=3, stride=2, padding=0, dilation=1, ceil_mode=False)
  )
  (classifier): Sequential(
    (0): Dropout(p=0.5, inplace=False)
    (1): Linear(in_features=9216, out_features=4096, bias=True)
    (2): ReLU(inplace=True)
    (3): Dropout(p=0.5, inplace=False)
    (4): Linear(in_features=4096, out_features=4096, bias=True)
    (5): ReLU(inplace=True)
    (6): Linear(in_features=4096, out_features=1000, bias=True)
  )
)
torch.Size([1, 1000])
```

### Input Layer

首先，对于一个CNN，输入是**图片**，单张图片embedding为矩阵的形式就是$224 \times 224 \times 3$的张量。（**张量**可以理解为是一种高维形式下的矩阵）。同时，模型可以一次输入**多张图片**，记为$N$,批量大小（同时处理的图片数量）。

{% note info %}

在深度学习中，通常用四个字母来表示**多维张量的不同维度**，尤其是在处理图像数据时。对于 AlexNet 输入的形状 \($N \times 224 \times 224 \times 3$)，这些字母通常代表：

- **N**: Batch size（批量大小）—— 一次输入到网络中的样本数量。
- **C**: Channels（通道数）—— 通常对于 RGB 图像来说是 3（红、绿、蓝）。
- **H**: Height（高度）—— 图像的高度，这里是 224 像素。
- **W**: Width（宽度）—— 图像的宽度，这里也是 224 像素。

因此，完整的表示可以是：

- **N**: Batch size
- **C**: Channels
- **H**: Height
- **W**: Width

在这种情况下，张量的形状可以表示为 \($N \times C \times H \times W$)，即 \($N \times 3 \times 224 \times 224$\)。

**注意！**批量指的是一次操作中输入的图片的数量，具体而言在训练细节中，指的是**批量**是指在一次前向传播和反向传播中处理的样本数量。比如，如果你有一个批量大小为 32 的训练集，那么在每次迭代中，模型将同时处理 32 张图像。批量反应的是**GPU和模型并行处理图像的能力**，但是对于网络结构而言，我们暂时不会考虑这一个参数（即每一次只研究一张图像）

{% endnote %}

### 卷积层

在输入层之后，就进入到了`Conv2d(3, 64, kernel_size=(11, 11), stride=(4, 4), padding=(2, 2))`的第一个卷积层。是一个$11 \times 11$的卷积核，$stride = 4$（单次移动的步幅），同时会有一定的padding操作。

注意，这里还有一个参数**64**，代表着**卷积核的通道数**，换句话说可以理解为有多少个不同的卷积核在对这个图像做卷积。当我们说一个卷积层有 64 个输出通道时，意味着该层使用了 64 个卷积核。每个卷积核会生成一个特征图，所以最终会得到 64 个特征图。每个**输出通道（特征图）**捕捉输入数据的不同特征。例如，在图像处理中，一个卷积核可能专注于**边缘检测**，而另一个卷积核可能专注于**纹理或颜色**。

对于这个卷积层，其单个通道下的输出维度可以使用下面的函数进行计算：

$$Output Size=\frac{Input Size−Kernel Size+2×Padding}{stride} + 1 = 55$$

在这里，$Inputsize = 224$, $KernelSize = 11$, $Padding = 2$, $stride = 4$。

因此输出张量的维度 $(64,55,55)$。

### ReLU

#### 激活函数

激活函数的作用，说到底就是在多层感知机线性的网络参数结构下引入**非线性的部分**，如果没有激活函数，神经网络的每一层其实都是线性变换，无法有效地拟合复杂的函数。同时，激活函数还可以有**控制输出范围**（$Sigmoid(x) = \frac{1}{1+e^{-x}}$）、改善**梯度消失现象**（$Tanh(x) = \frac{e^x - e^{-x}}{e^x + e^{-x}}$）的作用。

$$ReLU(x) = \begin{cases} 
0, & x \le 0 \\\\ 
x, & x > 0
\end{cases}$$

在这里**ReLU**是一个非线性的激活函数，并且由于负值被截断为零，ReLU 使得一部分**神经元在某些输入下不激活**（输出为零），这有助于模型的稀疏性和提高计算效率（ReLU的计算非常简单）。

`inplace=True` 表示在原地进行操作。这意味着 ReLU 函数会直接修改输入张量，而不是创建一个新的张量来存储输出。

### 池化层

在经过ReLU函数激活之后，我们来到了**池化层**：`nn.MaxPool2d(kernel_size=3, stride=2)`。在讲解这个操作之前，我们先来具体了解一下什么是**池化**。

在不同的网络层数之间，我们储存的都是**矩阵**，而这些矩阵都是**非常稀疏**的矩阵，或者说这些矩阵内部非常多的元素都是0，尤其是在经过多次ReLU激活之后，这样会带来好处（**详见高维数据的处理：高维空间下向量近似正交**）。但是这对**数据的存储和计算效率上带来了很大的困难**，因此，为了进一步压缩**图像卷积提取到的图片特征**，我们需要对特征图进行进一步的压缩。这一步过程就叫做**池化**（也可以叫做**下采样**）。

- **降维**: 池化可以减少特征图的尺寸，从而降低后续层的计算复杂度。
- **特征提取**: 通过选择特定区域内的最大值或平均值，池化能够保留重要的特征，同时抑制噪声。
- **防止过拟合**: 通过减少特征图的尺寸，池化有助于减少模型的复杂性，从而降低过拟合的风险。

池化的具体操作非常简单，主要分为**最大池化（Max Pooling）和平均池化（Average Pooling）**：

- **最大池化（Max Pooling）**: 在池化窗口内选**择最大值。它能够保留特征图中的重要信息**，尤其是在图像处理中。
- **平均池化（Average Pooling）**: 在池化窗口内**计算平均值**。它对特征图的平滑效果更强，但可能会丢失一些重要的特征。

池化过程中的参数有：

- **池化窗口大小（Kernel Size）**: 池化操作中使用的窗口的大小，例如 2×22×2 或 3×33×3。
- **步幅（Stride）**: 池化窗口在特征图上移动的步长。步幅越大，输出特征图的尺寸越小。
- **填充（Padding）**: 在某些情况下，可以在特征图的边缘添加额外的像素，以控制输出尺寸。

因此，我们便可以理解这一行代码是什么意思了：使用**最大池化**。其对应参数的解释：

- `dilation=1`

	- **含义**: 控制池化窗口元素之间的间距。

	- **解释**: `dilation=1` 表示池化窗口的元素之间没有间隔，窗口是紧凑的。增加 `dilation` 值会使得窗口的元素之间有间隔，这在某些情况下可以用于扩大感受野。

- `ceil_mode=False`

	- **含义**: 控制输出特征图尺寸的计算方式。
	- **解释**: `ceil_mode=False` 表示在计算输出特征图的尺寸时使用向下取整（floor）。如果设置为 `True`，则使用向上取整（ceil），这可能会导致输出特征图的尺寸略微增大。

假设有一个输入特征图，其尺寸为 $H×W$，经过池化操作后，输出特征图的尺寸可以通过以下公式计算：

$$Output Height=⌊\frac{H−kernel\_size}{stride}+1⌋$$

$$Output Width=⌊\frac{W−kernel\_size}{stride}+1⌋$$

{% note primary %}

学习完这三个层之后，你应该就可以看懂下面的代码了：（**当然也是上面的代码**）

```
 (features): Sequential(
    (0): Conv2d(3, 64, kernel_size=(11, 11), stride=(4, 4), padding=(2, 2))
    (1): ReLU(inplace=True)
    (2): MaxPool2d(kernel_size=3, stride=2, padding=0, dilation=1, ceil_mode=False)
    (3): Conv2d(64, 192, kernel_size=(5, 5), stride=(1, 1), padding=(2, 2))
    (4): ReLU(inplace=True)
    (5): MaxPool2d(kernel_size=3, stride=2, padding=0, dilation=1, ceil_mode=False)
    (6): Conv2d(192, 384, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1))
    (7): ReLU(inplace=True)
    (8): Conv2d(384, 256, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1))
    (9): ReLU(inplace=True)
    (10): Conv2d(256, 256, kernel_size=(3, 3), stride=(1, 1), padding=(1, 1))
    (11): ReLU(inplace=True)
    (12): MaxPool2d(kernel_size=3, stride=2, padding=0, dilation=1, ceil_mode=False)
  )
```

{% endnote %}

不过接下来AlexNet还没有结束！让我们继续往下看：

### 全连接层

在AlexNet类中的前向传播的函数中：

```python
def forward(self, x):
    # Pass input through the convolutional layers
    x = self.features(x)

    # Flatten the output for the fully connected layers
    x = torch.flatten(x, 1)

    # Pass through the fully connected layers
    x = self.classifier(x)
    return x
```

输入的向量$x$首先经过`self.features()`即若干个卷积层的处理之后，又经过`torch.flatten(x,1)`，展平为**一维向量**。接下来，AlexNet使用了一个传统的全连接神经网络，来拟合在提取和抽象过后图像矩阵的信息，即`x = self.classifier(x)`。

{% note primary %}

**严格来说**，并非展平为一维向量，因为还有**通道数**($N$)。

`torch.flatten` 函数用于将输入张量展平为一维张量。它可以选择性地从某个特定维度开始展平。

- **第一个参数**: `x` 是要展平的输入张量，通常是一个多维张量，例如卷积层的输出。
- **第二个参数**: `1` 指定从哪个维度开始展平。这意味着在维度 1 及其之后的所有维度都将被展平，而维度 0（通常是批量大小）将保持不变。

假设 `x` 是一个形状为 `(batch_size, channels, height, width)` 的四维张量（例如，来自卷积层的输出）。在这种情况下：

- `batch_size` 表示一次处理的样本数。
- `channels` 是特征图的通道数（例如，256）。
- `height` 和 `width` 是特征图的空间维度（例如，6 和 6）。

当你调用 `torch.flatten(x, 1)` 时，结果将是一个形状为 `(batch_size, channels * height * width)` 的二维张量。具体来说，如果 `x` 的形状是 `(N, 256, 6, 6)`，那么展平后它的形状将变为 `(N, 256 * 6 * 6)`，即 `(N, 9216)`。

{% endnote %}

我们来看全连接层的代码：

```python
# Define the fully connected layers
        self.classifier = nn.Sequential(
            nn.Dropout(),
            nn.Linear(256 * 6 * 6, 4096),  # Flattened feature map size is 256 * 6 * 6
            nn.ReLU(inplace=True),
            nn.Dropout(),
            nn.Linear(4096, 4096),
            nn.ReLU(inplace=True),
            nn.Linear(4096, num_classes),
        )
```

#### Dropout

`Dropout`技术是AlexNet的首创，它在训练过程中随机地“丢弃”网络中的一部分神经元，以减少模型对特定神经元的依赖，从而提高模型的**泛化能力**（防止过拟合）。具体而言，在每次训练迭代中，Dropout 会以一定的概率（通常在 0.2 到 0.5 之间）随机选择一些神经元，并将它们的输出设为零。这意味着这些神经元在这一轮训练中不会参与计算。

`nn.Linear(256 * 6 * 6, 4096)`代表输入被展平为一个$256*6*6=9216$的高维向量，并通过一次全连接的神经网络，最终输出的维度是$4096$维的向量。

最终经过多层线性全连接层的拟合之后，函数终于来到了最后一层。

### Output Layer

`nn.Linear(4096, num_classes)`，将 4096 维的特征向量转换为 `num_classes` 维的输出，网络能够为每个类别生成一个分数。通常，这些分数会通过 softmax 函数进行归一化，以便将它们转换为概率值。

> 在 PyTorch 中，通常在训练和推理阶段，softmax 操作会在损失计算时进行，而不是直接在模型结构中实现。这种做法可以避免在训练过程中出现数值不稳定的问题。

## Train The Model!

我们已经实现了卷积神经网络的**正向传播函数**，关键在于如何训练这个神经网络？

- 对于全连接层，使用正常的**梯度下降**。
- 对于卷积层，**基本思路**保持不变，在每一次迭代的过程中，比较$Y$与卷积层输出的误差平方，然后使用梯度下降来更新卷积核矩阵的参数。
- 对于池化层，如果是最大池化，只更新单个的值，如果是平均池化，需要更新卷积核对应每一个元素的值。

我们可以使用Python来实现CNN（AlexNet）的预训练过程，我们使用cifar10数据集。

## Demonstrations

在接下来的文件中，我们需要使用上面定义的AlexNet网络结构，来训练神经网络：

```python
print("Train the Basic Alexnet: session BEGIN")
import torch
import torch.nn as nn
import torch.optim as optim
from torch.utils.data import DataLoader
from torchvision import datasets, transforms
from AlexNet import AlexNet

# Check if GPU is available
device = torch.device("cuda" if torch.cuda.is_available() else "cpu")

# Hyperparameters
batch_size = 128
learning_rate = 0.001
num_epochs = 10

# Data preprocessing and augmentation
transform = transforms.Compose([
    transforms.RandomHorizontalFlip(),
    transforms.RandomCrop(32, padding=4),
    transforms.ToTensor(),
    transforms.Normalize((0.4914, 0.4822, 0.4465), (0.2023, 0.1994, 0.2010)),
])

# Load CIFAR-10 dataset
train_dataset = datasets.CIFAR10(root='./data', train=True, download=True, transform=transform)
test_dataset = datasets.CIFAR10(root='./data', train=False, download=True, transform=transform)

train_loader = DataLoader(train_dataset, batch_size=batch_size, shuffle=True, num_workers=4)
test_loader = DataLoader(test_dataset, batch_size=batch_size, shuffle=False, num_workers=4)

# Initialize the model
model = AlexNet(num_classes=10)  # CIFAR-10 has 10 classes

# Use multiple GPUs if available
if torch.cuda.device_count() > 1:
    print(f"Using {torch.cuda.device_count()} GPUs")
    model = nn.DataParallel(model)

model = model.to(device)

# Define loss function and optimizer
criterion = nn.CrossEntropyLoss()
optimizer = optim.Adam(model.parameters(), lr=learning_rate)

# Training loop
def train():
    model.train()
    for epoch in range(num_epochs):
        running_loss = 0.0
        for i, (inputs, labels) in enumerate(train_loader):
            inputs, labels = inputs.to(device), labels.to(device)

            # Zero the parameter gradients
            optimizer.zero_grad()

            # Forward pass
            outputs = model(inputs)
            loss = criterion(outputs, labels)

            # Backward pass and optimization
            loss.backward()
            optimizer.step()

            running_loss += loss.item()
            if (i + 1) % 100 == 0:  # Print every 100 batches
                print(f"Epoch [{epoch + 1}/{num_epochs}], Step [{i + 1}/{len(train_loader)}], Loss: {running_loss / 100:.4f}")
                running_loss = 0.0

# Testing loop
def test():
    model.eval()
    correct = 0
    total = 0
    with torch.no_grad():
        for inputs, labels in test_loader:
            inputs, labels = inputs.to(device), labels.to(device)
            outputs = model(inputs)
            _, predicted = torch.max(outputs, 1)
            total += labels.size(0)
            correct += (predicted == labels).sum().item()

    print(f"Accuracy on test set: {100 * correct / total:.2f}%")

if __name__ == "__main__":
    train()
    test()
```

### Output

```
Train the Basic Alexnet: session BEGIN
Using 8 GPUs
Epoch [1/10], Step [100/391], Loss: 2.0992
Epoch [1/10], Step [200/391], Loss: 1.8165
Epoch [1/10], Step [300/391], Loss: 1.6880
Epoch [2/10], Step [100/391], Loss: 1.5118
Epoch [2/10], Step [200/391], Loss: 1.4040
Epoch [2/10], Step [300/391], Loss: 1.3631
Epoch [3/10], Step [100/391], Loss: 1.2742
Epoch [3/10], Step [200/391], Loss: 1.1991
Epoch [3/10], Step [300/391], Loss: 1.1774
Epoch [4/10], Step [100/391], Loss: 1.1276
Epoch [4/10], Step [200/391], Loss: 1.1034
Epoch [4/10], Step [300/391], Loss: 1.0797
Epoch [5/10], Step [100/391], Loss: 1.0207
Epoch [5/10], Step [200/391], Loss: 1.0175
Epoch [5/10], Step [300/391], Loss: 1.0079
Epoch [6/10], Step [100/391], Loss: 0.9821
Epoch [6/10], Step [200/391], Loss: 0.9552
Epoch [6/10], Step [300/391], Loss: 0.9217
Epoch [7/10], Step [100/391], Loss: 0.8774
Epoch [7/10], Step [200/391], Loss: 0.8826
Epoch [7/10], Step [300/391], Loss: 0.8886
Epoch [8/10], Step [100/391], Loss: 0.8533
Epoch [8/10], Step [200/391], Loss: 0.8324
Epoch [8/10], Step [300/391], Loss: 0.8523
Epoch [9/10], Step [100/391], Loss: 0.7809
Epoch [9/10], Step [200/391], Loss: 0.8207
Epoch [9/10], Step [300/391], Loss: 0.8053
Epoch [10/10], Step [100/391], Loss: 0.7702
Epoch [10/10], Step [200/391], Loss: 0.7630
Epoch [10/10], Step [300/391], Loss: 0.7576
Accuracy on test set: 73.22%
```

{% note primary %}

**报错了？**我们需要做一些修改！

**输入图像的大小与 AlexNet 的架构**不匹配导致的。AlexNet 的设计是基于 ImageNet 数据集的输入图像大小（224x224）。而 CIFAR-10 数据集的图像大小是 32x32，经过 AlexNet 的卷积层和池化层后，特征图的大小变为 0，导致错误。

```python
class AlexNet(nn.Module):
    def __init__(self, num_classes=10):  # CIFAR-10 has 10 classes
        super(AlexNet, self).__init__()

        # Define the convolutional layers
        self.features = nn.Sequential(
            nn.Conv2d(3, 64, kernel_size=3, stride=1, padding=1),  # Adjusted for 32x32 input
            nn.ReLU(inplace=True),
            nn.MaxPool2d(kernel_size=2, stride=2),  # Output: 16x16

            nn.Conv2d(64, 192, kernel_size=3, padding=1),
            nn.ReLU(inplace=True),
            nn.MaxPool2d(kernel_size=2, stride=2),  # Output: 8x8

            nn.Conv2d(192, 384, kernel_size=3, padding=1),
            nn.ReLU(inplace=True),

            nn.Conv2d(384, 256, kernel_size=3, padding=1),
            nn.ReLU(inplace=True),

            nn.Conv2d(256, 256, kernel_size=3, padding=1),
            nn.ReLU(inplace=True),
            nn.MaxPool2d(kernel_size=2, stride=2),  # Output: 4x4
        )

        # Define the fully connected layers
        self.classifier = nn.Sequential(
            nn.Dropout(),
            nn.Linear(256 * 4 * 4, 4096),  # Adjusted for 4x4 feature map
            nn.ReLU(inplace=True),
            nn.Dropout(),
            nn.Linear(4096, 4096),
            nn.ReLU(inplace=True),
            nn.Linear(4096, num_classes),
        )

    def forward(self, x):
        x = self.features(x)
        x = torch.flatten(x, 1)
        x = self.classifier(x)
        return x
```

{% endnote %}

### Something Else

我们不妨问这样一个问题，**在AlexNet的学习过程中，模型学习到了什么？**

来看论文原文，Ilya是这样写的：

![Details of Learning](https://s1.imagehub.cc/images/2025/04/17/26604bda8b8525b7d2d507b3d98c4c36.png)

Figure 3 shows the convolutional kernels learned by the network's two data-connected layers. The network has learned **a variety of frequency and orientation-selective kernels**, as well as various colored blobs. Notice the specialization exhibited by the two GPUs, a result of the restricted connectivity described in Section 3.5. The kernels on GPU 1 are largely **color-agnostic,** while the kernels on on GPU 2 are largely **color-specific**. This kind of specialization occurs during every run and is independent of any particular random weight initialization (modulo a renumbering of the GPUs).

AlexNet 由于受限于当时GPU能力的限制，采用了**GPU并行训练**的方法，正如下面的图：

![AlexNet](https://s1.imagehub.cc/images/2025/04/17/5cbb1e56d8ddde319055009365e19819.png)

## Variant

在2012年的ImageNet图像识别竞赛中，AlexNet **一战成名**，以极大的优势豪取第一名，并从此掀起了深度学习的热潮。但是人类探索的脚步不止于此！在之后的七届竞赛中，**更多更加新颖更加复杂的网络结构**不断被设计出来，在测试机上的表现高于AlexNet！

他们包括：

- 使用重复块的网络（VGG）
- 网络中的网络（NiN）
- GoogLenet
- ResNet & ResNeXt（**残差神经网络**）
- DenseNet

这一部分的内容也非常的精彩，不过在这里就不介绍啦~

## Conclusion

本文主要以**AlexNet**为载体介绍了**最基本且传统的卷积神经网络架构**，从卷积的数学原理和定义出发，再到卷积神经网络的卷积层、池化层和全连接层等。在讲解完卷积神经网络的网络结构之后，有深入分析了卷积神经网络的训练细节，以及卷积核的可视化过程。

> 内容相对比较基础，但是一切的灵感都建立在扎实的基础之上，不是吗？

