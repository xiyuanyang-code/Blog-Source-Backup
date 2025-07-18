---
title: Pythonic Functional Programming
date: 2025-07-16 12:18:17
index_img: /img/cover/functionpro.jpg
math: true
excerpt: "Tutorial for Pythonic: the introduction of functional programming and its philosophy"
categories:
  - Code
  - Python
tags:
  - Tutorial
  - Pythonic
  - Python
---

# Functional Programming

## Coding Style

**编程范式**是相比于编程语法规则更加高级并且抽象的概念，他规范并且指导程序员如何组织规范的代码。编程范式的设计往往和语言本身的特性与应用场景息息相关，例如：

- **面向过程的编程范式**：比较低级且简单的编程范式，将程序分解为一系列的“过程”或“函数”，这些过程按照顺序执行，共同完成任务。数据和操作数据的函数是分离的。

> 以下两种编程方式依赖于特定的语言和应用场景

- **声明式编程**：关注“做什么”而不是“怎么做”。程序员描述期望的结果，而不是详细的步骤。系统会自动找到实现结果的方法。例如Coq，SQL，HTML/CSS

- **事件驱动编程**：程序流程由用户操作、传感器输出或其他程序的“事件”决定。程序响应这些事件来执行相应的处理。例如JavaScript (`event.onClick()`)

> 对于更广泛的软件工程而言，以下的两种编程范式是**较为主流且被广泛采纳的**：

- **面相对象编程**：核心在于不同层级的类的继承，封装以及接口的设计。将程序视为一组相互协作的“对象”。每个对象封装了数据（属性）和操作数据的方法（行为）。

- **函数式编程**：将程序视为一系列纯函数的求值过程。强调使用函数来转换数据，而不是改变数据的状态。

> 纯函数式编程的覆盖面叫窄，但是包括很多语言都提供了函数式编程的范式，学习函数式编程的思想对我们有很大的好处。

## Functional Programming Definition

{% note primary %}

函数式编程 (**Functional Programming**, FP) 是一种编程范式，它将计算视为数学函数的求值，并避免使用可变状态和可变数据。简单来说，它强调“做什么”而不是“如何做”，并且更侧重于**表达式**而不是**语句**。

> 表达式：计算并且产生一个**值**
> 语句：执行动作或者命令（表达式可以看做是会产生值的一个特殊的语句）

{% endnote %}

Well, let's explain it step by step!

## Why Function Programming

在主流的面向对象编程时，程序员会涉及**复杂的类之间继承关系**，设计内部的私有成员和函数，以及设计暴露在外的公有接口等来实现对现实世界的模拟。这样的做法是毫无争议的，但是，随着软件工程项目的不断扩展，面向对象编程，或者说传统的编程范式出现了一些问题：

- **状态爆炸问题**：在图灵机中，计算是通过不断修改共享的纸带状态来完成的。在复杂程序中，这意味着有**大量的变量和数据结构在不断变化**，并且可能在程序的多个部分被修改。例如，一条错误的指令就可能会让程序崩溃，这一个程序往往涉及对成千上万个变量的读写操作，当Bug出现时，可变的数据结构和变量让Debug的过程显的非常复杂。

- **并发性挑战**：对于现代多核CPU的高并发性能，如果多个“读写头”同时操作同一段“纸带”（内存），就可能发生竞态条件 (Race Condition)，导致不可预测的结果，需要复杂的锁机制来同步访问，这增加了巨大的复杂性和调试难度。

因此，为了提高安全性能和开发效率，现代编程语言越来越引入**不可变性**的概念，即**一旦一个数据或对象被创建后，它的状态就不能再被改变**。不可变性的引入可以解决传统计算机语言的很多问题，例如并发锁，同时带来更好的可预测性和缓存性能。

> 怎么一股 Rust 味。

因此，OOP的编程范式需要被颠覆！我们不希望直接操作语句修改变量，**不可变性是第一性原理**。


### Pure Function

有了至高无上的不可变性原理，我们来看如何实现并应用到语言层面，下面引入**纯函数** (Pure Function) 的概念。


Definition of pure function in Wikipedia:

In computer programming, a pure function is a function that has the following properties

- the function return values are **identical** for identical arguments (no variation with local static variables, non-local variables, mutable reference arguments or input streams, i.e., **referential transparency**)

  - referential transparency: 引用透明性，即不存在静态局部变量，非局部变量，可变的引用参数和输入流

- the function has **no side effects** (no mutation of non-local variables, mutable reference arguments or input/output streams).

