---
title: AINN Transformer
date: 2025-07-28 11:59:43
index_img: /img/cover/Transformer.jpg
excerpt: The introduction and explanation for the Basic Neural Network Transformer.
math: true
categories:
  - Artificial Intelligence
  - AINN
tags:
  - Artificial Intelligence
  - Deep Learning
  - Neural Networks
  - Transformer
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Transformer (is all you need?)

## Introduction

本文所有的内容和原始论文：Attention is All You Need的设计保持一致。Transformer最开始的论文（**Attention is all you need**）的任务其实是聚焦在**Machine Translation**上的，看摘要：

{% note primary %}

Our model achieves 28.4 BLEU on the WMT 2014 **English-to-German translation task**, improving over the existing best results, including ensembles, by over 2 BLEU. On the WMT 2014 English-to-French translation task, our model establishes a new single-model state-of-the-art BLEU score of 41.8 after training for 3.5 days on eight GPUs, a small fraction of the training costs of the best models from the literature.

{%  endnote %}

## Preliminaries

{% note primary %}

The dominant **sequence transduction models** are based on complex recurrent or convolutional neural networks that include **an encoder and a decoder**.

{% endnote %}

### Attention Mechanism

See this blog for more info: [Attention is all you need?](https://xiyuanyang-code.github.io/posts/AINN-Attention/)

{% note primary %}

It will cover:

- Intuitive Attention Machnism: the basic design of key, value and query.

- Attention Pooling Algorithm: Calculate the correlation of each key and the query.

- Attention Scoring Function:

    - Masked Softmax Operation

    - Additive Attention 

    - Scaled Dot-product Attention

- MultiHead Attention

- Self Attention

- Positional Encoding

{% endnote %}

### Seq2Seq

问题的大背景是序列预测问题，即给定一个序列 $(x_1, x_2, \dots, x_n)$，每个词元 $x_i$ 可以被 embedding 成一个 d 维空间下的向量表示：$x_i \in \mathbb{R}^d$。

序列预测的目标就是输出 $(y_1, y_2, \dots, y_m)$，考虑相同的 embedding 方式，序列转导模型实现了输入矩阵向输出矩阵的线性变换。

$$M \in \mathbb{R}^{n \times d} \to \text{Sequence transduction models} \to N \in \mathbb{R}^{m\times d}$$

#### Encoder & Decoder Structure

传统的神经网络难以应对长度可变的序列输入，因此，主流的应对序列到序列的学习任务往往采用 **Encoder - Decoder** 的架构，而编码器和解码器的设计都依赖于循环神经网络的设计。

![Encoder Decoder](https://s1.imagehub.cc/images/2025/07/28/04731eb3bbc45dc8cd69ec7ca0ce304a.png)

> 图节选自：[Hands on DL: Encoder and Decoder](https://zh.d2l.ai/chapter_recurrent-modern/encoder-decoder.html)

考虑传统的机器翻译任务，原始输入是待翻译的文本序列 $(x_1, x_2, \dots, x_n)$，这一部分会作为**编码器的输入**，编码器的任务是**接受长度可变的输入**编码为**具有固定形态的编码状态（例如，一个矩阵）**，这一部分会作为将来解码器的输入之一。

解码器的另一部分输入来自于**已经被翻译好的文本**，例如考虑生成目标词元 $y_p$ 过程（$p \in [1,m]$），之前被翻译的 $(y_1, y_2, \dots, y_{p-1})$ 会被**作为输入**传递进解码器里面。传统的 Encoder Decoder的架构会使用循环神经网络作为内部的网络结构。

#### Encoder

编码器的重点在于处理**长度可变的输入序列**并将其**转化为形状固定的上下文变量** $\mathbf{c}$。在时间步 $t$，输入序列的第 $x_t$ 被输入进去，循环神经网络会施加循环层 $\mathbf{h}_t = f(x_t, \mathbf{h}_{t-1})$，得到对隐状态的更新，最终编码器会选定函数 $q$ 来实现对完整序列状态的编码：

$$\mathbf{c} = q(\mathbf{h}_1, \mathbf{h}_2, \dots, \mathbf{h}_n)$$

#### Decoder

解码器同样也是**循环神经网络**的状态设计，考虑时间步长为 $t^{\prime}$（为了和 $t$ 做区分），隐状态更新的公式如下：

$$\mathbf{s}\_{t^{\prime}} = g(y_{t^{\prime} - 1}, \mathbf{c}, \mathbf{s}_{t^{\prime} - 1})$$

之后再利用输出层的 softmax 操作获得最终的概率分布：

$$P(y_{t^{\prime}} | y_1, y_2, \dots, y_{t^{\prime} - 1}, \mathbf{c})$$

> 后续有关模型损失函数设计和训练细节的部分，详见**循环神经网络**的详细解读。

#### Bahdanau Attention

使用注意力机制的 seq2seq 的模型。

传统的依赖于循环神经网络建模的 Encoder - Decoder 的架构很难捕捉原有序列的序列信息（因为在解码器输出的过程中编码器获得的隐状态 $\mathbf{c}$ 是共有的，但是在不同的时间步长下，不同位置的词的重要程度是不一样的。）

{% note info %}

例如，输入序列是 I like apple，目标序列是“我喜欢苹果”，在解码器输出“我”的时候和输出“苹果”时依赖相同的隐状态 $\mathbf{c}$，这样就会导致循环神经网络难以做较长的序列建模过程。

{% endnote %}

![Bahdanau Attention](https://s1.imagehub.cc/images/2025/07/28/c03446b61d7674b5d5eccd7f2e749113.png)

因此，在直觉上，我们希望隐藏层的输出 $\mathbf{h}_t$ 都能被利用（为了解码器的不同序列上的位置），而不仅仅是传入 $\mathbf{c} = q(\mathbf{h}_1, \mathbf{h}_2, \dots, \mathbf{h}_n)$ 作为上下文。

利用注意力机制可以做到这一点，在编码器和解码器的中间加入一个注意力机制，具体而言模型的设置如下：

- 原有序列 $(x_1, x_2, \dots, x_n)$ 仍然作为编码器的输入，编码器仍然使用循环神经网络建模，这一部分变化不大。

- 编码器除了输出 $\mathbf{c}$ 作为最终的编码隐状态，还会输出 $n$ 个键值对 $(\mathbf{h}_i, \mathbf{h}_i)$，作为键值对传入 Attention Module.

- 在解码器 RNN 中，考虑生成 $y_{t^{\prime}}$ 的过程，则注意力模块会将 $s_{t^{\prime} - 1}$ 作为 query 输入。

- Attention模块会输出一个 embedding, 这个embedding 和编码器的隐状态在相同的空间下（因为 Attention 的键值对都是编码器的隐藏状态序列），用公式表示就是：

$$\mathbf{c}\_{t^\prime} = \Sigma^{n}\_{t = 1} \alpha(\mathbf{s}\_{t^{\prime}-1}, \mathbf{h}\_t)\mathbf{h}\_t 
$$$$
\mathbf{s}\_{t^{\prime}} = g(y\_{t^{\prime}}, \mathbf{c}\_{t^{\prime}}, \mathbf{s}\_{t^{\prime} - 1})
$$


## Model Architecture

![Transformer](https://s1.imagehub.cc/images/2025/04/18/c18fb77b2a1fc2226a8e769cb8896c85.png)


### Overall Architecture

#### Input and Output

Transformer的整体网络结构仍然保留了 **Encoder-Decoder** 的网络结构。因此输入是一个 $(\text{batch} \times \text{sequence length} \times \text{embedding dimension})$ 的三维张量，经过位置编码之后（**Positional Encoding**）之后进入编码器。而解码器会接受（目标序列的Embeddings）和编码器的输出，不过为了保证模型的正确性，**Output Embeddings**需要先做一个掩码（Masked），再进行输入。

> 在Transformer训练的过程中，是可以看到完整的目标序列的，但是**我们希望Transformer**不可以未卜先知，而是应该根据之前的内容做预测，因此会采用掩蔽矩阵来消除后续词的影响。

最终解码器经过一个Linear层调整size并通过`softmax`输出最终归一化的概率。

> 对于训练过程，目标序列y是已知的，因此我们需要使用掩码来防止模型在训练过程中未卜先知，只能利用之前的生成序列。而对应生成过程，我们不知道目标序列，因此需要模型根据自己之前的生成加上编码器的新的输出来做预测。

#### Encoder and Decoder

![image](https://s1.imagehub.cc/images/2025/04/18/2ab890cbb37661ed1d1f5ed626c55f57.png)

首先，其采用了类似于ResNet的堆叠模式（$N = 6$）。下面只对对一个单元层进行分析：

- 在Encoder部分，我们可以看到两个子模块，第一个子模块是**Multi-Head Attention**(是用来模拟卷积神经网络的多通道特征学习来达到并行的目的，后面会详细介绍)，第二个子模块是**标准的全连接前馈神经网络**。
- 不过在每一个子模块内，**又加入了residual connection**（ResNet）并且做了**归一化**的处理（LayerNorm）。

- 对于解码器也是类似的结构，不过加了一个**Masked MultiHead Attention**的结构来防止模型看到后面的预测序列。

### Embeddings and softmax

### Feed-forward Networks

## Complexity

## Experiments
