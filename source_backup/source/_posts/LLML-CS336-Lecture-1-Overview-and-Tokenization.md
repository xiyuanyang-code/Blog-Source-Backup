---
title: "LLML CS336: Lecture 1 Overview and Tokenization"
date: 2025-07-28 23:39:49
index_img: /img/cover/tokenize.jpg
excerpt: "Lecture Notes for CS336: Lecture 1 Overview and Tokenization, as a part of tutorial for LLM Learning Plan as well."
math: True
categories:
  - Artificial Intelligence
  - LLM
tags:
  - Tutorial
  - LLM
  - Tokenization
---

# CS336: Lecture 1 Overview and Tokenization

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

## Course Overview

[Course Information](https://stanford-cs336.github.io/spring2025/)

**ALL FOCUSED ON EFFICIENCY**

The main 5 components for this course:

![Course Components](https://stanford-cs336.github.io/spring2025-lectures/images/design-decisions.png)

More descriptions about the course overview can be found in [Course Lecture Notes](https://stanford-cs336.github.io/spring2025-lectures/?trace=var%2Ftraces%2Flecture_01.json).

## More than a course overview...

What I will learn from this course?

Full understanding of this technology is necessary and crucial for fundamental search, rather than just calling a prompt. 

{% note info %}

There are three types of knowledge:
    
- Mechanics: how things work (what a Transformer is, how model parallelism leverages GPUs)
    
- Mindset: squeezing the most out of the hardware, taking scale seriously (scaling laws)
    
- Intuitions: which data and modeling decisions yield good accuracy
    
We can teach mechanics and mindset (these do transfer). We can only partially teach intuitions (do not necessarily transfer across scales).

{% endnote %}

### About Scaling Law & The bitter lesson

Is the algorithm really making no sense? Of course not! We cannot afford to make it so wasteful!

- Wrong interpretation: scale is all that matters, algorithms don't matter.
- Right interpretation: **algorithms that scale is what matters**.
    
$$\text{accuracy} = \text{efficiency} \times \text{resources}$$

In fact, efficiency is way more important at larger scale (can't afford to be wasteful). In other words, maximize efficiency!

## Tokenization

[Recommended Resources](https://www.youtube.com/watch?v=zduSFxRajkE)

The tokenization includes the **encoding and decoding process** for the word list. We will focus on **Byte-pair Encoding** (BPE) for this section.

- Encoding: convert string to numbers

- Decoding: convert numbers back to strings

The vocabulary size is the number.

### Several Notifications

- All the blank space are tokens

- spaCy internally uses **hash values to represent strings**, and these hashes map to integer IDs.