{% note primary %}

**引用透明性**：一个**表达式**如果具有引用透明性，那么在程序中，你可以用它的值（或计算结果）替换掉这个表达式的**任何出现**，而不会改变程序的行为。

例如，$2 + 3$这个表达式具有引用透明性，可以用计算结果或者返回值 $5$ 来代替。而下面的函数对应的表达式（例如: `increment(5)`）不具有引用透明性：

```python
global_val = 0
def increment_global(val):
    global global_val
    global_val += val
    return global_val
```

{% endnote %}

说人话，就是**不受任何外界影响**的黑箱函数，在任何情况下，$y = f(x_1, x_2, x_3, \dots x_n)$的取值只和Input Value有关。

> 这个定义貌似非常宽泛，实际上我们之前写的很多函数都是**纯函数**，纯函数保证了状态上的确定性，一切都服从于不可变性的原则。但是在OOP的世界里，**成员函数**就不属于纯函数的范畴因为这些往往涉及到私有数据变量的操作。

> 纯函数之间可以做 composition，这样的操作也是被KISS所鼓励的。

### FCC: Functions as first class citizens

在介绍完纯函数之后，我们进一步探讨函数和变量之间的关系和交互。

在传统的非函数式编程的编程范式中，变量和函数的功能往往分离，变量主要控制值的赋值和修改操作，而函数作为接口，提供一个计算的 Method. 在函数式编程中，**“函数是一等公民”（Functions as First-Class Citizens）**是一个核心概念。这意味着函数在编程语言中被当作普通的值来对待，拥有与其他数据类型（如数字、字符串、布尔值）相同的权利。

```python
def greet(name):
    return f"Hello, {name}!"

my_greeting_function = greet # 将函数 greet 赋值给变量 my_greeting_function
print(my_greeting_function("Alice")) # 调用这个变量，效果和直接调用 greet 一样
```

将函数视为一等公民打破了现有的OOP的编程范式（在OOP中，函数的层级往往低于类和对象），提高了代码的可抽象性和灵活性，在次之上，你也可以写更高级的高阶函数（函数作为参数被传递进去）

#### Everything in Python is an object!

Function is callable!

```python
def func(va_test = 1):
    print(va_test)


print(dir(func))
# ['__annotations__', '__builtins__', '__call__', '__class__', '__closure__', '__code__', '__defaults__', '__delattr__', '__dict__', '__dir__', '__doc__', '__eq__', '__format__', '__ge__', '__get__', '__getattribute__', '__getstate__', '__globals__', '__gt__', '__hash__', '__init__', '__init_subclass__', '__kwdefaults__', '__le__', '__lt__', '__module__', '__name__', '__ne__', '__new__', '__qualname__', '__reduce__', '__reduce_ex__', '__repr__', '__setattr__', '__sizeof__', '__str__', '__subclasshook__', '__type_params__']
```

在 Python 中查看一个函数对象（或者更广义地说，一个可调用对象）时，它所拥有的特殊属性（Special Attributes）或“魔术方法”（Magic Methods / Dunder Methods）。因为一切皆对象，所以函数本身也是一个对象，它拥有自己的属性和方法。这些以双下划线 `__` 开头和结尾的名称，是 Python 内部使用的约定，用来表示对象的特殊行为或特性。


