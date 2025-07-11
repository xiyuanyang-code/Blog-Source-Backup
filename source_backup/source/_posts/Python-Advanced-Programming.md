---
title: Python Advanced Programming
date: 2025-04-26 22:57:35
index_img: /img/cover/fp.jpg
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

## Table of Contents

- [Meta Programming](#Meta-Programming)
	- [Decorators](#Decorators)

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

> 如果这样想的话，各种脚本文件（bash，Dockerfile，Cmakelists）不都是Code as Data的思想吗？

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

        print(f"fuction {func.__name__} runs wirh time: {elapsed_time:.6f} seconds")
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
fuction sum_test runs wirh time: 0.000316 seconds
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
        # get the annotations (data types of the paramaters and return value)
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
