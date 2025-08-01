---
title: "LLML CS336 Lecture 1 Overview and Tokenization"
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

### BPE: `tiktoken`

`tiktoken` is a tokenization tool trained by OpenAI with **Byte-Pair Encoding** (BPE), it achieves great achievement in English encoding and decoding.

```python
import tiktoken

# Choose an encoding that matches the models you might be using (e.g., for GPT-4, GPT-3.5-turbo)
encoding = tiktoken.get_encoding("cl100k_base")
text_en = "Hello, world! This is a test sentence for tiktoken."

# --- Encode (Text to Token IDs) ---
# encode() converts the string into a list of integer token IDs
token_ids_en = encoding.encode(text_en)
print(f"Original English Text: '{text_en}'")
print(f"Encoded Token IDs (English): {token_ids_en}")
print(f"Number of tokens (English): {len(token_ids_en)}")

# To see the actual tokens that correspond to the IDs (optional, for understanding)
# This requires decoding each ID individually or using a helper.
# Note: decoding individual IDs might not always yield readable strings if tokens are sub-word units.
decoded_parts_en = [encoding.decode([token_id]) for token_id in token_ids_en]
print(f"Decoded Parts (English, for understanding): {decoded_parts_en}")

# --- Decode (Token IDs to Text) ---
# decode() converts a list of integer token IDs back into a string
decoded_text_en = encoding.decode(token_ids_en)
print(f"Decoded English Text: '{decoded_text_en}'")

decoded_modified = [encoding.decode([token_id + 1]) for token_id in token_ids_en]
print(decoded_modified)

# Check if decoded text matches original (should be True)
print(f"Decoded matches original? {decoded_text_en == text_en}")
print(f"After Modifications? {decoded_modified == text_en}")
```

The encoding and decoding process is a **reversible process**, for example, the `encoding_string` is a list of tokens which have been split, and generate a hash process to transform to a set of integer, which are called the `encoded_string`. The **reversible** part lies in that you can easily use `decode` method to generate the original `encoding_string`.

`titoken` module is specifically designed for ASCII text (English), thus its support for CHinese and other unicode string is poor.

```python
import tiktoken
# tiktoken is for byte-pair encoding (BPE), Chinese is not supported.

test_strings = [
    "Hello world, My name is Xiyuan Yang",
    "wow, it is so fantastic!",
    "你好，这里是中文，自古逢秋悲寂寥，我言秋日胜春朝",
    "international computational"
]
with open("../../README.md", "r", encoding="utf-8") as file:
    long_string = file.read()
    file.close()
# test_strings.append(long_string)
encoding = tiktoken.get_encoding("cl100k_base")

for test_string in test_strings:
    tokens = encoding.encode(test_string)
    num_bytes = len(bytes(test_string, encoding="utf-8"))
    # print(num_bytes)
    num_tokens = len(tokens)
    decoded = [encoding.decode([token]) for token in tokens]
    print(decoded)
    print(num_bytes / num_tokens)
```

Let's analyse the result:

$$\text{evluation} = \frac{\text{num bytes}}{\text{num tokens}}$$

- For ANSCI, one single character is 1 bytes. (`char` in C++)

- For Unicode, one single character is 3 bytes.

```text
['Hello', ' world', ',', ' My', ' name', ' is', ' X', 'iy', 'uan', ' Yang']
3.5
['wow', ',', ' it', ' is', ' so', ' fantastic', '!']
3.4285714285714284
['你', '好', '，', '这', '里', '是', '中', '文', '，', '自', '�', '�', '�', '�', '�', '�', '�', '�', '�', '�', '�', '�', '�', '，', '我', '言', '�', '�', '日', '�', '�', '�', '�', '�', '�']
2.057142857142857
['international', ' computational']
13.5
```

- For the English word, the tokenization process is based on **words**, so `num_bytes/num_tokens` is mainly based on the average length of words.

- For the Chinese word, the tokenization process is based on **single characters**.

### Principles

To make it more formalized: we want to find a hash function $f$, given a list of unicode string: $X = [x_1, x_2, \dots, x_n]$, it satisfied:

- The set of the unicode string can be converted to $Y = [y_1, y_2, \dots, y_m]$, and $y_j = \bigcup_{p \le i \le q}x_i$. (The splitting part, and we hope the splitting is meaningful, the value space of Y is the power set of the set composed of Unicode characters.)

- The hash function: $f(y) = z, y \in \mathbb{Y}, z \in \mathbb{Z}, \mathbb{Z} \subseteq \mathbb{N} $.

- No hash collision is allowed here. $\forall z \in \mathbb{Z}, \text{there exists only one } y \in \mathbb{Y}, f(y) = z $. (**Injection is required**)

#### Character-based Tokenization

For $\mathbb{Y} \subseteq \mathcal{P}(M)$ is finite, there exists at least one hash function that satisfy all the requirements above. A Unicode string is a sequence of Unicode characters. Each character can be converted into a code point (integer) via `ord`.

