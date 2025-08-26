---
title: LLML CS224n Lecture 10 Post-Training
date: 2025-08-23 17:24:13
index_img: /img/cover/cs224nposttraining.jpg
excerpt: "Lecture Notes for CS224n, focusing on several techniques of post-training, including in-context learning, RLHF training via reinforcement learning and DPO optimization."
math: True
categories:
  - Artificial Intelligence
  - LLM
tags:
  - Tutorial
  - LLM
  - Post Training
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>



# CS224N Post-Training

## Introduction

About generalization and emergent abilities of large language models...

What have large language models have been learning during the pre-training process?

- Syntax
- Knowledge
- More: Models of agents, beliefs and actions as well.

That is called **world models**, where AI can be generalized as general intelligence.

## In-Context Learning

With emergent abilities augmented, LLMs can execute tasks without examples (few-shot learning).

Thus, for SOTA large language models, all we need to input is efficiency instructions to get alignment of the task in my mind and the the task comprehension of the large language models.

> Large Language Models are few-shot learners: **In-context Learning**.

## Paradigm for Parameters Fine-tuning

- SFT
- RFT

### Supervised Fine-Tuning (SFT)

SFT is fundamentally a **supervised learning** task. Its core mathematical principle is **maximum likelihood estimation**.

The model's objective is to maximize the likelihood of generating the "correct" responses provided in a high-quality, curated dataset of instruction-response pairs. Given a dataset $D = \{(x_i, y_i)\}_{i=1}^N$ where $x_i$ is the instruction and $y_i$ is the desired response, the model's parameters $\theta$ are updated to maximize the following objective:
$$\mathcal{L}_{\text{SFT}}(\theta) = \sum_{i=1}^N \log p_\theta(y_i | x_i)$$

The training process uses standard **gradient descent** (or its variants like Adam) to find the parameters $\theta$ that best fit the supervised data. The gradient is calculated with respect to the negative log-likelihood, which is a form of **cross-entropy loss**. This loss function measures how well the model's predicted probability distribution aligns with the one-hot distribution of the correct response.

### Reinforcement Fine-Tuning (RFT)

RFT is based on **reinforcement learning (RL)**, which introduces a new element: a **reward signal**. Its core mathematical principle is **reward maximization**. RFT is teaching the model to **learn how to behave** by providing a reward signal, allowing it to explore and discover better ways to align with human values beyond what is explicitly shown in a fixed dataset.

- The model's objective is to learn a policy (its generation strategy) that maximizes the expected cumulative reward.
- The core objective is to find a policy $p_\theta$ that maximizes the expected reward. The base objective is:
$$\mathcal{L}_{\text{RFT}}(\theta) = \mathbb{E}_{y \sim p_\theta(y|x)}[R(x, y)]$$

- **Reward Function ($R$):** The key difference from SFT is the introduction of a **reward function**, which acts as a "judge" to score the model's output. This function is typically a **Reward Model (RM)** trained on human preference data.
- **Optimization:** Unlike SFT, RFT uses an RL algorithm (like **PPO** or **DPO**) to optimize the model. These algorithms don't just mimic a target answer; they use gradients to update the model in a direction that leads to higher reward scores.

A crucial aspect of RFT is the **regularization term** to prevent the model from drifting too far from its pre-trained knowledge. The final objective often includes a KL divergence penalty, like in the RLHF objective:

$$\mathcal{L}_{\text{RLHF}}(\theta) = \mathbb{E}_{y \sim p_\theta(y|x)}[R(x, y) - \beta \log \left(\frac{p_\theta(y|x)}{p^{PT}(y|x)}\right)]$$

This term ensures that the model **learns human preferences** while staying grounded in the knowledge it gained during pre-training.

## Instruction Finetuning

Original Large Language Models are focused on text generation, intentionally, it is not designed for user assistant! Language models are not aligned with user intent!

> https://arxiv.org/pdf/2203.02155

Instruction Finetuning: Collect examples of (instruction, output) pairs across many tasks and finetune an LM, then evaluate on unseen tasks.


### High Level Lessons

