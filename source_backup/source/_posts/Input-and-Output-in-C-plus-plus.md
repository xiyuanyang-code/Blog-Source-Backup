---
title: Input-and-Output-in-C-plus-plus
date: 2024-12-28 13:04:27
index_img: /img/cover/stream.jpg
excerpt: This blog focuses on C++ stream-based input and output, including console-based, file-based, and string-based and share advanced techniques for custom IO, including the use of the iomanip library.
categories:
  - Code
  - Cpp
tags:
  - tutorial
  - Finished
  - IO
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Input, Output and Files in C++

## Abstract

This blog will focus on **C++ stream-based input and output,** including **console-based, file-based, and string-based input and output**. The blog will share some advanced techniques for custom input and output, including the use of the **iomanip** library, accessing **ASCII files and binary files**.

## Table of Contents

- [**Introductions**](#Introduction)

- [**Several Basic Concepts**](#Several-Basic-Concepts)

- [**Console-based input and output**](#Console-based-input-and-output)

## Introduction

在**冯诺依曼体系结构**中，计算机通过输入设备向电脑输入信息，经过运算后由输出设备输出。由此可见，输入输出是**程序与外部进行数据通信的重要枢纽**。在过往C++的学习中，我们已经掌握了一些有关输入输出的基本知识（`cin`和`cout`）。本章节将从更加底层的视角介绍C++中常见的三种输入输出：**基于控制台的输入输出类型`iostream`、基于文件的输入输出类型`fstream`和基于字符串的输入输出类型`sstream`**。

![冯诺依曼体系结构](fnym.png)

## Several Basic Concepts

在正式开始我们的内容之前，我们需要做一些准备工作，下文将向读者介绍四个基本概念：**流、控制台、缓冲区和IO标准库**。

### Streams

在计算机编程中，**流(Stream)**是一种用于输入和输出（I/O）操作的抽象。流可以看作是数据元素的序列，这些数据元素**按时间顺序可供程序读取或写入**，通常用于在程序与外部源（如文件、网络连接或控制台）之间传输数据。

具体到C++的输入输出中，C++的输入/输出是以**一连串字节流的方式**进行的。在输入操作中，字节从**设备（如键盘、磁盘）流向内存**，称为**输入流**。在输出操作中，字节**从内存流向设备（如显示器、打印机、磁盘等）**，称为**输出流**。 C++同时提供“低层次”和“高层次”的输入/输出。低层次的输入/输出直接**处理字节流中的一个个字节**，把每个字节仅看成一个二进制比特串。高层次的输入/输出可以将字节组合成有意义的单位，如**整型数、浮点数及自定义类型的值进行操作**。  

具体而言，高层次IO主要是基于**流（Stream）**的 I/O，提供了更抽象、更易用的接口（`cin`和`cout`）。它隐藏了底层细节（如缓冲区管理、设备控制等）。而低层次IO基于**系统调用**或**C 标准库函数**的 I/O，提供了更底层的控制，例如C语言中的`printf()`和`scanf()`函数。

| 特性   | 高层次 I/O           | 低层次 I/O           |
|:---- |:----------------- |:----------------- |
| 抽象程度 | 高（基于流）            | 低（基于文件描述符或缓冲区）    |
| 易用性  | 高（简洁的接口，类型安全）     | 低（需要手动管理资源）       |
| 性能   | 较低（有抽象层开销）        | 较高（直接操作底层资源）      |
| 控制粒度 | 较粗（隐藏了底层细节）       | 较细（可以精确控制 I/O 细节） |
| 适用场景 | 通用场景（如控制台、文件 I/O） | 高性能场景或需要精细控制的场景   |

### Console

**控制台（Console）**，也叫做**命令行界面（CLI，Command Line Interface）**或**终端（Terminal）**，是一个用于与计算机进行交互的文本界面。在控制台中，用户可以通过输入命令和接收计算机输出结果来与操作系统和应用程序进行交互。

控制台的基本概念：

1. **文本交互界面**： 控制台通常是一个纯文本的界面，没有图形元素，用户通过键盘输入命令，计算机通过文本输出反馈给用户。这与图形用户界面（GUI）相比，显得更加简洁、直接。
2. **命令输入和输出**： 在控制台中，用户通过输入命令来执行各种任务，比如文件管理、程序启动、系统配置等。计算机则通过文本输出给出执行结果或提示信息。
3. **程序的交互式操作**： 很多程序（尤其是开发工具、编译器、数据库等）都支持控制台交互，用户可以在控制台中输入指令，实时获取程序输出的结果。

### Buffer

**缓冲区（Buffer）** 是计算机系统中的一块临时存储区域，通常用于存储数据，以便在数据处理过程中提高效率。缓冲区可以帮助程序减少频繁的I/O操作（如磁盘读写、网络通信等），通过缓存数据来实现更高效的操作。

程序只能访问内存中的信息，而不能直接访问外部设备中的信息。当程序需要读取外部设备中的某个信息时，操作系统会将**包含此信息的一批数据从外部设备读入内存，再从内存读入程序**，此时发生了**一次外部设备的访问**。如果程序需要读取的数据已经在内存中，则不会发生外部设备的访问。输出也是如此。如果程序向外部设备输出一个信息，此信息被写在内存的某个地方，操作系统会定期将内存中的信息输出到外部设备。内存中存放这些数据的区域称为**输入/输出缓冲区**。  

**缓冲区的工作原理：**

1. **写缓冲区**：当程序向文件或网络发送数据时，数据首先被写入缓冲区，<font color=red>**等缓冲区被填满或条件满足**</font>时，再一次性将数据写入目标设备。
2. **读缓冲区**：当程序从文件或网络读取数据时，<font color=red>**数据首先被从目标设备加载到缓冲区，再从缓冲区读取数据到程序中**</font>。这样可以避免每次读取时都访问设备，提高读取速度。

```cpp
#include <iostream>
#include <fstream>

int main() {
    // 使用带缓冲的文件输入输出流
    std::ifstream inputFile("example.txt");  // 默认使用缓冲
    std::string line;

    if (inputFile.is_open()) {
        while (std::getline(inputFile, line)) {
            std::cout << line << std::endl;  // 缓冲区中的数据一次性输出
        }
        inputFile.close();
    } else {
        std::cerr << "无法打开文件" << std::endl;
    }

    return 0;
}
```

在这个例子中，`std::ifstream` 默认使用缓冲区来读取文件内容。它首先从文件系统读取一些数据到缓冲区，然后一次性将这些数据提供给程序。

{% note info %}

有关缓冲区的具体实现原理我们会稍后提及。（**与输入输出类的实现有关**）

{% endnote %}

### Standard Library for IO

C++标准库中一个重要的组成部分就是IO流库，专门实现C++中的输入和输出。提供基于流的输入输出功能，包括控制台、文件和字符串的 I/O。主要头文件：

- `<iostream>`：标准输入输出流（如 `std::cin`、`std::cout`）。
- `<fstream>`：文件流（如 `std::ifstream`、`std::ofstream`）。
- `<sstream>`：字符串流（如 `std::istringstream`、`std::ostringstream`）。

![iostream](iostream.png)

| 头文件          | 功能描述                     | 主要类/对象                                                                                 | 使用场景                              |
|:------------ |:------------------------ |:-------------------------------------------------------------------------------------- |:--------------------------------- |
| `<iostream>` | 提供标准输入输出功能，用于控制台 I/O。    | - `std::cin`：标准输入流 -`std::cout`：标准输出流 - `std::cerr`：标准错误流 - `std::clog`：标准日志流          | 控制台输入输出，如用户交互、调试信息输出等。            |
| `<fstream>`  | 提供文件输入输出功能，用于读写文件。       | - `std::ifstream`：文件输入流 - `std::ofstream`：文件输出流 - `std::fstream`：文件流                   | 文件读写操作，如读取配置文件、保存数据到文件等。          |
| `<sstream>`  | 提供字符串输入输出功能，用于将字符串作为流处理。 | - `std::istringstream`：字符串输入流 - `std::ostringstream`：字符串输出流 - `std::stringstream`：字符串流 | 字符串格式化、解析，如将字符串转换为数字、将数据格式化为字符串等。 |

## Console-based input and output

在**运算符重载**这一小节时，笔者曾经介绍了cin/cout的真实面目是**`istream`类和`ostream`类的对象**。并讲解了如何重载流输入输出运算符。下面转载自己的部分博客内容，[原文点这里](https://xiyuanyang-code.github.io/posts/Introduction-to-OOP/)。

{% note info %}

### 输入/输出运算符的重载

#### 重新认识`<<`和`>>`运算符

在笔者的第一堂C++启蒙课上，笔者便体会到了**流操作**的精妙和便捷。（相比于`printf()`和`scanf()`的冗长语法）但是，当时笔者只是把`cin/cout`当做一个普通的函数来使用，并不知道其背后深层次的原理。下面，我们先来重新认识一下cin/cout的真面目。

##### `istream`和`ostream`

在 C++ 中，**流类（如 `std::istream` 和 `std::ostream`）是处理输入输出操作的核心类**，它们为程序提供了与数据流进行交互的功能。这些类是 C++ 标准库的一部分，用于简化与文件、控制台、字符串等设备的交互。

1. **`std::istream` 类**

`std::istream` 类是用于处理输入操作的类，提供了从输入流中读取数据的功能。它是所有输入流类（如 `cin`）的基类。

- **构造函数**：`std::istream` 的构造函数可以用来打开文件或初始化输入流。

- 成员函数
  
  - `operator>>`：流提取运算符，用于从流中提取数据。
  - `get()`：用于读取一个字符或一行数据。
  - `read()`：从流中读取特定数量的字符。
  - `eof()`：检查是否到达文件结束标志。
  - `fail()`：检查流是否进入失败状态。
2. **`std::ostream` 类**

`std::ostream` 类是用于处理输出操作的类，提供了将数据写入输出流的功能。它是所有输出流类（如 `cout`）的基类。

- **构造函数**：`std::ostream` 的构造函数用于打开输出流或初始化输出流。

- 成员函数
  
  - `operator<<`：流插入运算符，用于将数据插入到流中。
  - `put()`：用于向流中写入一个字符。
  - `write()`：用于向流中写入特定数量的字符。
  - `flush()`：强制输出缓冲区内容，确保数据立即写入流。
  - `endl`：插入一个换行符并刷新输出流。
3. **继承结构**

`std::istream` 和 `std::ostream` 类本身都继承自 `std::ios` 类，它们共享一些基本的输入输出功能。`std::ios` 类提供了管理流状态的功能，比如检查是否处于错误状态、是否已到达文件末尾等。

- **`std::ios` 类**：`std::ios` 是 `std::istream` 和 `std::ostream` 的基类，负责流的基本状态管理（如错误标志、格式控制等）。
  - `std::ios::good()`: 检查流是否处于良好状态。
  - `std::ios::eof()`: 检查流是否到达文件末尾。
  - `std::ios::fail()`: 检查流是否处于失败状态。
4. **常见的派生类**
- **`std::ifstream`**：`std::ifstream` 是 `std::istream` 的派生类，用于处理文件输入。它提供了从文件读取数据的功能。
- **`std::ofstream`**：`std::ofstream` 是 `std::ostream` 的派生类，用于处理文件输出。它提供了将数据写入文件的功能。
- **`std::stringstream`**：`std::stringstream` 是 `std::istream` 和 `std::ostream` 的派生类，允许在内存中处理字符串流。它既可以用于输入也可以用于输出。

##### `cin`和`cout`

`cin` 和 `cout` 是 C++ 标准库中**预定义的输入和输出流对象**，它们是由流类（如 `istream` 和 `ostream`）创建的全局对象，用于进行数据的输入和输出。

- **`cin`（标准输入流对象）**：`cin` 是一个全局的输入流对象，属于 `std::istream` 类，通常用于从标准输入（如键盘）获取数据。
- **`cout`（标准输出流对象）**：`cout` 是一个全局的输出流对象，属于 `std::ostream` 类，通常用于将数据输出到标准输出（如显示器）。

{% note danger %}

**是的，cin/cout根本不是函数，而是对象！！！**

{% endnote %}

##### `<<`和`>>`运算符

这些运算符实际上是被重载的，它们不是内置运算符，而是通过重载来定义流操作的行为。

- **流插入运算符 `<<`**：被 `std::ostream` 类重载，用于将数据插入到输出流中。当你写 `cout << x;` 时，实际上是调用了 `std::ostream` 类的 `operator<<` 函数。
- **流提取运算符 `>>`**：被 `std::istream` 类重载，用于从输入流中提取数据。当你写 `cin >> x;` 时，实际上是调用了 `std::istream` 类的 `operator>>` 函数。

流插入运算符<<是一个二元运算符。例如，表达式 cout << x 的运算符两侧分别是 cout 和 x， x 是一个整型变量， cout 是输出流类 `ostream` 的对象。 <<运算符将右边对象的值转换成文本形式插入左边的输出流对象， 执行结果是左边的输出流对象的引用。对于 cout << x，运算结果为对象 cout。正因为<<运算的结果是左边对象的引用，所以允许执行 `cout << x << y` 等的操作。因为<<是左结合的，所以上述表达式先执行 `cout << x`，执行的结果是对象 cout，然后执行 `cout << y`。

#### 在自定义类中重载`<<`和`>>`运算符

由于第一个参数是`ostream/istream` 类的对象，因此**流插入运算符不能重载成成员函数，必须重载成全局函数，流输出运算符也是如此**。

为什么？在 C++ 中，**成员函数的第一个参数通常是隐式的 `this` 指针**，指向当前对象。因此，成员函数可以通过 `this` 指针访问对象的成员变量和其他成员函数。也就是说，只有运算符的第一个参数是可以被**this指针指向的，才能够被定义为类内的成员函数**，否则必须被**定义为友元函数**。

```C++
#include <iostream>
using namespace std;

class Point {
private:
    int x, y;

public:
    // 构造函数
    Point(int x = 0, int y = 0) : x(x), y(y) {}

    // 重载 << 运算符
    friend ostream& operator<<(ostream& os, const Point& p);
    //参数中第一个对象是ostream类的对象（相当于cout）
};

// 重载 << 运算符
ostream& operator<<(ostream& os, const Point& p) {
    os << "(" << p.x << ", " << p.y << ")";
    return os;  // 返回 ostream 引用，以便链式调用
}

int main() {
    Point p(3, 4);
    cout << "Point: " << p << endl;  // 调用重载的 << 运算符
    return 0;
}
```

```C++
#include <iostream>
using namespace std;

class Point {
private:
    int x, y;

public:
    // 构造函数
    Point(int x = 0, int y = 0) : x(x), y(y) {}

    // 重载 >> 运算符
    friend istream& operator>>(istream& is, Point& p);
    //参数中第一个对象是istream类的对象（相当于cin）
};

// 重载 >> 运算符
istream& operator>>(istream& is, Point& p) {
    is >> p.x >> p.y;  // 从输入流中读取数据到成员变量
    return is;  // 返回 istream 引用，以便链式调用
}

int main() {
    Point p(0, 0);
    cout << "Enter coordinates for the point (x y): ";
    cin >> p;  // 调用重载的 >> 运算符
    cout << "You entered point: " << p << endl;
    return 0;
}
```

**相关注意点：**

- 注意返回类型是对`istream`或`ostream`的引用。

{% endnote %}

### iomanip

`iomanip` 是 C++ 标准库中的一个头文件，全称为 **Input/Output Manipulators**（输入输出操纵器）。它提供了一系列用于**格式化输入**输出的工具函数，通常与 `iostream` 库（如 `cin` 和 `cout`）结合使用，以控制数据的显示方式。**格式化输入/输出**是通过**流操纵符**或**`istream` 和 `ostream` 类的成员函数**实现的。流操纵符是以一个流引用作为参数，并返回同一流引用的函数，因此它可以嵌入输入/输出操作的链中。 `endl` 就是最常用的流操纵符。格式化输入/输出的功能包括**设置整型数的基数、设置浮点数的精度、设置和改变域宽、设置域的填充字符**等。  

`iomanip` 中常用的函数包括：

1. **设置字段宽度** 
   `setw(n)`：设置下一个输出字段的宽度为 `n` 个字符。
   
   ```cpp
   cout << setw(10) << "Hello"; // 输出 "     Hello"（前面有 5 个空格）
   ```

2. **设置浮点数精度** 
   `setprecision(n)`：设置浮点数输出的精度为 `n` 位。
   
   ```cpp
   cout << setprecision(3) << 3.14159; // 输出 "3.14"
   cout << fixed << setprecision(3) << 3.14159; //输出 "3.142"
   
   // 你也可以写成 cout.precision(3);cout<<x;
   ```
   
   在默认模式下，`setprecision(n)` 控制的是**有效数字位数**，即从第一个非零数字开始计算的总位数。在 `fixed` 模式下，`setprecision(n)` 控制的是**小数点后的位数**。(**是四舍五入**)。在 `scientific` 模式下，`setprecision(n)` 控制的是**小数点后的位数**，但数字会以科学计数法显示。

3. **设置填充字符** 
   `setfill(c)`：设置填充字符为 `c`，通常与 `setw` 一起使用。
   
   ```cpp
   cout << setfill('*') << setw(10) << "Hi"; // 输出 "********Hi"
   ```

4. **控制布尔值输出格式** 
   `boolalpha` / `noboolalpha`：将布尔值输出为 `true`/`false` 或 `1`/`0`。
   
   ```cpp
   cout << boolalpha << true; // 输出 "true"
   ```

5. **控制数字的进制** 
   `hex`、`dec`、`oct`：分别将数字输出为十六进制、十进制和八进制。
   
   也可以使用`setbase()`进行格式化控制。（**参数化的流操纵符**）
   
   ```cpp
   cout << hex << 255; // 输出 "ff"
   ```

```cpp
#include <iostream>
#include <iomanip>
using namespace std;

int main() {
    cout << setw(10) << setfill('-') << "Hello" << endl; // 输出 "-----Hello"
    cout << setprecision(4) << 3.14159 << endl;          // 输出 "3.142"
    cout << hex << 255 << endl;                          // 输出 "ff"
    return 0;
}
```

6. **自定义输出流操纵符**

在 C++ 中，自定义流操纵符函数（Custom Stream Manipulators）允许你创建自己的流操作符，用于简化或扩展输入输出流的格式化操作。自定义流操纵符函数可以是一个无参数的函数，也可以是一个带参数的函数，它们通过操作流的内部状态或执行特定操作来实现自定义功能。

1. **无参数的自定义流操纵符**

无参数的自定义流操纵符是一个简单的函数，它接受一个流对象（`std::ostream&` 或 `std::istream&`）并返回该流对象。通常用于设置流的格式状态。

**示例：实现一个自定义操纵符，将输出流的颜色设置为红色（假设终端支持 ANSI 颜色代码）：**

```cpp
#include <iostream>
using namespace std;

// 自定义操纵符函数：设置为红色
ostream& red(ostream& os) {
    os << "\033[31m"; // ANSI 颜色代码：红色
    return os;
}

// 自定义操纵符函数：恢复默认颜色
ostream& reset(ostream& os) {
    os << "\033[0m"; // ANSI 重置代码
    return os;
}

int main() {
    cout << red << "This text is red!" <<endl<< reset << " This text is back to default." << endl;
    return 0;
}
```

2. **带参数的自定义流操纵符**

带参数的自定义流操纵符需要借助一个辅助类来实现。通过重载 `operator<<` 或 `operator>>`，可以传递参数并执行更复杂的操作。

**示例：实现一个自定义操纵符，重复输出指定次数的字符串：**

```cpp
#include <iostream>
using namespace std;

// 辅助类
struct RepeatManipulator {
    int count;
    const string& str;

    RepeatManipulator(int n, const string& s) : count(n), str(s) {}
};

// 重载 operator<<
ostream& operator<<(ostream& os, const RepeatManipulator& manip) {
    for (int i = 0; i < manip.count; ++i) {
        os << manip.str;
    }
    return os;
}

// 自定义操纵符函数
RepeatManipulator repeat(int n, const string& s) {
    return RepeatManipulator(n, s);
}

int main() {
    cout << repeat(3, "Hello! ") << endl; // 输出 "Hello! Hello! Hello! "
    return 0;
}
```

---

3. **结合 `iomanip` 使用**

自定义流操纵符可以与 `iomanip` 中的标准操纵符结合使用，以实现更强大的功能。

**示例：实现一个自定义操纵符，将浮点数输出为百分比格式：**

```cpp
#include <iostream>
#include <iomanip>
using namespace std;

// 自定义操纵符函数
ostream& percent(ostream& os) {
    os << fixed << setprecision(2) << '*'; // 设置格式并添加百分号
    return os;
}

int main() {
    double num = 0.753;
    cout << "Percentage: " << num * 100 << percent << endl; // 输出 "Percentage: 75.30%"
    return 0;
}
```

---

4. **自定义输入流操纵符**

自定义流操纵符也可以用于输入流（`std::istream`），用于解析或处理输入数据。

**示例：实现一个自定义操纵符，跳过输入流中的逗号：**

```cpp
#include <iostream>
using namespace std;

// 自定义操纵符函数
istream& skipComma(istream& is) {
    char c;
    is >> c; // 读取一个字符
    if (c != ',') {
        is.putback(c); // 如果不是逗号，放回流中
    }
    return is;
}

int main() {
    int a, b;
    cout << "Enter two numbers separated by a comma: ";
    cin >> a >> skipComma >> b;
    cout << "You entered: " << a << " and " << b << endl;
    return 0;
}
```

> **自定义流操作符是C++输入输出的利器。**强烈推荐大家上手实现自己的流操作符！

## File-based input and output

### Preliminaries

在我们之前所遇到的程序中，程序的输入都指的是**键盘的手动输入**，这种输入方式灵活便捷但具有局限性。（例如我编写了一个程序需要统计公司所有员工的考勤记录，如果把所有数据全部手敲进终端中无疑是费时费力的。）因此，**文件的输入输出**在某些情况下相比于控制台的输入输出有着得天独厚的优越性。

为了展示文件输入输出的强大之处，先向笔者展示以下的程序：

![File-based input and output](Bash.png)

> 上图的命令行使用Bash语言，感兴趣的读者可自行学习，笔者这里使用的是GitBash（Powershell就是💩）

- `cat` 展示文件的内容
- `g++ testfile1.cpp =o main `编译`testfile1.cpp`文件，生成`main.exe`的可执行文件
- `./main.exe` 运行`main.exe`
- `./main.exe <1.txt >2.txt`
  - **Bash语言的输入重定向和输出重定向**：将`1.txt`的文件内容当做输入输入进`main.exe`中，然后将程序的输出流向`2.txt`

根据结果不难发现，`1.txt`作为输入内容，成功被程序所执行并且**成功将执行的结果导入到`2.txt`**中。**泰裤辣！**

### Files, Streams, and EOF

#### EOF

C++的文件没有记录的概念，它把文件看成字节序列，即由一个个字节顺序组成，每一个文件以**文件结束符（ End Of File， EOF）**结束，这种文件称为**流式文件**。我们可以将 C++的文件看成一个字符串。只不过这个字符串不是存放在内存中的，而是存放在外存中；不是用`'\0'`结束的，而是用 EOF 结束。

#### Files

根据不同的读写方式，**文件**在C++中具体被分类为**ASCII文件**和**二进制文件**。**ASCII文件**是指使用 ASCII 编码存储文本的文件，用来存储纯文本数据。而**二进制文件**储存的是**数据在内存中的表示**。例如我们常见的可执行文件`.exe`就是一种常见的二进制文件。

#### Streams

和`iostream`类似，在数据传输过程中，文件也可以被看做是**一种输入输出流**。当应用程序从文件中读取数据时，将文件与一个**输入文件流对象** `ifstream` 相关联（**这一步叫做打开文件**）。当应用程序将数据写入一个文件时，将文件与一个**输出文件流对象** `ofstream` 相关联（**这一步叫做关闭文件**）。如果既要输入又要输出，则与**输入/输出文件流对象** `fstream` 相关联。  文件被关闭后，该文件流对象可以与其他文件相关联。

在操作上，`fstream`是`iostream`的派生类，因此并无较大的差异。

### Using `fstream` for ASCII

下面的代码给出了使用`fstream`的若干基本用法：

```C++
#include <iostream>
#include <fstream> // 包含文件流库
#include <string>  // 包含字符串库
using namespace std;

int main() {
    // 1. 打开文件
    // 使用 fstream 对象，可以同时支持读写操作
    fstream file;

    // 打开文件 "example.txt"，模式为：
    // ios::out - 写入模式（如果文件不存在则创建）
    // ios::in  - 读取模式
    // ios::trunc - 如果文件存在，清空文件内容
    file.open("example.txt", ios::out | ios::in | ios::trunc);

    // 检查文件是否成功打开
    if (!file.is_open()) {
        cerr << "Error: Failed to open file!" << endl;
        return 1; // 返回错误码
    }

    // 2. 写入数据到文件
    string dataToWrite = "Hello, this is a line of text written to the file.\n";
    file << dataToWrite; // 使用流插入运算符写入数据
    file << "Another line of text.\n";

    // 3. 读取文件内容
    // 将文件指针移动到文件开头
    file.seekg(0, ios::beg);

    string line;
    cout << "Reading from file:\n";
    while (getline(file, line)) { // 逐行读取文件内容
        cout << line << endl;     // 输出到控制台
    }

    // 4. 关闭文件
    file.close();

    // 5. 重新打开文件以追加数据
    file.open("example.txt", ios::out | ios::app); // ios::app 表示追加模式
    if (!file.is_open()) {
        cerr << "Error: Failed to reopen file for appending!" << endl;
        return 1;
    }

    // 追加数据
    file << "This line is appended to the file.\n";

    // 6. 再次读取文件内容
    file.close();
    file.open("example.txt", ios::in); // 以只读模式打开文件
    if (!file.is_open()) {
        cerr << "Error: Failed to reopen file for reading!" << endl;
        return 1;
    }

    cout << "\nReading after appending:\n";
    while (getline(file, line)) { // 逐行读取文件内容
        cout << line << endl;     // 输出到控制台
    }

    // 7. 关闭文件
    file.close();

    return 0;
}
```

{% note primary %}

**代码的相关要点解释**

#### **定义文件流对象**

`fstream file`将**文件流对象**和输入的数据流绑定。

#### 打开文件

定义好文件流对象后，就可以使用文件流对象的`open()`成员函数实现**打开文件**的操作。下面是`open()`成员函数的参数列表：

```C++
void open(const char* filename, ios_base::openmode mode = ios_base::in | ios_base::out);
```

不难发现`open()`函数有两个参数，第一个参数接受C语言风格的字符串，第二个参数是**`ios_base::openmode`**。`ios_base::openmode` 是 C++ 标准库中定义的一个枚举类型，用于指定文件流的打开模式。它是 `std::ios_base` 类的成员类型，通常通过 `std::ios` 命名空间来访问（例如 `std::ios::in`）。`ios_base::openmode` 的主要作用是控制文件流的打开方式，例如是读取、写入、追加还是二进制模式等。这些模式可以通过位运算符 `|` 组合使用。

| 模式                 | 描述                                     |
|:------------------ |:-------------------------------------- |
| `std::ios::in`     | 以读取模式打开文件。如果文件不存在，打开失败。                |
| `std::ios::out`    | 以写入模式打开文件。如果文件不存在，会创建文件；如果文件存在，默认覆盖内容。 |
| `std::ios::app`    | 以追加模式打开文件。所有写入操作都在文件末尾进行，不会覆盖原有内容。     |
| `std::ios::ate`    | 打开文件后，将文件指针定位到文件末尾。                    |
| `std::ios::trunc`  | 如果文件存在，清空文件内容（截断文件）。                   |
| `std::ios::binary` | 以二进制模式打开文件（默认是文本模式）。                   |

因此，上文中`file.open("example.txt", ios::out | ios::in | ios::trunc);`的意思就是：打开`example.txt`文件，可读可写且截断文件。由默认参数可知，`fstream`类的对象默认可读也可写。

{% note danger %}

在实际的应用中，程序员为了保证安全性，会**实现ifstream和ofstream**的分离。在`ifstream`的情况下，就不可以使用`out`模式了，反过来也同样如此。

{% endnote %}

#### 检查文件

**在复杂程序中**，加入适当的**检查点**是非常好的习惯，这也有利于我们检查代码是否有一些隐式的错误。例如在这里，我们需要**检查文件是否被成功打开并且被读取**。`file.is_open()`就是这样一个函数，返回一个布尔值。`cerr << "Error: Failed to open file!" << endl;` 这行代码的作用是向标准错误流（`cerr`）输出一条错误信息，表示文件打开失败。此时程序会直接结束。

#### 流输入和流输出运算符

`file << dataToWrite;` 这行代码的作用是将数据（`dataToWrite`）写入到文件流（`file`）中。它是 C++ 中流插入运算符（`<<`）的典型用法，用于将数据**输出**到文件。

> 用法和`cout`没什么区别

同理，使用`>>`运算符，可以将**数据输入到文件**中。

#### 注意点

- 和`cin/cout`一样，文件的输入输出流也是**忽略空白字符的！**如果有必要也可以向上文的程序一样使用`getline()`。
- ASCII文件也可以写入各种数字和字符串。

{% endnote %}

### Access to binary files

在二进制文件中，所有的文件输入和读取都是以**字节**为基本单位，这与ASCII的读取有很大的不同。（**ASCII文件可以读取各种类型，包括整型和浮点型**）

由于在内存中，`char`类型的数据都只占1个字节，因此，在**二进制文件**中，输入输出都需要**强制转换为char类型指针**后进行操作。这一点会在后面的代码示例中做进一步解释。

#### 打开二进制文件

对于二进制文件，`fstream` 提供了 `read` 和 `write` 方法，用于直接读写二进制数据。

使用 `fstream` 打开二进制文件时，需要指定文件模式为 `ios::binary`。**使用方式和ASCII文件没有区别**。

```cpp
#include <fstream>
using namespace std;

int main() {
    // 打开二进制文件用于写入
    fstream file("data.bin", ios::out | ios::binary);

    if (!file) {
        cerr << "文件打开失败！" << endl;
        return 1;
    }

    // 文件操作...

    file.close(); // 关闭文件
    return 0;
}
```

---

#### **写入二进制数据**

使用 `write` 方法将二进制数据写入文件。`write` 方法的原型为：

```cpp
ostream& write(const char* buffer, streamsize size);
```

- `buffer`：指向要写入的数据的指针。
- `size`：要写入的字节数。

```cpp
#include <fstream>
#include <iostream>
using namespace std;

int main() {
    fstream file("data.bin", ios::out | ios::binary);
    if (!file) {
        cerr << "文件打开失败！" << endl;
        return 1;
    }

    int data[] = {1, 2, 3, 4, 5};
    file.write(reinterpret_cast<char*>(data), sizeof(data)); // 写入二进制数据

    file.close();
    return 0;
}
```

`reinterpret_cast` 是 C++ 中的一种强制类型转换操作符。它可以将任意类型的指针转换为另一种类型的指针，而不改变指针指向的实际数据。它通常用于低级别的内存操作，例如将 `int*` 转换为 `char*`。在这里， `data` 的指针类型被转换为 `char*` 类型，以便可以按字节访问数据。

#### **读取二进制数据**

使用 `read` 方法从二进制文件中读取数据。`read` 方法的原型为：

```cpp
istream& read(char* buffer, streamsize size);
```

- `buffer`：指向存储读取数据的缓冲区的指针。
- `size`：要读取的字节数。

示例：

```cpp
#include <fstream>
#include <iostream>
using namespace std;

int main() {
    fstream file("data.bin", ios::in | ios::binary);
    if (!file) {
        cerr << "文件打开失败！" << endl;
        return 1;
    }

    int data[5];
    file.read(reinterpret_cast<char*>(data), sizeof(data)); // 读取二进制数据

    // 输出读取的数据
    for (int i = 0; i < 5; ++i) {
        cout << data[i] << " ";
    }
    cout << endl;

    file.close();
    return 0;
}
```

### File Pointer

在上文的两种访问方式中，无论是对ASCII文件的访问还是对二进制文件的访问，都是**顺序访问**，即扫描一遍文件进行写入或输出等操作。但是，在实际应用中，我们需要更加灵活且高效的文件访问操作：例如**只扫描文件的特定部分**或者**自定义扫描文件的顺序**。换句话，我们需要程序能够自主控制**操作位置的移动**。因此，我们需要**文件指针**。

**文件指针**是用于标识文件中当前位置的一个标记，它指示了下一个读取或写入操作将从文件的哪个位置开始。文件指针的概念在文件操作中非常重要，尤其是在**随机访问文件**时。

在 C++ 中，文件指针的操作主要通过以下函数实现：

1. **移动文件指针**
- `seekg`：用于输入流，移动读取指针。
  
  ```cpp
  istream& seekg (streampos pos); // 移动到绝对位置
  istream& seekg (streamoff off, ios_base::seekdir way); // 移动到相对位置
  ```

- `seekp`：用于输出流，移动写入指针。
  
  ```cpp
  ostream& seekp (streampos pos); // 移动到绝对位置
  ostream& seekp (streamoff off, ios_base::seekdir way); // 移动到相对位置
  ```
2. **获取文件指针位置**
- `tellg`：用于输入流，返回当前读取指针的位置。
  
  ```cpp
  streampos tellg();
  ```

- `tellp`：用于输出流，返回当前写入指针的位置。
  
  ```cpp
  streampos tellp();
  ```

> `streampos` 是 C++ 标准库中用于表示**流位置**的数据类型。它通常用于标识文件流（如 `ifstream`、`ofstream` 等）中的位置，例如文件指针的位置。`streampos` 是一个与平台相关的类型，通常是一个整数类型（如 `long` 或 `long long`），用于表示从文件开头到某个位置的字节偏移量。

#### `seekg`和`seekp`

使用 `seekg` 和 `seekp` 可以移动文件指针，分别用于读取和写入操作。

```cpp
file.seekg(0, ios::beg); // 将读取指针移动到文件开头
file.seekp(0, ios::end); // 将写入指针移动到文件末尾
```

{% note info %}

`seekg` 和 `seekp` 是 C++ 中用于文件流定位的成员函数，分别用于输入流和输出流。它们允许你在文件中移动读取或写入的位置。

`seekg` 用于输入流（如 `ifstream`），用于设置文件读取位置。

```cpp
istream& seekg (streampos pos);
istream& seekg (streamoff off, ios_base::seekdir way);
```

- `pos`：绝对位置，表示从文件开头到该位置的字节数。
- `off`：偏移量，表示相对于 `way` 的字节数。
- `way`：基准位置，可以是以下值之一：
  - `ios_base::beg`：文件开头。
  - `ios_base::cur`：当前位置。
  - `ios_base::end`：文件末尾。

```cpp
#include <iostream>
#include <fstream>

int main() {
    std::ifstream file("example.txt", std::ios::in | std::ios::binary);
    if (file) {
        file.seekg(10, std::ios::beg); // 从文件开头移动10个字节
        char buffer[20];
        file.read(buffer, 20); // 读取20个字节
        std::cout.write(buffer, 20);
        file.close();
    }
    return 0;
}
```

`seekp` 用于输出流（如 `ofstream`），用于设置文件写入位置。

```cpp
ostream& seekp (streampos pos);
ostream& seekp (streamoff off, ios_base::seekdir way);
```

- `pos`：绝对位置，表示从文件开头到该位置的字节数。
- `off`：偏移量，表示相对于 `way` 的字节数。
- `way`：基准位置，可以是以下值之一：
  - `ios_base::beg`：文件开头。
  - `ios_base::cur`：当前位置。
  - `ios_base::end`：文件末尾。

```cpp
#include <iostream>
#include <fstream>

int main() {
    std::ofstream file("example.txt", std::ios::out | std::ios::binary);
    if (file) {
        file.seekp(10, std::ios::beg); // 从文件开头移动10个字节
        file.write("Hello, World!", 13); // 写入13个字节
        file.close();
    }
    return 0;
}
```

{% endnote %}

#### `tellg`和`tellp`

`tellp` 和 `tellg` 是 C++ 中用于文件流位置操作的成员函数，分别用于输出流和输入流。它们的作用是返回当前文件指针的位置（以字节为单位）。

`tellg` 用于输入流（如 `ifstream`），返回当前读取位置。

```cpp
streampos tellg();
```

- 返回一个 `streampos` 类型的值，表示当前读取位置（从文件开头的字节偏移量）。
- 如果失败，返回 `-1`。

```cpp
#include <iostream>
#include <fstream>

int main() {
    std::ifstream file("example.txt", std::ios::in | std::ios::binary);
    if (file) {
        file.seekg(10, std::ios::beg); // 移动读取位置到第10字节
        std::streampos pos = file.tellg(); // 获取当前读取位置
        std::cout << "Current read position: " << pos << std::endl;
        file.close();
    }
    return 0;
}
```

`tellp` 用于输出流（如 `ofstream`），返回当前写入位置。

```cpp
streampos tellp();
```

- 返回一个 `streampos` 类型的值，表示当前写入位置（从文件开头的字节偏移量）。
- 如果失败，返回 `-1`。

```cpp
#include <iostream>
#include <fstream>

int main() {
    std::ofstream file("example.txt", std::ios::out | std::ios::binary);
    if (file) {
        file.seekp(20, std::ios::beg); // 移动写入位置到第20字节
        std::streampos pos = file.tellp(); // 获取当前写入位置
        std::cout << "Current write position: " << pos << std::endl;
        file.close();
    }
    return 0;
}
```

#### 综合示例

```cpp
#include <iostream>
#include <fstream>

int main() {
    // 写入数据到文件
    std::ofstream outFile("example.txt", std::ios::out | std::ios::binary);
    if (outFile) {
        outFile << "Hello, World!"; // 写入字符串
        std::streampos writePos = outFile.tellp(); // 获取当前写入位置
        std::cout << "Write position after writing: " << writePos << std::endl;
        outFile.close();
    }

    // 读取数据从文件
    std::ifstream inFile("example.txt", std::ios::in | std::ios::binary);
    if (inFile) {
        inFile.seekg(0, std::ios::end); // 移动读取位置到文件末尾
        std::streampos endPos = inFile.tellg(); // 获取文件大小
        std::cout << "File size: " << endPos << " bytes" << std::endl;

        inFile.seekg(0, std::ios::beg); // 移动读取位置到文件开头
        char buffer[100];
        inFile.read(buffer, endPos); // 读取整个文件
        buffer[endPos] = '\0'; // 添加字符串结束符
        std::cout << "File content: " << buffer << std::endl;
        inFile.close();
    }

    return 0;
}
```

### Stream-based files

这一部分的内容笔者将会单独拉出来更新一期，敬请期待。

## String-based input and output

在 C++ 编程中，处理字符串和数字之间的转换是一项常见的任务。

`sstream` 是 C++ 标准库中的一个组件，它提供了一种方便的方式来处理字符串流（可以像处理流一样处理字符串）。`<sstream>` 允许你将字符串当作输入/输出流来使用，这使得从字符串中读取数据或将数据写入字符串变得非常简单。

### Definition

`sstream`是 C++ 标准库中的一个命名空间，它包含了几个类，用于处理字符串流，这些类包括：

- `istringstream`：用于从字符串中读取数据。
- `ostringstream`：用于将数据写入字符串。
- `stringstream`：是`istringstream`和`ostringstream`的组合，可以同时进行读取和写入操作。

### Grammar

使用`sstream`的基本语法如下：

```C++
#include <sstream>

// 使用istringstream
std::istringstream iss("some data");

// 使用ostringstream
std::ostringstream oss;

// 使用stringstream
std::stringstream ss;
```

#### 从字符串读取数据

下面是一个使用 `istringstream` 从字符串中读取整数和浮点数的例子：

```C++
#include <iostream>
#include <sstream>

int main() {
  std::string data = "10 20.5";
  std::istringstream iss(data);

  int i;
  double d;

  iss >> i >> d;

  std::cout << "Integer: " << i << std::endl;
  std::cout << "Double: " << d << std::endl;

  return 0;
}
```

**输出结果：**

```
Integer: 10
Double: 20.5
```

#### 向字符串写入数据

下面是一个使用 `ostringstream` 将数据写入字符串的例子：

```C++
#include <iostream>
#include <sstream>

int main() {
  std::ostringstream oss;
  int i = 100;
  double d = 200.5;

  oss << i << " " << d;

  std::string result = oss.str();
  std::cout << "Resulting string: " << result << std::endl;

  return 0;
}
```

**输出结果：**

```
Resulting string: 100 200.5
```

#### 使用stringstream进行读写操作

下面是一个使用 `stringstream` 同时进行读取和写入操作的例子：

```C++
#include <iostream>
#include <sstream>

int main() {
  std::string data = "30 40.5";
  std::stringstream ss(data);

  int i;
  double d;

  // 从stringstream读取数据
  ss >> i >> d;

  std::cout << "Read Integer: " << i << ", Double: " << d << std::endl;

  // 向stringstream写入数据
  ss.str(""); // 清空stringstream
  ss << "New data: " << 50 << " " << 60.7;

  std::string newData = ss.str();
  //使用str()来实现字符串的写入。
  std::cout << "New data string: " << newData << std::endl;

  return 0;
}
```

**输出结果：**

```
Read Integer: 30, Double: 40.5
New data string: New data: 50 60.7
```

例：

```C++
#include <iostream>
#include <sstream>
#include <string>
using namespace std;
int main(){
    stringstream input;
    input<<"hello world";
    string a,b;
    input>>a>>b;
    cout<<a<<endl;
    cout<<b<<endl;
    return 0;
}
```

### 字符串中的`str()`

`std::stringstream` 类提供了一个名为 `str` 的成员函数，用于**获取和设置流中的字符串内容**。这个函数有两个重载版本，一个是无参的，用于获取流中的字符串内容；另一个是带一个 `std::string` 参数的，用于设置流中的字符串内容。

##### 获取字符串内容

当你调用无参的 `str` 函数时，它会返回一个 `std::string`，包含当前流中的所有内容。

```cpp
#include <iostream>
#include <sstream>

int main() {
    std::stringstream ss;
    ss << "Hello, " << "world!";
    std::string result = ss.str(); // 获取流中的内容
    std::cout << result << std::endl; // 输出: Hello, world!
    return 0;
}
```

##### 设置字符串内容

当你调用带 `std::string` 参数的 `str` 函数时，它会设置流的内容为指定的字符串，并清除之前的内容。

```cpp
#include <iostream>
#include <sstream>

int main() {
    std::stringstream ss;
    ss << "Initial content.";
    std::cout << "Before: " << ss.str() << std::endl; // 输出: Before: Initial content.

    ss.str("New content."); // 设置新的内容
    std::cout << "After: " << ss.str() << std::endl; // 输出: After: New content.
    return 0;
}
```

##### 结合使用 `str` 和 `clear` 方法

在重用 `std::stringstream` 对象时，通常会结合使用 `str` 方法和 `clear` 方法。`str` 方法用于设置新的字符串内容，而 `clear` 方法用于重置流的状态（例如，清除错误标志）。

```C++
#include <iostream>
#include <sstream>

int main() {
    std::stringstream ss;
    ss << "123 456";
    int a, b;
    ss >> a >> b;
    std::cout << "a: " << a << ", b: " << b << std::endl; // 输出: a: 123, b: 456

    ss.clear(); // 重置流的状态
    //重置流的状态意味着清除流的状态标志（如错误标志、结束标志等），但并不改变流的内部缓冲区的内容。换句话说，重置状态让流恢复到一个干净的状态，但原有数据仍然保留。
    ss.str(""); // 清空流的内容

    ss << "789 1011";
    ss >> a >> b;
    std::cout << "a: " << a << ", b: " << b << std::endl; // 输出: a: 789, b: 1011
    return 0;
}
```

##### 总结

- `str()`：无参版本用于获取流中的字符串内容。
- `str(const std::string &s)`：带参数版本用于设置流中的字符串内容，并清除之前的内容。

通过使用 `str` 方法，你可以方便地获取和设置 `std::stringstream` 对象中的字符串内容，从而实现灵活的字符串操作。

## Conclusion

- 基于控制台的输入输出
- 基于文件的输入输出
- 基于字符串的输入输出

> 这一块的知识确实是有一点冗杂的~学会cin/cout其实就可以解决大部分的输入输出问题了。建议本章节可以在实践中学习，效果更佳~

{% note info %}

**至此，笔者有关C++语法专题的所有内容全部更新完成啦！！！完结撒花！！！**

梳理一下大概更新了些啥：

- 面向对象编程——C++ Primer Plus专题
  - [OOP入门](https://xiyuanyang-code.github.io/posts/Introduction-to-OOP/)
  - [类和动态内存分配](https://xiyuanyang-code.github.io/posts/Dynamic-Memory-and-Classes/)
  - [类的继承](https://xiyuanyang-code.github.io/posts/Class-Inheritance/)
  - [代码重用和高阶技巧](https://xiyuanyang-code.github.io/posts/Code-Reuse-in-OOP/)
- [基于结构体的链表实现代码](https://xiyuanyang-code.github.io/posts/Linked-List-Implementation-Based-on-Structs/)
- [C++中的异常处理](https://xiyuanyang-code.github.io/posts/Exception-Handling-in-C-plus-plus/)
- [数组——指针——函数](https://xiyuanyang-code.github.io/posts/Pointers-Arrays-and-Functions/)
- [C++中的输入输出](https://xiyuanyang-code.github.io/posts/Input-and-Output-in-C-plus-plus/)

**后续，笔者将会把更新的重点放在数据结构和算法以及Python语法和专业基础课上，敬请期待~**

{% endnote %}

> THE END
