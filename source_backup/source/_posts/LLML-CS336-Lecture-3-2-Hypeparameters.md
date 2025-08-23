---
title: "LLML CS336 Lecture 3.2: Hyperparameter for Transformers"
date: 2025-08-21 17:47:23
index_img: /img/cover/cs3363.jpg
excerpt: "Lecture Notes for CS336, Lecture 3.2 Hyperparameters for transformers."
math: True
categories:
  - Artificial Intelligence
  - LLM
tags:
  - Finished
  - Tutorial
  - LLM
  - Resource Accounting
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Hyperparameter



# CS 336 Lecture 3.2 Hyperparameter & Training Techniques for Large Language Models


## Hyperparameter for FFN Layer

For the feed forward layer for the transformer block:

$$\text{FFN}(x) = \max(0, (xW_1 + b_1)W_2 + b_2)$$

The dimension of this block is a kind of hyperparameter settings:

$$d_{\text{ff}} = 4 \times d_{\text{model}}$$

### Variants: GLU

Remember that GLU variants scale down by 2/3rd. This means most GLU variants have $d_{\text{ff}} = 8/3 \times d_{\text{model}}$


### Variants: T5

$$d_{\text{ff}} =  65536$$

$$d_{\text{model}} = 1024$$


## Ratio for Transformer Blocks

在 Transformer 模型中，一般使用两个参数量来描述模型本身：

- 模型的**深度**：即 $\text{Layer}$ 的层数，在解码器中模型会堆叠多少层的结构。
- 模型的**宽度**：即在多头注意力层中 $d_{\text{model}}$ 的层数和 FFN 层中的隐藏层的维度。当然也包括多头注意力中头的数量。

Several Model Config Settings:

| | Num heads | Head dim | Model dim | Ratio |
| :--- | :--- | :--- | :--- | :--- |
| **GPT3** | 96 | 128 | 12288 | 1 |
| **T5** | 128 | 128 | 1024 | 16 |
| **T5 v1.1** | 64 | 64 | 4096 | 1 |
| **LaMDA** | 128 | 128 | 8192 | 2 |
| **PaLM** | 48 | 258 | 18432 | 1.48 |
| **LLaMA2** | 64 | 128 | 8192 | 1 |

在这些参数设定中，$\text{ratio} = \frac{d_{\text{model}}}{n_{\text{layer}}}$ 是非常重要的一个模型参数。

### Aspects for parallelism

-  因为 Transformer 的层数的堆叠依赖线性的输入顺序，因此难以并行。只能做数据并行，而不能把不同的层放在不同的设备上同时做训练。

- 而提升宽度对并行化来说即为容易：当维度非常大时，你可以将一个大的矩阵乘法（例如，Weight × Input）操作分解成多个小的矩阵乘法，然后将这些小的操作分配到不同的设备上并行计算。这种技术被称为张量并行（Tensor Parallelism）或流水线并行（Pipeline Parallelism），它可以有效地利用多台机器或多个 GPU。





## Regularization

For the pre-training process, do we need dropout and other regularization method?

- DropOut

- WeightDecay: L2 Regularization penalty.


### Why Weight Decay LLMs?

Relevant Papers: https://arxiv.org/abs/2310.04415

For the amount of training data during pre-training much more than the total amount of parameters, thus there is no need for large language models to prevent over fitting.

However, the adjust of learning rate during training is quite important, and weight decay (such as cos LR weight decay) can help adjust learning rate and optimize losses at the end of the pre-training process.


#### Cos LR Decay

**余弦退火学习率调度**（Cosine Annealing Learning Rate Schedule），这是一种非常流行的学习率衰减策略。它通过一个余弦函数来调整学习率，而不是像传统的步长衰减（Step Decay）或指数衰减（Exponential Decay）那样进行线性或阶梯式的衰减。在深度学习的训练中，我们通常希望在训练初期使用较大的学习率，以快速接近损失函数的局部或全局最优解。随着训练的进行，为了更精确地收敛到最优解，我们需要逐渐减小学习率，以避免在最优解附近来回震荡。余弦退火策略正是利用了这一点，它将学习率的变化过程模拟成余弦函数曲线的一部分。


余弦退火学习率的计算公式通常如下：

$$\eta_t = \eta_{min} + \frac{1}{2}(\eta_{max} - \eta_{min})(1 + \cos(\frac{T_{cur}}{T_{max}}\pi))$$

