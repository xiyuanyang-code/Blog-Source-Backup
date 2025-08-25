---
title: LLML CS224n Lecture 9 Pre-Training
date: 2025-08-23 17:23:51
index_img: /img/cover/cs224npretraining.jpg
excerpt: "Focusing on several techniques of pre-training: traditional word2vec model, and several pre-training architectures, including encoder-only MLM like BERT, encoder-decoder like T5 and spam corruption and decoder-only structure like GPT models."
math: True
categories:
  - Artificial Intelligence
  - LLM
tags:
  - Finished
  - Tutorial
  - LLM
  - Pre Training
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>


# CS224N PreTraining & Advanced Transformer

The 4th lesson of CS336 will dive deep into MoE structure, which is too complex for a large language model greenhand... Thus, I decide to learn CS224n first for basic techniques for advanced transformers, pre-training and post-training.


## Transformer Preliminaries


RNN problems: 

- Fail to learn long-term dependencies for it requires $O(N)$, linear interaction problem.
- Lack of parallelizability.

Hence, we will use **Transformer**.

For the basic components of the original Transformer and several popular variants, I will just skip that part :).

- Self Attention
- Position Encoding (Sine Encoding & RoPE Encoding)
- Feed Forward Network for introducing non-linearities.
- Masked Attention Layer.



### Transformer Models

- Decoder
    - Multi-Head Attention
    - Residual Block
    - Layer Normalization

- Encoder
    - Actually the same...

- Connection: Cross-Attention


## Word Structure and SubWord Models

- For efficiency reasons, we will not embed every word as an independent token.
    - For there are so many variants among a single word, especially in languages with complex morphology.
    - These variants has quite similar or even the same meaning, but its relatedness has lost during the embedding process.

- We will just split the word into a sequence of known subwords.
    - For mountains of unused word, we  can use **Byte-Pair Encoding**.


- What is the result of BPE?
    - For common words (e.g. words that are split bt blank characters.), things will remain unchanged.
    - For uncommon words (misspelling & variants & novel items), it will split the word into several sub word based on the frequency it has seen in the training data.
    
- After the BPR Process, what about the embedding process?
    - For example, for the word **embeddings**, we can just embed this into a single word (for it is frequently seen) or just split them into several multiple words (em, bed, dings).
    - The actually tokenizing process is based on greedy strategy:
        - Iterate through the word: Starting from the beginning of the word, it attempts to match the longest possible subword in the vocabulary.
        - Longest Match Principle: If a word can be broken down in multiple ways, the BPE tokenizer prioritizes the longest subword that exists in the vocabulary.


## Word2Vec & Distributional Semantics

> "You shall know a word by the company it keeps" (J. R. Firth 1957)

This quote is a summary of distributional semantics, and motivated word2vec.

### Word2Vec

**Word2Vec** is a popular technique for learning **word embeddings**. Developed by Tomas Mikolov and his team at Google, it is a quintessential model of the **distributional semantics** paradigm. 

The goal of Word2Vec is to transform words from a discrete, high-dimensional **one-hot encoding** representation into a **continuous, low-dimensional, and dense vector** format. These vectors are designed to capture the **semantic and syntactic relationships** between words.

For instance, in a well-trained Word2Vec model, we can perform vector arithmetic like:
$$\text{vec}(\text{king}) - \text{vec}(\text{man}) + \text{vec}(\text{woman}) \approx \text{vec}(\text{queen})$$
This demonstrates that the model has successfully learned the semantic relationship of "gender."

Word2Vec consists of two main model architectures: **CBOW** and **Skip-gram**.

#### CBOW (Continuous Bag of Words) Model

The training objective of the CBOW model is to **predict a target word from its surrounding context words**.

-   **How it works**:
    -   The model operates within a sliding window. For example, with a window size of 2, for the sentence `the quick brown fox jumps over the lazy dog`, the context words for the target word `fox` would be `the`, `quick`, `jumps`, `over`.
    -   CBOW takes the vectors of these context words (which are randomly initialized at the start of training), averages or sums them to get a single context vector.
    -   This context vector is then used as input to a simple neural network to predict the target word `fox`.
    -   The training process involves continuously adjusting the word vectors to maximize the accuracy of predicting the target word.

#### Skip-gram Model

The Skip-gram model's training objective is the opposite of CBOW: it **predicts the context words from a target word**.

-   **How it works**:
    -   Again, it operates within a sliding window. With `fox` as the target word, it attempts to predict each of the context words in the window: `the`, `quick`, `jumps`, `over`.
    -   The model takes the vector of the target word `fox` as input and uses a neural network to calculate the probability of each context word appearing.
    -   The training process adjusts the word vectors to **maximize the probability of predicting each of the context words**.

-   **Advantages**:
    -   It works better on large datasets.
    -   It is particularly good at handling **rare words**, as each rare word is used as a target word multiple times to predict its context, leading to more accurate representations.

#### Training Process and Optimizations

