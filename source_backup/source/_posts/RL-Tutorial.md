---
title: "RL Tutorial: MDP Process"
date: 2025-09-14 17:14:31
index_img: /img/cover/RL.jpg
math: true
excerpt: Tutorials for reinforcement learning, and the basic introduction of markov decision process.
categories:
  - Artificial Intelligence
  - RL
tags:
  - reinforcement learning
  - Artificial Intelligence
  - Deep Learning
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Reward is all you need?

It is the era of reinforcement learning!

## Several Courses

- [CS285 Berkeley](https://rail.eecs.berkeley.edu/deeprlcourse/) for Deep Reinforcement Learning
  - The [resources](https://rail.eecs.berkeley.edu/deeprlcourse/resources/) it provided.
- [ACM Hands with Reinforcement Learning](https://hrl.boyuai.com/chapter/1/%E5%88%9D%E6%8E%A2%E5%BC%BA%E5%8C%96%E5%AD%A6%E4%B9%A0)
- [Deep Reinforcement Learning by Hongyi Li](https://www.youtube.com/watch?v=z95ZYgPgXOY&list=PLJV_el3uVTsODxQFgzMzPLa16h6B8kWM_&index=3)
- [CS 234 Reinforcement Learning](https://web.stanford.edu/class/cs234/)


## Introduction

> The initial course for CS234, introduction to reinforcement learning.

Reinforcement learning generally involves:

- Optimization methods (General)
- Delayed consequences
- Exploration
- Generalization

Evaluation and explore!

| | **AI Planning** | **SL** | **UL** | **RL** | **IL** |
| :--- | :---: | :---: | :---: | :---: | :---: |
| **Optimization** | X | | | X | X |
| **Learns from experience** | | X | X | X | X |
| **Generalization** | X | X | X | X | X |
| **Delayed Consequences** | X | | | X | X |
| **Exploration** | | | | X | |

> IL: Imitation learning, like a parrot.
> RLHF: Reinforcement learning based on human feedback.

RLHF and DPO are classical offline Rl algorithms. We will not explicitly "explore" during the training process, instead, we will just inspect and learn from the collected historical data.(e.g. policy gradient algorithm.)

{% note primary %}

We can see that exploration is the uniqueness of reinforcement learning!

{% endnote %}

## Simple Demo

- Action Space?
- State Space?
- Reward?
- Actions for transformation?
- Reward Hacking

### Interacting with the world

For each time within a discrete timestamp, the agent will do:

- Action $A_t$ to interact with the environment
- Agent get the reward $R_t$ and the observation $O_t$
- Finally, it will update itself for the next iteration!
- History: $(a_1, o_1, h_1, a_2, o_2, h_2, \dots )$

## Markov Decision Process, MDP

A **Markov Decision Process (MDP)** is a mathematical framework for modeling sequential decision-making in situations where outcomes are partly random and partly under the control of a decision-maker. 

An MDP is formally defined by five key components: $(S, A, P, R, \gamma)$.

* **$S$ (State Space):** This is the set of all possible states of the environment. A state must have the **Markov property**, meaning the future **depends only on the current state and action**, not on the sequence of events that led to it. 

  For example: after $t$ steps, the future depends on $S_{t-1}$ and $A_t$

* **$A$ (Action Space):** This is the set of all possible actions an agent can take. 

* **$P$ (Transition Probability):** This defines the dynamics of the environment. $P(s' | s, a)$ is the probability of transitioning to state $s'$ from state $s$ after taking action $a$. This is the stochastic part of the MDP, representing the uncertainty in the environment.

  - It is just a probability.
  - The state $s_t$ is markov iff:
  $$p(s_t|a_{t-1}, s_{t-1}) = p(s_t|a_{t-1}, h_{t-1})$$

* **$R$ (Reward Function):** This function determines the immediate reward the agent receives after taking action $a$ in state $s$ and landing in state $s'$. The reward is a numerical value that tells the agent how good or bad a particular action is in a given state. The ultimate goal of the agent is to maximize its cumulative reward over time.
  - reward function: 
  $$r(s_t =s, a_t = a) = \mathbb{E}([r_t|s_t, a_t])$$
  - or we can say the general reward for the state $s_{t+1}$

* **$\gamma$ (Discount Factor):** This is a value between 0 and 1 that discounts future rewards. It's used to give more weight to immediate rewards compared to rewards received in the distant future. A discount factor of 0 makes the agent "myopic" (only considers immediate rewards), while a factor closer to 1 makes the agent "farsighted" (values long-term rewards).

  - Define the state value function:
  $$V(s) = \mathbb{E}[G_t|s_t =s]$$

  $$G_t = r_t + \gamma r_{t+1} + \dots + \gamma^{H} r_{t+H}$$

The central problem of an MDP is to find an optimal **policy** $(\pi)$. A policy is a strategy that tells the agent **which action to take in each state**. The optimal policy is the one that maximizes the agent's total expected cumulative reward over a long sequence of decisions.

- Determine: $\pi(s) = a$
- Stochastic: $\pi(a|s) = Pr(a_t = a | s_t = s)$

### Matrix & Linear Transformation

$$P = 
\begin{pmatrix}
P_{11} & P_{12} & \cdots & P_{1N} \\
P_{21} & P_{22} & \cdots & P_{2N} \\
\vdots & \vdots & \ddots & \vdots \\
P_{N1} & P_{N2} & \cdots & P_{NN}
\end{pmatrix}$$

$$\pi_{t+1} = P \pi_{t}$$


### Is the world markov?

The world is partially observable.

- We have **Partially Observable Markov Decision Process (POMDP)**

- In these cases, state is less the observations.(In real world senarios, it actually is!)


$$b'(s') = P(s' | o, a, b) = \frac{P(o | s', a) \sum_{s \in S} P(s'|s, a) b(s)}{P(o | a, b)}$$


$$V(b) = \max_{a \in A} \left[ R(b, a) + \gamma \sum_{o \in \Omega} P(o|a, b) V(b'_{o,a}) \right]$$


* **$V(b)$**：当前信念 $b$ 的价值。
* **$R(b, a)$**：在信念 $b$ 下执行动作 $a$ 的**期望即时奖励**。这是对所有可能状态的奖励加权求和：$R(b, a) = \sum_{s \in S} b(s)R(s, a)$。
* **$\sum_{o \in \Omega} P(o|a, b) V(b'_{o,a})$**：这部分是**期望未来价值**。它对所有可能的观察 $o$ 进行加权，每个观察的权重是其发生的概率 $P(o|a, b)$，而其价值是新的信念 $b'$ 的价值 $V(b')$.





