---
title: "LLML CS336 Lecture 3.1: Architectures for Transformers"
date: 2025-08-19 23:10:41
index_img: /img/cover/cs33631.jpg
excerpt: "Lecture Notes for CS336, Lecture 3.1 Architectures for transformers, focusing on various optimizations. This blog introduces different variations of transformer architecture, including layernorm, activation functions and positional embeddings."
math: True
categories:
  - Artificial Intelligence
  - LLM
tags:
  - Finished
  - Tutorial
  - LLM
  - Architectures
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Lecture 3.1: Architecture for Transformer

## Introduction

开山鼻祖 Attention is all you need 针对的事机器翻译的问题而提出的，并非完全针对大语言模型。因此，后续大语言模型的调优过程在架构层面做了许多创新性的优化，也带来性能上的大幅度提升。本文将逐一介绍之。

## Variations of transformers

Starting with the original transformer...

* (Masked) MultiHead Attention
* Positional Embeddings
* FFN (MLP), with RELU activation function.
* LayerNorm

However, the original transformer is designed for machine translation, which are viewed as a variant of RNNs. Nowadays, different models have different architecture design in detail:

| Name | Year | LayerNorm | Parallel Layer | Pre-norm | Position embedding | Activations | Stability tricks |
| --- | --- | --- | --- | --- | --- | --- | --- |
| Original transformer | 2017 | LayerNorm | Serial | ❌ | Sine | ReLU |  |
| GPT | 2018 | LayerNorm | Serial | ❌ | Absolute | GeLU |  |
| T5 (11B) | 2019 | RMSNorm | Serial | ✅ | Relative | ReLU |  |
| GPT2 | 2019 | LayerNorm | Serial | ❌ | Absolute | GeLU |  |
| GPT2 | 2020 | RMSNorm | Serial | ✅ | Relative | GeGLU |  |
| T5 (XXL 11B) v1.1 | 2020 | RMSNorm | Serial | ✅ | Relative | GeGLU |  |
| mT5 | 2020 | RMSNorm | Serial | ✅ | Relative | GeGLU |  |
| GPT3 (175B) | 2020 | LayerNorm | Serial | ✅ | Absolute | GeLU |  |
| GPTJ | 2021 | LayerNorm | Parallel | ✅ | RoPE | GeLU |  |
| LaMDA | 2021 |  |  |  | Relative | GeGLU |  |
| Anthropic LM (not claude) | 2021 |  |  |  |  |  |  |
| Gopher (280B) | 2021 | RMSNorm | Serial | ✅ | Relative | ReLU |  |
| GPT-NeoX | 2022 | LayerNorm | Parallel | ✅ | RoPE | GeLU |  |
| BLOOM (175B) | 2022 | LayerNorm | Parallel | ✅ | Alibi | GeLU |  |
| OPT (175B) | 2022 | LayerNorm | Serial | ❌ | Absolute | ReLU |  |
| PaLM (540B) | 2022 | RMSNorm | Parallel | ✅ | RoPE | SwiGLU | Z-loss |
| Chinchilla | 2022 | RMSNorm | Serial | ✅ | Relative | ReLU |  |
| Mistral (7B) | 2023 | RMSNorm | Serial | ✅ | RoPE | SwiGLU |  |
| LLaMA2 (70B) | 2023 | RMSNorm | Serial | ✅ | RoPE | SwiGLU |  |
| LLaMA (65B) | 2023 | RMSNorm | Serial | ✅ | RoPE | SwiGLU |  |
| GPT4 | 2023 |  |  | ❌ |  |  |  |
| Olmo 2 | 2024 | RMSNorm | Serial | ❌ | RoPE | SwiGLU | Z-loss, QK-norm |
| Gemma 2 (27B) | 2024 | RMSNorm | Serial | ✅ | RoPE | GeGLU | Logit soft capping, Pre+post norm |
| Nemotron-4 (340B) | 2024 | LayerNorm | Serial | ✅ | RoPE | SqReLu |  |
| Qwen 2 (72B) - same for 2.5 | 2024 | RMSNorm | Serial | ✅ | RoPE | SwiGLU |  |
| Falcon 2 11B | 2024 | LayerNorm | Parallel | ✅ | RoPE | GeLU | Z-loss |
| Ph3 (small) - same for ph4 | 2024 | RMSNorm | Serial | ✅ | RoPE | SwiGLU |  |
| Llama 3 (70B) | 2024 | RMSNorm | Serial | ✅ | RoPE | SwiGLU |  |
| Reka Flash | 2024 | RMSNorm | Serial | ✅ | RoPE | SwiGLU |  |
| Command R+ | 2024 | LayerNorm | Parallel | ✅ | RoPE | SwiGLU |  |
| OLMo | 2024 | RMSNorm | Serial | ✅ | RoPE | SwiGLU |  |
| Qwen (14B) | 2024 | RMSNorm | Serial | ✅ | RoPE | SwiGLU |  |
| DeepSeek (67B) | 2024 | RMSNorm | Serial | ✅ | RoPE | SwiGLU |  |
| Yi (34B) | 2024 | RMSNorm | Serial | ✅ | RoPE | SwiGLU |  |
| Mixtral of Experts | 2024 |  |  | ❌ |  |  |  |
| Command A | 2025 | LayerNorm | Parallel | ✅ | Hybrid (RoPE+NoPE) | SwiGLU |  |
| Gemma 3 | 2025 | RMSNorm | Serial | ✅ | RoPE | GeGLU | Pre+post norm, QK-norm |
| SmolLM2 (1.7B) | 2025 | RMSNorm | Serial | ✅ | RoPE | SwiGLU |  |