The Word2Vec model is essentially a **shallow neural network**. It has no hidden layer, and the word vectors are learned as the weights of the network. To handle the immense size of typical vocabularies, the standard Softmax layer would be computationally prohibitive. Thus, Word2Vec introduced two powerful optimization techniques:

##### Negative Sampling

-   **Problem**: The Softmax function's computation scales linearly with the vocabulary size. With millions of words, computing and updating the Softmax probabilities for all words during each training step is extremely slow.
-   **Solution**: Negative sampling transforms a large multiclass classification problem (predicting the correct word) into a simpler **binary classification problem**.
-   **How it works**:
    -   For a given training sample (e.g., target word `fox`, context word `quick`), we treat it as a **positive sample**.
    -   Simultaneously, we **randomly sample a few unrelated words** from the vocabulary (e.g., `cat`, `tree`, `table`) and designate them as **negative samples**.
    -   The model's objective becomes:
        -   Maximize the similarity of the positive pair (`fox`, `quick`).
        -   Minimize the similarity of the negative pairs (`fox`, `cat`), (`fox`, `tree`), etc.
-   **Effect**: This dramatically reduces the number of parameters that need to be updated in each iteration, significantly speeding up training.

##### Hierarchical Softmax

-   **An alternative optimization method**: It structures the vocabulary into a **Huffman Tree**.
-   **Effect**: This reduces the computational complexity of the Softmax layer from $O(V)$ (where V is the vocabulary size) to $O(\log_2 V)$, accelerating training.

> word2vec will be discussed further in the future blog updating.


## Pre-Trained Word Embeddings

What are the problems?

- The training data we have for our downstream task (like question answering) must be sufficient to teach all contextual aspects of language.
- Most of the parameters in our network are randomly initialized!
- For some words, it just have the mixture meaning of the same word with different meanings in the context.

Thus, we will pre-train the whole network during the pre-training process! The model will get better representations of learning during the joint pre-training process.


## Paradigm: PreTraining-FineTuning Paradigm

We will just do the training:

$$\theta_0 \to \hat{\theta} = \argmin_{\theta} \mathcal{L}_{\text{pre-train}}(\theta)$$

$$\hat{\theta} \to \hat{\theta}' = \argmin_{\theta} \mathcal{L}_{\text{post-train}}(\theta)$$

## Basic Pre-Training Paradigm: Self-Supervised Learning

Self-supervised learning is a variant of unsupervised learning. Its core idea is to **automatically generate "pseudo-labels" from a large amount of unlabeled data** and then use these pseudo-labels to construct a supervised learning task. The model learns the underlying structure and patterns of the data by completing this task. For large language models, these "pseudo-labels" and tasks are automatically created from the raw text data, requiring no human annotation.

The pre-training of Large Language Models (LLMs) is typically based on one of the following two core self-supervised tasks:

### Masked Language Models

> Just like the blanked cloze in the English quiz!

In large language models like BERT, the model randomly "masks" some words in a sentence and is trained to predict what those masked words are.

"Pseudo-labels": In this task, the model is given a masked sentence (e.g., "I [MASK] to Beijing"), and the "pseudo-label" is the unmasked word that exists in the original text (e.g., "want"). This "label" is generated automatically because it's already present in the raw data.


### Causal Language Modeling

- Representative Models: GPT series, Llama, etc.

The model is trained to predict the next word in a sequence. For a sentence sequence (e.g., "The weather is great today, with bright sunshine."), the model is fed "The weather," then predicts "is"; then "The weather is," and predicts "great"; and so on. The "pseudo-label" is **simply the next word in the sequence**. This label is also automatically derived from the raw text.

In math, we will just model that:

$$p_\theta(w_t | w_{1..t-1})$$

> Actually, that is what decoder do in current machine translation problems, like the seq2seq problems which can be solved by RNNs and LSTM models.


## Pre-Training

After explaining the paradigm, let's finally dive into the world of **pre-training**!

### Three Types of architectures

- Encoder Only
- Decoder Only
- Encoder & Decoder Structure



### Encoder Structure

In the original encoder structure, no masked-attention is provided, hence the model can **peek** the sibling words (both left and right), which means the model will directly see the future output during the training process.

Hence, it will get the **bidirectional** context, which mismatch from the target of causal large language models.

$$p_\theta(w_t | w_{1..t-1})$$

However, for language models using mask like BERT (Masked Language Models), it is the perfect architecture to do self-supervised learning! Hence, we will learn like this:

$$h_1, h_2, \dots, h_T = \text{Encoder}(w_1, w_2, \dots, w_T)$$

For the position which has been masked, we call it $h_{\text{masked}}$.

> The Encoding process contains multiple encoding layers.

Then the target of the model is to trying to predict the masked word, if $\tilde{x}$ means the masked sentence:

$$\theta_0 \to \hat{\theta} =  - \argmin_{\theta} \sum_{i} \log p_\theta(x|\tilde{x})$$

Then, the masked vector $h_{\text{masked}} \in \mathbb{R}^{d}$ will be re-embed into the vocabulary size (high dimension) via learned-linear transformation, and each dimension means the probability of certain words fitting into the masked position.

