---
title: Python Pythonic
date: 2025-07-16 12:15:17
index_img: /img/cover/pythonic.jpg
excerpt: This article introduces advanced Python programming concepts, including Pythonic style, metaprogramming, functional programming, and more.
categories:
  - Code
  - Python
tags:
  - Tutorial
  - Finished
  - Python
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>


# Python Advanced Programming

## Introduction

[What is Pythonic?](https://mail.python.org/pipermail/tutor/2003-October/025932.html)

简单来说，Pythonic不仅仅指编写出能够运行的 Python 代码，更深层次地，它代表了一种**符合 Python 语言哲学和最佳实践的编程风格。简单来说，Pythonic 的代码就是“用 Python 的思维方式”写出来的代码**。

它强调代码的**可读性、简洁性、明确性以及效率**。

## Table of Contents

- Meta Programming

- Duck Typing

- Callable & Iterable

- Generator

- Functional Programming & FCC (First Class Citizen)

## Meta Programming

**元编程（Meta Programming）** 是一种编程技术，允许**程序在运行时动态地生成、分析或修改代码结构**，甚至改变自身的行为。它的核心思想是“**编写能够操作代码的代码**”，从而提升抽象层次，减少重复工作，增强灵活性。

### **Core Principles**

1. **代码即数据（Code as Data）**
	将代码视为可操作的数据结构（如字符串、抽象语法树/AST），允许程序像处理普通数据一样生成或修改代码。
2. **运行时与编译时**
	- **编译时元编程**：在编译阶段生成或转换代码（如宏、模板）。
	- **运行时元编程**：在程序运行时动态修改行为（如反射、动态方法生成）。
3. **自省（Introspection）**
	程序能够分析自身的结构（如获取类的方法、检查参数类型）。
4. **反射（Reflection）**
	在运行时动态调用或修改代码（如通过类名实例化对象、动态调用方法）。

这么说貌似有点抽象了！我们来看一个具体的例子：

#### Hello world！

```python
command1 = "print('Hello, world')"
exec(command1)
```

程序将会输出`Hello, world`。`exec()` 是 Python 中用来执行 **Python 代码字符串**的（比如 `exec("print(1+1)")`）。

这就是Meta Programming中**Code as Data**的思想，即编写操作代码的代码。

> 如果这样想的话，各种脚本文件（bash，Dockerfile，CMakelists）不都是Code as Data的思想吗？

当然，你也可以使用`os`库实现更加高级的操作：

```python
import os
command1 = "python test2.py"
os.system(command1)
os.system("echo 'This is a bash command'")
os.system("mkdir test_for_os")
```

在有了最基本的感知之后，我们来一条一条分析Meta Programming的基本思想。

### 编译时 & 运行时元编程

其实C++中的**宏**就是一种**编译时元编程**。即在编译的阶段将`#define`的东西转换进程序中然后开始编译。

当然这不止于C++，例如Rust中的`println!()`就是宏而不是函数。

#### Decorators

下面本文将要介绍**装饰器（Decorators）**。

{% note info %}

**语法糖**（英语：Syntactic sugar）是由英国[计算机科学家](https://zh.wikipedia.org/wiki/计算机科学家)[彼得·兰丁](https://zh.wikipedia.org/wiki/彼得·兰丁)发明的一个术语，指[计算机语言](https://zh.wikipedia.org/wiki/计算机语言)中添加的某种语法，这种语法对语言的功能没有影响，但是更方便[程序员](https://zh.wikipedia.org/wiki/程序员)使用。语法糖让程序更加简洁，有更高的可读性。语法糖通常是常见操作的简写，也可以用另一种更冗长的形式表达：程序员可以选择使用较短的形式还是较长的形式，但通常会使用较短的形式，因为它更短，更容易输入和阅读。

{% endnote %}

装饰器是Python中一种强大的语法糖，允许**在不修改原函数代码的情况下扩展或修改函数的行为**。例如运行下面的代码：

```python
def my_decorator(func):
    def wrapper():
        print("Before function call")
        func()
        print("After function call")

    return wrapper


@my_decorator
def say_hello():
    print("Hello!")


if __name__ == "__main__":
    say_hello()
```

程序输出如下：

```
Before function call
Hello!
After function call
```

可以看到，装饰器在不改变原函数的情况下实现了对函数的包裹，即内部的`wrapper()`，这样相当于**函数控制函数**，同时各函数之间的代码相互独立。

我们来看两个更加复杂的装饰器：

- Time Decorator

```python
"""
Author: Xiyuan Yang   xiyuan_yang@outlook.com
Date: 2025-04-28 14:58:15
LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
LastEditTime: 2025-04-28 14:58:20
FilePath: /Lec8_MetaProgramming/time_decorator.py
Description:
Do you code and make progress today?
Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved.
"""

import time
import random
from functools import wraps


def timer(func):
    """Measuring time"""

    @wraps(func)
    def wrapper(*args, **kwargs):
        start_time = time.perf_counter()
        result = func(*args, **kwargs)
        end_time = time.perf_counter()
        elapsed_time = end_time - start_time

        print(f"function {func.__name__} runs with time: {elapsed_time:.6f} seconds")
        return result

    return wrapper

@timer
def sum_test(arr: dict) -> int:
    sum = 0
    for num in arr:
        sum += num
    
    return sum


if __name__ == "__main__":
    test_arr = []
    arr_size = 10000
    left_bound = 1
    right_bound = 10000

    for _ in range(arr_size):
        test_arr.append(random.randint(left_bound, right_bound))
    
    ans = sum_test(test_arr)
    print(f"The final answer is {ans}")


```

程序的输出结果：

```
function sum_test runs with time: 0.000316 seconds
The final answer is 50281843
```

- A more advanced time decorator!

```python
import time
import functools
from colorama import Fore, Style


def time_method(func):
    """
    A decorator to measure the execution time of a method. (For class usage)
    """

    @functools.wraps(func)
    def wrapper(self, *args, **kwargs):
        object_name = getattr(self, "name", f"Unnamed {self.__class__.__name__}")

        print(
            f"{Fore.CYAN}Starting '{func.__name__}' for {object_name}...{Style.RESET_ALL}"
        )
        start_time = time.time()
        result = func(self, *args, **kwargs)
        end_time = time.time()
        elapsed_time = end_time - start_time
        print(
            f"{Fore.GREEN}Finished '{func.__name__}' for {object_name}. Elapsed time: {elapsed_time:.4f} seconds.{Style.RESET_ALL}"
        )
        return result

    return wrapper


class MyClass:
    def __init__(self, name="MyObject"):
        self.name = name

    @time_method
    def test(self):  # Add 'self' here to make it an instance method
        print(f"  {self.name} is executing its test method...")
        time.sleep(2)  # Simulate work for 2 seconds
        print(f"  {self.name} finished its test method.")


if __name__ == "__main__":
    my_instance = MyClass(name="MyCoolInstance")
    my_instance.test()  # Call the method on an instance

    another_instance = MyClass(name="AnotherInstance")
    another_instance.test()

```

> This decorator is specially designed for class method's usage, rather than a simple function.

- Decorator for type check (**Reflection for Meta Programming**)

因为Python是一种弱类型的编程语言，我们可以在写一个decorator来实现简单的语法类型检查。

> `*args`代表接受任意的关键字参数，而`**kwargs`可以接受任意的位置参数。

```python
"""
Author: Xiyuan Yang   xiyuan_yang@outlook.com
Date: 2025-04-28 15:05:43
LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
LastEditTime: 2025-04-28 15:05:48
FilePath: /Lec8_MetaProgramming/type_check.py
Description:
Do you code and make progress today?
Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved.
"""


def type_check(func):
    def wrapper(*args, **kwargs):
        # get the annotations (data types of the parameters and return value)
        annotations: dict = func.__annotations__

        # check parameters
        for arg, (param_name, param_value) in zip(args, annotations.items()):
            if not isinstance(arg, param_value):
                raise TypeError(f"Argument {param_name} must be {param_value}")
        print("All args check passed.")

        # run the function
        result = func(*args, **kwargs)

        # check return value
        if "return" in annotations and not isinstance(result, annotations["return"]):
            raise TypeError(f"Return value must be {annotations['return']}")
        else:
            print(f"Return value is {annotations['return']}, check passed.")

        return result

    return wrapper


@type_check
def add(a: int, b: int) -> int:
    return a + b


@type_check
def add_fail(a: int, b: int) -> str:
    return a + b


def add_fail_2(a: str, b: str) -> int:
    return a + b


if __name__ == "__main__":
    x = add(5, 6)
    print(x)

    try:
        add_fail(3, 4)
    except Exception as e:
        print(f"Error, {e}!")

    try:
        print(add_fail_2("Hello, world", " Wow"))
    except Exception as e:
        print(f"Error, {e}!")

```

Python的类型审查相对比较松散，因此通过类型审查的decorator可以使Python函数的编写变的更加安全。

```
All args check passed.
Return value is <class 'int'>, check passed.
11
All args check passed.
Error, Return value must be <class 'str'>!
Hello, world Wow
```

- Decorators with parameters

```python
def repeat(num_times):
    def decorator(func):
        def wrapper(*args, **kwargs):
            for _ in range(num_times):
                result = func(*args, **kwargs)
            return result

        return wrapper

    return decorator


@repeat(num_times=3)
def greet(name):
    print(f"Hello {name}")


if __name__ == "__main__":
    greet(name="SJTU")
```

### Applications

#### Make & Makefile

Linux中有自带的`make`工具，可以实现**编译的脚本自动化**，和`.sh`非常的类似，但是**相比于Bash脚本更加的高级**。

- **Makefile**：主要用于构建和管理项目，特别是编译型语言的编译过程
- **Bash脚本**：通用的自动化脚本工具，用于执行各种系统任务

> Makefile很大的优势是他会**最小化编译的成本**，例如如果没有任何的源代码或者dependencies发生改变，Makefile不会重新编译这部分的dependencies。同时，Makefile有自己的语法框架，相比于灵活的bash脚本更加的规整一点。

例如，在编译C++程序的时候：

```makefile
program: main.o utils.o
    gcc -o program main.o utils.o

main.o: main.c
    gcc -c main.c

utils.o: utils.c
    gcc -c utils.c

clean:
    rm -f *.o program
```

Makefile要求代码中要给出目标(target)、依赖(dependencies)和命令(commands)。例如在语句：

```makefile
program: main.o utils.o
    gcc -o program main.o utils.o
```

`program`就是最后编译需要生成的binary files，而`main.o utils.o`就是这一个语句需要使用的dependencies，然后在第二行就使用特定的命令行语句来实现。

> 这一点相对于Bash脚本来说更规整，尤其对于C++这种需要编译语言。（不过Cmakelists才是神中神）

我们再来举一个例子：

```makefile
run: 
	python test1.py
	python test2.py
	rm -rf ./test_for_os

```

运行`make`之后，程序的输出结果如下：

```
python test1.py
Hello world
Test for test2.py
The random number is 94
This is a bash command
python test2.py
Test for test2.py
The random number is 29
rm -rf ./test_for_os
```

> 不过谁会这样使用Makefile呢？🤣不如直接写bash

## Duck Typing

### Introduction to Duck Typing

**Duck Typing** is a programming concept often used in dynamically-typed languages, where the type or class of an object is determined by its behavior (methods and properties) rather than its explicit inheritance or interface implementation. The term originates from the phrase: 

> *"If it walks like a duck and quacks like a duck, then it must be a duck."*

In essence, Duck Typing focuses on what an object **can do** rather than what it **is**. This approach provides flexibility and promotes code reusability, but it also requires careful handling to avoid runtime errors due to missing methods or properties.

### Key Characteristics of Duck Typing

1. **Behavior Over Type**:
	- An object's suitability is determined by the presence of specific methods or properties, not by its class or type.
	- For example, if an object has a `quack()` method and a `walk()` method, it can be treated as a "duck" regardless of its actual class.

2. **Flexibility**:
	- Code can work with any object that implements the required behavior, making it highly adaptable and reusable.
	- This eliminates the need for strict type hierarchies or interfaces.

3. **Common in Dynamic Languages**:
	- Duck Typing is frequently used in dynamically-typed languages like Python, Ruby, and JavaScript, where type checking is done at runtime rather than compile time.

### Example

Below is an example that demonstrates Duck Typing using a `Duck` class, a `Person` class, and even a `Chicken` class. The key idea is that any object with the required methods (`quack()` and `walk()`) can be treated as a "duck."

```python
# Define a Duck class
class Duck:
    def quack(self):
        print("Quack!")

    def walk(self):
        print("Walking like a duck")

# Define a Person class
class Person:
    def quack(self):
        print("I'm quacking like a duck!")

    def walk(self):
        print("I'm walking like a duck")

# Define a Chicken class
class Chicken:
    def quack(self):
        print("Cluck cluck! (Pretending to quack)")

    def walk(self):
        print("Walking like a duck (but I'm actually a chicken)")

# Function to check if an object is a "duck"
def check_if_duck(animal):
    try:
        animal.quack()
        animal.walk()
        print("This is a duck!")
    except AttributeError:
        print("This is not a duck!")

# Test the function
duck = Duck()
person = Person()
chicken = Chicken()

check_if_duck(duck)     # Output: Quack! Walking like a duck. This is a duck!
check_if_duck(person)   # Output: I'm quacking like a duck! I'm walking like a duck. This is a duck!
check_if_duck(chicken)  # Output: Cluck cluck! (Pretending to quack) Walking like a duck (but I'm actually a chicken). This is a duck!
```

#### Explanation of the Code

1. **Duck Typing in Action**:
	- The `check_if_duck` function does not check the type of the `animal` object. Instead, it checks whether the object has the `quack()` and `walk()` methods.
	- If the object has these methods, it is treated as a "duck," regardless of whether it is an instance of `Duck`, `Person`, or even `Chicken`.

2. **Flexibility and Reusability**:
	- The `Chicken` class is not a duck, but it can still be treated as one because it implements the required methods.
	- This demonstrates how Duck Typing allows objects of different types to be used interchangeably, as long as they exhibit the expected behavior.

3. **Runtime Risks**:
	- If an object passed to `check_if_duck` does not have the required methods (e.g., a `Dog` class without `quack()` and `walk()`), the program will raise an `AttributeError` at runtime.


## Iterable

### Iterator

In Python, an **iterable** is an object that can be iterated over. More technically, an iterator is an object which implements the iterator protocol, which consist of the methods `__iter__()` and `__next__()`. An iterator is an object that can be iterated upon, meaning that you can traverse through all the values.

- `__iter__()`: init an iterator

- `__next__()`: get the next element of the iterator.

For examples:

- Lists, tuples and Sets and are all iterable objects!

- For custom classes, you need to implement yourself!

- They are iterable containers which you can get an iterator from.

- All these objects have an `iter()` method to get an iterator.


{% note primary %}

**Traditional Index** & **Generalized Index**

**Traditional Index** allows you to directly access an element at a specific position using an integer, like `my_list[0]` or `my_string[2]`.

However, in generalized iterable object, we cannot access to an element at a specific position, (**就像字符串和链表的区别**，为了内存友好，对于较大的迭代器对象不可能将一整段存储全部读入，但是迭代器的设置只需要读入一个单个元素就可以)

> for 循环通常和迭代器搭配使用。或者说for循环的本质，就是调用`__iter__()` 和 `__next__()`方法的过程。

{% endnote %}

```python
ilst = iter(["2", "3", "4", 3, 5])

for i in range(5):
    print(ilst.__next__())
```

```python
test_dict = iter({1: 1, 2: 2, 4: 45, 5: 5})

for i in range(4):
    print(test_dict.__next__())
```

```python
ilst = iter("SJTU")
print(ilst)

for i in range(4):
    print(ilst.__next__())
```

```python
test_tuple = iter(tuple((1, 2, 3, 5, 6, 7)))

for i in range(6):
    print(test_tuple.__next__())
```

**迭代器的迭代器是他本身**：当一个对象是本身就是迭代器时，它的 `__iter__()` 方法（也就是 iter() 函数在后台调用的方法）被设计成直接返回 `self`（也就是它自己）。

这保证了**无法重置迭代器**以及**惰性求值**。并且，迭代器的消耗是一个**不可逆过程**。

```python
my_list = [1, 2, 3]
my_iterator = iter(my_list) # 从可迭代对象中获取迭代器

print(next(my_iterator)) # 输出: 1
print(next(my_iterator)) # 输出: 2
print(next(my_iterator)) # 输出: 3

# 尝试获取第四个元素
try:
    print(next(my_iterator))
except StopIteration:
    # 迭代器的消耗是不可逆的，为了性能考虑，此时会抛出StopIteration的异常
    print("Caught StopIteration: No more elements in the iterator.")
```
对于自定义的类和对象，需要完成`__iter__()` 和 `__next__()` 的方法实现，之后就可以通过for循环进行调用：

```python
class Mynumber:
    def __init__(self, init_number, increasing_round) -> None:
        self.num = init_number
        self.bound = increasing_round + init_number

    def __iter__(self):
        # initialize a iterator
        print(f"__iter__ is called, now the value is {self.num}")
        # !attention, it need to return self
        return self


    def __next__(self):
        if self.num < self.bound:
            value = self.num
            self.num += 1
            return value
        else:
            raise StopIteration

test_number = Mynumber(10, 4)

for x in test_number:
    print(x)
```

### Iterable

如果一个类型是iterable的，那其就可以实现很多**更加高级**的操作。

- Unpacking

```python
a, b, c, *other = {1: 2, 3: 4, 5: 7, 9: 1, 10: 10}
print(a)
print(b)
print(c)
print(other)
# the object to be unpacked must be iterable
```

Something more interesting...

```python
# tuple unpacking
i = 0
L = [1, 2, 3]
i, L[i] = L[i], i
# in this line, it seems that the two elements swap its position, actually, it is called a simultaneous assignment (tuple unpacking). The right side will be evaluated first.

# i, L[i] = 1, 0
# In tuple unpacking, the right side is fully evaluated before any assignment happens
# Python evaluates the right-hand side of an assignment completely before performing any assignments on the left-hand side.
print(i, L)
```

- Other methods

    - `find()` and `join()` in `str`
    - `merge()` in List & Dict
    - `update()` in dict 

### `itertools`

{% note primary %}

itertools: **Functions creating iterators for efficient looping**
This module implements a number of iterator building blocks inspired by constructs from APL, Haskell, and SML. Each has been recast in a form suitable for Python.

The module standardizes a core set of fast, memory efficient tools that are useful by themselves or in combination. Together, they form an “iterator algebra” making it possible to construct specialized tools succinctly and efficiently in pure Python.

{% endnote %}

See [itertools](https://docs.python.org/3/library/itertools.html) for more info.

- `product()`: generating a list for cartesian product, equivalent to a nested for-loop.

```python
import itertools

list_1 = [1, 3, 4, 5]
list_2 = ["alice", "tom", "bob", "warning"]

print(list(itertools.product(list_1, list_2)))
```

- `permutations(p[,r])`: r-length tuples, all possible orderings, no repeated elements

```python
import itertools

print(list(itertools.permutations(list_2)))
```

- `combinations(p, r)`: r-length tuples, in sorted order, no repeated elements

```python
import itertools

items = ['A', 'B', 'C', 'D']
print(list(itertools.combinations(items, 2)))
```
- `combinations_with_replacement`: r-length tuples, in sorted order, with repeated elements.

### `collection`

| Container Datatype | Usage Description                                                              |
| :----------------- | :----------------------------------------------------------------------------- |
| `namedtuple()`     | Factory function for **creating tuple subclasses with named fields**.              |
| `deque`            | List-like container with fast appends and pops on either end.                  |
| `ChainMap`         | Dict-like class for creating a single view of multiple mappings.               |
| `Counter`          | Dict subclass for counting hashable objects.                                   |
| `OrderedDict`      | Dict subclass that remembers the order entries were added.                     |
| `defaultdict`      | Dict subclass that calls a factory function to supply missing values.          |
| `UserDict`         | Wrapper around dictionary objects for easier dict subclassing.                 |
| `UserList`         | Wrapper around list objects for easier list subclassing.                       |
| `UserString`       | Wrapper around string objects for easier string subclassing.                   |

For more info, you can visit: [Collections](https://docs.python.org/3/library/collections.html)


## Structural Pattern Matching (PEP 636)

Source Code: [PEP 636](https://peps.python.org/pep-0636/)

First, we can have a small demo, where `split()` is a string function which returns a list of the substrings in the string, using sep as the separator string.

```python
input_command = "Hello world, this is xiyuanyang speaking "

print(input_command.strip())

[action, *obj] = input_command.split()

print(input_command.split())
print(obj)
print(action)
```

The problem with that line of code is that it’s missing something: **what if the user types more or fewer than 2 words**? To prevent this problem you can either check the length of the list of words, or capture the ValueError that the statement above would raise. Or, we can use a string matching instead!

{% note primary %}

The match statement evaluates the “subject” (the value after the match keyword), and checks it against the pattern (the code next to case). A pattern is able to do two different things:

Verify that the subject has certain structure. In your case, the `[action, obj]` pattern matches any sequence of exactly two elements. This is called **matching**.
It will bind some names in the pattern to component elements of your subject. In this case, if the list has two elements, it will bind `action = subject[0]` and `obj = subject[1]`.
If there’s a match, the statements inside the case block will be executed with the bound variables. If there’s no match, nothing happens and the statement after match is executed next.

{% endnote %}

- For example, if I use `match()` in previous situation:

```python
test_input = str("Hello, this is xiyuanyang's speaking").strip()

match test_input.split():
    case [single_action]:
        print("Wow, it is single!")
    case [obj_1, obj_2]:
        print('two')
    case [obj_3, onj_4, onj_5]:
        print("three")
    case [test1, *test2]:
        print("wow")
    case _: # a wild card
        print("Final")

# final output: wow
```

{% note info %}

It is much more convenient and easier than `switch` in C++!

{% endnote %}

- Or you can match specific value:

```python
match command.split():
    case ["quit"]:
        print("Goodbye!")
        quit_game()
    case ["look"]:
        current_room.describe()
    case ["get", obj]:
        character.get(obj, current_room)
    case ["go", direction]:
        current_room = current_room.neighbor(direction)
    # The rest of your commands go here
```
- Or patterns:

```python
match command.split():
    ... # Other cases
    case ["north"] | ["go", "north"]:
        current_room = current_room.neighbor("north")
    case ["get", obj] | ["pick", "up", obj] | ["pick", obj, "up"]:
        ... # Code for picking up the given object
```

```python
match command.split():
    case ["go", ("north" | "south" | "east" | "west") as direction]:
        current_room = current_room.neighbor(direction)
```

- Adding conditions for simple if judgement

```python
match command.split():
    case ["go", direction] if direction in current_room.exits:
        current_room = current_room.neighbor(direction)
    case ["go", _]:
        print("Sorry, you can't go that way")
```

![conclusion](https://s1.imagehub.cc/images/2025/07/17/492d1f7b809ba062bd9bede5d9d05e1a.png)

## Generator

迭代器和**可迭代属性**是Python抽象编程中非常重要的属性，他不仅和Python中常见的**数据结构**相耦合（列表，元组，字典等），并且灵活性高，也支持自定义类型。下面，我们重点关注一类built-in的可迭代数据结构：**列表**及其高阶玩法。

### List Comprehension

请看下面的demo:

```python
import time


total_num = 8000000

# for simple while_loop
time_start = time.time()
list_1 = []
for i in range(total_num):
    list_1.append(i * i)
time_end = time.time()
print(f"Time for single for loop: {time_end - time_start}")

time_start = time.time()
list_2 = [i * i for i in range(total_num)]
time_end = time.time()
print(f"Time for list comprehension: {time_end - time_start}")

assert list_1 == list_2
# just a demo, the experiment is not very accurate
# Time for single for loop: 1.133270263671875
# Time for list comprehension: 0.7049026489257812
```

- A list comprehension consists of brackets [ ] containing an expression followed by a for clause, then zero
or more for or if clauses.

- Format:

    - `[expression for target in iterable if condition]`: **single loop**

    - `[expression for target1 in iterable1 if condition1 for target2 in iterable2 if condition2 .. for targetN in iterableN if conditionN]`: **multiple loop**

- The result will be a new list resulting from evaluating the expression in the context of the for and if clauses which follow it.

### List is Fast

CPython 是 Python 最常用的解释器，它是用 C 语言实现的。这意味着 Python 代码在执行时，很多底层操作实际上是通过 C 语言代码完成的。C 语言的执行速度远快于 Python 本身。当你编写纯粹的 Python 循环或逻辑时，解释器需要执行大量的字节码操作，这些操作本身就带有 Python 层面的开销。但是，像列表推导式、内置函数（如 sum()、map()、filter() 等）以及某些列表方法（如 append()、sort() 等）在 CPython 内部都有高度优化的 C 语言实现。当你的代码调用这些 C 实现的部分时，速度会大大提升。

- 能用系统操作就不要用for循环，Python已经优化好了

- 能用列表推导 & 切片等操作就不要用for循环

{% note primary %}

 SIMD 是一种处理器架构或指令集，允许 CPU 在单个指令周期内同时处理多个数据点。例如，如果你要对一个数组中的每个元素进行相同的加法操作，SIMD 可以在一次操作中同时对多个元素执行加法，而不是一个一个地处理。

{% endnote %}

### Generator Functions

使用`range()`而不是`list`生成数据就是为了减少存储，只在需要的时候生成即可。

- Generator functions (available since 2.3) are coded as normal def statements, but use **yield** statements to return results one at a time, suspending and resuming their state between each. 当一个函数包含 yield 关键字时，它就不再是一个普通的函数，而变成了生成器函数。调用生成器函数不会立即执行函数体内的代码，而是返回一个生成器对象（Generator Object）。

- Generator expressions (available since 2.4) are similar to the **list comprehensions** of the prior section, but they return an object that produces results on demand instead of building a result list.

    - 函数执行到 yield 后暂停，并返回一个值。

    - 函数的状态（包括局部变量）会被保存下来。

    - 下次调用 next() 时，函数会从上次暂停的地方继续执行。

    - 一个生成器函数可以包含多个 yield 表达式，每次调用 next() 返回一个不同的值。

```python
def my_generator():
    n = 1
    print("This is the first print")
    yield n

    n += 1
    print("This is the second print")
    yield n

    n += 1
    print("This is the third print")
    yield n

# 创建生成器对象
gen = my_generator()

# 每次调用 next() 都会执行到下一个 yield
print(next(gen)) # 输出: This is the first print \n 1
print(next(gen)) # 输出: This is the second print \n 2
print(next(gen)) # 输出: This is the third print \n 3

# 再次调用 next() 会触发 StopIteration
try:
    print(next(gen))
except StopIteration:
    print("No more values from generator.")
```

```python
def gene_squares(N: int):
    for i in range(N):
        yield i*i


for i in gene_squares(5):
    # 此处调用for循环本身就是调用迭代器的__next__()属性
    print(f"i is {i}")
```

### Generator Expressions

Syntactically, generator expressions are just like normal list comprehensions, and support all their syntax, including if filters and loop nesting, but they are **enclosed in parentheses** instead of square brackets (like tuples, their enclosing parentheses are often optional)

```python
lst_1 = [x**2 for x in range(20)]
print(lst_1)

lst_2 = (x**2 for x in range(20))
print(lst_2)
# [0, 1, 4, 9, 16, 25, 36, 49, 64, 81, 100, 121, 144, 169, 196, 225, 256, 289, 324, 361]
# <generator object <genexpr> at 0x7f25a96fbe00>
# tuple and list can also be generated via: tuple(lst_2) and set(lst_2)

lst_3 = ({x: x**2 for x in range(20)})
print(type(lst_3))
print(set(lst_3))
# <class 'dict'>
# {0, 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19}
```

> 当你把一个生成器对象作为参数传递给 list() 构造函数时，list() 会完全消耗这个生成器。它会从生成器中取出所有可用的值，直到生成器耗尽（即抛出 StopIteration 异常），然后用这些值构建一个完整的列表。一旦生成器被消耗，它就不能再次生成值了，因为它是一个一次性迭代器。

## Functional Programming

函数式编程（**Functional Programming**，简称 FP）是一种编程范式，它将计算视为数学函数的求值，并强调避免状态改变和可变数据。

- 纯函数（Pure Functions）：

    - 相同输入，相同输出： 每次给定相同的输入，函数总是产生相同的输出。

    - 无副作用： 函数不会修改其外部的任何数据（比如全局变量）或对外部产生任何可见的影响（比如打印到控制台、修改文件等）。

- 不可变性（Immutability）：

    - 数据一旦创建就不能被修改。如果需要改变数据，你会创建一个新的数据副本，而不是原地修改。

- 函数是一等公民（First-Class Functions）：

    - 函数可以像任何其他数据类型（如数字、字符串）一样被处理。

    - 这意味着函数可以作为参数传递给其他函数，可以作为其他函数的返回值，也可以赋值给变量。

- 避免共享状态（No Shared State）：

    - 程序中的不同部分不会共享和修改同一个可变状态。这使得代码更模块化，减少了并发编程中的复杂性。

See more in [Functional Programming](https://xiyuanyang-code.github.io/posts/Pythonic-Functional-Programming/)

## LEGB

**LEGB** is an acronym that stands for:

* **L** - **Local**: The innermost scope, where names defined inside the *current function* are found. Variables created within a function (not declared `global` or `nonlocal`) live here.

* **E** - **Enclosing (Nonlocal)**: The scope of any *outer functions* that wrap the current function. If you have nested functions, names in the immediate parent function's scope are considered "enclosing."

* **G** - **Global**: The module-level scope. Names defined outside of any function at the top level of a Python file (script) are global.

* **B** - **Built-in**: The broadest scope, containing names pre-defined in Python, like `print()`, `len()`, `True`, `False`, `None`, `list()`, etc.


When Python tries to find the definition of a name (like a variable, function, or class) in your code, it follows this specific order:

1.  **First, it looks in the `Local` scope.** If it finds the name there, it uses it.
2.  **If not found locally, it moves to the `Enclosing` scope(s).** It searches outwards through any nested functions.
3.  **If still not found, it checks the `Global` scope.**
4.  **Finally, if the name isn't found anywhere else, it checks the `Built-in` scope.**

If the name is not found in any of these four scopes, Python raises a `NameError`.

```python
def outer():
    x = "hello"
    y = "welcome"
    print(f"x is {x}")
    print(f"y is {y}")

    def inner():
        nonlocal y
        y = "welcome to the inner"
        print(f"x is {x}")
        print(f"y is {y}")
    
    inner()
    print(f"x is {x}")
    print(f"y is {y}")

outer()

# x is hello
# y is welcome
# x is hello
# y is welcome to the inner
# x is hello
# y is welcome to the inner
```

variable `x` and `y` is local to `outer()` but **enclosing** to `inner()`.

![LEGB Rules](https://s1.imagehub.cc/images/2025/07/20/e25bfa11a56fbdb2d2b03b2376fa5e5e.png)

The `nonlocal` keyword is used to work with variables inside nested functions, where the variable should not belong to the inner function.

