---
title: LLM Learning Initial
date: 2025-07-27 15:19:57
index_img: /img/cover/llmini.jpg
excerpt: Initial plan and learning materials for learning large language models from scratch.
math: True
categories:
  - Artificial Intelligence
  - LLM
tags:
  - Tutorial
  - LLM
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# LLM Learning Initial

Welcome to the world of Large Language Models

For this summer vacation, I will begin the learning process for the fundamental principle and downstream techniques and applications for **LLM** (Large Language Models). I hope to find the journey interesting and fruitful!

{% note info %}

All the code will be refactored and categorized into [LLM Learn](https://github.com/xiyuanyang-code/LLM_Learn)

{% endnote %}

## LLM Learning Materials

- Courses and Videos

    - [Post Training by DeepLearning Ai](https://www.deeplearning.ai/short-courses/post-training-of-llms/)

    - [CS25: Transformers United V5](https://web.stanford.edu/class/cs25/): For advanced architectures for transformers.

    - [Transformers for 3b1b](https://www.youtube.com/playlist?list=PLZHQObOWTQDNU6R1_67000Dx_ZCJB-3pi): A good visualize demo!

    - [**Advanced Natural Language Processing**](https://phontron.com/class/anlp-fall2024/): A very good course for detailed lecture notes and homework. (I will try to finish that project in the next semester.)

    - [LLMs and Transformers](https://www.ambujtewari.com/LLM-fall2024/#logistics--schedule): with several discussion topics (lecture notes, blogs and papers are included)

    - [Dive into LLMs](https://sjtullm.gitbook.io/dive-into-llms): The chinese version of learning large language models

    - [**Learning Large Language Models from scratch**](https://stanford-cs336.github.io/spring2025/): The course for learning LLMs in stanford.

    - [Large Language Models for DataWhale](https://www.datawhale.cn/learn/summary/107): Courses of Chinese version.

- Several books and codes: 

    - Hands on Large Language Models (English Version)

        - Original English Version: [Hands on Large Language Models](https://github.com/HandsOnLLM/Hands-On-Large-Language-Models)

        - https://www.llm-book.com/

    - Hands on Large Language Models (CHN Version)

        - [Source Code](https://github.com/bbruceyuan/Hands-On-Large-Language-Models-CN)

    - Build a Large Language Model (From Scratch)

        - [Github Repo](https://github.com/rasbt/LLMs-from-scratch)

        - [Additional Technical Blog](https://magazine.sebastianraschka.com/p/llm-research-papers-the-2024-list)

- Projects: 

    - LLM Hero to Zero:

        - Build a simple GPT from scratch.

        - [LLM from hero to zero, karpathy version](https://karpathy.ai/zero-to-hero.html)

            - [Source Code](https://github.com/karpathy/ng-video-lecture)

            - [Lectures Videos](https://www.youtube.com/watch?v=kCc8FmEb1nY)

        - [LLM from hero to zero, CHN version](https://yuanchaofa.com/llms-zero-to-hero/)

            - [Source Code](https://github.com/bbruceyuan/LLMs-Zero-to-Hero)

- Tool Usage

    - HuggingFace for downloading models and datasets

    - [vllm](https://github.com/vllm-project/vllm)

## LLM Learning Contents

- Basic architecture for Large Language Models

    - Attention Mechanism (Attention is all you need!) ✅

    - RNN, LSTM, GRU (will be covered in the future)

    - Seq2Seq Model ✅

    - Transformer Architecture ✅

    - Other basic NLP knowledge (word embeddings, etc.)

- Pre Training for LLM

    - Loading Datasets

    - Self-supervised Learning

    - More advanced architecture for LLM pre-training, see advanced structure part.

- Post Training for LLM

    - Quantization for Model Optimization

    - Knowledge Distillation

    - Fine-tuning Techniques
        - SFT
        - RFT
        - RLHF (Reinforcement Learning from Human Feedback)

    - LLM Evaluation

- Advanced Structure for LLM

    - [Advanced Transformer](https://spaces.ac.cn/search/Transformer%E5%8D%87%E7%BA%A7%E4%B9%8B%E8%B7%AF/)

    - Sparse Attention & Lightning Attention

    - KV cache

    - Mixture of Experts (MoE)
        - [MoE Introduction](https://mp.weixin.qq.com/s/kUF4cy1QA_xQSyT5HtcKIA)

        - [MoE Advanced](https://yuanchaofa.com/llms-zero-to-hero/the-way-of-moe-model-evolution.html#_2-%E7%89%88%E6%9C%AC2-sparsemoe-%E5%A4%A7%E6%A8%A1%E5%9E%8B%E8%AE%AD%E7%BB%83%E4%BD%BF%E7%94%A8)

    - LoRA: Low-Rank Adaptation of Large Language Models

    - PPO, GRPO, DPO, etc. (Deep Reinforcement Learning)

- Test time compute for LLM (after training)

    - LLM Reasoning (CoT, ToT, etc.)

        - Recommend Blog: [Why we think by Lilian Weng](https://lilianweng.github.io/posts/2025-05-01-thinking/)

- LLM DownStream Applications

    - This section will be recorded in the future.

    - RAG

    - LangChain Community

## Updating Status

- `2025/07/28`: Finish two long-standing blog posts: `AINN-Attention` & `AINN-Transformer`

    - Finish tutorial for basic Attention mechanism and Transformer Structure.

- `2025/08/23`: Finish the first three course for CS336 Building large language models from scratch
    - Lecture1: The course overview & word embeddings
    - Lecture2: resource accounting & Pytorch basics (some fancy techniques)
    - Lecture3: Different variants on transformer architectures, some tricks on hyperparameter and stable training
    - **Learning Plan adjusting!**: For CS 336 is a bit hard for advanced techniques, we will first learn some lessons on CS224n, focusing on **training**.
        - Unofficial Course Website: [Course on bilibili](https://www.bilibili.com/video/BV1vQMBz6EvP?spm_id_from=333.788.videopod.episodes&vd_source=6955add1d28c52cd48096d58e09ce798&p=15)
        - Week5: Pre-Training
        - Week6: Post-Training
        - Optional: [Hugging Face Tutorial](https://www.youtube.com/watch?v=b80by3Xk_A8&list=PLoROMvodv4rMFqRtEuo6SGjY4XbRIVRd4&index=21)
        - Optional: [MultiModel DeepLearning](https://www.youtube.com/watch?v=5vfIT5LOkR0&list=PLoROMvodv4rMFqRtEuo6SGjY4XbRIVRd4&index=22)
        - Optional: [Model Interpretability & Editing](https://www.youtube.com/watch?v=cd3pRpEtjLs&list=PLoROMvodv4rMFqRtEuo6SGjY4XbRIVRd4&index=23)


## Current Todo List

- Finish the implementation code of Transformer Module in dl2ai.

- Learning courses: Word Embedding and basic NLP knowledge.