```python
import string

ord_result = [ord(letter) for letter in string.ascii_letters]
print(ord_result)

print(ord("你"))

print(ord(","))
print(ord(" "))

try:
    print(ord("Hello world"))
except Exception as e:
    print(e)

# [97, 98, 99, 100, 101, 102, 103, 104, 105, 106, 107, 108, 109, 110, 111, 112, 113, 114, 115, 116, 117, 118, 119, 120, 121, 122, 65, 66, 67, 68, 69, 70, 71, 72, 73, 74, 75, 76, 77, 78, 79, 80, 81, 82, 83, 84, 85, 86, 87, 88, 89, 90]
# 20320
# 44
# 32
# ord() expected a character, but string of length 11 found
```

The problems:

- The context meaning has been lost during the encoding process.

- $|\mathbb{Y}|$ is quite large, we need to make it smaller for the training process.

```python
from abc import ABC, abstractmethod


class Tokenizer(ABC):
    """Abstract interface for a tokenizer."""

    @abstractmethod
    def encode(self, string: str) -> list[int]:
        raise NotImplementedError
    
    @abstractmethod
    def decode(self, indices: list[int]) -> str:
        raise NotImplementedError


class CharacterTokenizer(Tokenizer):
    """Represent a string as a sequence of Unicode code points."""

    def encode(self, string: str) -> list[int]:
        return list(map(ord, string))

    def decode(self, indices: list[int]) -> str:
        return "".join(map(chr, indices))
```

```python
string = "自古逢秋悲寂寥，我言秋日胜春朝"
tokenizer = CharacterTokenizer()

indices = tokenizer.encode(string)  # @inspect indices
reconstructed_string = tokenizer.decode(indices)  # @inspect reconstructed_string

print(indices)
print(reconstructed_string)

assert string == reconstructed_string
vocabulary_size = max(indices) + 1
print(vocabulary_size)
```

#### Byte-based Tokenization

Unicode strings can be represented as a sequence of bytes, which can be represented by integers between 0 and 255.

```python
class ByteTokenizer(Tokenizer):
    """Represent a string as a sequence of bytes."""
    def encode(self, string: str) -> list[int]:
        string_bytes = string.encode("utf-8")  # @inspect string_bytes
        indices = list(map(int, string_bytes))  # @inspect indices
        return indices
    def decode(self, indices: list[int]) -> str:
        string_bytes = bytes(indices)  # @inspect string_bytes
        string = string_bytes.decode("utf-8")  # @inspect string
        return string

tokenizer = ByteTokenizer()
print(tokenizer.encode("自古逢秋悲寂寥，我言秋日胜春朝"))
print(tokenizer.decode(tokenizer.encode("自古逢秋悲寂寥，我言秋日胜春朝")))
```

Problem: quite long sequences.

#### Word-based Tokenization

Use regex to split strings into word and do word-based tokenizations.

Problems:

- The number of words is huge (like for Unicode characters).
    
- Many words are rare and the model won't learn much about them.
    
- This doesn't obviously provide a fixed vocabulary size.

#### Byte-Pair Encoding

