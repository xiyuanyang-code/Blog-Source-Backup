---
title: CS294-3-Autogen
date: 2025-03-07 15:08:36
index_img: /img/cover/autogen.jpg
excerpt: This Blog introduces Autogen, which is developed by Microsoft, an multi-agent framework.
categories:
  - Artificial Intelligence
  - CS294 LLM Agents
tags:
  - Artificial Intelligence
  - Autogen
  - Finished
  - Agent
  - Deep Learning
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# CS294/194-196 Autogen

Finally back... So sorry for the updating absence these days.

## Introduction

In nowadays GPT models, which is known as **Generative Large Language Models**, is extremely powerful in **generating new tokens**, including generating texts, images, or even videos. We see the hope of **AGI** (Artificial General Intelligence) coming in the next few years. However, for real-world scenario, there is still plenty of room for improvement in **enhancing the breadth and depth of problem-solving** for large language models.

- **For depth**: LLM's reasoning and reinforcement learning may be the final way out! (We won't discuss this topic now, you can skip to my another blog focusing on LLM reasoning instead)
- **For breadth**: Well, let's dive deeper into this problem.

Assume you want to develop a small program for your study (a program for arranging daily plans, etc). You can you **GPT-4o** or **Deepseek** by using prompt of "Please help me develop a python program to arrange my daily routines and tasks automatically."

Then models may reply a response like this:

![gpt](https://s1.imagehub.cc/images/2025/03/07/b3b864199610be7ceaf0158631223db9.png)

> I use **Next-chat** applications by filling my `gpt-api-key`.

It's great, but **when the task is becoming more complex**, it's likely for models to make mistakes. For example, if you ask only one AI model to write a brand new **operating system**, it won't get a satisfied response.

![When you help AI to build Large-scale projects...](https://s1.imagehub.cc/images/2025/03/07/85e76179162461fb85698975d7750dca.png)

**So what's next**? Enhancing the single performance won't get too much progress compared to the increasing cost of computing resources. Thus, **multi-agent** is here to implement and solve large-scale developing projects!

Today, we gonna introduce `Autogen`[^1], a framework for creating multi-agent AI applications that can act autonomously or work alongside humans. This framework is developed by **Microsoft**.

## Multi-Agent Orchestration

- Static / Dynamic
- Context Sharing / isolation
- Cooperation / competition
- Centralized / decentralized
- Intervention / automation

## Agent Design Patterns

- Conversations
- Prompting & Reasoning
	- React
	- Chain of thought
- Tool use
- Planning
- Integrating multiple models, modalities and memories

## 2 Core Operations

- **Define Agents**: Conversable & Customizable
- **Get them to talk**: Conversation Programming

# LlamaIndex

## A better Knowledge Assistant

Basic Rag use the traditional **Embeddings** methods to split the text into chunks and search the text with the similarity... It is too naive sometimes, leading to the hallucination and moreover, large language models only needs to summarize the given text which is a quite simple task for them.

So we need a better RAG!

- **High-Quality Multi-Modal RAG**
- Complex Output Generation.(Generating a report, etc.)
- Agentic Reasoning over complex inputs
- Towards a scalable, full-stack development.

Traditional Embedding methods only handle with text messages.

### Setting up a Multi Modal RAG

Visual Data...

We need a LLM-PDF-parser to make the parsing and chunking period, which could decrease the **hallucination** when LLMs are faced with visual data.

![Document Parser](https://s1.imagehub.cc/images/2025/04/06/34a89d11e713bb31c1f1facdc9d48342.png)

### Agentic Reasoning over complex inputs

Like the ReAct model

- Tool Use
- Planning
- Memory
- Reflection

#### Unconstrained & Constrained Flows

- Constrained Flows
- ![Constrained FLows](https://s1.imagehub.cc/images/2025/04/06/c3d3cff5953222f1b9f16ce683176dde.png)
	- We design a **router** based on prompts to let LLM design which tools to use, and then let the LLM reflect on the performance, finally getting the output.
	- It is more reliable, but less expressive, because the router will only allocate different tasks to relatively fixed-tool use.

- Unconstrained Flows
- ![Unconstrained Flows](https://s1.imagehub.cc/images/2025/04/06/e45c84af30feadaf226fa761aba64512.png)
	- **Agent Orchestrator** could decide which tools to use automatically and combine different tools together.
	- In this case, you no longer use simple if-else allocation logic, but instead give the agent greater autonomy. This can lead to a heavy reliance on the agent's capabilities and may result in consequences such as **infinite loops**. (That is why it is **less reliable**)



## References

[^1]: https://github.com/microsoft/autogen