- Synthetical Data
- You may don't need to sample many instructions to tune.
    - [Less is More Alignment](https://arxiv.org/abs/2305.11206)
    - **Data quantity, Data quality and data diversity**.
- CrowdSourcing (Like ImageNet)


### Problems

- Lack of high-quality data
- For open-ended tasks?
- All token-level mistakes are penalized equally, which makes no sense!
- Human generate sub-optimal answers.

Thus, we will attempt to satisfy human preferences directly!


## RLHF (Reinforcement learning from human preferences)

### Optimizing for human preferences


Begin with maths and reinforcement learning...

For a specific scene of summary generation, we expect LLMs to generate an output $s$ with the given input, and we design a fair grading systems which returns $R(\hat{s}) \in \mathbb{R}$, which represents the human reward in reinforcement.

Now we want to maximize the expected rewards of samples for out large language models.

$$\mathbb{E}_{s \sim p_{\theta}(s)}[R(\hat{s})]$$

- $\boldsymbol{p}_{\theta}$ is the model policy (how LLM generate, from the view of reinforcement learning).
- $\theta$: Model parameters
- $p_{\theta}(s)$ means the probability distribution given model and given prompts, generated the target output $s$.

Make it more specific:

$$\mathbb{E}_{\hat{y} \sim p_{\theta}(y|x)}[R(x, \hat{y})]$$

#### RL optimization

For optimization based on gradient, this will not work, for our reward function may be non-differentiable.

$$\theta_{t+1}:=\theta_{t}+\alpha \nabla_{\theta_{t}} \mathbb{E}_{\hat{s} \sim p_{\theta_{t}}(s)}[R(\hat{s})]$$

We can use Reinforcement Learning algorithms! **Policy-gradient** Algorithms.

- We want to obtain

$$\nabla_{\theta} \mathbb{E}_{\hat{s} \sim p_{\theta}(s)}[R(\hat{s})]=\nabla_{\theta} \sum_{s} R(s) p_{\theta}(s)=\sum_{s} R(s) \nabla_{\theta} p_{\theta}(s)$$

- Here we'll use a very handy trick known as the log-derivative trick. Let's try taking the gradient of $\log p_{\theta}(s)$

$$\nabla_{\theta} \log p_{\theta}(s)=\frac{1}{p_{\theta}(s)} \nabla_{\theta} p_{\theta}(s) \quad \Rightarrow \quad \nabla_{\theta} p_{\theta}(s)=p_{\theta}(s) \nabla_{\theta} \log p_{\theta}(s)$$

- Plug back in:

$$\sum_{s} R(s) \nabla_{\theta} p_{\theta}(s)=\sum_{s} p_{\theta}(s) R(s) \nabla_{\theta} \log p_{\theta}(s)$$



#### Simple Introduction on Policy Gradient


$$\nabla_{\theta} \mathbb{E}_{\hat{s} \sim p_{\theta}(s)}[R(\hat{s})]=\nabla_{\theta} \sum_{s} R(s) p_{\theta}(s)=\sum_{s} R(s) \nabla_{\theta} p_{\theta}(s)$$
这个公式的目标是求**策略参数** $\theta$ 的梯度，从而最大化**预期回报** $\mathbb{E}[R(\hat{s})]$。

- $\nabla_{\theta}$ 表示对参数 $\theta$ 求梯度。
- $\mathbb{E}_{\hat{s} \sim p_{\theta}(s)}[R(\hat{s})]$ 是智能体采取策略 $p_{\theta}(s)$ 时的**预期回报**。
- $R(s)$ 是在状态 $s$ 下得到的总回报。
- $p_{\theta}(s)$ 是策略，它给出了在参数 $\theta$ 下，智能体**采取行动并最终到达状态 $s$ 的概率**。
- 最终的等式 $\sum_{s} R(s) \nabla_{\theta} p_{\theta}(s)$ 是将求导和求和运算交换了顺序。


$$\nabla_{\theta} \log p_{\theta}(s)=\frac{1}{p_{\theta}(s)} \nabla_{\theta} p_{\theta}(s) \quad \Rightarrow \quad \nabla_{\theta} p_{\theta}(s)=p_{\theta}(s) \nabla_{\theta} \log p_{\theta}(s)$$

这个公式引入了“对数导数技巧”。直接对策略 $p_{\theta}(s)$ 求梯度通常很困难，但它的对数 $\log p_{\theta}(s)$ 形式往往更简单。

$$\sum_{s} R(s) \nabla_{\theta} p_{\theta}(s)=\sum_{s} p_{\theta}(s) R(s) \nabla_{\theta} \log p_{\theta}(s)$$

- 带入公式1，因为它把**对策略的梯度** $\nabla_{\theta} p_{\theta}(s)$ 转换为了**策略概率** $p_{\theta}(s)$ 乘以**对数策略的梯度** $\nabla_{\theta} \log p_{\theta}(s)$。
- 最终的形式 $\sum_{s} p_{\theta}(s) [R(s) \nabla_{\theta} \log p_{\theta}(s)]$ 可以被看作是**一个期望的采样形式**：$\mathbb{E}_{\hat{s} \sim p_{\theta}(s)}[R(\hat{s}) \nabla_{\theta} \log p_{\theta}(\hat{s})]$。这意味着我们不需要遍历所有可能的状态，只需要**从当前策略中采样**（Sample）出一些轨迹（即智能体经历的状态序列），就可以近似地计算出梯度。这就是策略梯度算法（如REINFORCE算法）的核心思想。


### RLHF Pipeline

- Instruction Finetuning with supervised learning and labeled data.

- Collect comparison data and **train a reward model**.
    - Human labelers will grade the model output, as the training data for reward model.
    
- Optimize a policy against the reward model using reinforcement learning.

#### Modeling Human preference

- We can use a classifier (trained given human labeled data), for letting humans to grade all the $R(s)$ is quite expensive.

- Instead of asking for direct ratings, ask for pairwise comparisons.
    - This could help vanish bias and noise for human grading process.

Hence, for the Reward Model: $\text{RM}_{\phi}$, the sampling functions are like:

$$J_{\text{RM}}(\phi)=-\mathbb{E}_{\left(s^W,s^l\right)\thicksim D}[\log\sigma(\text{RM}_\phi(s^W)-\text{RM}_\phi(s^l))]$$

- $s^W$ means the winner sample, while $s^l$ means the loser sample.
- We require the RM to score higher grade for $s^W$, thus $\text{RM}_\phi(s^W)-\text{RM}_\phi(s^l) > 0$
- We use sigmoid function to map the difference into a probability between 0 and 1, then do Negative Log-Likelihood Loss.

#### Optimizing the parameters

$$\mathbb{E}_{\hat{y} \sim p_{\theta}^{RL}(\hat{y}|x)}[\text{RM}_{\phi}(x, \hat{y})]$$

Directly using $\text{RM}_{\phi}(x, \hat{y})$ as the reward function may have bias, for this reward model is trained. Gradient-based optimizers are highly sensitive to numerical errors.

Thus, we need to augment the design of optimization target.

$$R(s)=RM_\phi(s)-\beta\log\left(\frac{p_\theta^{RL}(s)}{p^{PT}(s)}\right)$$

We introduce a Penalty term (KL Divergence Penalty): $\beta\log\left(\frac{p_\theta^{RL}(s)}{p^{PT}(s)}\right)$, to prevent the policy model cheat when it finds bugs of reward model. (We need to train a reward model robust enough)

> 这个 KL 散度惩罚项是为了保证强化微调的模型的输出不会离预训练的模型太过遥远，注意**后训练（尤其是 RLHF）的意义**：我们不乞求模型在后训练的阶段学习更多深刻的知识（这个部分在庞大的预训练过程中完成），而是为了**对齐规范和语义信息**。因此，在预训练模型本身就具有一定泛化能力的情况下，后训练微调不能**舍本逐末**选择牺牲泛化性！


## Direct Preference Optimization (DPO)

### Limitations of RL in Fine-Tuning

Human preferences are unreliable!

- Reward hacking” is a common problem in RL.
- Chatbots are rewarded to produce responses that seem authoritative and helpful, regardless of truth.
- This can result in making up facts and hallucinations.

Thus the bottleneck for current RLHF is about **the design and training of reward model**.

### Removing the RL from RLHF

- Recall we want to maximize the following objective in RLHF

$$\mathbb{E}_{\hat{y} \sim p_{\theta}^{RL}(\hat{y}|x)}[RM_{\phi}(x, \hat{y}) - \beta \log \left(\frac{p_{\theta}^{RL}(\hat{y}|x)}{p^{PT}(\hat{y}|x)}\right)]$$

There is a closed form solution to this:
$$p^{*}(\hat{y}|x) = \frac{1}{Z(x)} p^{PT}(\hat{y}|x) \exp(\frac{1}{\beta} RM(x, \hat{y}))$$

> $Z(x)$ is a normalization constant.

- Rearrange this via a log transformation

$$RM(x, \hat{y}) = \beta \left(\log p^{*}(\hat{y}|x) - \log p^{PT}(\hat{y}|x)\right) + \beta \log Z(x) = \beta \log \frac{p^{*}(\hat{y}|x)}{p^{PT}(\hat{y}|x)} + \beta \log Z(x)$$

- This holds true for any arbitrary LMs, thus
$$RM_{\theta}(x, \hat{y}) = \beta \log \frac{p_{\theta}^{RL}(\hat{y}|x)}{p^{PT}(\hat{y}|x)} + \beta \log Z(x)$$

What does that tells us? The ideal RM model can be computed if we get the optimized policy $p_\theta^{\text{RL}}$.


### Algorithm

Hence, we can derive the reward model directly:

$$RM_\phi(x, \hat{y}) \propto \log \frac{p_{\theta}^{RL}(\hat{y}|x)}{p^{PT}(\hat{y}|x)}$$

> We just ignore $Z(x)$ for it is constants.

Defining **Direct Preference Optimization**:

Previous: Highly relied on trained reward model (bottleneck)

$$L_{RL}(\theta) = -\mathbb{E}_{\hat{y} \sim p_{\theta}^{RL}(\hat{y}|x)}[RM_{\phi}(x, \hat{y}) - \beta \log \left(\frac{p_{\theta}^{RL}(\hat{y}|x)}{p^{PT}(\hat{y}|x)}\right)]$$

Now: Using DPO into a loss function, learning directly from $p^{PT}$ to $p_{\theta}^{RL}$

$$L_{DPO}(\theta) = -\mathbb{E}_{(x,y_W,y_l)\sim D} \log\sigma\left(\beta \log\frac{p_{\theta}^{RL}(y_w|x)}{p^{PT}(y_w|x)} - \beta \log\frac{p_{\theta}^{RL}(y_l|x)}{p^{PT}(y_l|x)}\right)$$

$$L_{DPO}(\theta) = -\mathbb{E}_{(x,y_W,y_l)\sim D} \log\sigma\left(\beta \log\frac{p_{\theta}^{RL}(y_w|x) p^{PT}(y_l|x)}{p_{\theta}^{RL}(y_l|x) p^{PT}(y_w|x)}\right)$$

> Using the data directly (learn directly!)