| 名称             | 类型     | 描述                                                                                                                                                                                                                                                                      | 常见用途/备注                                                                                                          |
| :--------------- | :------- | :------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------ | :--------------------------------------------------------------------------------------------------------------------- |
| `__annotations__`| 属性     | 一个字典，存储函数的参数和返回值的**类型注解**（type hints）。例如 `def func(a: int) -> str:` 这里的注解就会存储在这里。                                                                                                                                                   | 静态类型检查工具（如 MyPy）使用；代码文档；运行时元编程。                                                            |
| `__builtins__`   | 属性     | 函数定义时所在的模块的全局命名空间中的内置函数（`__builtins__`）的引用。                                                                                                                                                                                                  | 调试或高级元编程，通常不直接使用。                                                                                     |
| `__call__`       | 方法     | 如果一个对象定义了这个方法，那么它就是**可调用**的，你可以像调用函数一样调用它。函数对象本身就实现了这个方法。                                                                                                                                                              | 使类的实例像函数一样被调用（例如，装饰器类）。                                                                         |
| `__class__`      | 属性     | 对象所属的类。对于函数，通常是 `<class 'function'>`。                                                                                                                                                                                                                    | 反射、类型检查。                                                                                                       |
| `__closure__`    | 属性     | 如果函数是一个**闭包**，这个属性会是一个元组，包含外部作用域变量的绑定（cell 对象）。如果不是闭包，则为 `None`。                                                                                                                                                           | 检查闭包的实现细节，高级调试。                                                                                         |
| `__code__`       | 属性     | 函数的**字节码对象**。包含了函数的编译代码信息（如参数名、局部变量名、字节码指令等）。                                                                                                                                                                                 | 运行时代码检查、动态代码生成、调试工具。                                                                               |
| `__defaults__`   | 属性     | 一个元组，包含函数的**位置参数默认值**。例如 `def func(a, b=1):`，那么 `__defaults__` 会是 `(1,)`。                                                                                                                                                                      | 运行时检查或修改默认参数。                                                                                             |
| `__delattr__`    | 方法     | 当尝试删除对象的属性时调用（例如 `del obj.attr`）。                                                                                                                                                                                                                    | 自定义属性删除行为，通常不直接在函数对象上使用。                                                                     |
| `__dict__`       | 属性     | 存储对象的可写属性的字典。对于函数，你可以通过 `func.my_attr = value` 的方式添加自定义属性，它们会存储在这里。                                                                                                                                                           | 存储动态添加的属性。                                                                                                   |
| `__dir__`        | 方法     | 当调用 `dir()` 函数时被调用，返回对象可用的属性和方法的列表。                                                                                                                                                                                                        | 自定义 `dir()` 的行为。                                                                                                |
| `__doc__`        | 属性     | 函数的**文档字符串**（docstring）。如果你在函数定义的第一行写了字符串，它就会存储在这里。                                                                                                                                                                               | 生成文档、帮助系统 (`help(func)`)。                                                                                     |
| `__eq__`         | 方法     | 定义对象之间的**相等性**（`==` 操作符）。                                                                                                                                                                                                                              | 自定义对象比较行为。                                                                                                   |
| `__format__`     | 方法     | 定义使用 `format()` 函数或 f-string 时对象的格式化行为。                                                                                                                                                                                                                | 自定义对象格式化输出。                                                                                                 |
| `__ge__`         | 方法     | 定义对象的“大于等于”比较（`>=` 操作符）。                                                                                                                                                                                                                              | 自定义对象比较行为。                                                                                                   |
| `__get__`        | 方法     | 如果函数是**描述符**，当它被访问时调用。通常用于实现属性、方法和类方法等。                                                                                                                                                                                            | 实现自定义的属性访问控制。                                                                                             |
| `__getattribute__`| 方法     | 每次访问对象的所有属性时调用（即使属性不存在）。                                                                                                                                                                                                                        | 非常高级的属性访问控制，慎用。                                                                                         |
| `__getstate__`   | 方法     | 用于对象序列化（如 `pickle` 模块）。定义对象在被 pickle 化时要保存的状态。                                                                                                                                                                                           | 自定义对象的序列化。                                                                                                   |
| `__globals__`    | 属性     | 函数定义时所在的模块的**全局命名空间**的字典引用。                                                                                                                                                                                                                        | 检查或修改函数运行时的全局变量，高级调试。                                                                             |
| `__gt__`         | 方法     | 定义对象的“大于”比较（`>` 操作符）。                                                                                                                                                                                                                                  | 自定义对象比较行为。                                                                                                   |
| `__hash__`       | 方法     | 定义对象的哈希值。可哈希（hashable）的对象可以作为字典的键或集合的元素。对于函数，其哈希值通常基于其 ID。                                                                                                                                                             | 将函数作为字典键或集合元素。                                                                                           |
| `__init__`       | 方法     | 对象的构造函数。当一个类的实例被创建时调用。**函数对象自身不是通过 `__init__` 直接构造的，它们在 Python 解释器加载时被创建。** | 初始化类的实例。                                                                                                       |
| `__init_subclass__`| 方法     | 当一个类被继承时，在其子类被定义时调用。                                                                                                                                                                                                                            | 记录子类、注册子类等，用于元类编程。                                                                                   |
| `__kwdefaults__` | 属性     | 一个字典，包含函数的**关键字参数默认值**。例如 `def func(*, a=10):`，那么 `__kwdefaults__` 会是 `{'a': 10}`。                                                                                                                                                         | 运行时检查或修改关键字参数默认值。                                                                                     |
| `__le__`         | 方法     | 定义对象的“小于等于”比较（`<=` 操作符）。                                                                                                                                                                                                                              | 自定义对象比较行为。                                                                                                   |
| `__lt__`         | 方法     | 定义对象的“小于”比较（`<` 操作符）。                                                                                                                                                                                                                                  | 自定义对象比较行为。                                                                                                   |
| `__module__`     | 属性     | 函数所属模块的名称。                                                                                                                                                                                                                                                   | 反射，确定函数定义的位置。                                                                                             |
| `__name__`       | 属性     | 函数的名称。例如 `def my_func():`，那么 `__name__` 会是 `"my_func"`。                                                                                                                                                                                                  | 调试、日志、模块导入。                                                                                                 |
| `__ne__`         | 方法     | 定义对象之间的**不相等性**（`!=` 操作符）。                                                                                                                                                                                                                          | 自定义对象比较行为。                                                                                                   |
| `__new__`        | 方法     | 对象的创建方法。在 `__init__` 之前调用，负责创建并返回对象的实例。**函数对象不是通过 `__new__` 直接创建的。** | 自定义对象创建过程，通常用于单例模式或不可变对象。                                                                     |
| `__qualname__`   | 属性     | 函数的限定名称，包含了定义它的类或函数的名称（如果它是一个嵌套函数或方法）。例如 `MyClass.my_method`。                                                                                                                                                                   | 调试、显示更完整的函数路径。                                                                                           |
| `__reduce__`     | 方法     | 用于对象序列化（如 `pickle` 模块）。返回一个元组，描述如何重构对象。                                                                                                                                                                                                 | 自定义对象的序列化。                                                                                                   |
| `__reduce_ex__`  | 方法     | 类似 `__reduce__`，但支持协议版本。                                                                                                                                                                                                                                | 自定义对象的序列化。                                                                                                   |
| `__repr__`       | 方法     | 定义对象的**官方字符串表示**。当你在交互式会话中直接输出对象或使用 `repr()` 函数时调用。目标是提供一个清晰、无歧义的表示，能够重新创建对象。                                                                                                                        | 调试，生成可用于重新创建对象的字符串。                                                                                 |
| `__setattr__`    | 方法     | 当尝试设置对象的属性时调用（例如 `obj.attr = value`）。                                                                                                                                                                                                                | 自定义属性设置行为，通常不直接在函数对象上使用。                                                                     |
| `__sizeof__`     | 方法     | 返回对象在内存中所占的字节数。                                                                                                                                                                                                                                         | 内存分析。                                                                                                             |
| `__str__`        | 方法     | 定义对象的**非正式的、用户友好的字符串表示**。当使用 `str()` 函数或 `print()` 函数时调用。                                                                                                                                                                          | 用户友好的输出。                                                                                                       |
| `__subclasshook__`| 方法     | 一个类方法，用于实现**抽象基类 (ABC)** 的 `issubclass()` 和 `isinstance()` 的自定义行为。                                                                                                                                                                           | 实现多态和类型检查。                                                                                                   |
| `__type_params__`| 属性     | Python 3.12+ 中新增，一个元组，包含函数的**类型参数**（如 `TypeVar`）。用于泛型函数。                                                                                                                                                                                 | 处理泛型代码，类型检查。                                                                                               |