## Normalization

### PreNorm vs PostNorm

Several discussions for PreNorm and PostNorm:

* [Discussion](https://kexue.fm/archives/9009)

**TLDR**: With the same parameter settings, pre-norm can be trained more easily than post-norm, however, the final performance does not outweigh post-norm.(or exactly the same for the pre-training process.)

However, for the post-training fine-tuning, the post-normalization architecture has better transfer performance.

> However, the gradients of Pre-LN at bottom layers tend to be larger than at top layers, leading to a degradation in performance compared with Post-LN.

But for pre-norm, which can keep the good parts of residual connections, with nicer gradient propagation and fewer spike.


Maybe we can do Double Norm ;)


### Layer Norm & RMS Norm

#### Layer Normalization

在层归一化中，我们沿着 **特征维度 ($D$)** 计算统计量，对每个 **样本 ($N$)** 和 **序列位置 ($L$)** 进行归一化。
这意味着 $\mu$ 和 $\sigma^2$ 是针对每个样本 $n$ 和每个序列位置 $l$ 计算的。

* **均值 $\mu_{n,l}$：**
  $$\mu_{n,l} = \frac{1}{D} \sum_{d=1}^D x_{n,l,d}$$
* **方差 $\sigma_{n,l}^2$：**
  $$\sigma_{n,l}^2 = \frac{1}{D} \sum_{d=1}^D (x_{n,l,d} - \mu_{n,l})^2$$
* **缩放因子和平移因子：** $\gamma$ 和 $\beta$ 在所有样本和序列位置上共享，但通常也是与特征维度 $D$ 相同长度的向量，即 $\gamma, \beta \in \mathbb{R}^{D}$。
  因此，对于每个 $x_{n,l,d}$：
  $$y_{n,l,d} = \gamma_d \left( \frac{x_{n,l,d} - \mu_{n,l}}{\sqrt{\sigma_{n,l}^2 + \epsilon}} \right) + \beta_d$$

> 在 Transformer 等序列建模模型中，因为序列长度 $N$ 可变（并且是频繁变化），因此为了提升模型的鲁棒性，往往采用 **Layer Normalization** 的方式实现归一化。


#### RMS Norm

RMSNorm, which stands for **Root Mean Square Normalization**, is a layer normalization technique used in some transformer architectures. It's a simpler and more computationally efficient alternative to traditional Layer Normalization.

