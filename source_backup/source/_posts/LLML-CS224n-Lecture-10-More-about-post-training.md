---
title: More About Post Training
date: 2025-09-14 11:31:36
index_img: /img/cover/cs224nposttraining.jpg
excerpt: "Lecture notes for online courses launched by deeplearningai, focusing on advanced online RL training techniques and practices about LLM post-training methodology, including PPO and GRPO."
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

# More About Post Training: About Online Reinforcement Learning

[Course Websites](https://www.deeplearning.ai/short-courses/post-training-of-llms/)

{% note primary %}

All kinds of PO are too difficult! This blog will be updated later after the update of simple reinforcement learning.

{% endnote %}

## Introduction

- Offline RL: The model learns from a purely pre-collected prompt-response(-reward) tuple. It is like a huge defined table for action space and reward functions. No fresh responses generated during the training process.
    - It is more like a detective.
    - The action space and reward functions are all finite.
    - The ultimate goal for offline RL is to learn optimized strategies based on stable historical data.

- Online RL:
    - We will interact with environment!
    - The model learns by generating new responses in real time, and update its weights.


## Online RL methods

Prompts $\to$ Current Models $\to$ (Prompts, Answers) $\to$ (Prompts, Answers, Rewards) (Given a fixed reward function.)

- TRPO (Trust Region Policy Optimization)
- PPO (Proximal Policy Optimization)
    - Using "Proximal" instead of trusted optimization.
- GRPO 

[PPO & GRPO](https://builder.aws.com/content/2rJrpj6m2eh591fjMcRZ3ushpB7/deep-dive-into-group-relative-policy-optimization-grpo)


## Preliminaries

### 策略评估恒等式

**策略评估恒等式**。它揭示了新策略的期望回报与旧策略的期望回报之间的关系。

期望回报 Expected Return

$$\eta(\pi_\theta) = \mathbb{E}_{s \sim \rho_{\pi_\theta}, a \sim \pi_\theta} \left[ \sum_{t=0}^\infty \gamma^t R(s_t, a_t) \right]$$

* **$\eta(\pi_\theta)$**: 表示在**策略 $\pi_\theta$** 下的**期望累积回报**（或称价值）。这是我们想要优化的最终目标。
* **$\mathbb{E}_{s \sim \rho_{\pi_\theta}, a \sim \pi_\theta}$**: 表示期望的来源。我们对所有可能的**状态-动作轨迹**取期望。
    * **$s \sim \rho_{\pi_\theta}$**: 状态 $s$ 来自于在策略 $\pi_\theta$ 下的**状态访问分布**。这表示智能体在执行策略 $\pi_\theta$ 时，访问到各个状态的概率。
    * **$a \sim \pi_\theta$**: 动作 $a$ 来自于策略 $\pi_\theta$。
* **$\sum_{t=0}^\infty \gamma^t R(s_t, a_t)$**: 这部分是**折扣累积回报**，也叫**回报**（Return）。$R(s_t, a_t)$ 是在时间步 $t$ 时采取动作 $a_t$ 获得的即时奖励，$\gamma$ 是折扣因子，用来衡量未来奖励的重要性。

从定义出发经过严格的数学推导，可以得到下面的更复杂的**策略评估恒等式**，它将新策略的回报与旧策略的回报以及两者之间的优势函数联系起来：

$$\eta(\pi_\theta) = \eta(\pi_{\theta_{old}}) + \mathbb{E}_{\tau \sim \pi_\theta} \left[ \sum_{t=0}^\infty \gamma^t A_{\pi_{\theta_{old}}}(s_t, a_t) \right]$$

这个公式的推导过程比较复杂，但其核心思想是基于**状态价值函数**和**优势函数**的定义。

**推导过程：**

1.  **从状态价值函数开始**：一个策略的价值，可以分解为在每个状态下的价值。
    $$\eta(\pi_\theta) = \mathbb{E}_{s_0 \sim \rho_0} [V^{\pi_\theta}(s_0)]$$
    其中 $\rho_0$ 是初始状态分布。

2.  **利用贝尔曼方程**：状态价值函数 $V^\pi(s)$ 和状态-动作价值函数 $Q^\pi(s, a)$ 满足贝尔曼方程。一个关键的恒等式是：
    $$V^{\pi}(s) = \mathbb{E}_{a \sim \pi} [Q^\pi(s, a)] = \mathbb{E}_{a \sim \pi} [A^\pi(s, a) + V^\pi(s)]$$
    这可以推导出：
    $$V^{\pi}(s) = \mathbb{E}_{a \sim \pi}[A^\pi(s,a)] + V^\pi(s)$$
    这个式子看似无用，但如果把 $V^\pi(s)$ 替换为**旧策略的价值** $V^{\pi_{old}}(s)$，并用**新策略** $\pi_\theta$ 的动作来求期望，就会有新的含义：
    $$V^{\pi_\theta}(s) = \mathbb{E}_{a \sim \pi_\theta} [Q^{\pi_{old}}(s,a)]$$
    根据 $Q(s, a) = V(s) + A(s, a)$，可以得到：
    $$V^{\pi_\theta}(s) = \mathbb{E}_{a \sim \pi_\theta} [V^{\pi_{old}}(s) + A^{\pi_{old}}(s,a)] = V^{\pi_{old}}(s) + \mathbb{E}_{a \sim \pi_\theta} [A^{\pi_{old}}(s,a)]$$

3.  **对所有状态进行累加**：这个关系式在每个状态都成立。我们可以通过将它扩展到整个马尔可夫决策过程，来得到一个更通用的**累积回报恒等式**。这是通过**马尔可夫链**的性质得到的。最终推导结果正是你图片中的第二个公式。

* **$\eta(\pi_{\theta_{old}})$**: 这是**旧策略 $\pi_{\theta_{old}}$** 的期望回报，一个基准值。
* **$\mathbb{E}_{\tau \sim \pi_\theta}$**: 表示期望的来源是**新策略** $\pi_\theta$ 产生的**轨迹** $\tau$。
* **$\sum_{t=0}^\infty \gamma^t A_{\pi_{\theta_{old}}}(s_t, a_t)$**: 这是**优势函数的累积和**。
    * **$s_t, a_t$**: 这对状态-动作对是在**新策略** $\pi_\theta$ 下被访问或执行的。
    * **$A_{\pi_{\theta_{old}}}(s_t, a_t)$**: 这表明我们评估的优势函数是**基于旧策略**的，它衡量了**新策略**在访问的状态上所采取的动作，相对于**旧策略**的平均表现如何。

第二个公式表明，新策略的回报等于旧策略的回报**加上**在新策略下，**沿途所遇到的所有状态和动作的优势函数的累积期望**。换句话说，要提升策略，你只需要在新策略下，更多地执行那些在旧策略看来是**好**的动作（**$A>0$**），并避免那些**坏**的动作（**$A<0$**）。这就是 TRPO 和 PPO 算法的核心思想来源。


### 重要性采样

我们想要优化的目标是新策略的期望回报 $\eta(\pi_\theta)$。我们已经知道一个重要的关系：

$$\eta(\pi_\theta) = \eta(\pi_{\theta_{old}}) + \mathbb{E}_{\tau \sim \pi_\theta} \left[ \sum_{t=0}^\infty \gamma^t A_{\pi_{\theta_{old}}}(s_t, a_t) \right]$$

问题出在右边的期望项 $\mathbb{E}_{\tau \sim \pi_\theta}[\dots]$。这个期望是**在新策略 $\pi_\theta$ 的轨迹分布下**求的。但在训练过程中，我们只有**旧策略 $\pi_{\theta_{old}}$** 产生的数据。我们必须找到一个方法，**用旧策略的数据来近似计算新策略的期望**

重要性采样的基本思想是：如果我想要计算一个函数在某个分布下的期望，但从这个分布中采样很困难，我可以转而从另一个更容易采样的分布中获取样本，然后通过一个**权重**来修正。

数学上，它的公式是这样的：
$$\mathbb{E}_{x \sim P} [f(x)] = \mathbb{E}_{x \sim Q} \left[ \frac{P(x)}{Q(x)} f(x) \right]$$

这里：
* $P$ 是我们想要的**目标分布**（即新策略 $\pi_\theta$）。
* $Q$ 是我们实际采样的**行为分布**（即旧策略 $\pi_{\theta_{old}}$）。
* $\frac{P(x)}{Q(x)}$ 是**修正权重**，也就是我们说的**重要性采样比值**。

现在，我们把这个思想应用到我们的期望项上：

* **目标分布 $P$**: 是新策略产生的轨迹分布 $\tau \sim \pi_\theta$。
* **行为分布 $Q$**: 是旧策略产生的轨迹分布 $\tau \sim \pi_{\theta_{old}}$。
* **函数 $f(\tau)$**: 是轨迹回报的累积和 $\sum_{t=0}^\infty \gamma^t A_{\pi_{\theta_{old}}}(s_t, a_t)$。

将这些代入重要性采样的公式，我们会得到：

$$\mathbb{E}_{\tau \sim \pi_\theta} \left[ \sum_{t=0}^\infty \gamma^t A_{\pi_{\theta}}(s_t, a_t) \right] = \mathbb{E}_{\tau \sim \pi_{\theta_{old}}} \left[ \frac{P(\tau)}{Q(\tau)} \cdot \left(\sum_{t=0}^\infty \gamma^t A_{\pi_{\theta_{old}}}(s_t, a_t)\right) \right]$$

这里的 $\frac{P(\tau)}{Q(\tau)}$ 是轨迹的概率比值，它等于沿途所有状态-动作对的概率比值的乘积。

$$\frac{P(\tau)}{Q(\tau)} = \prod_{t=0}^\infty \frac{\pi_\theta(a_t|s_t)}{\pi_{\theta_{old}}(a_t|s_t)}$$

这个连乘积的计算非常复杂，因此在实际计算中采用**蒙特卡洛模拟**的方式，只模拟单步的优化表现。


### TRPO (Trust Region Policy Optimization)

https://arxiv.org/pdf/1502.05477

TRPO的核心思想是：在策略优化过程中，我们希望**策略的性能单调提升**，并且避免因**更新步长过大而导致性能崩溃**。它通过信任区域来限制新策略与旧策略之间的差异，确保更新在安全的范围内进行。

> 这也是强化学习最基本的思想之一：The tradeoff between exploration and exploitation.

策略梯度方法的最终目标是最大化期望累积回报 $J(\pi_\theta)$。TRPO的优化目标源于一个理论上的下界。

经过近似推导（忽略状态分布的变化），可以得到一个替代优势（Surrogate Advantage）： 

$$L_{\pi_{\theta_{old}}}(\pi_\theta) = \eta(\pi_{\theta_{old}}) + \mathbb{E}_{s \sim \rho_{\pi_{\theta_{old}}}, a \sim \pi_{\theta_{old}}} \left[ \frac{\pi_\theta(a|s)}{\pi_{\theta_{old}}(a|s)} A_{\pi_{\theta_{old}}}(s, a) \right]$$

最大化 $L$ 理论上可以保证提升真实的 $\eta$。因此，TRPO的目标函数是最大化这个替代优势的第二部分： 

$$J_{TRPO}(\theta) = \mathbb{E}_{t} \left[ \frac{\pi_\theta(a_t|s_t)}{\pi_{\theta_{old}}(a_t|s_t)} \hat{A}_t \right]$$

然而，上述近似只有在 $\pi_\theta$ 和 $\pi_{\theta_{old}}$ 足够接近时才有效。TRPO使用**KL散度**作为两个策略分布差异的度量，并将这个差异约束在一个信任区域 $\delta$ 内。

$$\mathbb{E}_{s\sim\pi_{\theta_{old}}}[KL[\pi_{\theta_{old}}(\cdot|s),\pi_\theta(\cdot|s)]]\leq\delta$$


最终，TRPO的优化问题是一个带约束的优化问题： 

$$\max_{\theta}\quad\mathbb{E}_{s,a\sim\pi_{\theta_{odd}}}\left[\frac{\pi_{\theta}(a|s)}{\pi_{\theta_{odd}}(a|s)}\hat{A}(s,a)\right]$$

$$\mathrm{subject~to}\quad\mathbb{E}_{s\sim\pi_{\rho_{odd}}}[KL[\pi_{\theta_{odd}}(\cdot|s),\pi_{\theta}(\cdot|s)]]\leq\delta$$

直接求解这个带约束的问题计算量很大。TRPO使用共轭梯度法和线搜索来近似求解。

- 首先，将目标函数做一阶近似（即梯度），将约束做二阶近似（因为KL散度的一阶导为0，二阶导是Fisher信息矩阵 $F$）。
- 这样问题转化为一个二次规划问题，其解的方向为 $\theta - \theta_{old} \propto F^{-1} \nabla_\theta J$，即自然梯度的方向。
- 计算出更新方向后，再通过线搜索找到一个合适的步长，确保KL散度约束被满足的同时，目标函数确实有所提升。

我们想找到一个更新方向 $\Delta\theta = \theta - \theta_{old}$，来最大化目标函数。在参数 $\theta_{old}$ 的附近，我们可以对目标函数进行**一阶泰勒展开**。

最大化目标函数就近似于**最大化梯度方向上的更新步长** $g^T(\theta - \theta_{old})$。


KL 散度函数在两个分布相同时，值为零，且其**一阶导数也为零**。因此，为了得到一个非零的近似，我们必须使用**二阶泰勒展开**。

* **泰勒展开**：$f(x) \approx f(a) + f'(a)(x-a) + \frac{1}{2}f''(a)(x-a)^2$
* **TRPO 约束**：我们对 KL 散度在 $\theta = \theta_{old}$ 处进行展开。

$$KL[\pi_{\theta_{old}}(\cdot|s), \pi_\theta(\cdot|s)] \approx \frac{1}{2}(\theta - \theta_{old})^T F(s) (\theta - \theta_{old})$$
其中 **$F(s)$ 是 Fisher 信息矩阵**，它衡量了在状态 $s$ 下，策略参数的变化对策略分布的影响。

对所有状态取平均，我们得到约束的二阶近似：
$$\mathbb{E}_{s \sim \pi_{\theta_{old}}} [KL[\pi_{\theta_{old}}(\cdot|s), \pi_\theta(\cdot|s)]] \approx \frac{1}{2}(\theta - \theta_{old})^T F (\theta - \theta_{old})$$
其中 $F = \mathbb{E}_{s \sim \pi_{\theta_{old}}}[F(s)]$ 是**平均 Fisher 信息矩阵**。

通过这些近似，原始的 TRPO 优化问题被转化为了一个更简单的**二次规划**问题：

$$\begin{align} \underset{\Delta\theta}{\max} \quad & g^T \Delta\theta \\ \text{subject to} \quad & \frac{1}{2}\Delta\theta^T F \Delta\theta \leq \delta \end{align}$$

这个问题的解有一个非常著名的名称：**自然梯度**。它是一个更优化的梯度方向，它**考虑了参数空间中的几何结构**。它确保了更新方向不仅仅在参数上是“陡峭的”，在策略分布上也是“有意义的”。

这个问题的解析解是：

$$\Delta\theta \propto F^{-1}g$$

这就是**自然梯度的方向**。TRPO 的巧妙之处在于，它不需要显式地计算 Fisher 矩阵的逆 $F^{-1}$（计算量巨大），而是使用**共轭梯度法**来高效地求解一个线性方程，从而得到自然梯度的方向。


### PPO

PPO（**Proximal Policy Optimization**）是一种强化学习算法，它旨在解决传统策略梯度方法训练不稳定、容易崩溃的问题。PPO 的核心思想是**用一种简单但有效的方式，来近似实现 TRPO 的稳健性**。

与 TRPO 类似，PPO 同样使用一个替代目标函数来近似策略的提升。这个函数基于**重要性采样**，用旧策略的数据来评估新策略的性能。

PPO 的目标函数和 TRPO 保持相同：

$$L(\theta) = \mathbb{E}_{t} \left[ \frac{\pi_\theta(a_t|s_t)}{\pi_{\theta_{old}}(a_t|s_t)} \hat{A}_t \right]$$

但是 PPO **不使用复杂的 KL 散度约束**，而是通过**裁剪**重要性采样比值 $r_t(\theta)$ 来限制策略的更新幅度。

PPO 的裁剪目标函数是：

$$L^{CLIP}(\theta) = \mathbb{E}_{t} \left[ \min\left(r_t(\theta)\hat{A}_t, \text{clip}(r_t(\theta), 1-\epsilon, 1+\epsilon)\hat{A}_t\right) \right]$$

让我们分解一下这个公式：

* **$r_t(\theta)\hat{A}_t$**：原始的策略梯度目标，也就是不加限制的更新。
* **$\text{clip}(r_t(\theta), 1-\epsilon, 1+\epsilon)$**：将 $r_t(\theta)$ 裁剪到 $[1-\epsilon, 1+\epsilon]$ 这个区间内。
    * $\epsilon$ 是一个超参数，通常取值很小（如 0.1 或 0.2）。
    * 这意味着，如果新策略选择某个动作的概率比旧策略**大得多**（$r_t > 1+\epsilon$），它会被限制在 $1+\epsilon$。
    * 如果新策略选择某个动作的概率比旧策略**小得多**（$r_t < 1-\epsilon$），它会被限制在 $1-\epsilon$。

这样的裁剪可以达到和 TRPO 中的区域约束很像，都是惩罚模型过度的更新权重，一次迈步迈太大而导致表现不稳定，性能迅速下降的现象。同时，这个相比于 TRPO 的二阶优化约束需要更少的计算量，但是达到了相同的惩罚效果。

PPO 采用标准的**随机梯度下降（SGD）**来优化裁剪后的目标函数，并结合**多轮小批量训练**。
> 因为这个函数是可微分的。

* **数据收集**：使用当前策略与环境互动，收集一批数据（如 2048 个时间步）。
* **多轮训练**：使用这批数据，对策略网络和价值网络进行多次迭代更新（例如，进行 10 轮训练）。每轮训练都将数据打乱，分成小批量进行梯度下降。


### GRPO

![GRPO](https://assets.community.aws/a/2ra6RfOUylhUM8M3eDEMfDlB3l7/Scre.webp?imgSize=1698x894)


## All Codes

For the latest updated code, see [Github Repo](https://github.com/xiyuanyang-code/LLM_Learn/tree/master/training/post_training/src) for more info.


### SFT Training

```python
import os
import json
import torch
import pandas as pd
import warnings
import argparse


from tqdm import tqdm
from accelerate import Accelerator
from datasets import load_dataset, Dataset
from transformers import TrainingArguments, AutoTokenizer, AutoModelForCausalLM
from trl import SFTTrainer, SFTConfig

SYSTEM_PROMPT = "You are a software engineer good at solving all kinds of problems."
USER_PROMPT = "The Task you need to solve is \n\n\n ============= TASK ============= \n{task}\n =======================\n\nPlease keep your response to approximately {num} words."
warnings.filterwarnings("ignore")
# accelerator = Accelerator()


def generate_responses(
    model,
    tokenizer,
    user_message=None,
    system_message=None,
    max_new_tokens=3000,
    full_message=None,
):
    # Format chat using tokenizer's chat template
    if full_message:
        messages = full_message
    else:
        messages = []
        if system_message:
            messages.append({"role": "system", "content": system_message})
        messages.append({"role": "user", "content": user_message})

    prompt = tokenizer.apply_chat_template(
        messages,
        tokenize=False,
        add_generation_prompt=True,
        enable_thinking=False,
    )
    inputs = tokenizer(prompt, return_tensors="pt").to(model.device)

    # for inference stages, no gradient operations are needed.
    with torch.no_grad():
        outputs = model.generate(
            **inputs,
            max_new_tokens=max_new_tokens,
            do_sample=False,
            pad_token_id=tokenizer.eos_token_id,
            eos_token_id=tokenizer.eos_token_id,
        )
    input_len = inputs["input_ids"].shape[1]
    generated_ids = outputs[0][input_len:]
    response = tokenizer.decode(generated_ids, skip_special_tokens=True).strip()

    return response


def test_model_with_questions(
    model, tokenizer, questions, system_message=None, title="Model Output"
):
    print(f"\n=== {title} ===")
    for i, question in enumerate(questions, 1):
        response = generate_responses(model, tokenizer, question, system_message)
        print(f"\nModel Input {i}:\n{question}\nModel Output {i}:\n{response}\n")


def load_model_and_tokenizer(model_name, use_gpu=False, gpu_device="cuda"):
    # Load base model and tokenizer
    global tokenizer
    tokenizer = AutoTokenizer.from_pretrained(model_name)
    model = AutoModelForCausalLM.from_pretrained(
        model_name, dtype=torch.bfloat16, device_map="auto"
    )

    if not tokenizer.chat_template:
        tokenizer.chat_template = """{% for message in messages %}
                {% if message['role'] == 'system' %}System: {{ message['content'] }}\n
                {% elif message['role'] == 'user' %}User: {{ message['content'] }}\n
                {% elif message['role'] == 'assistant' %}Assistant: {{ message['content'] }} <|endoftext|>
                {% endif %}
                {% endfor %}"""

    # Tokenizer config
    if not tokenizer.pad_token:
        tokenizer.pad_token = tokenizer.eos_token

    return model, tokenizer


def load_datasets(dataset_path):
    train_dataset = load_dataset(dataset_path)["train"]
    test_dataset = load_dataset(dataset_path)["test"]

    print(f"Length of training data: {len(train_dataset)}")
    print(f"Length of test data set: {len(test_dataset)}")

    # load several parquet
    corpus_data = pd.read_parquet(
        "./data/stack_exchange/corpus/corpus-00000-of-00001.parquet"
    )
    query_data = pd.read_parquet(
        "./data/stack_exchange/queries/queries-00000-of-00001.parquet"
    )
    return train_dataset, test_dataset, query_data, corpus_data


def formatting_func(examples):
    formatted_texts = []
    for q, c in zip(examples["User Prompt"], examples["Assistant Prompt"]):
        messages = [{"role": "user", "content": q}, {"role": "assistant", "content": c}]
        formatted_text = tokenizer.apply_chat_template(
            messages, tokenize=False, add_generation_prompt=False
        )
        formatted_texts.append(formatted_text)
    return {"text": formatted_texts}


def formatting_func_(example):
    messages = [
        {"role": "user", "content": example["User Prompt"]},
        {"role": "assistant", "content": example["Assistant Prompt"]},
    ]
    formatted_text = tokenizer.apply_chat_template(
        messages, tokenize=False, add_generation_prompt=False
    )
    return formatted_text


def evaluate(model, tokenizer, output_path):
    # feat: support resumable download
    if not os.path.exists(output_path):
        with open(output_path, "w") as file:
            # create new output file
            pass

    with open(output_path, "r") as file:
        current_lines = sum([1 for line in file])

    print(f"Loading from {current_lines}")
    total_lines = len(test_dataset)
    with open(output_path, "a", encoding="utf-8") as file:
        # generating test problems for models before tuning
        for index, test_data in tqdm(
            enumerate(test_dataset),
            total=total_lines,
            colour="CYAN",
        ):
            if index < current_lines:
                # for resumable download
                continue

            answer = dict()
            # get query data
            query_id = test_data["query-id"]
            query_row = query_data[query_data["_id"] == query_id]
            query_text = query_row["text"].iloc[0]

            # get corpus data
            corpus_id = test_data["corpus-id"]
            corpus_row = corpus_data[corpus_data["_id"] == corpus_id]
            corpus_answer = str(corpus_row["text"].iloc[0])

            # getting score
            score = int(test_data["score"])

            # loading prompt templates
            user_prompt = USER_PROMPT.format(
                task=query_text, num=len(query_text.split())
            )

            try:
                # generating responses
                model_response = generate_responses(
                    model=model,
                    tokenizer=tokenizer,
                    user_message=user_prompt,
                    system_message=SYSTEM_PROMPT,
                )
            except Exception as e:
                print(f"error: {e}")
                model_response = "ERROR_MODEL_RESPONSE"

            answer["index"] = index
            answer["query-id"] = query_id
            answer["query"] = query_text
            answer["corpus-id"] = corpus_id
            answer["full_score"] = score
            answer["corpus-answer"] = corpus_answer
            answer["model-answer"] = model_response

            # load it to output path
            file.write(json.dumps(answer, ensure_ascii=False) + "\n")
            file.flush()
    print(f"Evaluating Done! File saved to {output_path}")


def test_before_post_training():
    print(f"Loading model {model_path} before SFT process...")

    # loading models
    model, tokenizer = load_model_and_tokenizer(model_name=model_path, use_gpu=True)
    print(f"using device: {model.device}")

    # generating recording files
    model_name = model_path.split("/")[-1]
    os.makedirs(f"./output/stack_exchange/{model_name}", exist_ok=True)
    output_path = f"./output/stack_exchange/{model_name}/answers.jsonl"

    # evaluating
    evaluate(model=model, tokenizer=tokenizer, output_path=output_path)


def SFT_train(train_dataset_op):
    model, tokenizer = load_model_and_tokenizer(
        model_name=model_path, use_gpu=True, gpu_device="cuda:2"
    )

    # SFTTrainer config
    sft_config = SFTConfig(
        learning_rate=8e-5,  # Learning rate for training.
        num_train_epochs=1,  #  Set the number of epochs to train the model.
        per_device_train_batch_size=1,  # Batch size for each device (e.g., GPU) during training.
        gradient_accumulation_steps=8,  # Number of steps before performing a backward/update pass to accumulate gradients.
        gradient_checkpointing=False,  # Enable gradient checkpointing to reduce memory usage during training at the cost of slower training speed.
        logging_steps=2,  # Frequency of logging training progress (log every 2 steps).
    )

    sft_trainer = SFTTrainer(
        model=model,
        args=sft_config,
        train_dataset=train_dataset_op,
        formatting_func=formatting_func_,
        processing_class=tokenizer,
    )

    sft_trainer.train()

    # training process done
    print("Training process done! Start evaluating after SFT")

    # generating recording files
    model_name = model_path.split("/")[-1]
    os.makedirs(f"./output/stack_exchange/{model_name}", exist_ok=True)
    output_path = f"./output/stack_exchange/{model_name}/answers_sft.jsonl"

    # evaluating
    evaluate(model=model, tokenizer=tokenizer, output_path=output_path)


if __name__ == "__main__":
    # parsing argument
    parser = argparse.ArgumentParser(
        description="argument for SFT training and evaluation"
    )
    parser.add_argument("--eva", type=bool, default=False)
    parser.add_argument("--tune", type=bool, default=False)
    args = parser.parse_args()

    print("Demo for the sft tuning process.")
    dataset_path = "./data/stack_exchange"
    model_path = "./models/Qwen/Qwen2.5-7B"
    print(f"Using default model: {model_path}")

    # loading datasets
    print("Loading datasets")
    global train_dataset, test_dataset, query_data, corpus_data
    train_dataset, test_dataset, query_data, corpus_data = load_datasets(
        dataset_path=dataset_path
    )

    train_data_list = []

    query_data_indexed = query_data.set_index("_id")
    corpus_data_indexed = corpus_data.set_index("_id")
    train_pairs = pd.DataFrame(train_dataset)

    for _, row in train_pairs.iterrows():
        query_id = row["query-id"]
        corpus_id = row["corpus-id"]

        try:
            query_text = query_data_indexed.loc[query_id, "text"]
            corpus_text = corpus_data_indexed.loc[corpus_id, "text"]
            train_data_list.append(
                {"User Prompt": query_text, "Assistant Prompt": corpus_text}
            )
        except KeyError as e:
            print(f"Warning: ID {e} not found. Skipping this pair.")
            continue

    train_df = pd.DataFrame(train_data_list)
    train_dataset_op = Dataset.from_pandas(train_df)

    if args.eva is True:
        os.environ["CUDA_VISIBLE_DEVICES"] = "0,1,2,3"
        print("device count:", torch.cuda.device_count())
        print("Start Evaluating")
        print(f"Evaluation of model: {model_path} before post-training")
        test_before_post_training()

    if args.tune is True:
        os.environ["CUDA_VISIBLE_DEVICES"] = "4,5,6,7"
        print("device count:", torch.cuda.device_count())
        print("Start finetuning using SFT")
        # todo add more tuning methods in the future
        SFT_train(train_dataset_op=train_dataset_op)

    print("Evaluation and tuning process done.")
```

### DPO Training

```python
import os
import json
import torch
import pandas as pd
import warnings
import argparse


from tqdm import tqdm
from accelerate import Accelerator
from datasets import load_dataset, Dataset
from transformers import TrainingArguments, AutoTokenizer, AutoModelForCausalLM
from trl import DPOTrainer, DPOConfig

warnings.filterwarnings("ignore")
# accelerator = Accelerator()

ORG_NAME = "Qwen"
POS_NAME = "陈大师"
SYSTEM_PROMPT = "You're a helpful assistant."


def generate_responses(
    model,
    tokenizer,
    user_message=None,
    system_message=None,
    max_new_tokens=3000,
    full_message=None,
):
    # Format chat using tokenizer's chat template
    if full_message:
        messages = full_message
    else:
        messages = []
        if system_message:
            messages.append({"role": "system", "content": system_message})
        messages.append({"role": "user", "content": user_message})

    prompt = tokenizer.apply_chat_template(
        messages,
        tokenize=False,
        add_generation_prompt=True,
        enable_thinking=False,
    )
    inputs = tokenizer(prompt, return_tensors="pt").to(model.device)

    # for inference stages, no gradient operations are needed.
    with torch.no_grad():
        outputs = model.generate(
            **inputs,
            max_new_tokens=max_new_tokens,
            do_sample=False,
            pad_token_id=tokenizer.eos_token_id,
            eos_token_id=tokenizer.eos_token_id,
        )
    input_len = inputs["input_ids"].shape[1]
    generated_ids = outputs[0][input_len:]
    response = tokenizer.decode(generated_ids, skip_special_tokens=True).strip()

    return response


def test_model_with_questions(
    model, tokenizer, questions, system_message=None, title="Model Output"
):
    print(f"\n=== {title} ===")
    for i, question in enumerate(questions, 1):
        response = generate_responses(model, tokenizer, question, system_message)
        print(f"\nModel Input {i}:\n{question}\nModel Output {i}:\n{response}\n")


def load_model_and_tokenizer(model_name, use_gpu=False, gpu_device="cuda"):
    # Load base model and tokenizer
    global tokenizer
    tokenizer = AutoTokenizer.from_pretrained(model_name)
    model = AutoModelForCausalLM.from_pretrained(
        model_name, dtype=torch.bfloat16, device_map="auto"
    )

    if not tokenizer.chat_template:
        tokenizer.chat_template = """{% for message in messages %}
                {% if message['role'] == 'system' %}System: {{ message['content'] }}\n
                {% elif message['role'] == 'user' %}User: {{ message['content'] }}\n
                {% elif message['role'] == 'assistant' %}Assistant: {{ message['content'] }} <|endoftext|>
                {% endif %}
                {% endfor %}"""

    # Tokenizer config
    if not tokenizer.pad_token:
        tokenizer.pad_token = tokenizer.eos_token

    return model, tokenizer


def formatting_func(examples):
    formatted_texts = []
    for q, c in zip(examples["User Prompt"], examples["Assistant Prompt"]):
        messages = [{"role": "user", "content": q}, {"role": "assistant", "content": c}]
        formatted_text = tokenizer.apply_chat_template(
            messages, tokenize=False, add_generation_prompt=False
        )
        formatted_texts.append(formatted_text)
    return {"text": formatted_texts}


def formatting_func_(example):
    messages = [
        {"role": "user", "content": example["User Prompt"]},
        {"role": "assistant", "content": example["Assistant Prompt"]},
    ]
    formatted_text = tokenizer.apply_chat_template(
        messages, tokenize=False, add_generation_prompt=False
    )
    return formatted_text


def test_before_post_training(model, tokenizer):
    print(f"Loading model {model_path} before SFT process...")

    # loading models
    print(f"using device: {model.device}")

    # generating recording files
    model_name = model_path.split("/")[-1]
    os.makedirs(f"./output/stack_exchange/{model_name}", exist_ok=True)
    output_path = f"./output/stack_exchange/{model_name}/answers.jsonl"

    response = generate_responses(
        model=model,
        tokenizer=tokenizer,
        user_message="Introduce yourself",
        system_message="You are a helpful assistant.",
    )

    return response


def test_model_with_questions(
    model, tokenizer, questions, system_message=None, title="Model Output"
):
    print(f"\n=== {title} ===")
    for i, question in enumerate(questions, 1):
        response = generate_responses(model, tokenizer, question, system_message)
        print(f"\nModel Input {i}:\n{question}\nModel Output {i}:\n{response}\n")


def build_dpo_chatml(example):
    msgs = example["conversations"]
    prompt = next(m["value"] for m in reversed(msgs) if m["from"] == "human")
    try:
        # view the response the model generate as the rejected response
        rejected_resp = generate_responses(model, tokenizer, prompt)
    except Exception as e:
        rejected_resp = "Error: failed to generate response."
        print(f"Generation error for prompt: {prompt}\n{e}")

    # we need to define chosen response manually
    chosen_resp = rejected_resp.replace(ORG_NAME, POS_NAME)
    chosen = [
        {"role": "system", "content": SYSTEM_PROMPT},
        {"role": "user", "content": prompt},
        {"role": "assistant", "content": chosen_resp},
    ]
    rejected = [
        {"role": "system", "content": SYSTEM_PROMPT},
        {"role": "user", "content": prompt},
        {"role": "assistant", "content": rejected_resp},
    ]

    return {"chosen": chosen, "rejected": rejected}


def DPO_training(model, tokenizer, dpo_ds):
    # setting DPO config
    config = DPOConfig(
        beta=0.2,
        per_device_train_batch_size=1,
        gradient_accumulation_steps=8,
        num_train_epochs=1,
        learning_rate=5e-5,
        logging_steps=2,
    )

    # set up trainer
    dpo_trainer = DPOTrainer(
        model=model,
        ref_model=None,
        args=config,
        processing_class=tokenizer,
        train_dataset=dpo_ds,
    )
    dpo_trainer.train()

    # evaluate after post_Training
    questions = [
        "What is your name?",
        "Are you ChatGPT?",
        "Tell me about your name and organization."
        "9.11 and 9.9, which number is bigger?",
    ]

    test_model_with_questions(model=model, tokenizer=tokenizer, questions=questions)

    dpo_trainer.save_model("./models/Own/Qwen2.5-7B-DPO")


def gen_dpo_dataset():
    # for this demo, we will use a identity dataset to optimize model behavior
    raw_ds = load_dataset("./data/mrfakename/identity", split="train")
    print(len(raw_ds))
    dpo_ds = raw_ds.map(build_dpo_chatml, remove_columns=raw_ds.column_names)
    print(f"Loading data successfully. Length: {len(dpo_ds)}")
    return dpo_ds


if __name__ == "__main__":
    os.environ["CUDA_VISIBLE_DEVICES"] = "0,1,2,3,4,5,6,7"
    # parsing argument
    parser = argparse.ArgumentParser(
        description="argument for SFT training and evaluation"
    )
    parser.add_argument("--eval", action="store_true")
    parser.add_argument("--tune", action="store_true")
    args = parser.parse_args()

    print("Demo for the sft tuning process.")
    model_path = "./models/Qwen/Qwen2.5-7B"
    print(f"Using default model: {model_path}")

    # loading datasets
    print("Loading datasets")
    global model
    model, tokenizer = load_model_and_tokenizer(model_name=model_path, use_gpu=True)

    if args.eval:
        # os.environ["CUDA_VISIBLE_DEVICES"] = "1,2,3"
        print("device count:", torch.cuda.device_count())
        print("Start Evaluating")
        response = test_before_post_training(model=model, tokenizer=tokenizer)
        print(response)

    if args.tune:
        # os.environ["CUDA_VISIBLE_DEVICES"] = "1,2,3"
        print("device count:", torch.cuda.device_count())
        # loading data
        dpo_ds = gen_dpo_dataset()
        print("Start finetuning using DPO")
        DPO_training(model=model, tokenizer=tokenizer, dpo_ds=dpo_ds)

    print("Evaluation and tuning process done.")
```


### GRPO Training

```python
import torch
import re
import os
import pandas as pd
from tqdm import tqdm
from datasets import load_dataset, Dataset
from transformers import AutoTokenizer, AutoModelForCausalLM, TrainingArguments
from trl import GRPOTrainer, GRPOConfig


def generate_responses(
    model,
    tokenizer,
    user_message=None,
    system_message=None,
    max_new_tokens=300,
    full_message=None,
):
    # Format chat using tokenizer's chat template
    if full_message:
        messages = full_message
    else:
        messages = []
        if system_message:
            messages.append({"role": "system", "content": system_message})
        messages.append({"role": "user", "content": user_message})

    prompt = tokenizer.apply_chat_template(
        messages,
        tokenize=False,
        add_generation_prompt=True,
        enable_thinking=False,
    )

    inputs = tokenizer(prompt, return_tensors="pt").to(model.device)
    with torch.no_grad():
        outputs = model.generate(
            **inputs,
            max_new_tokens=max_new_tokens,
            do_sample=False,
            pad_token_id=tokenizer.eos_token_id,
            eos_token_id=tokenizer.eos_token_id,
        )
    input_len = inputs["input_ids"].shape[1]
    generated_ids = outputs[0][input_len:]
    response = tokenizer.decode(generated_ids, skip_special_tokens=True).strip()

    return response


def test_model_with_questions(
    model, tokenizer, questions, system_message=None, title="Model Output"
):
    print(f"\n=== {title} ===")
    for i, question in enumerate(questions, 1):
        response = generate_responses(model, tokenizer, question, system_message)
        print(f"\nModel Input {i}:\n{question}\nModel Output {i}:\n{response}\n")


def load_model_and_tokenizer(model_name, use_gpu=False):

    # Load base model and tokenizer
    tokenizer = AutoTokenizer.from_pretrained(model_name, device_map="auto")
    model = AutoModelForCausalLM.from_pretrained(model_name)

    if not tokenizer.chat_template:
        tokenizer.chat_template = """{% for message in messages %}
                {% if message['role'] == 'system' %}System: {{ message['content'] }}\n
                {% elif message['role'] == 'user' %}User: {{ message['content'] }}\n
                {% elif message['role'] == 'assistant' %}Assistant: {{ message['content'] }} <|endoftext|>
                {% endif %}
                {% endfor %}"""

    # Tokenizer config
    if not tokenizer.pad_token:
        tokenizer.pad_token = tokenizer.eos_token

    return model, tokenizer


def display_dataset(dataset):
    # Visualize the dataset
    rows = []
    for i in range(3):
        example = dataset[i]
        user_msg = next(
            m["content"] for m in example["messages"] if m["role"] == "user"
        )
        assistant_msg = next(
            m["content"] for m in example["messages"] if m["role"] == "assistant"
        )
        rows.append({"User Prompt": user_msg, "Assistant Response": assistant_msg})

    # Display as table
    df = pd.DataFrame(rows)
    print(df)


def post_process_dataset(example: dict) -> dict:
    """
    Extracts the final numeric answer and formats the prompt for the model.

    Args:
        example (dict): A single example from the dataset.

    Returns:
        dict: The processed example with 'ground_truth' and 'prompt' keys.
    """
    match = re.search(r"####\s*(-?\d+)", example["answer"])
    example["ground_truth"] = match.group(1) if match else None
    SYSTEM_PROMPT = (
        "You are a helpful assistant that solves problems step-by-step. "
        "Always include the final numeric answer inside \\boxed{}."
    )
    example["prompt"] = [
        {"role": "system", "content": SYSTEM_PROMPT},
        {"role": "user", "content": example["question"]},
    ]
    return example


def reward_func(completions, ground_truth, **kwargs):
    """
    Calculates the reward for model completions based on a ground truth.

    Args:
        completions (list): A list of model completions, each a list of dictionaries.
        ground_truth (list): A list of true answers.

    Returns:
        list: A list of rewards (1.0 for correct, 0.0 for incorrect).
    """
    # Regular expression to capture content inside \boxed{}
    matches = [
        re.search(r"\\boxed\{(.*?)\}", completion[0]["content"])
        for completion in completions
    ]
    contents = [match.group(1) if match else "" for match in matches]
    # Reward 1 if the content is the same as the ground truth, 0 otherwise
    return [1.0 if c == gt else 0.0 for c, gt in zip(contents, ground_truth)]


def evaluate_model(model, tokenizer, eval_dataset: torch.utils.data.Dataset):
    """
    Evaluates a model's performance on a given dataset using the reward function.

    Args:
        model: The model to evaluate.
        tokenizer: The tokenizer for the model.
        eval_dataset (Dataset): The evaluation dataset.
    """
    all_preds = []
    all_labels = []

    print("Starting evaluation...")
    for example in tqdm(eval_dataset, desc="Evaluating"):
        input_prompt = example["prompt"]
        ground_truth = example["ground_truth"]
        with torch.no_grad():
            response = generate_responses(model, tokenizer, full_message=input_prompt)
        all_preds.append([{"role": "assistant", "content": response}])
        all_labels.append(ground_truth)

    rewards = reward_func(all_preds, all_labels)
    accuracy = sum(rewards) / len(rewards) if len(rewards) > 0 else 0.0
    print(f"Evaluation Accuracy: {accuracy:.2%}")


def main():
    """
    Main function to orchestrate the GRPO training and evaluation process.
    """
    USE_GPU = torch.cuda.is_available()
    DATASET_PATH = "./data/openai/gsm8k"
    TRAIN_MODEL_NAME = "HuggingFaceTB/SmolLM2-135M-Instruct"
    EVAL_MODEL_NAME = "Qwen/Qwen2.5-0.5B-Instruct"

    # --- 1. Load and preprocess datasets ---
    print("Loading and preprocessing datasets...")
    dataset = load_dataset(DATASET_PATH, "main")
    train_dataset = (
        dataset["train"]
        .map(post_process_dataset)
        .remove_columns(["question", "answer"])
    )
    eval_dataset = (
        dataset["test"]
        .select(range(5))
        .map(post_process_dataset)
        .remove_columns(["question", "answer"])
    )

    print(f"Length of dataset: {len(train_dataset)}")

    # --- 2. GRPO Training ---
    print("Starting GRPO training...")
    grpo_config = GRPOConfig(
        per_device_train_batch_size=1,
        gradient_accumulation_steps=8,
        num_generations=4,
        num_train_epochs=1,
        learning_rate=5e-6,
        logging_steps=2,
        no_cuda=not USE_GPU,
    )
    model, tokenizer = load_model_and_tokenizer(f"./models/{TRAIN_MODEL_NAME}", USE_GPU)
    grpo_trainer = GRPOTrainer(
        model=model,
        args=grpo_config,
        reward_funcs=reward_func,
        train_dataset=train_dataset,
    )
    grpo_trainer.train()

    # --- 3. Save the trained model ---
    print("Saving the trained model...")
    grpo_trainer.save_model("./models/own/SmolLM2-135M-Instruct-GRPO")

    # --- 4. Evaluate the base model ---
    print("Evaluating base model...")
    base_model, base_tokenizer = load_model_and_tokenizer(
        f"./models/{EVAL_MODEL_NAME}", USE_GPU
    )
    evaluate_model(base_model, base_tokenizer, eval_dataset)

    # --- 5. Evaluate the fine-tuned model ---
    print("Evaluating trained GRPO model...")
    trained_model = grpo_trainer.model
    evaluate_model(trained_model, tokenizer, eval_dataset)


if __name__ == "__main__":
    # todo: add argparse for GRPO training
    # setting gpu environ
    os.environ["CUDA_VISIBLE_DEVICES"] = "0,1,2,3,4,5,6,7"
    print(f"Device num: {torch.cuda.device_count()}")
    main()
```