```python
def func(va_test = 1):
    """doc string"""
    print(va_test)

func.greetings = "Hello"

print(func.__dict__)
print(func.__class__)
print(func.__code__)
print(func.__name__)
print(func.__defaults__)
print(func.__hash__)
print(func.__doc__)

# output:
# {'greetings': 'Hello'}
# <class 'function'>
# <code object func at 0x7f3439c72cd0, file "/tmp/ipykernel_7816/1690028656.py", line 1>
# func
# (1,)
# <method-wrapper '__hash__' of function object at 0x7f3439ab4180>
# doc string
```

#### Closure

- Functions can be nested in another function!

- Function Factory in Design Pattern

- It should satisfy:

  - 函数嵌套：内部函数 & 外部函数

  - 内部函数引用外部函数的数据

  - 内部函数被外部函数返回

例如：

```python
def make(N):
    def action(x):
        return x**N

    return action


# it is a factory
result = [(make(make_func))(input) for make_func in [1, 2, 3] for input in [1, 2, 3]]
print(result)
# [1, 2, 3, 1, 4, 9, 1, 8, 27]
```

> 当然，函数的装饰器也可以用作同等用途。被装饰的函数相当于参数被传递进去。

> 当然 ，lambda匿名函数也可以，the return value is the value of single expression.