The core principle of RMSNorm is to **re-scale the activations of a layer based on their root mean square (RMS)** value, rather than their mean and standard deviation.

For a given input vector $x$ (the activations from a layer), the RMSNorm operation is defined as:

> The normalization also happens in the dimensional feature.

$$y_{n,l,d} = \gamma_d \left( \frac{x_{n,l,d}}{\sqrt{\frac{1}{D} \sum_{i=1}^{D} x_{n,l,i}^2 + \epsilon}} \right)$$

**RMSNorm** calculates the root mean square, which is essentially the L2 norm divided by the square root of the dimension. This method is simpler as it **doesn't subtract the mean**, which is the most computationally expensive part of Layer Normalization. This makes RMSNorm more **efficient and faster**, especially in large-scale models (Runtime: Memory Movement). It normalizes the magnitude of the activations, which is often sufficient for stabilizing training.

* also: it drops bias terms for memory and optimization stability.


## Activations

### Activation Functions

For non-linear components in the neural network.

### GLU (Gated Activations)

Original Feed Forward Network: $FF(x) = \max(0, xW_1)W_2$.

With GLU:

$$\max(0, xW_1) \to \max(0, xW_1) \otimes (xV)$$

$\otimes$ means element-wise operations, the former part of GLU is also the RELU function, and the latter let the input $x$ passing through the matrix $V$ and controls which message can be passed into this successfully.

$$\text{FF}_{\text{REGLU}}(x) = (\max(0, xW_1) \otimes (xV) ) W_2$$

### SwiGLU Mathematical Formula

SwiGLU, which stands for **Swish-Gated Linear Unit**, is a variant of the Gated Linear Unit (GLU) family. It was introduced by Google Brain in the PaLM model and has since been adopted by other modern large language models, such as LLaMA, as the activation function within their feed-forward networks (FFN). It has been shown to outperform both ReLU and GeLU in terms of performance.

SwiGLU functions as a sub-module within the feed-forward network. Its core idea is to **replace the traditional single-path activation with a two-path structure controlled by a "gate."**.

**SwiGLU-based FFN**:
$$FFN(x) = \text{SwiGLU}(xW_1, xW_2)W_3$$

Here, $x$ is the input vector, and $W_1, W_2, W_3$ are learnable weight matrices.

The core operation of SwiGLU is defined as:

$$\text{SwiGLU}(x, W_1, W_2) = (\text{Swish}(xW_1)) \otimes (xW_2)$$

Where:

* $x$ is the input vector.
* $W_1$ and $W_2$ are two distinct weight matrices.
* **$\text{Swish}(\cdot)$** is the activation function defined by:
  $$\text{Swish}(z) = z \cdot \sigma(z)$$
  where $\sigma(z)$ is the Sigmoid function:
  $$\sigma(z) = \frac{1}{1 + e^{-z}}$$
* **$\otimes$** denotes **element-wise multiplication**.

1. **Path 1 (Activation Path)**: The input vector $x$ is multiplied by the weight matrix $W_1$ to produce a vector $z_1 = xW_1$. This vector is then passed through the Swish activation function: Swish($z_1$).
2. **Path 2 (Gating Path)**: The input vector $x$ is multiplied by a separate weight matrix $W_2$ to produce another vector $z_2 = xW_2$.
3. **Gating**: The result from Path 1, Swish($z_1$), is multiplied element-wise by the result from Path 2,$z_2$.

This element-wise multiplication is the key "gating" mechanism. The second path, $z_2 = xW_2$, acts as a gate, dynamically adjusting the weight of each element in the activated vector from Path 1 based on the input $x$.

* **Dynamic Information Flow**: The gating design allows the network to learn which features should be amplified or suppressed, providing a more precise way to control information flow. This adaptive gating ability helps the model better capture complex dependencies.
* **Smoother Gradients**: Unlike ReLU's hard cutoff (with a gradient of 0 or 1), the Swish function has a smooth gradient. This helps in mitigating the vanishing gradient problem in deep networks, leading to more stable optimization.