Finally, we can compute the loss function and do optimization!

$$P(v_i | h_{\text{mask}}) = \frac{\exp(y_{\text{logits}, i})}{\sum_{j=1}^{|V|} \exp(y_{\text{logits}, j})}$$

$$L = H(P_{\text{true}}, P_{\text{pred}}) = -\sum_{i=1}^{|V|} P_{\text{true}}(v_i) \log P_{\text{pred}}(v_i)$$

$$L = -\log P(\text{store} | h_{\text{mask}})$$


#### BERT: Bidirectional Encoder Representations from Transformers

The training process and the forward process itself does not bring any innovations or variants in architecture. So the key is **the way of masking**.

- Predict a random 15% of (sub)word tokens.
    - Replace input word with [MASK] 80% of the time
    - Replace input word with a random token 10% of the time (Enhance robustness)
    - Leave input word unchanged 10% of the time (but still predict it!)
- Why? Doesn’t let the model get complacent and not build strong representations of non-masked words. (No masks are seen at fine-tuning time!)

Finally, for down-stream fine-tuning or inference, the model parameter will remain unchanged (or optimized based on labels), and the mask mechanism will be removed, downstream tasks will be based on all the encoded vectors the trained encoder generated.


### Encoder - Decoder

For the text-generation task, BERT doesn't do well for its encoder-only structure. Thus, **span corruption** is created!

- BERT: Encoder Only Structure, mostly used for seq2single task. (semantic analysis)
- Encoder-Decoder: Mostly focused on seq2seq problems.

So the key is, how to transform text-generation task into a seq2seq problem?

#### Span Corruption

Span Corruption is a **self-supervised task** used for pre-training large language models (LLMs). It was first introduced by Google Brain in their renowned **T5 (Text-to-Text Transfer Transformer)** model and has since been adopted by models like **UL2**. 

> The T5 model unifies all NLP tasks into a "text-to-text" format. The Span Corruption task itself is a "text-to-text" task (inputting corrupted text, outputting the original text), which aligns perfectly with the model's overall design philosophy.
> The T5 model uses the basic **Encoder-Decoder** Structure.

Its core idea is to **randomly select and remove contiguous spans of text from an input, and then train the model to reconstruct these deleted spans.** This is similar to BERT's Masked Language Modeling (MLM), but with a key difference: BERT masks individual tokens, while Span Corruption corrupts **a continuous segment of text that may contain multiple words.**

The training process for Span Corruption can be broken down into a few steps:

1.  **Select Spans**: From a complete sentence or passage, the model randomly selects some **contiguous sequences of tokens** to be "corrupted."
    -   For example, in the sentence `The quick brown fox jumps over the lazy dog.`, the model might choose `brown fox` and `lazy` as the corrupted spans.
2.  **Replace Spans**: The selected spans are then replaced with a special **sentinel token**. These tokens are unique, typically represented as `<extra_id_0>`, `<extra_id_1>`, and so on.
    -   The corrupted input sequence becomes: `The quick <extra_id_0> jumps over the <extra_id_1> dog.`
3.  **Construct the Task**: The model's objective is to generate a **target sequence** based on the corrupted input. This target sequence contains all the deleted spans, along with the sentinel tokens used to delimit them.
    -   For our example, the target sequence is: `<extra_id_0> brown fox <extra_id_1> lazy <extra_id_2>`.
    -   Note that the target sequence also starts with a sentinel token and ends with a sentinel token that does not correspond to any corrupted span, which helps the model know when to stop generating.

Span Corruption **explicitly** trains the model to generate **sequences of text**, not just isolated tokens. This makes the model more proficient at **generative tasks** such as summarization, question answering, and machine translation, as these tasks inherently involve generating a text sequence.


### Decoder Only


Using the decoder just like the generator:

$$p_\theta(w_t|w_{1..t-1})$$

For the training process, the target remains unchanged, using the Cross-Entropy Loss:

$$P_\theta(x) = \prod_{t=1}^{T} P_\theta(x_t | x_1, \dots, x_{t-1})$$

$$\mathcal{L}(\theta) = -\sum_{i=1}^{N} \log P_\theta (w_i | w_1, \dots, w_{i-1})$$



#### Generative Pretrained Transformer (GPT)

Original GPT Model

- Transformer decoder with 12 layers, 117M parameters.
- 768-dimensional hidden states, 3072-dimensional feed-forward hidden layers.
- Byte-pair encoding with 40,000 merges
- Trained on BooksCorpus: over 7000 unique books.
    - Contains long spans of contiguous text, for learning long-distance dependencies.


GPT-3: Large Language Models & In-context Learning (Few-shot Learning)!

> Evolutionary TimeStone


### More...

- Scaling Law

- LLM Reasoning: Chain of Thought

- Generalizations: With big models (Gemini), you get a combination of ‘infer the task’ and ‘learn the task’
Explanations (and mechanisms) underlying LM behavior are complex!

- Scaling Laws


