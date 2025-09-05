---
title: LLML CS224n Lecture 12 Evaluation
date: 2025-09-05 23:40:15
index_img: /img/cover/llmevaluation.jpg
excerpt: "Lecture Notes for CS224n, focusing on current bottleneck for LLM evaluations and several shallow thoughts of the new blog proposed by Shunyu Yao: We are on the second half."
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

Evaluation is really a hard problem...

{% note primary %}

Just a really short record for the course for grasping the basic meanings on the slide...

I will optimize it in the near future for I am recently devoted on the work of the novel MCP bench. :)

{% endnote %}


# CS224N Lecture 12: Evaluation & Benchmarking


## Intro: We are on the second half

Original article: [We are on the second half by Shunyu Yao](https://ysymyth.github.io/The-Second-Half/)

> tldr: We are at AI's second half, benchmarking is much more important than training.

In three words: **RL finally works**. More precisely: **RL finally generalizes**. After several major detours and a culmination of milestones, we’ve landed on a working recipe to solve a wide range of RL tasks using language and reasoning. Even a year ago, if you told most AI researchers that a single recipe could tackle software engineering, creative writing, IMO-level math, mouse-and-keyboard manipulation, and long-form question answering — they’d laugh at your hallucinations. Each of these tasks is incredibly difficult and many researchers spend their entire PhDs focused on just one narrow slice.

Up to now, thanks to efficient "pre-training - post-training" paradigm, AI has successfully solved nearly all learnable problems with clear answers, even achieving performance far beyond human level. Thus, the bottleneck for AI development lies in:

- How to maximize AI's potential and prevent it from being confined to a limited dialogue box: We have **Agents**.

- How to leverage AI's generalization problems and solving more problems that have no accurate answer (creative & innovative)?

Thus, the capability of foundation model still needs to be improved.

In the past, we mainly focus on getting high-quality training data, training or tuning better models and finding innovative algorithms and methods. This leads to the great progress of LLM technical improvement. In this case, **Evaluation and Benchmarking** seems less important, for they are obvious on those training tasks. (Maths, Codes, Problem solving...)

> In contrast, defining tasks for AI often felt more straightforward: we simply took tasks humans already do (like translation, image recognition, or chess) and turned them into benchmarks. Not much insight or even engineering.

### Example: Reinforcement Learning

- algorithms: attract more attention!
- environment: show less attention, but of equal (in current status, even more) importance.
    - e.g.: `gym` in OpenAI.
- priors: 先验知识
    - For the initial state, the model can only do exploration but no exploitation, which leads to extremely bad results.
    - e.g.: Imitate Learning, pre-training models

{% note primary %}

Language pre-training created good priors for chatting, but not equally good for **controlling computers or playing video games**. Why? These domains are further from the distribution of Internet text, and naively doing SFT / RL on these domains generalizes poorly. I noticed the problem in 2019, when GPT-2 just came out and I did SFT / RL on top of it to solve text-based games - CALM was the first agent in the world built via pre-trained language models. But it took millions of RL steps for the agent to hillclimb a single game, and it doesn’t transfer to new games. Though that’s exactly the characteristic of RL and nothing strange to RL researchers, I found it weird because we humans can easily play a new game and be significantly better zero-shot. Then I hit one of the first eureka moment in my life - we generalize because we can choose to do more than “go to cabinet 2” or “open chest 3 with key 1” or “kill dungeon with sword”, we can also choose to think about things like “The dungeon is dangerous and I need a weapon to fight with it. There is no visible weapon so maybe I need to find one in locked boxes or chests. Chest 3 is in Cabinet 2, let me first go there and unlock it”.


> We still need to collect high-quality data in the pre-training process, which agents can learn general abilities.

{% endnote %}

### Tradition RL & Reasoning

For the reasoning process, the tradition RL view may be quite strange, for its action space is infinitely (almost) large, which could cause expectations from sampling will be zero. (I think that is why strong-reasoning models may act hallucination in real-world actions.)

However, the reasoning will lead to augmentation of generalization, choosing these boxes prepare you to better choose the box with money for any given game. My abstract explanation would be: **language generalizes through reasoning in agents**.



### The second half

- Current benchmark has been explored, for the great enhancement of generalization ability for SOTA foundation models.

{% note primary %}

If novel methods are no longer needed and harder benchmarks will just get solved increasingly soon, what should we do?

{% endnote %}

Evaluation process should be refactored and rebuilt. **It means not just to create new and harder benchmarks, but to fundamentally question existing evaluation setups and create new ones**, so that we are forced to invent new methods beyond the working recipe. It is hard because humans have inertia(惯性) and seldom question basic assumptions - you just take them for granted without realizing they are assumptions, not laws: the utility problems.

- Agents should not pass the benchmark automatically. In real-world problems, agent should frequently interacting with humans.

- Agents should not run i.i.d (independent and identically distributed). Real world tasks should be run mostly sequentially.
    - Long-term Memory Methods & and some benchmarks!


{% note primary %}

This game is hard because it is unfamiliar. But it is exciting. While players in the first half solve video games and exams, players in the second half get to build billion or trillion dollar companies by building useful products out of intelligence. While the first half is filled with incremental methods and models, the second half filters them to some degree. The general recipe would just crush your incremental methods, unless you create new assumptions that break the recipe. Then you get to do truly game-changing research.



{% endnote %}


## Types of Evaluation

- Close-ended
    - Has fixed answers, can be grades easily.
    - Several Metrics: F1 score, precision, recall rate, average accuracy, AUC score.
    - Methods to aggregate different metrics? **A complex task** for different benchmark has different meanings! 
- Open-ended

## Close Ended Evaluations

Achieving accurate results doesn't mean this test set accurately measures the performance of large language models. A significant number of inaccurate and fabricated tests have been discovered by relevant research.


### Example: SNLI (Stanford Natural Language Inference)

SNLI stands for **Stanford Natural Language Inference**. It's a large, widely-used dataset specifically designed for the task of **Natural Language Inference (NLI)**.

Natural Language Inference is a task where you determine the logical relationship between a pair of sentences: a **premise** and a **hypothesis**. Your goal is to classify this relationship into one of three categories:

1.  **Entailment:** If the premise is true, the hypothesis must also be true.
    * **Premise:** A man is riding a horse.
    * **Hypothesis:** A man is outdoors.
    * **Relationship:** Entailment (Because if he's riding a horse, he's very likely outdoors).

2.  **Contradiction:** If the premise is true, the hypothesis must be false.
    * **Premise:** A cat is sleeping on the mat.
    * **Hypothesis:** A dog is standing awake.
    * **Relationship:** Contradiction (These two sentences describe conflicting situations).

3.  **Neutral:** There is no clear logical relationship between the premise and the hypothesis.
    * **Premise:** Several women are talking about an exhibit.
    * **Hypothesis:** They are looking at paintings.
    * **Relationship:** Neutral (They might be looking at paintings, but we can't be certain from the premise alone).

The SNLI dataset contains over **570,000** premise-hypothesis pairs, each manually labeled with one of the three relationships. The premise sentences are sourced from the **Flickr 30k** dataset, which are sentences that describe images. The hypothesis sentences were created by human crowdworkers based on those premises.


## Open Ended Evaluations

For long generation tasks (e.g. Translating, Summary...), the grading and judges of the given tests are continuous, these are open ended evaluations.

- Content overlap metrics


### Content overlap metrics

- Using **Word overlap–based metrics (BLEU, ROUGE, METEOR, CIDEr, etc.)**.

    - Fit and efficient for simple translating and summary tasks.

    - Using N-grams for evaluate similarity beyond the word layer. (BLEU: Bilingual Evaluation Understudy)

- **Model-based metrics** to capture more semantics
    - Use learned representations of words and sentences to compute semantic similarity between generated and reference texts.

    - No more N-grams bottle necks for the trained bottleneck.

    - The embeddings are pretrained, distance metrics used to measure the similarity can be fixed.

    - Vector Similarity, Word Mover's Distance, BERTSCORE.

- Beyond simple model-based metrics
    - Sentence Movers Similarity: Based on **Word Movers Distance** to evaluate text in a continuous space using sentence embeddings from recurrent neural network representations.
    - BLEURT: A regression model based on BERT returns a score that indicates to what extent the candidate text is **grammatical and conveys the meaning of the reference text**.
        - Very important thought: pre-training large language models (or just simply using large language models) to evaluate for grasping more semantic meaning.

> From BLEU to nowadays, the evaluation has never stopped.


## Human Evaluations

Original and naive grading from human feedback is weak and biased!

- Results are inconsistent / not reproducible
- can be illogical
- misinterpret your question
- Precision not recall.

For human evaluation, we often use **Arena** (Just like RLHF in post-training process).
- Side by side ratings will eliminate the bias.

As large language models shows great generalization capabilities and context-learning abilities, we can use LLM as the judger to replace human.
> Compared with humans, LLM has less bias and more consistency.


### AlpacaEval

See the paper and repo, quite important work!

- [GitHub Repo](https://github.com/tatsu-lab/alpaca_eval)
- [Paper](https://arxiv.org/abs/2404.04475)


## Current Challenges

- Consistency (Hard to know if we are evaluating the right thing)

- Contaminations (Data contaminations from the pre-training data)

- Biases