其中：
* $\eta_t$：在当前训练步（或 epoch）$t$ 的学习率。
* $\eta_{min}$：学习率的最小值。
* $\eta_{max}$：学习率的最大值（通常是初始学习率）。
* $T_{cur}$：当前训练步数（或 epoch）。
* $T_{max}$：整个衰减周期的总步数（或总 epoch 数）。

余弦函数的特点是：在开始时下降缓慢，在中间阶段下降速度最快，在结束时再次下降缓慢。

当我们将这个函数应用于学习率时，会得到一个非常平滑的衰减曲线：
* **训练初期**：学习率下降缓慢，允许模型进行大步的参数更新，快速探索解空间。
* **训练中期**：学习率下降加速，使得模型能够更稳定地收敛。
* **训练末期**：学习率再次下降缓慢，允许模型在最优解附近进行精细调整，从而找到更精确的解。

这种平滑的衰减模式被认为比简单的线性或阶梯式衰减更有助于模型的稳定训练和最终收敛。

余弦退火策略最早在论文 **"SGDR: Stochastic Gradient Descent with Warm Restarts"** 中被提出。这篇论文不仅提出了余弦退火，还引入了**周期性重启**（Periodic Restarts）的概念。

* **SGDR** 的核心思想是，每隔一段时间，将学习率突然重置回初始的最大值，然后再次按照余弦函数衰减。
* 这就像是训练过程中给模型一个“冲击”，帮助它从局部最优解中“跳出”，从而有机会找到更好的全局最优解。

因此，当提到“Cos LR decay”时，它可能指：
1.  **单纯的余弦退火**：学习率从最大值平滑衰减到最小值，直到训练结束。
2.  **带重启的余弦退火（SGDR）**：学习率周期性地进行余弦衰减和重启。


## Stable Training

For the pre-training process, training stability is quite important.

### Be aware of softmax

While the softmax is combined with cross-entropy loss, it will cause unstability!

- **Overflow** for exponential: when dealing with large number input $x$, the exponential result for $e^x$ may be extremely large. (Larger than the upper bound for the float number)

- `inf` problems: divide by zero.

### z-loss

回顾 Softmax 的基础公式：

$$P_i = \frac{e^{z_i}}{\sum^{K}_{j=1} e^{z_j}}$$

$$\log P_i = z_i - \log (\sum^{K}_{j=1} e^{z_j})$$

对于输入样本 $x$，定义维度 $i$ 的值 $z_i$ 为 $U_{i}(x)$，最终 Softmax 输出的分布向量为 $P(x)$，则公式可以被修正为：

$$P(x)_r = U_r(x) - \log (\sum^{|V|}_{r' = 1} U_{r'}(x))$$

但是当Softmax 和二进制交叉熵损失放在一起之后，就会出现上述的训练不稳定的问题（归根到底还是基于梯度的优化器对数值范围非常敏感），因此，对于优化过程，需要**调整损失函数**来提升稳定性。

对于原始的二进制交叉熵损失函数：

$$L_{\text{CE}} = \sum_{i} \log P(x_i)$$

> 这里没有加符号，因为是最小化损失。

Z-loss 的核心是惩罚 Softmax 归一化常数 Z 的对数，即 $\log^2(Z) = \log^2(\sum^{K}_{j=1} e^{z_j})$。通过将这个惩罚项加入到总损失函数中，模型在训练过程中会被激励去限制 logits 的整体幅度，防止它们变得过大。这样就可以一定程度避免因为指数项过大导致的溢出问题。

因此，在 Z-loss 中，加入了这一项惩罚项：

$$L_{\text{ZLoss}} = \alpha \sum_i\log^2(Z(x_i))$$

$$L' = \sum_{i} \log P(x_i) - \alpha \sum_i\log^2(Z(x_i))$$


### QK-Norm

Normalization 本身就是提升训练稳定性的一种有效的方式，因此一种很常见的技巧是在 Attention 的核心模块，对Q,K矩阵先做一次 RMS Norm，在送入批量矩阵乘法之后再做 softmax。这个步骤被嵌入了多头注意力的模块内部。


### Logit SoftCapping

同样是基于 Softmax 的大数溢出问题的优化，使用 $\tanh$ 函数。

具体而言，对于 Softmax 操作之前做如下的优化：

$$x \longleftarrow \text{soft cap} \times \tanh(\frac{x}{\text{soft cap}})$$

对于较小的输入，因为 $\tanh$ 在 $x = 0$ 处的点近似线性，因此不受影响，运算几乎等价，但是在远处的点受限于函数本身的值域限制，就保证了在 Softmax 层输入的时候不会有太大的数。同时引入超参数 $\text{soft cap}$ 可以调节缩放的scaling。





