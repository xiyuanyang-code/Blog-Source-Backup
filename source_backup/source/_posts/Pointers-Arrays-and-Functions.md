---
title: Pointers-Arrays-and-Functions
date: 2024-12-14 23:45:20
index_img: /img/cover/pointers.jpg
excerpt: This article will primarily focus on pointers, delving deep into the essence of memory and, based on this foundation, exploring some advanced functionalities of pointers.
categories:
  - Code
  - Cpp
tags:
  - tutorial
  - Finished
  - pointers
  - functions
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# 函数、数组和指针

封面来源：[Pointers](https://knowyourmeme.com/photos/2214241-two-soyjaks-pointing)

> 对不起真的太好笑了哈哈哈哈哈哈

## Abstract

在C++中，函数，数组和指针是三个非常重要的概念，他们是C++实现许多高级功能（例如STL，OOP）的基石。同时，这三者之间又有着密不可分的联系。本文将主要从**指针**的角度出发，深入探究**内存**的本质，并在此基础之上探寻一些指针的高级功能。

> In C++, functions, arrays, and pointers are three essential concepts that serve as the cornerstone for implementing many advanced features, such as the Standard Template Library (STL) and Object-Oriented Programming (OOP). At the same time, these three elements are intricately interconnected. This article will primarily focus on **pointers**, delving deep into the essence of **memory** and, based on this foundation, exploring some advanced functionalities of pointers.

## Before the article begins

在文章开始前，请务必确保你已经掌握了**指针，数组，函数三者分别的基本用法和一些初步的内存管理的知识**。本文对相关基础代码不再过多赘述。

## Table of Contents

- Pointers and Memories
- Types of Pointers matters
- Array of an Pointer and Pointer of an array
- Rvalue
- parameters in the main function
- Lambda expression
- Smart Pointer

## Pointers and Memories

在这一节，我们将从内存的视角重新认识指针。

**内存是什么？**通俗来说，内存就是计算机**存储数据的地方**（这个定义非常的不严谨，甚至是一派胡言。但是这篇文章不会涉及硬件的太多知识，大家只要认识到这一步就行了）。第二个问题，**如何存储数据？**我们知道，数据存储的最小单元是**位（bit）**，代表一个二进制位。因此，我们会把内存进行**编码**，每一个bit都会拿到自己的编号，代表自己的具体位置。

例如：

```C++
#include <iostream>
using namespace std;
int main(){
    int a=100;
    int *p=&a;
    cout<<p;
    return 0;
}
//输出结果：0x61fe14(变量a的值，变量a的内存块第一个bit的编号，指针p所储存的值)
```

> 内存的硬件知识有兴趣的可以看这篇 [博客](https://blog.csdn.net/hellojoy/article/details/102933809)，讲的比较清楚。

接下来，回到指针。指针存储了一个**变量**的**地址（内存）**，这是指针的功能。但是，指针本身其实**也是一种变量**，在内存中有相对应的地址。一般来说，在64位的系统上，指针在内存中占**8个字节（64bit）**，在大小上和`unsigned long long`类型是一样的。（不过指针的值是十六进制的存储方式）

{% note success %}
**因此，指针也是一种数据类型！和int，double，char等等一样，在内存中占一定空间，并且储存对应的值。（只不过指针储存的值就是内存的编号罢了）**
{% endnote %}

## Types of pointers matters

指针有着**解引用**的操作，这是如何实现的？

我们来看下面的代码示范：

```C++
#include <iostream>
using namespace std;
int main(){
    unsigned long long num=4758843763784;
    unsigned long long* p1=&num;
    int* p2=(int*)p1;
    double* p3=(double*)p1;
    char* p4=(char*)p2;
    float* p5=(float*)p1;
    //对指针进行强制类型转换
    cout<<"the size of each pointers:"<<endl;
    cout<<sizeof(p1)<<" "<<sizeof(p2)<<" "<<sizeof(p3)<<" "<<sizeof(p4)<<" "<<sizeof(p5)<<endl;
    cout<<"The memory:"<<endl;
    cout<<p1<<" "<<p2<<" "<<p3<<" "<<(void*)p4<<" "<<p5<<endl;
    //强制类型转换字符指针，否则会输出实际字符的值。
    cout<<"Dereferencing Various Pointers"<<endl;
    cout<<*p1<<" "<<*p2<<" "<<*p3<<" "<<*p4<<" "<<*p5<<endl;
    return 0;
}
```

```
the size of each pointers:
8 8 8 8 8
The memory:
0x61fdf0 0x61fdf0 0x61fdf0 0x61fdf0 0x61fdf0
Dereferencing Various Pointers
4758843763784 19999816 2.35118e-311 H 3.25415e-38
```

这个代码告诉了我们以下几点：

- 指针在内存中存储的大小和**指针指向数据类型的大小无关**。（这一点很重要，稍后会解释）
- **指向不同数据类型的指针，对于同一块内存解引用所得到的是完全不同的值**！

换句话说，指针的类型不会改变指针在内存中的存储和指针的值，只会影响指针在**解引用时**对内存的解释（即如何将0/1的比特位转换成有效数据）。

因此，**Types of pointers matters！**实际上，如果不进行强制类型的转换，编译器面对不匹配的指针类型的时候也会报错。

以上两个章节是对指针和内存一些基础知识的回顾，接下来，我们将介绍**数组，指针和函数**三者在C++中一些高级的操作。

## Advanced Technique 1  Array of pointers and Pointer to an array

**Definition**

数组指针：数组指针是**指向数组的指针**。

指针数组：是一个数组，数组的每个元素都是**指针**。

定义还是非常直白的，**数组指针是指针，指针数组是数组。**

**Usage**

```cpp
int (*ptr)[size];
//声明一个数组指针
```

```cpp
int *ptr[size];
//声明一个指针数组
```

{% note warning %}

在 C++ 中，`*` 运算符用于声明指针，而 `[]` 运算符用于声明数组。因为**运算符的优先级不同**（`[]` 运算符的优先级高于`*` 运算符），直接写 `*ptr[size]` 会让编译器理解为声明一个数组 `ptr`，每个元素是一个指向 `type` 的指针，而不是声明一个指向数组的指针。

{% endnote %}

**使用方法**

如果是数组指针：

```cpp
#include <iostream>

int main() {
    int arr[3] = {1, 2, 3};
    int (*ptr)[3] = &arr;  // ptr 是指向包含 3 个整数的数组的指针

    // 访问数组元素
    std::cout << (*ptr)[0] << std::endl;  // 输出 1
    std::cout << (*ptr)[1] << std::endl;  // 输出 2
    std::cout << (*ptr)[2] << std::endl;  // 输出 3

    return 0;
}
```

在这个例子中，`ptr` 是指向一个包含 3 个整数的数组的指针。可以通过 `(*ptr)[i]` 的方式来访问数组元素。

如果是指针数组：

```cpp
#include <iostream>

int main() {
    int a = 1, b = 2, c = 3;
    int *ptr[3];  // ptr 是一个指针数组，包含 3 个指向整数的指针

    ptr[0] = &a;
    ptr[1] = &b;
    ptr[2] = &c;

    // 访问数组元素
    std::cout << *ptr[0] << std::endl;  // 输出 1
    std::cout << *ptr[1] << std::endl;  // 输出 2
    std::cout << *ptr[2] << std::endl;  // 输出 3

    return 0;
}
```

在这个例子中，`ptr` 是一个包含 3 个指针的数组，每个指针指向不同的整数变量。通过 `*ptr[i]` 可以访问指针所指向的值。

**Applications**

**数组指针和指针数组都可以用来操作二维数组**。

假设你有一个二维数组 `arr[3][4]`，它有 3 行 4 列，你可以通过以下两种方式来使用指针来访问它。

使用数组指针：

```C++
int (*ptr)[4] = arr;  // ptr 是指向数组中每一行的指针
```

在这种情况下，`ptr` 是指向数组中每一行的指针。你可以通过 `ptr[i]` 来访问第 `i` 行的数据。

使用指针数组：

```C++
int *ptr[3];  // ptr 是一个指针数组，包含 3 个指向整数的指针
ptr[0] = arr[0];  // ptr[0] 指向 arr 的第一行
ptr[1] = arr[1];  // ptr[1] 指向 arr 的第二行
ptr[2] = arr[2];  // ptr[2] 指向 arr 的第三行
```

在这种情况下，`ptr` 是一个数组，每个元素都是一个指向数组行的指针。你可以通过 `ptr[i][j]` 来访问数组中的元素。

{% note warning %}

**使用二级指针不可以直接指向一个二维数组！（因为类型并不匹配）**

```C++
int main(){
    int ** testptr=nullptr;
    int a[5][5]={0};
    testptr=a;  //INVALID!
    return 0;
}

/*
会报错：
testcode_1.cpp: In function 'int main()':
testcode_1.cpp:40:13: error: cannot convert 'int [5][5]' to 'int**' in assignment
     testptr=a;
*/
```

```C++
int main(){
    int ** testptr=nullptr;
    int (*testptr2)[5]=nullptr;
    int a[5][5]={0};
    testptr2=a;
    cout<<*testptr2<<" "<<**testptr2;
    return 0;
}
/*
程序可以正常运行：
输出结果：0x61fd90 0
解释：*testptr2代表数组指针的首元素的值，是一个指向int的指针，再解引用一次才得到a[0][0]的值。
*/
```

但是可以使用**动态内存分配**的方式，也可以让一个二级指针与二维数组相关联。

```C++
#include <iostream>

int main() {
    int rows = 3;
    int cols = 4;

    // 使用 new 动态分配二维数组
    int **arr = new int*[rows];  // 创建一个指针数组，每个元素指向一行
    for (int i = 0; i < rows; i++) {
        arr[i] = new int[cols];  // 每一行分配 cols 个 int
    }

    // 给二维数组赋值
    arr[0][0] = 1;
    arr[0][1] = 2;
    arr[0][2] = 3;
    arr[0][3] = 4;

    arr[1][0] = 5;
    arr[1][1] = 6;
    arr[1][2] = 7;
    arr[1][3] = 8;

    arr[2][0] = 9;
    arr[2][1] = 10;
    arr[2][2] = 11;
    arr[2][3] = 12;

    // 使用二级指针访问元素
    std::cout << arr[0][0] << std::endl;  // 输出 1
    std::cout << arr[1][2] << std::endl;  // 输出 7
    std::cout << arr[2][3] << std::endl;  // 输出 12

    // 释放内存
    for (int i = 0; i < rows; i++) {
        delete[] arr[i];  // 释放每行的内存
    }
    delete[] arr;  // 释放指针数组

    return 0;
}

```

{% endnote %}

## Advanced Technique 2 Rvalue

**左值（Lvalue）**：可以表示一个对象的内存位置，并且可以获取其地址。通常是变量、对象或数组元素。

**右值（Rvalue）**：通常是临时对象、字面值或表达式的结果。右值不能获取地址，代表的是一个不再需要的值。

例如在`x=y+z`这个表达式中，x,y,z都是变量（左值），在内存中有对应的空间，可以进行取址运算，但是编译器会先计算`y+z`的值（**这是一个表达式是右值**），将这个值赋给x后，y+z的结果就消失了。

C++3 中引用类型的变量只能是左值，除非声明的是 const 的引用，因而称之为左值引用。而C++11 引入了右值引用。右值引用以`&&`来表示，它的初值只能是一个将要被销毁的对象。例如：

```C++
int x = 10; 
int &&y = x + 9; 
int &&z = 8 * 9 % 4;
```

由于**右值引用只能绑定到临时对象**，即该对象将要被销毁，这意味着**右值引用的变量可以接管所引用的对象的资源**。定义 y 和 z 时都没有分配空间，而是接管了存储右边表达式计算结果的临时变量的空间。 y 接管了存放 x+9 结果值的临时变量的空间。 z 接管了存放 8 * 9 % 4 结果值的临时变量的空间。 

> 右值引用相当于给一个即将要消亡的值续了一口命让他一直存在。

通过右值引用，C++ 能够实现**移动语义**，即当对象的资源不再需要时，可以直接将资源“移动”到另一个对象中，而不是进行昂贵的拷贝操作。例如，`std::vector`和`std::string`等容器类会利用右值引用来避免不必要的内存分配和数据复制。

**移动语义的核心思想是避免不必要的拷贝**。例如，当我们将一个临时对象传递给容器时，容器可以通过移动构造而不是拷贝构造来获取该对象的资源。

```C++
#include <iostream>
#include <vector>
using namespace std;

class LargeObject {
public:
    int* data;
    
    LargeObject(int value) : data(new int(value)) {}
    ~LargeObject() { delete data; }
    
    // 移动构造函数
    LargeObject(LargeObject&& other) noexcept : data(other.data) {
        other.data = nullptr;
    }
    
    // 移动赋值运算符
    LargeObject& operator=(LargeObject&& other) noexcept {
        if (this != &other) {
            delete data;
            data = other.data;
            other.data = nullptr;
        }
        return *this;
    }
};

int main() {
    vector<LargeObject> vec;
    vec.push_back(LargeObject(42));  // 移动构造而非拷贝构造
    return 0;
}

```

> 简单来说，赋值构造函数是将对象的所有值拷贝一份赋给一个新的对象（无论是浅拷贝还是深拷贝），如果我只是希望实现值的移动，我还需要将原来的对象删除，这样的效率十分低下。有了移动语义（移动构造函数），我们便可以实现**不拷贝直接实现值的转移**。

{% note primary %}

### `const`引用和右值引用的区别

**什么是 `const` 引用？**

`const` 引用是一种引用类型，它允许**你通过引用来访问对象，但不允许修改对象的值**。通过 `const` 引用，你可以**避免复制对象，同时确保引用的对象不会被修改**。这通常用于传递大对象或需要确保数据不被修改的场合。

```cpp
const Type& ref = object;
```

这里的 `const Type&` 表示一个常量引用，`object` 可以是左值或右值。

> `const`引用经常出现在函数参数的传递过程中，即保证了**高效**，又保证了安全性。

```cpp
#include <iostream>

void printValue(const int& value) {
    std::cout << value << std::endl;  // 不能修改 value，因为它是 const 引用
}

int main() {
    int x = 10;
    printValue(x);  // 传递左值
    printValue(20); // 传递右值
    return 0;
}
```

**`const` 引用与右值引用的区别：**

1. **`const` 引用**：

- **允许绑定左值和右值**：`const` 引用可以**绑定到左值或右值。对于右值，它的作用是将其延长生命周期，保证在函数中可以安全地使用。**当我们希望通过引用传递一个右值并延长其生命周期时。通过 `const` 引用绑定到右值，可以避免不必要的拷贝，同时又不会修改该对象。

```cpp
#include <iostream>
#include <vector>

void processConstRef(const std::vector<int>& vec) {
    std::cout << "Size of vector (const ref): " << vec.size() << std::endl;
    // vec.push_back(4); // 错误！不能修改 const 引用绑定的对象
}

int main() {
    // 创建一个右值（临时对象）
    processConstRef(std::vector<int>{1, 2, 3});  // 这里传递的是一个右值

    return 0;
}
/*
输出：Size of vector (const ref): 3
*/
```

`std::vector<int>{1, 2, 3}` 是一个 **右值**，它是一个临时对象。我们通过 `const std::vector<int>&` 将右值传递给 `processConstRef` 函数，`const` 确保了我们在函数内部不能修改这个对象。在 `processConstRef` 中，`vec` 是一个 **`const` 引用**，它绑定到这个临时对象上，并延长了这个临时对象的生命周期。这样，直到 `processConstRef` 函数结束，右值才会被销毁。

> 如果删除const关键词，会有报错：`cannot bind non-const lvalue reference of type 'std::vector<int>&' to an rvalue of type 'std::vector<int>'`

2. **右值引用（`Type&&`）**：

- **只能绑定到右值**：右值引用是 C++11 引入的，它主要用于“移动语义”，只能绑定到右值（例如临时对象、函数返回值等）。
- **允许移动资源**：右值引用允许转移对象的资源，避免不必要的复制（例如通过 `std::move`），提高性能。
- **可以修改原对象**：通过右值引用，我们可以修改被移动的对象（例如，置为空）。

```cpp
#include <iostream>
#include <vector>
#include <utility>

void processConstRef(const std::vector<int>& vec) {
    std::cout << "Size of vector (const ref): " << vec.size() << std::endl;
    // vec.push_back(4); // 错误！不能修改 const 引用绑定的对象
}

void processRvalueRef(std::vector<int>&& vec) {
    std::cout << "Size of vector (rvalue ref): " << vec.size() << std::endl;
    vec.push_back(4);  // 可以修改 vec，因为它是右值引用
}

int main() {
    std::vector<int> vec1 = {1, 2, 3};

    // 使用 const 引用
    processConstRef(vec1);  // 传递左值

    // 使用右值引用
    processRvalueRef(std::move(vec1));  // 传递右值，vec1 资源被“移动”
    std::cout << "vec1 size after move: " << vec1.size() << std::endl;  // vec1 现在为空

    return 0;
}

/*
Size of vector (const ref): 3
Size of vector (rvalue ref): 3
vec1 size after move: 0
*/
```

1. **`const&` 引用**：`processConstRef` 函数使用了 `const` 引用，它可以接收左值或右值，但无法修改 `vec` 的内容。
2. **右值引用（`&&`）**：`processRvalueRef` 函数使用了右值引用，它只能接收右值，并且**可以修改 `vec`**（在本例中就是将数据“移动”到 `processRvalueRef`，并且通过 `push_back` 添加数据）。

**总结一下，const引用和右值引用都可以绑定到右值上，但是两者的具体功能是不一样的：const引用重点在`const`上，而右值引用主要在移动语义等方面提供了更高的效率（避免了不必要的复制过程）。**

{% endnote %}

## Advanced Technique 3 parameters in the main function

在我们之前写的C++程序中，main函数是**没有参数且返回值为0**的，程序能够正常退出。

```C++
#include <iostream>
using namespace std;

int main(){
    cout<<"Hello world"<<endl;
    return 1;
}
//如果更改为return 1,那么程序仍能运行，但是会显示异常退出。
```

**带参数的 `main` 函数**：它接受命令行参数，允许程序在启动时接收外部输入。这种形式在处理用户输入或命令行参数时非常有用。

```C++
int main(int argc, char* argv[]) {
    // 程序代码
    return 0;
}

```

1. **`argc`**：`argc` 是 **argument count**（参数计数）的缩写，表示传递给程序的命令行参数的个数。它的值至少为 1，因为程序名称本身也算作一个参数。
2. **`argv`**：`argv` 是 **argument vector**（参数向量）的缩写，它是一个指向字符指针数组的指针，数组的每个元素都是一个命令行参数（字符串）。`argv[0]` 通常是程序的名称，`argv[1]`、`argv[2]` 等依次是传递给程序的其他参数。

例如下面的程序：

```C++
#include <iostream>
using namespace std;

int main(int argc, char* argv[]) {
    cout << "程序名称: " << argv[0] << endl;
    cout << "传递的参数个数: " << argc - 1 << endl;  // 不算程序名称
    cout << "传递的参数: ";
    
    for (int i = 1; i < argc; ++i) {  // 从1开始，跳过程序名称
        cout << argv[i] << " ";
    }
    cout << endl;

    return 0;
}

```

- **`argv[0]`**：程序名称，通常是执行程序时给出的文件名（例如 `"./my_program"`）。
- **`argv[1]`、`argv[2]`**：这些是用户传递给程序的额外命令行参数。
- **`argc`**：命令行参数的个数。对于上述程序，`argc` 至少为 1，因为 `argv[0]` 是程序名称。

**编译与运行**：

假设程序的文件名为 `program.cpp`，你可以按以下方式编译和运行它：

1. 编译程序：

   ```Bash
   g++ program.cpp -o program
   ```

2. 运行程序，并传递一些命令行参数：

   ```bash
   ./program arg1 arg2 arg3
   ```

   输出将是：

   ```makefile
   程序名称: ./program
   传递的参数个数: 3
   传递的参数: arg1 arg2 arg3
   ```

> 在一些第三方库的使用中，带参数的main函数是非常强大的。

## Advanced Technique 4 Function Pointer

{% note info %}

**问题引入：函数在内存中有自己的地址？**

**函数在内存中是有地址的**。每个函数在程序加载到内存时，都会被分配一个唯一的内存地址，通常这个地址是指向函数代码开始位置的地址。在 C++ 中，我们可以通过**函数指针**来获取这个地址，从而间接地访问和调用函数。

```C++
#include <iostream>
using namespace std;

void myFunction() {
    cout << "Hello, this is myFunction!" << endl;
}

int main() {
    cout << (void*)myFunction << endl;  // 输出函数的地址
    return 0;
}

```

**函数名**和数组名类似，代表了函数在内存中的地址。

> 这里用到`(void*)`进行指针的强制类型转换，因为函数指针在cout中会被隐式转换成布尔值（即输出1）。

```C++
#include <iostream>
using namespace std;

void myFunction() {
    cout << "Hello, this is myFunction!" << endl;
}

int main() {
    // 获取函数的地址，并将其赋给一个函数指针
    void (*funcPtr)() = myFunction;

    // 通过函数指针调用函数
    funcPtr();  // 等同于 myFunction()
    
    // 输出函数的内存地址
    cout << "Function address: " << (void*)funcPtr << endl;
    return 0;
}
/*
输出：
Hello, this is myFunction!
Function address: 0x401550
*/
```

在上面的例子中，`funcPtr` 是一个指向 `myFunction` 函数的指针。`funcPtr` 存储了 `myFunction` 函数的内存地址。通过这个指针，我们可以调用函数。

- **函数名**本身在 C++ 中是一个**常量指针**，指向函数的内存地址。例如，`myFunction` 就代表了 `myFunction` 函数的地址。
- 通过 `(void*)funcPtr` 可以将函数指针转为 `void*` 类型，进而打印出函数的内存地址。

{% endnote %}

在 C++ 中，**函数指针**（Function Pointer）是一个指向函数的指针变量，类似于指向普通数据类型的指针。通过函数指针，我们可以动态地调用函数，而不需要直接调用它们的名称。函数指针在实现回调机制、事件驱动编程、以及某些设计模式（如策略模式）时非常有用。

1. **函数指针的声明**

函数指针的声明需要指定函数的返回类型、参数类型以及指针本身的类型。声明的形式与函数的声明很相似，关键在于指针的符号 `*` 和 `&` 的使用。

假设有一个返回 `int` 类型，参数为 `int` 和 `float` 的函数：

```cpp
int my_function(int, float);
```

那么，指向该函数的函数指针应该这样声明：

```cpp
int (*func_ptr)(int, float);
```

- `int (*func_ptr)(int, float)`：表示 `func_ptr` 是一个指向函数的指针，函数的返回类型是 `int`，参数类型是 `int` 和 `float`。
- `(*func_ptr)` 表示 `func_ptr` 是一个指针，指向的内容是一个函数。

2. **函数指针的初始化**

函数指针可以通过将它指向某个具体的函数来初始化。你可以将函数名赋给函数指针，因为函数名本身就是指向函数的指针。

```cpp
int my_function(int a, float b) {
    return a + b;
}

int main() {
    int (*func_ptr)(int, float);  // 函数指针声明
    func_ptr = my_function;       // 初始化指针，指向 my_function

    // 通过函数指针调用函数
    int result = func_ptr(5, 3.2); // 等价于 my_function(5, 3.2)
    std::cout << result << std::endl;  // 输出 8
    return 0;
}
```

在上面的代码中，`func_ptr` 是一个指向 `my_function` 函数的指针。通过 `func_ptr` 调用函数，功能与直接调用 `my_function` 一样。

3. **通过函数指针调用函数**

一旦函数指针被正确初始化，你可以使用该指针来调用函数。通过 `*` 解引用函数指针，然后传递参数来调用目标函数。

```cpp
int (*func_ptr)(int, float);  // 函数指针声明
func_ptr = my_function;       // 函数指针初始化

// 使用函数指针调用函数
int result = (*func_ptr)(10, 5.5);  // 等同于 my_function(10, 5.5)
std::cout << "Result: " << result << std::endl;
```

你也可以直接用 `func_ptr(10, 5.5)` 来调用函数，因为 `func_ptr` 本身就代表了一个函数。

4. **函数指针数组**

你可以创建一个指向多个函数的数组。这样的数组允许你根据索引动态选择不同的函数进行调用。

```cpp
#include <iostream>
using namespace std;

int add(int a, int b) { return a + b; }
int subtract(int a, int b) { return a - b; }
int multiply(int a, int b) { return a * b; }

int main() {
    // 创建函数指针数组，数组元素是指向函数的指针
    int (*operations[3])(int, int) = {add, subtract, multiply};

    // 动态调用不同的操作
    int x = 5, y = 3;
    cout << "Add: " << operations[0](x, y) << endl;        // add(5, 3)
    cout << "Subtract: " << operations[1](x, y) << endl;   // subtract(5, 3)
    cout << "Multiply: " << operations[2](x, y) << endl;   // multiply(5, 3)

    return 0;
}
```

在这个例子中，`operations` 是一个函数指针数组，其中每个元素都指向一个函数。你可以使用数组的索引来选择要调用的函数。

> 可以用于菜单选择。

{% note warning %}

注意，函数指针数组要求**数组中的每一个函数的返回值和特征表都要相同！**

- 例如，如果修改add函数为`int add(int a, double b) { return a + b; }`，会产生报错：`invalid conversion from 'int (*)(int, double)' to 'int (*)(int, int)' [-fpermissive]`
- 修改`int add(int a) { return a; }`，会产生报错：`invalid conversion from 'int (*)(int)' to 'int (*)(int, int)' [-fpermissive]`
- 修改`double add(int a ,int b) { return a+b; }`，会产生报错：`invalid conversion from 'double (*)(int, int)' to 'int (*)(int, int)' [-fpermissive]`
  - 你当然可以用指针的**强制类型转换**来实现这一点，但是这又会带来更大的隐患。

{% endnote %}

5. **作为函数参数的函数指针**

函数指针常常作为参数传递给其他函数，允许在运行时选择不同的函数进行调用。这是一种典型的**回调机制**。

示例：**回调函数**

```cpp
#include <iostream>
using namespace std;

// 定义一个函数类型
using FuncPtr = void(*)(int);  // 函数指针类型，指向接受 int 参数并返回 void 的函数

// 函数 1
void print_square(int x) {
    cout << "Square: " << x * x << endl;
}

// 函数 2
void print_cube(int x) {
    cout << "Cube: " << x * x * x << endl;
}

// 一个函数，它接受一个函数指针作为参数
void process_number(int num, FuncPtr callback) {
    callback(num);  // 调用回调函数
}

int main() {
    int num = 5;
    
    // 使用不同的回调函数
    process_number(num, print_square);  // 调用 print_square
    process_number(num, print_cube);    // 调用 print_cube
    
    return 0;
}
```

在这个例子中，`process_number` 函数接收一个函数指针 `callback`，它指向一个接受 `int` 参数并返回 `void` 的函数。你可以传递不同的函数来处理同一个数字，这样就实现了动态的行为。

举个实际的例子：**函数指针和冒泡排序**

```C++
#include <iostream>
#include <cstring>
using namespace std;
template <class T>
void sort(T a[],int size,bool(*f)(T,T)) {
    bool flag;
    int i,j;
    for(i=0;i<size;++i) {
        flag=false;
        for(j=0;j<size-i-1;++j) {
            if(f(a[j],a[j+1])) {
                T temp=a[j];
                a[j]=a[j+1];
                a[j+1]=temp;
                flag=true;
            }
        }
        if(!flag)break;
    }
}
bool increaseInt(int x,int y){return x>y;}
bool decreaseInt(int x,int y){return x<y;}
bool increaseString(const char* x,const char* y){return strcmp(x,y)>0;}
bool decreaseString(const char* x,const char* y){return strcmp(x,y)<0;}
int main() {
    int a[]={92,73,36,63,13};
    const char*b[]={"hsc","cbs","abx","bcj"};
    //cout<<strcmp(b[1],b[2]);
    sort<int>(a,5,increaseInt);
    for(int i=0;i<5;i++) cout<<a[i]<<"\t";
    cout<<endl;
    sort<const char*>(b,4,decreaseString);
    for(int i=0;i<4;i++) cout<<b[i]<<"\t";
    cout<<endl;
    return 0;
}

/*
输出结果:
13      36      63      73      92
hsc     cbs     bcj     abx
*/
```

`bool(*f)(T,T))`就是函数指针作为函数参数的用法（**回调函数**）。类似于`sort`函数的`compare`函数一样，在冒泡排序算法中，搭配不同的`bool(*f)(T,T))`可以实现自定义的排序顺序。

6. **函数指针和 `const` 修饰符**

如果你想确保函数指针指向的函数不被修改，可以使用 `const` 修饰符。

```cpp
int add(int a, int b) { return a + b; }
int subtract(int a, int b) { return a - b; }

int main() {
    // 定义一个常量函数指针，指向不修改函数的函数
    int (*const func_ptr)(int, int) = add;
    
    // func_ptr = subtract;  // 错误，不能改变指针的值
    int result = func_ptr(10, 5);  // 正确，调用 add
    cout << "Result: " << result << endl;
    
    return 0;
}
```

在这里，`func_ptr` 是一个常量函数指针，意味着它一旦指向 `add` 函数，就不能再改变指向其他函数。

## Advanced Technique 5 Lambda expression

C++ 中的 **Lambda 表达式** 是一种轻量级的匿名函数，它允许在代码中定义和使用一个函数对象，而不需要事先为其命名。Lambda 表达式使得 C++ 代码更加简洁和灵活，特别是在需要临时传递函数作为参数时非常有用。

C++11 引入了 Lambda 表达式，基本语法如下：

```cpp
[捕获列表](参数列表) -> 返回类型 { 函数体 }
```

其中各部分的含义如下：

- **捕获列表**（Capture list）：指定 Lambda 表达式访问外部变量的方式。捕获列表允许 Lambda 表达式“捕获”**外部作用域中的变量**，并可以在函数体内使用这些变量。
- **参数列表**（Parameter list）：指定 Lambda 表达式的输入参数，类似于普通函数的参数。
- **返回类型**（Return type）：指定 Lambda 表达式的返回类型，可以省略，编译器会根据返回值推导类型。
- **函数体**（Function body）：Lambda 表达式的实际代码，包含执行的语句。

1. **捕获列表（Capture List）**

捕获列表定义了 Lambda 表达式如何访问外部作用域的变量。捕获方式有以下几种：

- 按值捕获：捕获外部变量的副本

  ```cpp
  [x] { std::cout << x; }  // 捕获 x 的副本
  ```

- 按引用捕获：捕获外部变量的引用

  ```cpp
  [&x] { x = 10; }  // 捕获 x 的引用，允许修改 x
  ```

- 捕获所有变量按值：捕获所有外部变量的副本

  ```cpp
  [=] { std::cout << x << y; }  // 捕获所有外部变量的副本
  ```

- 捕获所有变量按引用：捕获所有外部变量的引用

  ```cpp
  [&] { x = 10; y = 20; }  // 捕获所有外部变量的引用
  ```

- 按值和引用混合捕获：

  ```cpp
  [=, &x] { x = 10; }  // 捕获所有外部变量按值，x 按引用
  ```

2. **参数列表**

Lambda 表达式的参数列表与普通函数类似，可以定义输入参数。如果没有参数，参数列表可以省略。

```cpp
[]() { std::cout << "Hello, World!" << std::endl; }
```

3. **返回类型**

**Lambda 表达式的返回类型通常可以省略**，编译器会根据函数体中的返回语句推导出返回类型。如果需要显式指定返回类型，可以使用 `->` 语法。

> 这怎么那么像python的语法？？？

```cpp
auto lambda = []() -> int { return 42; };
std::cout << lambda() << std::endl;  // 输出 42
```

4. **Lambda 表达式的实际例子**

- 按值捕获

```cpp
#include <iostream>
using namespace std;

int main() {
    int x = 5;
    auto lambda = [x]() { cout << "x = " << x << endl; };
    lambda();  // 输出: x = 5
    return 0;
}
```

这里的 Lambda 捕获了变量 `x` 的值，并输出它。

- 按引用捕获

```cpp
#include <iostream>
using namespace std;

int main() {
    int x = 5;
    auto lambda = [&x]() { x = 10; };  // 捕获 x 的引用
    lambda();
    cout << "x = " << x << endl;  // 输出: x = 10
    return 0;
}
```

这段代码通过按引用捕获，Lambda 表达式能够修改外部变量 `x`。

- 带参数的 Lambda

```cpp
#include <iostream>
using namespace std;

int main() {
    auto add = [](int a, int b) { return a + b; };
    cout << add(5, 3) << endl;  // 输出: 8
    return 0;
}
```

这是一个带有两个整型参数的 Lambda 表达式，用于计算两个数的和。

5. **Lambda 表达式的高级用法**

- Lambda **表达式作为参数**

Lambda 表达式常用于 STL 算法中作为参数，尤其是像 `std::sort` 这样的函数，它允许你提供自定义的排序规则。

```cpp
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main() {
    vector<int> vec = {4, 2, 5, 1, 3};
    
    // 使用 Lambda 表达式自定义排序规则
    sort(vec.begin(), vec.end(), [](int a, int b) { return a > b; });
    
    for (int val : vec) {
        cout << val << " ";  // 输出: 5 4 3 2 1
    }
    
    return 0;
}
```

在这个例子中，Lambda 表达式 `[ ]` 用于指定排序的比较规则。

- **使用 `mutable` 修改捕获变量**

默认情况下，捕获的变量在 Lambda 中是 **常量**，不能修改。如果你需要在 Lambda 内部修改捕获的变量，可以使用 `mutable` 关键字。

```cpp
#include <iostream>
using namespace std;

int main() {
    int x = 5;
    auto lambda = [x]() mutable { x = 10; cout << "x inside lambda: " << x << endl; };
    lambda();
    cout << "x outside lambda: " << x << endl;  // 输出: x outside lambda: 5
    return 0;
}
```

使用 `mutable` 后，捕获的变量 `x` 在 Lambda 内部变成可修改的副本，但是外部的 `x` 不会受到影响。

- **返回 Lambda 表达式**

**Lambda 表达式也可以返回 Lambda**。例如，返回一个根据输入参数变化的 Lambda：

```cpp
#include <iostream>
using namespace std;

auto createAdder(int x) {
    return [x](int y) { return x + y; };
}

int main() {
    auto add5 = createAdder(5);
    //在这里add5是一个函数指针，相当于createAdder的返回值，可以继续进行操作。
    cout << add5(10) << endl;  // 输出: 15
    return 0;
}
```

在这个例子中，`createAdder` 返回一个 Lambda 表达式，它会将传入的 `x` 与每次调用时传入的 `y` 相加。

6. **总结**

- **Lambda 表达式** 是 C++ 中一种轻量级的匿名函数，可以捕获外部变量并在局部上下文中使用。
- 捕获方式有按值捕获、按引用捕获、按值或按引用混合捕获等。
- Lambda 表达式可以带有参数和返回类型，可以通过 `mutable` 关键字修改捕获的外部变量。
- Lambda 表达式常用于 STL 算法、回调函数、事件处理等场景，提供了更高的灵活性和代码简洁性。

## Advanced Technique 6 Smart Pointer

在之前的学习过程中我们知道，使用`new`命令动态分配内存，需要手动`delete`来释放分配在**堆**的内存，否则会发生严重的内存泄漏，导致程序崩溃。在C++98中，**引入了`auto_ptr`，来自动实现内存回收的功能（所以被称作智能指针）。但是，在C++11的新标准中，`auto_ptr`被摒弃，取而代之的是三种新的智能指针：`unique_ptr`,`shared_ptr`,`weak_ptr`**。

### 	为什么需要智能指针

​	我们希望智能指针能够在程序终止时**自动回收为指针所分配的内存而非手动删除**，看下面的例子：

```C++
#include <iostream>
#include <cassert>
using namespace std;

void testfunction(string &str,int n){
    str="Hello world";
    string *pd=new string(str);
    assert(n==0);
    pd=&str;
    cout<<pd<<endl;
    cout<<*pd<<endl;
    cout<<&str<<endl<<str<<endl;
    delete pd;
    return;
}

int main(){
    string str;
    testfunction(str,0);
    return 0;
}
```

输出结果：

```
0x61fd20
Hello world
0x61fd20
Hello world
```

​	我们不难发现**手动删除内存是一件效率十分低下的事情！**，如果我没有正确的通过`assert`断言，我就不会执行`delete`语句实现内存的回收。因此，**我们希望pd指针有着更强大的功能——在程序终止时自动回收**。如何在类中实现对象的回收与销毁？**析构函数！**

> `auto_ptr`的类定义(确实是有析构函数的！)

```C++
 template<typename _Tp>
     class auto_ptr
     {
     private:
       _Tp* _M_ptr;
         //声明了一个私有成员变量 _M_ptr，它是一个指向模板类型 _Tp 的指针，用于存储 auto_ptr 管理的对象的地址
     public:
       /// The pointed-to type.
       typedef _Tp element_type;
       //定义了一个类型别名 element_type，它是 _Tp 的同义词，用于表示 auto_ptr 所管理的对象类型。
       explicit
       auto_ptr(element_type* __p = 0) throw() : _M_ptr(__p) { }
       /*
         这是 auto_ptr 的构造函数：
           explicit 关键字意味着这个构造函数不能用于隐式类型转换。
           构造函数接受一个指向 element_type 类型的指针  __p 作为参数，默认值为 0（即 NULL）。
           throw() 表示这个构造函数不会抛出异常（在C++98中，异常规范已弃用）。
           构造函数体通过初始化列表将传入的指针赋值给 _M_ptr
           
       */
       ~auto_ptr() { delete _M_ptr; }
         //当 auto_ptr 对象被销毁时，析构函数被调用。
         //它使用 delete 来释放 _M_ptr 所指向的内存，从而自动管理资源。
       //other members.............
     };
```

### 	`auto_ptr`

​	这就是`auto_ptr`的基本思想，**通过析构函数在指针结束其生命周期的时候自动释放内存**。

```C++
#include <memory>
using namespace std;
int main(){
    auto_ptr<double> pd(new double);
}
```

**智能指针和普通的动态内存分配在使用上还有哪些差异？**请看下面的两组程序的对比：在程序中实现了相同的`MyClass`类，包含数据成员`value`以及对应的构造函数，析构函数和`showMessage()`函数。

```Cpp

#include <iostream>
#include <memory>  // 引入 auto_ptr 需要的头文件

class MyClass {
    int value;
public:
    MyClass(int n):value(n){
        std::cout << "MyClass constructor" << std::endl;
    }

    ~MyClass() {
        std::cout << "MyClass destructor" << std::endl;
    }

    void showMessage() {
        std::cout << "Hello from MyClass!" << std::endl;
        std::cout<<"The value is"<<value<<std::endl;
    }
};
void demonotAutoPtr() {
    MyClass* ptr1=new MyClass(4);
    ptr1->showMessage();
    MyClass *ptr2;
    ptr2=ptr1;
    ptr2->showMessage();
    ptr1->showMessage();
    delete ptr1;
}

int main() {
    demonotAutoPtr();
    return 0;
}
/*
MyClass constructor
Hello from MyClass!
The value is4
Hello from MyClass!
The value is4
Hello from MyClass!
The value is4
MyClass destructor
(这里程序正常运行并且正常退出)
*/
```

```cpp

#include <iostream>
#include <memory>  // 引入 auto_ptr 需要的头文件

class MyClass {
    int value;
public:
    MyClass(int n):value(n){
        std::cout << "MyClass constructor" << std::endl;
    }

    ~MyClass() {
        std::cout << "MyClass destructor" << std::endl;
    }

    void showMessage() {
        std::cout << "Hello from MyClass!" << std::endl;
        std::cout<<"The value is"<<value<<std::endl;
    }
};

void demoAutoPtr() {
    // 创建一个 auto_ptr，并初始化为指向动态分配的 MyClass 对象
    std::auto_ptr<MyClass> ptr1(new MyClass(4));
    ptr1->showMessage();  // 使用 auto_ptr 指向的对象

    // 将 ptr1 转移给 ptr2，这样 ptr1 变为空，ptr2 拥有 MyClass 的所有权
    std::auto_ptr<MyClass> ptr2 = ptr1;
    // 此时 ptr1 不再持有对象，只有 ptr2 拥有 MyClass 对象
    ptr2->showMessage();
    ptr1->showMessage();
    // ptr2 会在超出作用域时自动销毁，并调用 MyClass 的析构函数
}

int main() {
    demoAutoPtr();
    return 0;
}

/*
MyClass constructor
Hello from MyClass!
The value is 4
Hello from MyClass!
The value is 4
Hello from MyClass!
The value is
(最后程序会异常退出)
*/
```

通过对比可以发现，两者唯一的不同是在函数`demoAutoPtr()`中一个使用**动态内存**，另一个使用**智能指针**。两者实现的操作都是一样的，创建`ptr1`后将`ptr1`的值转移给`ptr2`。但是程序在最后调用`ptr1->showMessage();`这条语句的时候出现了差异：**基于动态内存实现的指针保留的原来的value值，但是基于智能指针实现的指针在此处value值丢失了，导致程序的异常终止。**

在 `auto_ptr` 的赋值操作中，资源的所有权被转移给了目标 `auto_ptr`，而源 `auto_ptr` 被置为空。具体来说，这种行为是通过以下方式实现的：

1. 当你将一个 `auto_ptr` 赋值给另一个 `auto_ptr` 时，源对象的指针会被转移到目标对象，而源对象的指针则被置为 `nullptr`。这种行为是通过“**转移所有权**”来避免多次删除相同的资源。
2. 这种转移的目的是确保动态分配的内存在程序结束时被释放，但是如果没有进行所有权转移，可能会发生**双重删除**的错误。

因此，我们可以发现，在智能指针的使用上，仅仅依靠`auto_ptr`似乎是不够的：上文的**转移所有权**的特性确实能够避免内存泄漏的问题出现，但是我们有时也需要进行**指针拷贝**的操作。实际上，在C++的新标准中，**`auto_ptr`已经被弃用**，主要有以下原因：

1. **不安全的转移语义**： `auto_ptr` 在赋值或传递给另一个 `auto_ptr` 时，进行的是“**浅拷贝**”，即将指针转移到另一个 `auto_ptr`，并将原来的 `auto_ptr` 设置为 `nullptr`。这种行为会导致潜在的资源管理问题，尤其是多个 `auto_ptr` 管理同一内存时，容易发生重复释放内存。

   ```cpp
   auto_ptr<int> p1(new int(10));
   auto_ptr<int> p2 = p1;  // 现在 p1 为空，p2 拥有该内存
   std::cout << *p2 << std::endl;  // p2 仍然有效，但 p1 不再持有该内存
   ```

2. **不一致的所有权语义**： `auto_ptr` 的所有权语义比较模糊。它使得传递和返回指针时，所有权的转移比较不直观，容易造成内存管理的混乱。

3. **不支持移动语义**： `auto_ptr` 不支持现代 C++ 中引入的“移动语义”。C++11 引入了 `std::move` 和 `std::unique_ptr`，使得可以安全高效地转移所有权，而无需担心意外的复制和删除。

### other smart pointers

现代 C++ 中有**三种常见的智能指针**，它们分别是：

1. **`std::unique_ptr`**：用于表示**独占所有权**。每个 `unique_ptr` 只能有一个所有者，当它超出作用域时会自动释放资源。
2. **`std::shared_ptr`**：用于表示**共享所有权**。多个 `shared_ptr` 可以共享同一块资源，当所有指向该资源的 `shared_ptr` 都被销毁时，资源才会被释放。
3. **`std::weak_ptr`**：用于表示对某个资源的“非拥有”引用。它不会影响资源的生命周期，但可以用来观察 `shared_ptr` 所管理的对象，避免循环引用。

> 智能指针的关键在于对**内存地址的所有权**进行自动管理。

#### 1. **`std::unique_ptr`**

`std::unique_ptr` 是 C++11 引入的，适用于确保资源的独占所有权。**一个 `unique_ptr` 不能被复制，只能被移动**。

{% note info %}

**就相当于原来的`auto_ptr`一样。**

{% endnote %}

```cpp
#include <iostream>
#include <memory>

class MyClass {
public:
    MyClass() { std::cout << "MyClass constructor" << std::endl; }
    ~MyClass() { std::cout << "MyClass destructor" << std::endl; }
    void showMessage() { std::cout << "Hello from MyClass!" << std::endl; }
};

void demoUniquePtr() {
    // 创建一个 unique_ptr，指向 MyClass 对象
    std::unique_ptr<MyClass> ptr1 = std::make_unique<MyClass>();
    ptr1->showMessage();

    // 转移所有权
    std::unique_ptr<MyClass> ptr2 = std::move(ptr1);  // 这里发生了所有权的转移
    if (!ptr1) {
        std::cout << "ptr1 is now empty (nullptr)" << std::endl;
    }
    ptr2->showMessage();
}

int main() {
    demoUniquePtr();
    return 0;
}
```

```
MyClass constructor
Hello from MyClass!
ptr1 is now empty (nullptr)
Hello from MyClass!
MyClass destructor
```

#### 2. **`std::shared_ptr`**

`std::shared_ptr` 是 C++11 引入的，用于表示共享所有权。**多个 `shared_ptr` 可以同时管理一个资源**，资源只有在所有 `shared_ptr` 被销毁时才会释放。

```cpp
#include <iostream>
#include <memory>

class MyClass {
    int value;
public:
    MyClass(int n):value(n) { std::cout << "MyClass constructor" << std::endl; }
    ~MyClass() { std::cout << "MyClass destructor" << std::endl; }
    void showMessage() { std::cout << "Hello from MyClass!" << std::endl; }
    void showValue(){ std::cout<<"the value: "<<value<<std::endl;}
    void modifyValue(int n){ value=n;}
};

void demoSharedPtr() {
    std::shared_ptr<MyClass> ptr1 = std::make_shared<MyClass>(4);
    ptr1->showMessage();

    // 创建第二个 shared_ptr，指向同一资源
    std::shared_ptr<MyClass> ptr2 = ptr1;
    std::cout << "Use count: " << ptr1.use_count() << std::endl;  // 输出共享计数

    ptr1->showValue();
    ptr2->showValue();
    
    ptr1->modifyValue(3);
    ptr2->showValue();

    ptr2->modifyValue(2);
    ptr1->showValue();
    // ptr2 和 ptr1 都指向同一对象，资源不会被释放直到两个都超出作用域
}

int main() {
    demoSharedPtr();
    return 0;
}
```

```powershell
MyClass constructor
Hello from MyClass!
Use count: 2
the value: 4
the value: 4
the value: 3
the value: 2
MyClass destructor
```

#### 3. **`std::weak_ptr`**

`std::weak_ptr` 是 C++11 引入的，通常与 `shared_ptr` 配合使用，用来观察由 `shared_ptr` 管理的对象。`weak_ptr` 不增加对象的引用计数，因此**它不会影响对象的生命周期**。

> 在某些场景下，`shared_ptr` 之间可能会相互引用，导致引用计数永远不为零，从而导致内存泄漏。这时可以使用 `weak_ptr` 来避免这种循环引用的问题。

想象你有一个 `shared_ptr` 管理的对象 `A`，同时，你有另一个对象 `B`，它想引用 `A`，但不想干扰 `A` 的生命周期。你就可以用 `weak_ptr` 来观察 `A` 是否存在。

```cpp
#include <iostream>
#include <memory>  // 引入 shared_ptr 和 weak_ptr

class MyClass {
public:
    MyClass() { std::cout << "MyClass constructor" << std::endl; }
    ~MyClass() { std::cout << "MyClass destructor" << std::endl; }
    void showMessage() { std::cout << "Hello from MyClass!" << std::endl; }
};

void demoWeakPtr() {
    // 创建一个 shared_ptr，指向 MyClass 对象
    std::shared_ptr<MyClass> ptr1 = std::make_shared<MyClass>();
    std::cout << "Use count before weak_ptr: " << ptr1.use_count() << std::endl;

    // 创建一个 weak_ptr，指向同一个对象，但不增加引用计数
    std::weak_ptr<MyClass> weakPtr = ptr1;

    // 使用 weak_ptr 时，我们需要“锁定”它，检查它是否仍然有效
    if (auto sharedPtr = weakPtr.lock()) {  // lock() 方法返回一个 shared_ptr，如果对象仍然存在
        sharedPtr->showMessage();  // 如果 weak_ptr 指向的对象仍然存在，就可以使用它
    } else {
        std::cout << "Object is no longer available" << std::endl;
    }

    // 释放 ptr1，MyClass 对象的引用计数变为 0，对象会被销毁
    ptr1.reset();  // 释放 ptr1 指向的对象
    std::cout << "Use count after reset: " << weakPtr.use_count() << std::endl;

    // 尝试再次锁定 weak_ptr
    if (auto sharedPtr = weakPtr.lock()) {
        sharedPtr->showMessage();
    } else {
        std::cout << "Object is no longer available" << std::endl;  // 此时对象已经被销毁
    }
}

int main() {
    demoWeakPtr();
    return 0;
}
```

```
MyClass constructor
Use count before weak_ptr: 1
Hello from MyClass!
Use count after reset: 0
Object is no longer available
MyClass destructor
```

#### 总结

- **`std::unique_ptr`**：表示独占所有权，只能转移所有权，不支持拷贝。常用于资源的独占管理。
- **`std::shared_ptr`**：表示共享所有权，多个 `shared_ptr` 可以共同拥有一个资源，直到最后一个 `shared_ptr` 被销毁，资源才会被释放。
- **`std::weak_ptr`**：不增加引用计数，仅用于观察由 `shared_ptr` 管理的对象，防止出现循环引用的问题。



