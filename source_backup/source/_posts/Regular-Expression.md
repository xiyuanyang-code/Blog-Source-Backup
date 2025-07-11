---
title: Regular-Expression
date: 2025-02-03 11:20:33
index_img: /img/cover/Regex.png
excerpt: The basic usage of Regular Expression and the powerful application of Regex in String matching and coding.
categories:
  - Code
tags:
  - string
  - regular expression
  - tutorial
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Regular Expression (EN)

> The Blog is written in English, than Google translate into Chinese.

## Abstract

## Introduction

What is Regular Expression? Lets have a look at Wikipedia's definition:

> A **regular expression** (shortened as **`regex`** or **`regexp`**), sometimes referred to as **rational expression**, is a sequence of [characters](https://en.wikipedia.org/wiki/Character_(computing)) that specifies a [match pattern](https://en.wikipedia.org/wiki/Pattern_matching) in [text](https://en.wikipedia.org/wiki/String_(computer_science)). Usually such patterns are used by [string-searching algorithms](https://en.wikipedia.org/wiki/String-searching_algorithm) for "find" or "find and replace" operations on [strings](https://en.wikipedia.org/wiki/String_(computer_science)), or for [input validation](https://en.wikipedia.org/wiki/Data_validation). Regular expression techniques are developed in [theoretical computer science](https://en.wikipedia.org/wiki/Theoretical_computer_science) and [formal language](https://en.wikipedia.org/wiki/Formal_language) theory.

From the text above, we can conclude that:

- Regex is a **sequence of characters**, or we can say **Regex is a special kind of string**.
- Regex is powerful in **String searching algorithms**.

To explain this more clearly, let's assume we have the following C++ program.

```cpp
#include <iostream>
#include <random>

bool SearchString(std::string text, std::string target){
    //There are some powerful algorithms here

    //We just create a random number!
    std::random_device rd;  
    std::mt19937 gen(rd()); 
    std::uniform_int_distribution<> dis(0, 1); 

    return dis(gen);
}

int main(){
    std::string text;
    std::string target;
    std::cout << (SearchString(text, target) ? "Matched" : "Mismatched") << std::endl;
    return 0;
}
```

Assuming that we have a powerful algorithm (like **KMP** algorithm) that could judge whether there exists a target string in the whole text, which is shown in the `SearchSting` function. For example, `text` is `I love SJTU` while `target` is `SJTU`, then the program will output "Matched"!

But we want to make the function more powerful, like: I want to search whether there is a sentence that has the structure of `I love ...`, where `...` means any word consisting of four uppercase English letters. Or we can say we expect the consequences as below:

```bash
target: I love `XXXX`(4 uppercase English Letters)
text1: I love SJTU                                    `Matched!`
text2: I love FUDAN                                    `Matched!(The funtion reads FUDA)`
text3: I love CUDA                                    `Matched`
text4: I love CuDA                                    `Mismatched`
text5: I love C0DA                                    `Mismatched`
```

We definitely can implement the function by adding some code into `SearchString`, but it compromises universality. Over time, the algorithm has turned into a pile of if-else statements, resembling a mountain of messy code. In a word, the algorithm seems weak when facing **more lenient string matching problems with more detailed requirements**.

This problem does exist in the real world! For example, if you want to search all the files with suffix `.md`, how would you implement this using the command line?

### Regex counts!

This is where the power of **Regex** shines. Regex offers advanced pattern matching capabilities, supporting complex patterns, quantifiers, character classes, and groups, which simple string matching lacks, enabling more flexible and powerful text search and manipulation. Moreover, regex plays as a powerful tool in programming language like **Python** and **Javascripts**. All the demos in the text will use **Python** as the demonstrating language.

For the questions above, we can use **Regex** to search files with certain suffixes using only one command line!

```bash
find . -type f -regex '.*\.md$'
```

![Search Files with certain suffixes](https://ooo.0x0.ooo/2025/02/01/OGtRCq.png)

In today's Blog, we will master the basic usage of **Regex** and implement some simple applications using Regex.

## Basic Usage

### Learning maps

After knowing what Regex can do, next question we need to clarify is **What Regex truly is**.

- The fundamental principle: **Regex is used for string matching problems.**
- To say more specifically, for **more complex** string matching problems.

What is **complex**? Assume that there is a picky person asking you to find all the strings with some peculiar and bizarre rules. It's easy for you to understand the rules while hard for computers to do so. Therefore we can say: **Regex is a series of laws to make computers understand "peculiar and bizarre rules" of complex string matching problems.**

> Like the question above, "find all file names with certain suffix '.md'" is a certain kind of **peculiar and bizarre rules**.

There are countless peculiar and bizarre rules for string matching, and people intelligently **create a system to unify** recordings for these peculiar and bizarre rules.

| Syntax                           | Explanations                                                                                                                              |
| -------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------- |
| **Special Position Matching**    | ✅Characters that match the **start** and **end** of a string or a word.<br>✅**Zero-Width Assertion**: match customized certain positions. |
| **Special Character Matching**   | ✅Numbers, words, null characters.<br>**✅Escape characters for Metacharacter**.<br>✅Wildcard                                               |
| **Character Classes**            | ✅Match **one character** from a **set of possible characters**.                                                                           |
| **Repetition** and **Capturing** | ✅Quantifiers<br>✅Grouping<br>✅Capturing                                                                                                   |

> So regex is just a hand-made syntax rule for string-matching problems! Don't be afraid, and you can learn Regex gradually through your learning and working process. You don't need to learn all the Regex syntax for it doesn't make sense for most of the circumstances.

### Metacharacters

Metacharacters is characters that has **different meanings**, which is often used as the case and mark for special positions and characters. The table below shows several most frequently used metacharacters and we will discuss it during a concrete example. 

| Metacharacter | Description                                                  |
| ------------- | ------------------------------------------------------------ |
| `.`           | Matches any single character (except newline).               |
| `^`           | Matches the start of a string.                               |
| `$`           | Matches the end of a string.                                 |
| `*`           | Matches 0 or more repetitions of the preceding element.      |
| `+`           | Matches 1 or more repetitions of the preceding element.      |
| `?`           | Matches 0 or 1 repetition of the preceding element (makes it optional). |
| `\`           | Escapes a metacharacter (e.g., `\.` matches a literal dot).  |
| `[]`          | Matches any single character within the brackets (e.g., `[abc]`). |
| `[^]`         | Matches any single character **not** within the brackets (e.g., `[^abc]`). |
| `()`          | Groups patterns and captures the matched text.               |
| `{}`          | Specifies exact repetition (e.g., `a{2,4}` matches 2 to 4 `a`'s). |
| `\d`          | Matches any digit (equivalent to `[0-9]`).                   |
| `\w`          | Matches any word character (letters, digits, underscore).    |
| `\s`          | Matches any whitespace character (space, tab, newline).      |
| `\b`          | Matches a word boundary.                                     |
| `\B`          | Matches a non-word boundary.                                 |

### Examples

**Learning Metacharacters** is the most fundamental thing in learning Regex, as Regex's power simply lies in searching different patterns and positions. In the Blog below, I will use a practical example to show readers how to use Regex in **grep** command to search for specific files and string patterns.

#### Example 1: search certain file name

This current directory is the place where I store all my Blog files in markdown format. If I type `ls` command, I will see all the files and folders in the current directories.

```bash
> pwd
/mnt/d/Blog/source/_posts
> ls
AI-indepth-reading-AlexNet
AI-indepth-reading-AlexNet.md
Above-All
Above-All.md
Algorithm-Introduction
Algorithm-Introduction.md
Bash-commands
Bash-commands.md
Bash-exercises
Bash-exercises.md
C-plus-plus-Primer-Plus-tutorial
C-plus-plus-Primer-Plus-tutorial.md
CMake-tutorial-episode2
CMake-tutorial-episode2.md
CMake-tutorial1
CMake-tutorial1.md
CS294-1-LLM-Reasoning
CS294-1-LLM-Reasoning.md
Class-Inheritance
Class-Inheritance.md
Code-Reuse-in-OOP
Code-Reuse-in-OOP.md
Data-Structure-Tutorial
Data-Structure-Tutorial.md
DataStructure-LinearList
DataStructure-LinearList.md
DataStructure-Queue
DataStructure-Queue.md
DataStructure-Stack
DataStructure-Stack.md
DataStructure-String
DataStructure-String.md
DataStructure-Tree
DataStructure-Tree-Binary-Tree
DataStructure-Tree-Binary-Tree.md
DataStructure-Tree.md
Dynamic-Memory-and-Classes
Dynamic-Memory-and-Classes.md
Exception-Handling-in-C-plus-plus
Exception-Handling-in-C-plus-plus.md
Input-and-Output-in-C-plus-plus
Input-and-Output-in-C-plus-plus.md
Introduction-to-OOP
Introduction-to-OOP.md
Jotting-References-and-Encapsulation-in-OOP
Jotting-References-and-Encapsulation-in-OOP.md
LaTeX-tutorial
LaTeX-tutorial.md
Leetcode-Mistake-collection
Leetcode-Mistake-collection-1-10
Leetcode-Mistake-collection-1-10.md
Leetcode-Mistake-collection-11-20
Leetcode-Mistake-collection-11-20.md
Leetcode-Mistake-collection-21-30
Leetcode-Mistake-collection-21-30.md
Leetcode-Mistake-collection-31-40
Leetcode-Mistake-collection-31-40.md
Leetcode-Mistake-collection.md
Life-musings
Life-musings.md
Linked-List-Implementation-Based-on-Structs
Linked-List-Implementation-Based-on-Structs.md
Linux-Bash-Introduction
Linux-Bash-Introduction.md
MYGITHUB-Lightweight-speech-recognition-conversion-model
MYGITHUB-Lightweight-speech-recognition-conversion-model.md
Missing-Semester-Notes
Missing-Semester-Notes.md
My-Posts
My-Posts.md
My-WorkFlow
My-WorkFlow.md
Pointers-Arrays-and-Functions
Pointers-Arrays-and-Functions.md
Python-Update-Learning
Python-Update-Learning.md
Python-advanced-File-Management
Python-advanced-File-Management.md
Python-cheatsheet
Python-cheatsheet.md
Python-tutorial
Python-tutorial.md
Regular-Expression
Regular-Expression.md
Tools-Tutorial
Tools-Tutorial.md
Vim-tutorial
Vim-tutorial.md
filenames.txt
hello-world.md
```

If one day I want to search for all Blog files with **Python**, I can use the simple `grep` command like this:

```bash
ls | grep -i "python"
# where -i means ignore whether it is the uppercase or the lowercase
```

It will get result as below:

```bash
Python-Update-Learning
Python-Update-Learning.md
Python-advanced-File-Management
Python-advanced-File-Management.md
Python-cheatsheet
Python-cheatsheet.md
Python-tutorial
Python-tutorial.md
```

There are actually several sub-folders for storing blog images which I don;t want them to be displayed. Thus I can use Regex for searching **speific suffixes** as below:

```bash
❯ ls | grep -iE "python.*\.md"
```

`"python.*\.md"` : How to read Regex? just read it from left to right and **take notice of every metacharacters for its special meaning**! For example in this string, `python` contains no metacharacters and `.` is a **very important metacharacters with the meaning of matching any single characters.**

And `*` is use for **greedy research**, which means matching 0 or more repetitions of the preceding element and make sure the matches as long as possible. So `.*` is a very powerful tool that matches all strings!

`\` means **escape characters**, which is used for "escaping for the special functions for metacharacters". If you want to match the metacharacters itself, you can use `\`! In this example, `\` is used for escaping `.`, which means to match the `.` itself.

So you now get to know how to search string with certain suffixes `.md`!

There are many other commands for more complex string-pattern matching, you can look up in the table above for searching accordingly!

### Advanced Techniques

#### Zero-Width Assertion 

Zero-width assertions are advanced features in regular expressions that allow you to **match specific conditions at a position in the text without consuming characters**. They are called "zero-width" because they do not contribute to the match result but instead assert whether a pattern exists (or does not exist) at a certain position.

#### Types of Zero-Width Assertions:

1. **Positive Lookahead (`(?=...)`)**
	Asserts that a pattern must follow the current position.
	Example: `\d+(?= dollars)` matches numbers followed by "dollars" but does not include "dollars" in the match.
2. **Negative Lookahead (`(?!...)`)**
	Asserts that a pattern must not follow the current position.
	Example: `\d+(?! dollars)` matches numbers not followed by "dollars."
3. **Positive Lookbehind (`(?<=...)`)**
	Asserts that a pattern must precede the current position.
	Example: `(?<=\$)\d+` matches numbers preceded by a dollar sign.
4. **Negative Lookbehind (`(?<!...)`)**
	Asserts that a pattern must not precede the current position.
	Example: `(?<!\$)\d+` matches numbers not preceded by a dollar sign.

## Conclusion

Just use regex in real practice! I will update this blog when I have some new comprehension for Regex~

## References

[Regexr](https://regexr.com/) : **a powerful website for practicing Regex**. 

https://www.regular-expressions.info/ : You can search for more advanced usage regarding Regex on this website.