Wikipedia: [Byte Pair Encoding](https://en.wikipedia.org/wiki/Byte-pair_encoding)

The original paper: [http://www.pennelynn.com/Documents/CUJ/HTML/94HTML/19940045.HTM](http://www.pennelynn.com/Documents/CUJ/HTML/94HTML/19940045.HTM)

It was adapted to NLP for neural machine translation.

{% note primary %}

**Neural Machine Translation of Rare Words with Subword Units**

Neural machine translation (NMT) models typically operate with a fixed vocabulary, but translation is an open-vocabulary problem. Previous work addresses the translation of out-of-vocabulary words by backing off to a dictionary. In this paper, we introduce a simpler and more effective approach, making the NMT model capable of open-vocabulary translation by encoding rare and unknown words as **sequences of subword units**. This is based on the intuition that various word classes are translatable via smaller units than words, for instance names (via character copying or transliteration), compounds (via compositional translation), and cognates and loanwords (via phonological and morphological transformations). We discuss the suitability of different word segmentation techniques, including simple character *n-gram* models and a segmentation based on the byte pair encoding compression algorithm.

{% endnote %}

- Basic idea: train the tokenizer on raw text to automatically determine the vocabulary.
- Intuition: common sequences of characters are represented by a single token, rare sequences are represented by many tokens.

The GPT-2 paper used word-based tokenization to break up the text into inital segments and run the original BPE algorithm on each segment.

Sketch: start with each byte as a token, and **successively merge the most common pair of adjacent tokens**.

{% note primary %}

The BPE (Byte Pair Encoding) training process is an iterative algorithm that builds a vocabulary and a set of merge rules by finding and combining the most frequent adjacent symbols (bytes or subwords) in a given text.

1.  **Initialization:**
    * Start with a **vocabulary** that contains all unique individual bytes (0-255) present in your training text.
    * Represent your training text as a sequence of these individual byte IDs.

2.  **Iterative Merging (for `N` desired merges):**
    * **Count Pairs:** In the current sequence of IDs (tokens), count the occurrences of all adjacent pairs of symbols.
    * **Find Most Frequent:** Identify the pair of symbols that appears most frequently.
    * **Create New Symbol & Rule:**
        * Assign a **new, unique ID** to this most frequent pair (e.g., if IDs up to 255 are used for single bytes, start new IDs from 256).
        * Add this new ID to your **vocabulary**, mapping it to the combined byte sequence of the two symbols it represents (e.g., if `b'p'` and `b'a'` were merged, the new ID maps to `b'pa'`).
        * Record this **merge rule** (the original pair and its new ID).
    * **Update Sequence:** Replace all occurrences of the most frequent pair in your text sequence with the new symbol's ID.

3.  **Repeat:** Continue steps 2 until you've performed the desired number of merges (`N`) or no more pairs can be found.

In essence, BPE training works by:

* **Starting small:** Treating every byte as a distinct unit.
* **Gradually expanding:** Learning to combine frequently occurring sequences of bytes into new, larger "subwords."
* **Building a dictionary:** Creating a mapping from these new subwords (and original bytes) to unique numerical IDs.

This process allows the tokenizer to represent common words and subword units efficiently, leading to shorter token sequences for given texts, which is crucial for the performance of large language models.

{% endnote %}

> Somehow like the process of Huffman Encoding.

```python
from collections import defaultdict
from dataclasses import dataclass


def merge(
    indices: list[int], pair: tuple[int, int], new_index: int
) -> list[int]:  # @inspect indices, @inspect pair, @inspect new_index
    """Return `indices`, but with all instances of `pair` replaced with `new_index`."""
    new_indices = []  # @inspect new_indices
    i = 0  # @inspect i
    while i < len(indices):
        if i + 1 < len(indices) and indices[i] == pair[0] and indices[i + 1] == pair[1]:
            new_indices.append(new_index)
            i += 2
        else:
            new_indices.append(indices[i])
            i += 1
    # update the whole indices, thus it is very low
    return new_indices


@dataclass(frozen=True)
class BPETokenizerParams:
    """All you need to specify a BPETokenizer."""

    vocab: dict[int, bytes]  # index -> bytes
    merges: dict[tuple[int, int], int]  # index1, index2 -> new_index

class BPETokenizer(Tokenizer):
    """BPE tokenizer given a set of merges and a vocabulary."""
    def __init__(self, params: BPETokenizerParams):
        self.params = params
    def encode(self, string: str) -> list[int]:
        indices = list(map(int, string.encode("utf-8")))  # @inspect indices
        # Note: this is a very slow implementation
        # simulate the whole merging process
        for pair, new_index in self.params.merges.items():  # @inspect pair, @inspect new_index
            indices = merge(indices, pair, new_index)
        return indices
    def decode(self, indices: list[int]) -> str:
        bytes_list = list(map(self.params.vocab.get, indices))  # @inspect bytes_list
        string = b"".join(bytes_list).decode("utf-8")  # @inspect string
        return string

def train_bpe(
    string: str, num_merges: int
) -> BPETokenizerParams:  # @inspect string, @inspect num_merges
    # Start with the list of bytes of string.
    indices = list(map(int, string.encode("utf-8")))  # @inspect indices
    merges: dict[tuple[int, int], int] = {}  # index1, index2 => merged index

    # initial vocab
    vocab: dict[int, bytes] = {x: bytes([x]) for x in range(256)}  # index -> bytes

    for i in range(num_merges):
        # Count the number of occurrences of each pair of tokens
        counts = defaultdict(int)

        # !really pythonic!
        for index1, index2 in zip(indices, indices[1:]):  # For each adjacent pair
            counts[(index1, index2)] += 1  # @inspect counts
        # Find the most common pair.
        pair = max(counts, key=counts.get)  # @inspect pair
        index1, index2 = pair
        # Merge that pair.
        new_index = 256 + i  # @inspect new_index
        merges[pair] = new_index  # @inspect merges
        vocab[new_index] = vocab[index1] + vocab[index2]  # @inspect vocab
        indices = merge(indices, pair, new_index)  # @inspect indices
    return BPETokenizerParams(vocab=vocab, merges=merges)
```

```python
string = "the cat in the hat"  # @inspect string
params = train_bpe(string, num_merges=3)
print(params)

tokenizer = BPETokenizer(params)
string = "the quick brown fox"  # @inspect string
indices = tokenizer.encode(string)  # @inspect indices
print(indices)
reconstructed_string = tokenizer.decode(indices)  # @inspect reconstructed_string
assert string == reconstructed_string
```

For the data string provided and the `num_merges`, you can see the tokenize result.

```python
# if you use different traning data?

params_2 = train_bpe("quick quick brown brown fox fox the the hfbdn ghdnb hfj", num_merges=20)
tokenizer_2 = BPETokenizer(params_2)
indices_2 = tokenizer_2.encode(string)

print(indices)
print(indices_2)

for index in indices:
    print(tokenizer.decode([index]))

print('\n')

for index in indices_2:
    print(tokenizer_2.decode([index]))
```

```text
[258, 113, 117, 105, 99, 107, 32, 98, 114, 111, 119, 110, 32, 102, 111, 120]
[271, 261, 265, 267]
the 
q
u
i
c
k
 
b
r
o
w
n
 
f
o
x


the 
quick 
brown 
fox
```