## Serial vs Parallel layers

Original serialized attention mechanism cannot do parallel computations.

* $y = x + \text{MLP}(\text{LayerNorm}(x + \text{Attention}(\text{LayerNorm}(x))))$
* $y = x + \text{MLP}(\text{LayerNorm}(x)) + \text{Attention}(\text{LayerNorm}(x))$


## Positional Embeddings

### Basic Positional Encoding


注意力机制抛弃了串行式的词元处理状态，采用并行化的设计而牺牲了序列信息，例如，在计算注意力分数的时候交换不同键值对的位置在形式上对最后的 Attention Score 没有影响，这会影响最后建模的序列性。

因此，需要加入额外的项来表示**序列的顺序信息**，即在输入中添加 Positional Encoding 来注入绝对的或者相对的位置信息。下面介绍一种最常见的位置编码：**基于正余弦的固定位置编码**。

输入 $\mathbf{X} \in \mathbb{R}^{n \times d}$，考虑嵌入矩阵 $\mathbf{P} \in \mathbb{R}^{n \times d}$，最终输出的结果是 $\mathbf{X} + \mathbf{P}$。其中：

$$
p_{(i, 2j)} = \sin(\frac{i}{10000^{2j/d}}), \ p_{(i, 2j+1)} = \cos(\frac{i}{10000^{2j/d}})
$$

#### 绝对位置信息

在二进制中，较高位的交替频率低于较低位，因此，位置编码效仿这样的设计，随着编码维度的增加（列，代表编码位置的不同维度），因此，对于不同的行，（行即代表着序列的原始位置信息），横坐标 $i$ 对应的位置编码矩阵在不同的列 $j$ 下的值都不相同。

> $j$ 可以看做控制三角函数的频率信息（以 $i$ 为自变量），而频率不同可以很好的在一个周期性函数上表示非周期的特征，简单来说，**每一个行学习到的 `p[i, :]` 都是不同的**。


#### 相对位置信息

使用固定位置编码也可以学习到**相对位置的特征**，例如一些词组的特定搭配等等，从数学语言解释：**对于任何确定的位置偏移量 $\delta$，位置 $\delta + i$ 处的位置编码可以用线性投影 $i$ 处的位置编码通过一个与位置无关的线性投影 $M$ 得到**。其中：

$$M = \begin{bmatrix}
  \cos(\delta\omega_j)& \sin(\delta\omega_j) \\
  -\sin(\delta\omega_j) & \cos(\delta\omega_j)
\end{bmatrix}$$

{% note info %}

上文的线性投影矩阵也具有几何意义，考虑线性变换：

$$\left[\begin{array}{l}
x^{\prime} \\
y^{\prime}
\end{array}\right]=\left[\begin{array}{cc}
\cos \left(\delta \omega_{j}\right) & \sin \left(\delta \omega_{j}\right) \\
-\sin \left(\delta \omega_{j}\right) & \cos \left(\delta \omega_{j}\right)
\end{array}\right]\left[\begin{array}{l}
x \\
y
\end{array}\right]$$

$$\begin{array}{l}
x^{\prime}=x \cos \left(\delta \omega_{j}\right)+y \sin \left(\delta \omega_{j}\right) \\
y^{\prime}=-x \sin \left(\delta \omega_{j}\right)+y \cos \left(\delta \omega_{j}\right)
\end{array}$$

其中 $\omega_j = \frac{1}{10000^{2j/d}}$。

这代表着坐标点 $(x^{\prime}, y^{\prime})$ 是原坐标点 $(x,y)$ 经过旋转 $\delta\omega_j = \frac{\delta}{10000^{2j/d}}$ 个弧度制得来的。在这个语境下，指的是从 $(p_{(i,2j)}, p_{(i, 2j+1)})$ 到 $(p_{(i + \delta,2j)}, p_{(i + \delta, 2j+1)})$ 的线性变换过程。

{% endnote %}

