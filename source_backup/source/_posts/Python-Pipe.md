---
title: "Python Pipe & Functional Programming"
date: 2025-07-03 16:32:43
index_img: /img/cover/tuxwelcome.png
excerpt: The basic tutorial of using pipes in Python programming, including advanced usage. The blog also introduces the introduction of functional programming.
categories:
  - Code
  - Python
tags:
  - Tutorial
  - Finished
  - Python
  - Functional Programming
  - pipe
---

# From `pipe` in Python to The introduction of Functional Programming

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>
All the source code for section 1 is available for [this GitHub repo](https://github.com/xiyuanyang-code/Python-Tutorial/blob/master/pipe_tutorial/tutorial.ipynb).

# Pipe Tutorial

## Pipe in Command Line

In command line, **pipe** (`|`) is used to connect different commands, where the input of subsequent commands is the output of previous commands.

For example, the commands below are basic demos of **pipe**:

```bash
# check how many commands of 'ls' you have typed in
history | grep "ls" | wc -l

# check several PID
ps aux | grep "zsh"
!ps aux | grep "zsh" | wc -l
```

## Pipe Coding in Python

## Installation

```python
# using pip
%pip install pipe 
import pipe
```

## Basic Usage

Official GitHub Repo: [Repo for Pipe](https://github.com/JulienPalard/Pipe)

Website for pypi: [Pipe in pypi](https://pypi.org/project/pipe/)

### Basic Syntax

The same as what you use in the shell!

```python
from itertools import count

# itertools is a functional tools for creating and using iterators
from pipe import select, take

value = sum(count() | select(lambda x: x**2) | take(10))
# or you can use range here!

print(value)
```

You can pass **arguments** in an easier way.

```python
from pipe import Pipe


# a test function
@Pipe
def test_func(x):
    print(f"{x} is the input value")
    return x + 3


if __name__ == "__main__":
    print(5 | test_func)
    # functional programming
```

Making it more complex...

```python
from pipe import Pipe, where, select

# we need to find all the square numbers from 1 to 100


def square(x):
    return x**2


result = list(range(11) | select(square))
print(result)
```

> **generator** in Python

Generators in Python offer significant advantages, primarily due to their **lazy evaluation** and **memory efficiency.** Unlike functions that return entire lists or other collections, generators yield one item at a time, on demand.

This "**yield-as-you-go**" approach means that generators don't store all their values in memory simultaneously. For instance, when processing a massive dataset, a generator won't try to load everything at once, which could quickly exhaust your system's RAM. Instead, it computes and provides values only when requested, making them ideal for handling large or even infinite data streams without overwhelming memory resources.

> **iterable** in Python

If an object is iterable, it means it implements the **iter** method (which returns an iterator) or the **getitem** method (which allows access to elements by index, like lists do).

### `where` for filtering

- `where(predicate)`: filter elements from iterable objects.

`predicate` is a function which returns a bool value (true/false).

```python
from pipe import where

list_tobe_filtered = [1, 2, 3, 4, 5]

print(list(list_tobe_filtered | where(lambda x: x % 2 == 0)))
```

Built-in filtering `dedup`:

Deduplicate values, using the given key function if provided

```python
from pipe import dedup

original_data = [1, 2, 3, 4, 5, 6, 7, 7, 7, 8, 9, 9]
print(list(original_data | dedup))
```

### `select` for selecting

Choose a **mapping** function for every element in an iterable object.

Quite similar to `forEach()` method for array in JavaScripts.

```python
from pipe import Pipe, select, where

# method-1: using lambda function
numbers = [1, 2, 3, 4, 5]
squares = list(numbers | select(lambda x: x**2))
print(squares)


# method-2: using function or decorator
def square_number(x):
    return x**2


squares_2 = numbers | select(square_number)
# squares_2 is a map! (lazy evaluation and supports iteration)

print(list(squares_2))
```

Or you can using the `map` function, just like javascripts!

```python
# for single map: it need to support 2 arguments
# print(list([1,2,3,4] | map(lambda x: x**2))): this will cause error
# right way to do so:

print(list(map((lambda x: x**2), [1, 2, 3, 4, 5])))

from pipe import map

print(list([1, 2, 3, 4, 5] | map(lambda x: x**2)))

# just choose one you like
# !pay attention to the name conflict
```

## Choosing elements

- `take(n)`: take the first `n` elements from the iterable objects, like the `head` command in Shell scripts
- `skip(n)`: skip the first `n` elements from the iterable objects
- `skip_while(predicate)`: using a predicate function to skip certain objects

### `groupby` method

Using `groupby(key_selector)` to select all the elements by groups.

```python
from pipe import groupby

numbers = [1, 2, 3, 4, 5, 6]
grouped = numbers | groupby(lambda x: "even" if x % 2 == 0 else "odd")

for key, group in grouped:
    print(f"{key}: {list(group)}")
```

### Traversing

Using `traverse` in Pipe to traverse an iterable object.

```python
from pipe import traverse

numbers = [1, 2, [3, [4, 5, 6], 5], 4, 5]

for i in numbers | traverse:
    print(i)
```

## Chaining

Chaining a sequence of iterable objects.

> Warning : chain only unfolds an iterable containing ONLY iterables:

```python
from pipe import chain, traverse

array_1 = [1, 2, 3, 4, 5]
array_2 = [3, 4, [5, 6, 7, 8], 6]
array_3 = ["alice", "jim", "tom"]

integrated = list([array_1, array_2, array_3] | chain)

for i in integrated | traverse:
    print(i)
```

## Other commands

- `islice`: slice for iterable objects, which is the same as `itertools.islice`.
- `izip`: zipping like `izip` or `zip` in Python 3.

We do not need to load all the elements into the RAM, instead, it will slice (`start:stop:step`) and return a new iterator.

- `tee`: tee outputs to the standard output and yield unchanged items, useful for debugging a pipe stage by stage:

```python
from pipe import islice, izip

print(list((1, 2, 3, 4, 5, 6, 7, 8, 9) | islice(1, 100, 2)))
print(list([1, 2, 3, 4, 5] | izip([2, 3, 4, 5, 6])))
from pipe import tee

list([1,2,3,4,5] | tee)
```

## Creating my own Pipes

Using the `Pipe` class.

```python
class Pipe:
    """
    Represent a Pipeable Element :
    Described as :
    first = Pipe(lambda iterable: next(iter(iterable)))
    and used as :
    print [1, 2, 3] | first
    printing 1

    Or represent a Pipeable Function :
    It's a function returning a Pipe
    Described as :
    select = Pipe(lambda iterable, pred: (pred(x) for x in iterable))
    and used as :
    print [1, 2, 3] | select(lambda x: x * 2)
    # 2, 4, 6
    """

    def __init__(self, function, *args, **kwargs):
        self.function = lambda iterable, *args2, **kwargs2: function(
            iterable, *args, *args2, **kwargs, **kwargs2
        )
        functools.update_wrapper(self, function)

    def __ror__(self, other):
        return self.function(other)

    def __call__(self, *args, **kwargs):
        return Pipe(
            lambda iterable, *args2, **kwargs2: self.function(
                iterable, *args, *args2, **kwargs, **kwargs2
            )
        )
from pipe import Pipe

# you need to pass in a necessary function variables
square = Pipe(lambda iterable: (x ** 2 for x in iterable))
```

# Introduction to Functional Programming

接下来，我们介绍**函数式编程**（Functional Programming）