### Dive Deeper into Sine Embeddings

Sine Embeddings, 或者叫做 Sinusoidal Embeddings，是一种对输入向量进行位置编码的方式，这种**基于正弦函数**的位置编码也是最先在 Attention is all you need 论文中提出的方法。

下文的一些摘录节选自 [Transformer 升级之路](https://spaces.ac.cn/archives/9675)，并且加上自己的注解。

位置编码重要性不必多言，在自回归模型 Attention Score 计算的过程中，其模型是**全对称的**，满足恒等式 $f(x,y) = f(y,x)$，这会对最终的序列预测任务造成困扰，因此，需要引入**位置编码**在学习相关性的基础上学习不同词元的序列特征。而这里的序列特征指的是**绝对序列特征**和**相对序列特征**。

为了打破对称性，位置编码改变了自注意力层的函数输入：

$$f'(x) = (\cdots, x_m, \cdots, x_n, \cdots) = f(\cdots, x_m + p_m, \cdots, x_n+p_n, \cdots) $$

> 这里采用**加**作为注入位置信息的方式，计算简单并且容易学习，但是也会在训练过程中带来问题，后面会详细讲到这个点。

只考虑这两个位置，并进行泰勒展开：

$$\tilde{f} \approx f + \boldsymbol{p}_m^\top \frac{\partial f}{\partial \boldsymbol{x}_m} + \boldsymbol{p}_n^\top \frac{\partial f}{\partial \boldsymbol{x}_n} + \frac{1}{2} \boldsymbol{p}_m^\top \frac{\partial^2 f}{\partial \boldsymbol{x}_m^2} \boldsymbol{p}_m + \frac{1}{2} \boldsymbol{p}_n^\top \frac{\partial^2 f}{\partial \boldsymbol{x}_n^2} \boldsymbol{p}_n + \underbrace{\boldsymbol{p}_m^\top \frac{\partial^2 f}{\partial \boldsymbol{x}_m \partial \boldsymbol{x}_n} \boldsymbol{p}_n}_{\boldsymbol{p}_m^\top \mathcal{H} \boldsymbol{p}_n}$$

之前的项要么只有 $p_m$，要么只有 $p_n$，要么两个都没有（注意，只有$p$存储了位置信息，$x_n$, $x_m$本身只存储了语义信息），因此表达的是绝对信息。（因为只依赖于单一位置），而最后一项同时出现了 $p_m$ 和 $p_n$，因此这是**模型存储相对位置信息的关键**。

$\frac{\partial^2 f}{\partial \boldsymbol{x}_m \partial \boldsymbol{x}_n}$ 这一项记为矩阵 $\mathcal{H}$，因此我们的目标项是 $p_m^\top \mathcal{H} p_n = g(m-n)$，而我们需要找到符合条件的编码 $p_m$, $p_n$。

> 当 $\mathcal{H}$ 为对角矩阵的时候，下面的解可以严格的被表示为 $g(m-n)$，但是对于一般的矩阵，这样的性质是不成立的，我们只能希望 $\mathcal{H}$ 的对角线占主元。

具体的计算过程详见：[Original Blog](https://spaces.ac.cn/archives/8231)，简直是极为精彩的推导过程！最终，我们从 $d = 2$ 的简单复数情形出发，一步步推导到高维偶数编码的一个可行解：

$$p_m = \begin{pmatrix}
\cos m\theta_0
\\ \sin m \theta_0
\\ \cos m \theta_1
\\ \sin n \theta_1
\\ \cdots
\\ \cos m \theta_{d/2-1}
\\ \sin m \theta_{d/2-1}
\end{pmatrix}$$

更深一步，这样的位置编码可以抽象为一种$\beta$进制的编码过程。

一个数 $n$ 在 $\beta$ 进制下的第 $m$ 位：$x_m = \left\lfloor\frac{n}{\beta^{m-1}}\right\rfloor \bmod \beta$

$$\left[\cos \left(\frac{n}{\beta^{0}}\right), \sin \left(\frac{n}{\beta^{0}}\right), \cos \left(\frac{n}{\beta^{1}}\right), \sin \left(\frac{n}{\beta^{1}}\right), \cdots, \cos \left(\frac{n}{\beta^{d / 2-1}}\right), \sin \left(\frac{n}{\beta^{d / 2-1}}\right)\right]$$

$$\beta = 10000^{\frac{2}{d}}$$

取模本质上也是一个周期函数（和 $\sin$,$\cos$如出一辙。）因此，在不同位置的向量做编码，会携带的信息是位置 $n$ 在特定数位进制（$\beta = 10000^{\frac{2}{d}}$ 是一个超参数）下的表示。这样的进制编码信息本身就携带了相对位置信息和绝对位置信息。

* sine embeddings
* absolute embeddings
* relative embeddings
* rope embeddings

**Relative positions encoding**: $\langle f(x, i), f(y, j) \rangle = g(x, y, i - j)$

Where $\langle .,. \rangle$ means the dot product(which is the attention score) for two different tokens. $g$ is a function which consider **relative positional embedding** as its input.

For the original sime embeddings: $\text{Embed}(x, i) = v_x + \text{Positional Embedding}(\text{pos})$

$$\begin{aligned}
\langle \text{Embed}(x, i), \text{Embed}(y, j) \rangle &= \langle v_x + PE_i, v_y + PE_j \rangle \\
&= \langle v_x, v_y \rangle + \langle v_x, PE_j \rangle + \langle PE_i, v_y \rangle + \langle PE_i, PE_j \rangle
\end{aligned}$$

$\langle v_x, v_y \rangle + \langle v_x, PE_j \rangle + \dots$, these are cross-terms that are not relative.

Thus, for the original traditional embeddings, in face of mountains of training data, the model tends to memorize the absolute position instead of the relative position, which would cause a rise of perplexity outside the boundary of training data.


### RoPE

#### Intuition

Unlike the simple adding operation ($v_x + \text{PE}(i)$), RoPE tries to "rotate" the vector, which could perfectly satisfy the "relative position" property.

Consider the most simple situation:

$x = (x_0, x_1) \in \mathbb{R}^2$, we can compute $x'$ with a rotation matrix:

$$R_{\theta}=\left(\begin{array}{cc}
\cos \theta & -\sin \theta \\
\sin \theta & \cos \theta
\end{array}\right)$$

$$x' = R_\theta x$$

The rotation of angles and doc product will only focus on the relative rotation!

$$\langle R_\alpha a, R_\beta b \rangle = \langle R_{(\alpha - \beta)} a, b \rangle$$

#### Algorithm

Consider a word vector (we define it as $\{q,k\} = W_{\{q, k\}} x_m$), then we can apply the rotation matrix to the word vector in position $m$:

$$\{q, k\}_m = R_{\Theta, m}^d \cdot \{q, k\}$$

* Before the rotation, the vector does not contain any positional information. (only with semantic meaning)
* After the rotation, the vector contain the positional information with the rotated angle. $\theta_m$
* The core component is the rotation matrix: $R_{\Theta, m}^d$

Of course, we know:

$$R_{\Theta, m}^2= \begin{pmatrix} \cos(m\theta_j) & -\sin(m\theta_j) \\ \sin(m\theta_j) & \cos(m\theta_j) \end{pmatrix}$$


> 下面的内容用中文，因为太精彩了。

为了简化，我们在后续使用**复数运算**来代替对二维向量的运算（这两个是等价的），并且我们考虑 $d$ 是一个偶数，这样一个维度为 $d$ 的高维向量就可以被切割为 $\frac{d}{2}$ 个二维向量。（对于 $q$ 和 $k$）

令 $N = \frac{d}{2}$，则原始的高维向量就被表示为了 $N$ 个复数表示：$\{z_1, z_2, \dots, z_j, \dots, z_N\}$, $z_j = x_{2j-1} + x_{2j} i$

则旋转的过程可以表示为：

$$z_j' = z_j \cdot e^{im\theta_j}$$

$$\theta_j = \frac{1}{10000^{2(j-1)/d}}$$

来一点数学：

$$z_{j}^{\prime}=z_{j} \cdot e^{i m \theta_{j}}=\left(x_{2 j-1}+i x_{2 j}\right)\left(\cos \left(m \theta_{j}\right)+i \sin \left(m \theta_{j}\right)\right)$$

$$z_{j}^{\prime}=\left(x_{2 j-1} \cos \left(m \theta_{j}\right)-x_{2 j} \sin \left(m \theta_{j}\right)\right)+i\left(x_{2 j-1} \sin \left(m \theta_{j}\right)+x_{2 j} \cos \left(m \theta_{j}\right)\right)$$

其实，这就是一个二维向量 $(x_{2j-1}, x_{2j})$ 在施加旋转矩阵 $R_{\Theta, m}^2$之后的结果。

最终把向量的维度整合，$\{q,k\}_m$ 的结果就是对 $N$ 个二维向量分别施加旋转矩阵之后的结果，这些二维矩阵因为 $j = 1,2,\dots, N$ 的选择取值不同因此带来旋转的角度也不同。

当然，实际过程中不会具体地做高维向量的切割和合并，而是整合成一个大的分块矩阵：

$$
\mathbf{R}_{\Theta,m}^d =
\begin{pmatrix}
\cos(m\theta_1) & -\sin(m\theta_1) & 0 & 0 & \dots \\
\sin(m\theta_1) & \cos(m\theta_1) & 0 & 0 & \dots \\
0 & 0 & \cos(m\theta_2) & -\sin(m\theta_2) & \dots \\
0 & 0 & \sin(m\theta_2) & \cos(m\theta_2) & \dots \\
\vdots & \vdots & \vdots & \vdots & \ddots
\end{pmatrix} \in \mathbb{R}^{d \times d}
$$

在施加旋转位置编码之后的高维向量就携带着相对位置信息进入 Attention 模块进行参数学习。

#### Proof

RoPE 的精妙之处在于，经过旋转操作后的两个向量 $\mathbf{q}$ 和 $\mathbf{k}$，它们的内积只依赖于它们之间的相对位置 $m-n$。

在注意力机制中，查询向量 $\mathbf{q}$ 和键向量 $\mathbf{k}$ 分别对应位置 $m$ 和 $n$。RoPE 对它们进行旋转操作：

* 旋转后的查询向量：$\mathbf{q}_m = \mathbf{R}_m \mathbf{q}$
* 旋转后的键向量：$\mathbf{k}_n = \mathbf{R}_n \mathbf{k}$

它们之间的内积为：
$$
\langle \mathbf{q}_m, \mathbf{k}_n \rangle = \langle \mathbf{R}_m \mathbf{q}, \mathbf{R}_n \mathbf{k} \rangle
$$
利用旋转矩阵的性质，即 $\mathbf{R}_m^T = \mathbf{R}_{-m}$，以及 $\mathbf{R}_m^T \mathbf{R}_n = \mathbf{R}_{n-m}$，我们可以得到：
$$
\langle \mathbf{R}_m \mathbf{q}, \mathbf{R}_n \mathbf{k} \rangle = \langle \mathbf{q}, \mathbf{R}_m^T \mathbf{R}_n \mathbf{k} \rangle = \langle \mathbf{q}, \mathbf{R}_{n-m} \mathbf{k} \rangle
$$
这个结果表明，旋转后向量的内积（即注意力分数）只取决于相对位置 $n-m$，而不是绝对位置 $m$ 和 $n$。这正是 RoPE 能够有效编码相对位置信息的关键。


#### More Than That...

相关资料参考：

* [Paper: Roformer](https://arxiv.org/abs/2104.09864)
* [Transformer 升级之路之 RoPE 编码](https://spaces.ac.cn/archives/9675)
* [Transformer 升级之路之 位置编码](https://spaces.ac.cn/archives/8231)
