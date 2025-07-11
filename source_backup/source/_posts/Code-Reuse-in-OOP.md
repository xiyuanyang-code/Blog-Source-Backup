---
title: Code-Reuse-in-OOP
date: 2024-12-12 10:13:03
index_img: /img/cover/Stack.jpg
excerpt: In this article, you will learn several efficient ways to implement code reuse in C++, including Containment, composition, and Class templates. This chapter also involves several advanced techniques of using OOP. 
categories:
  - Code
  - Cpp
tags:
  - tutorial
  - Finished
  - OOP
  - C++ Primer Plus
  - Inheritance
  - Code Reuse
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Code Reuse in OOP

# C++ Primer Plus Tutorial-15

# 面向对象编程教程——Section④

<center><p style="color: red;"><b><font size=6.5>Chapter 15 Code Reuse in OOP</font></b></p></center>

<center><p style="color: red;"><b><font size=6.5>面向对象编程中的代码重用</font></b></p></center>

【写在前面的话】

[C++ Primer Plus Tutorial](https://xiyuanyang-code.github.io/posts/C-plus-plus-Primer-Plus-tutorial/)

[系列文章](https://xiyuanyang-code.github.io/tags/OOP/)

## Abstract

C++的一个主要目标是促进代码重用。公有继承是实现这种目标的机制之一，但并不是唯一的机制。本章将介绍其他方法，其中之一是使用这样的类成员：本身是另一个类的对象。这种方法称为**包含（containment）、组合（composition）或层次化（layering）**。另一种方法是使用私有或保护继承。通常，**包含、私有继承和保护继承**用于实现 **has-a** 关系，即新的类将包含另一个类的对象。多重继承使得能够使用两个或更多的基类派生出新的类，将基类的功能组合在一起。

同时，本章将介绍**类模板**——另一种重用代码的方法。类模板使我们能够使用通用术语定义类，然后使用模板来创建针对特定类型定义的特殊类。

> One of the main goals of C++ is to promote code reuse. Public inheritance is one of the mechanisms to achieve this goal, but not the only one. This chapter will introduce other methods, one of which is to use class members that are themselves objects of another class. This method is called containment, composition, or layering. Another method is to use private or protected inheritance. Generally, containment, private inheritance, and protected inheritance are used to implement the has - a relationship, that is, the new class will contain an object of another class. Multiple inheritance enables new classes to be derived from two or more base classes, combining the functionality of the base classes.
>
> At the same time, this chapter will introduce class templates - another method of reusing code. Class templates enable us to define classes in general terms and then use the templates to create special classes defined for specific types.

## Table of Contents

- Containment Composition Layering
- Multiple Inheritance
- Class Template
- Friend Class and Friend Functions

## Containment Composition Layering

### `valarray`

`valarray`被定义为一个模板类，能够处理不同的数据类型。

下面来看`valarray`的基本操作：

```C++
#include <iostream>
#include <valarray>
using namespace std;
int main(){
    valarray<int> list(10,8);
    for(auto num:list){
        cout<<num<<endl;
    }
    return 0;
}
```

输出结果：8个10

`valarray`的基本使用和数组完全一样，但是`valarray`在数值计算方面具有更大的优势。

{% note info %}

1. 构造函数
   - **默认构造函数**：可以创建一个空的`valarray`。例如`std::valarray<int> va;`，这样就创建了一个空的`valarray`，其中元素类型为`int`。
   - **大小指定构造函数**：可以指定`valarray`的大小和初始值。例如`std::valarray<double> va1(10, 3.14);`，这个构造函数创建了一个包含 10 个元素的`valarray`，每个元素初始值为 3.14。
   - **拷贝构造函数**：用于从另一个`valarray`创建一个新的`valarray`。例如`std::valarray<int> va2(va1);`，这里`va2`是`va1`的一个拷贝，它们具有相同的元素个数和对应位置相同的元素。
   - **初始化列表构造函数**：可以使用花括号初始化列表来创建`valarray`。例如`std::valarray<int> va3 = {1, 2, 3, 4, 5};`，这种方式方便快捷地创建并初始化一个`valarray`。
2. 元素访问方法
   - **`[]`操作符**：用于访问`valarray`中的单个元素。例如：

```cpp
     std::valarray<int> va = {1, 2, 3, 4, 5};
     int element = va[2];  // 访问第三个元素，element的值为3
```

- **`size()`方法**：返回`valarray`中元素的个数。例如：

```cpp
     std::valarray<double> va1(10, 3.14);
     std::cout << va1.size() << std::endl;  // 输出10
```

1. 数学运算方法
   - 算术运算符重载
     - **加法**：可以将两个`valarray`相加，或者将一个`valarray`与一个标量相加。例如：

```cpp
       std::valarray<int> va1 = {1, 2, 3};
       std::valarray<int> va2 = {4, 5, 6};
       std::valarray<int> result1 = va1 + va2;  // 对应元素相加，result1为{5, 7, 9}
       std::valarray<int> result2 = va1 + 2;    // 每个元素加2，result2为{3, 4, 5}
```

- 减法、乘法、除法等运算与之类似
  - 减法：`std::valarray<int> result3 = va2 - va1;`（对应元素相减）
  - 乘法：`std::valarray<int> result4 = va1 * 3;`（每个元素乘以 3）
  - 除法：`std::valarray<double> va3 = {4.0, 8.0, 12.0};`，`std::valarray<double> result5 = va3 / 2.0;`（每个元素除以 2.0）
- 数学函数应用
  - **`std::pow`函数**：用于计算`valarray`中每个元素的幂。例如

```cpp
       std::valarray<int> va = {2, 3, 4};
       std::valarray<int> result = std::pow(va, 2);  // 计算每个元素的平方，result为{4, 9, 16}
```

- `std::sin`、`std::cos`等三角函数也可以应用于`valarray`元素
  - 例如`std::valarray<double> va1 = {0.0, 3.14159 / 2.0, 3.14159};`，`std::valarray<double> sin_result = std::sin(va1);`，会分别计算`va1`中每个元素的正弦值。

1. 切片操作（`slice`）相关方法
   - **`valarray`支持切片操作**：可以通过定义`slice`对象来提取`valarray`中的一部分元素。例如：

```cpp
     std::valarray<int> va = {1, 2, 3, 4, 5, 6, 7, 8, 9, 10};
     std::slice s(2, 4, 2);  // 从索引2开始，长度为4，步长为2的切片
     std::valarray<int> sliced_va = va[s];  // sliced_va为{3, 5, 7, 9}
```

- **`cshift`方法（循环移位）**：可以对`valarray`中的元素进行循环移位操作。例如：

```cpp
     std::valarray<int> va = {1, 2, 3, 4, 5};
     std::valarray<int> shifted_va = va.cshift(2);  // 元素向右循环移2位，shifted_va为{4, 5, 1, 2, 3}
```

{% endnote %}

### 如何操作`has-a`关系？

公有继承比较适合的是`is-a-king-of`关系的建立，即构建一种**种类上子集**的关系。那如果是是一种**has-a**关系呢？举个简单的例子，我们现在又**string**类（姓名）和**valarry**类（分数）两个底层的基类，我想从这两个基类派生出我的**student类**（每个学生都有自己的姓名和分数）。

如果使用**公有继承**（在这里是多重公有继承），显然不合适。因为学生和姓名不属于同类事物。通常，我们使用**包含（containment）**的技术来构建一种**has-a**关系，即**创建一个包含其他类对象的类**。

下文给出`student`类的代码示例：

```C++
// studentc.h -- defining a Student class using containment
#ifndef STUDENTC_H_
#define STUDENTC_H_

#include <iostream>
#include <string>   
#include <valarray>
class Student
{   
private:
    typedef std::valarray<double> ArrayDb;
    std::string name;       // contained object
    ArrayDb scores;         // contained object
    // private method for scores output
    std::ostream & arr_out(std::ostream & os) const;
public:
    Student() : name("Null Student"), scores() {}
    explicit Student(const std::string & s)
        : name(s), scores() {}
    explicit Student(int n) : name("Nully"), scores(n) {}
    Student(const std::string & s, int n)
        : name(s), scores(n) {}
    Student(const std::string & s, const ArrayDb & a)
        : name(s), scores(a) {}
    Student(const char * str, const double * pd, int n)
        : name(str), scores(pd, n) {}
    ~Student() {}
    double Average() const;
    const std::string & Name() const;
    double & operator[](int i);
    double operator[](int i) const;
// friends
    // input
    friend std::istream & operator>>(std::istream & is,
                                     Student & stu);  // 1 word
    friend std::istream & getline(std::istream & is,
                                  Student & stu);     // 1 line
    // output
    friend std::ostream & operator<<(std::ostream & os,
                                     const Student & stu);
};

#endif

```

> 复习一下：`explicit`关键词可以避免编译器进行隐式类型转换。
>
> 在这种情况下，Student类的成员函可以使用string类的共用接口来修改和访问name对象，但是在类的外部只能通过Student类的成员函数对私有数据进行访问。
>
> 换句话说，在has-a关系的继承中，**类对象不能自动获得被包含对象的接口，而应该通过定义成员函数来实现。**举个例子，在string类中重载了加法运算符用于字符串的拼接，但是student类继承这种方法是没有任何意义的（你不可以把两个学生拼在一起）。

```C++
// studentc.cpp -- Student class using containment
#include "studentc.h"
using std::ostream;
using std::endl;
using std::istream;
using std::string;

//public methods
double Student::Average() const
{
    if (scores.size() > 0)
        return scores.sum()/scores.size();  
    else
        return 0;
}

const string & Student::Name() const
{
    return name;
}

double & Student::operator[](int i)
{
    return scores[i];         // use valarray<double>::operator[]()
}

double Student::operator[](int i) const
{
    return scores[i];
}

// private method
ostream & Student::arr_out(ostream & os) const
{
    int i;
    int lim = scores.size();
    if (lim > 0)
    {
        for (i = 0; i < lim; i++)
        {
            os << scores[i] << " ";
            if (i % 5 == 4)
                os << endl;
        }
        if (i % 5 != 0)
            os << endl;
    }
    else
        os << " empty array ";
    return os; 
}

// friends

// use string version of operator>>()
istream & operator>>(istream & is, Student & stu)
{
    is >> stu.name;
    return is; 
}

// use string friend getline(ostream &, const string &)
istream & getline(istream & is, Student & stu)
{
    getline(is, stu.name);
    return is;
}

// use string version of operator<<()
ostream & operator<<(ostream & os, const Student & stu)
{
    os << "Scores for " << stu.name << ":\n";
    stu.arr_out(os);  // use private method for scores
    return os;
}

```

### 私有继承与保护继承（Additional）

在C++中，**私有继承**也是实现**has-a**关系的重要实现方式之一，在这里不再展开涉及具体的细节（毕竟第一种方法已经够用了，不是吗），只给出头文件的语法规范示例：

```C++
// studenti.h -- defining a Student class using private inheritance
#ifndef STUDENTC_H_
#define STUDENTC_H_

#include <iostream>
#include <valarray>
#include <string>   
class Student : private std::string, private std::valarray<double>
{   
private:
    typedef std::valarray<double> ArrayDb;
    // private method for scores output
    std::ostream & arr_out(std::ostream & os) const;
public:
    Student() : std::string("Null Student"), ArrayDb() {}
    explicit Student(const std::string & s)
            : std::string(s), ArrayDb() {}
    explicit Student(int n) : std::string("Nully"), ArrayDb(n) {}
    Student(const std::string & s, int n)
            : std::string(s), ArrayDb(n) {}
    Student(const std::string & s, const ArrayDb & a)
            : std::string(s), ArrayDb(a) {}
    Student(const char * str, const double * pd, int n)
            : std::string(str), ArrayDb(pd, n) {}
    ~Student() {}
    double Average() const;
    double & operator[](int i);
    double operator[](int i) const;
    const std::string & Name() const;
// friends
    // input
    friend std::istream & operator>>(std::istream & is,
                                     Student & stu);  // 1 word
    friend std::istream & getline(std::istream & is,
                                  Student & stu);     // 1 line
    // output
    friend std::ostream & operator<<(std::ostream & os,
                                     const Student & stu);
};

#endif

```

## Multiple Inheritance

从单继承，我们很容易的推理出多重继承（Multiple Inheritance），简称MI。

![Multiple Inheritance](MI.png)

{% note danger %}

**注意！！！多重继承在一定程度上大大提高了继承的复杂性！一定要慎用！（笔者建议能使用单继承就使用单继承）**

{% endnote %}

{% note info %}

### 小故事：钻石问题

钻石问题（Diamond Problem）是多重继承中的一个经典问题，特别是在面向对象编程语言中。这个问题发生在一个类继承自两个或更多有共同父类的类时，导致继承结构中产生二义性和不明确的继承路径。它的名字来源于继承关系图形的形状，通常呈现一个钻石形状。

**钻石问题的例子**：

假设有一个类 `A`，它是两个类 `B` 和 `C` 的父类，而 `B` 和 `C` 又被一个类 `D` 继承。继承关系可以表示为一个钻石形状：

```Css
      A
     / \
    B   C
     \ /
      D
```

**问题的具体情况**：

在这个继承关系中，类 `B` 和类 `C` 都继承了类 `A`，并且类 `D` 同时继承了 `B` 和 `C`。假设类 `A` 中有一个成员函数 `foo()`，而类 `B` 和类 `C` 都没有覆盖（override）该函数。那么，类 `D` 在继承时会遇到以下问题：

- **二义性**：类 `D` 继承了 `B` 和 `C`，它同时会继承来自 `A` 的 `foo()` 函数。但是，当你尝试调用 `foo()` 时，编译器无法确定应该调用 `B` 中的 `foo()`，还是 `C` 中的 `foo()`（假设 `B` 和 `C` 都没有覆盖这个函数）。如果 `B` 和 `C` 都没有 `foo()`，那么 `D` 也会得到二义性错误。
- **重复继承**：由于 `B` 和 `C` 都继承自 `A`，在类 `D` 中可能会包含 `A` 的多个副本，导致同一个基类成员被多次继承，这种冗余的继承可能会导致不必要的资源浪费和代码混乱。

> 实际上，C++ 引入了 **虚拟继承（Virtual Inheritance）** 来解决钻石问题。虚拟继承可以确保在多重继承时，`A` 类的子对象只会被继承一次。

{% endnote %}

## Class Template

面向对象的程序设计提供了一种称为**泛型程序设计**的机制，即允许将类中成员的类型设置为一个可变的参数，使多个类变成一个类。泛型程序设计可以以独立于任何特定类型的方式编写代码。使用泛型程序时，必须提供具体所操作的类型或值。第 5 章介绍的**函数模板**就是泛型机制的一种实现方法，本章将介绍**类模板**，即**用泛型机制设计的类**。  

在函数章节，我们学习了模版函数，实现了一个模版在多个不同数据类型之间的通用，大大提高了代码重用的效率。在类的定义中同样也是如此，模板提供**参数化（parameterized）类型**，即能够将类型名作为参数传递给接收方来建立类或函数。例如，将类型名 `int` 传递给 `Queue` 模板，可以让编译器构造一个对 int 进行排队的 `Queue` 类。  

在接下来的内容中，我们将以非常重要的数据结构——**栈（Stack）**为示例，向读者演示模板类的强大之处。

### The Implementation of Stack

```C++
// stack.h -- class definition for the stack ADT
#ifndef STACK_H_
#define STACK_H_

typedef unsigned long Item;

class Stack
{
private:
    enum {MAX = 10};    // constant specific to class
    Item items[MAX];    // holds stack items
    int top;            // index for top stack item
public:
    Stack();
    bool isempty() const;
    bool isfull() const;
    // push() returns false if stack already is full, true otherwise
    bool push(const Item & item);   // add item to stack
    // pop() returns false if stack already is empty, true otherwise
    bool pop(Item & item);          // pop top into item
};
#endif


// stack.cpp -- Stack member functions
#include "stack.h"
Stack::Stack()    // create an empty stack
{
    top = 0;
}

bool Stack::isempty() const
{
    return top == 0;
}

bool Stack::isfull() const
{
    return top == MAX;
}

bool Stack::push(const Item & item) 
{
    if (top < MAX)
    {
        items[top++] = item;
        return true;
    }
    else
        return false;
}

bool Stack::pop(Item & item)
{
    if (top > 0)
    {
        item = items[--top];
        return true;
    }
    else
        return false; 
}

```

> 在第十章书中给出了针对`unsigned long`类型的栈数据结构示例，此处不再解释具体代码的含义。

### The Definition

我们先给出模板类的头文件声明，读者可以自行比较有哪边进行了修改：

```C++
template <class Type>
class Stack
{
private:
    enum {SIZE = 10};    // default size
    int stacksize;
    Type * items;       // holds stack items
    //在这里优化为了指针，更加的高效（相比使用数组存储）
    int top;            // index for top stack item
public:
    explicit Stack(int ss = SIZE);
    Stack(const Stack & st);
    ~Stack() { delete [] items; }
    bool isempty() { return top == 0; }
    bool isfull() { return top == stacksize; }
    bool push(const Type & item);   // add item to stack
    bool pop(Type & item);          // pop top into item
    Stack & operator=(const Stack & st);
};
```

采用模板时，将使用模板定义替换 `Stack` 声明，使用模板成员函数替换 `Stack` 的成员函数。和模板函数一样，模板类以下面这样的代码开头 :`template <class Type>`。

剩下的操作基本上没什么区别了。（因为定义类在很大程度上就是定义成员函数和方法。那定义模版类就是定义模版函数的过程）

接下来我们来看类方法的定义：

```C++
template <class Type>
    //这一行必不可少（相当于定义模版函数的过程）
Stack<Type>::Stack(int ss) : stacksize(ss), top(0)
{
    items = new Type [stacksize];
}

template <class Type>
Stack<Type>::Stack(const Stack & st)
{
    stacksize = st.stacksize;
    top = st.top;
    items = new Type [stacksize];
    //使用动态内存分配，注意指针的安全使用！
    for (int i = 0; i < top; i++)
        items[i] = st.items[i];
}
//这个显示复制构造函数也是为了指针栈的使用（后文会提到）

template <class Type>
bool Stack<Type>::push(const Type & item)
{
    if (top < stacksize)
    {
        items[top++] = item;
        return true;
    }
    else
        return false;
}

template <class Type>
bool Stack<Type>::pop(Type & item)
{
    if (top > 0)
    {
        item = items[--top];
        return true;
    }
    else
        return false;
}

template <class Type>
Stack<Type> & Stack<Type>::operator=(const Stack<Type> & st)
{
    if (this == &st)
        return *this;
    delete [] items;
    stacksize = st.stacksize;
    top = st.top;
    items = new Type [stacksize];
    for (int i = 0; i < top; i++)
        items[i] = st.items[i];
    return *this; 
}

#endif

```

{% note info %}

类模板的成员函数本身不是一个函数，故不可以**单独编译**，需要在指定模版形式参数的值后（即实现**模版的实例化**后）才能被编译成一个程序。因此，对于类模版而言，往往将**函数定义和类模板的定义**写在同一个头文件中。

{% endnote %}

### The Use of Pointer Stack

细心的读者可能会发现，在模版类的函数定义中，多出了这样一个函数（对赋值运算符的运算符重载）

```C++
Stack & operator=(const Stack & st);
//在public中的函数声明

template <class Type>
Stack<Type> & Stack<Type>::operator=(const Stack<Type> & st)
{
    if (this == &st)
        return *this;
    delete [] items;
    stacksize = st.stacksize;
    top = st.top;
    items = new Type [stacksize];
    for (int i = 0; i < top; i++)
        items[i] = st.items[i];
    //考虑到如果数据类型为指针，直接整块赋值会产生非常严重的问题。
    return *this; 
}
```

> 同时，对构造函数和析构函数的定义也做出了相关修改。

为什么要做这样一个修改？**模板类和模版函数非常重要的一点就是模版必须具有通用性**（也可以使用**显示具体化**来为特殊类型打补丁，后文会讲到）。如果模板类型是**指针**（即**指针栈**），那么使用默认的赋值运算符会产生比较严重的问题。

> 什么严重的问题？欢迎参考第十二章有关动态内存分配的知识，此处不再展开。
>
> [我的博客链接](https://xiyuanyang-code.github.io/posts/Dynamic-Memory-and-Classes/)

#### 正确使用指针栈

使用指针栈的方法之一是，让调用程序提供一个指针数组，其中每个指针都指向不同的字符串。把这些指针放在栈中是有意义的，因为每个指针都将指向不同的字符串。注意，创建不同指针是调用程序的职责，而不是栈的职责。**栈的任务是管理指针，而不是创建指针**。  

### Advanced Usage

#### 非类型参数

在使用模板类的时候，我们通过构造函数在堆上动态分配内存，这样的效率往往比较低。如果**在确定具体类型之后再栈上开辟空间，将会变得更加的高效。**

```C++
//arraytp.h  -- Array Template
#ifndef ARRAYTP_H_
#define ARRAYTP_H_

#include <iostream>
#include <cstdlib>

template <class T, int n>
class ArrayTP
{
private:
    T ar[n];
public:
    ArrayTP() {};
    explicit ArrayTP(const T & v);
    virtual T & operator[](int i);
    virtual T operator[](int i) const;
};

template <class T, int n>
ArrayTP<T,n>::ArrayTP(const T & v)
{
    for (int i = 0; i < n; i++)
        ar[i] = v;
}

template <class T, int n>
T & ArrayTP<T,n>::operator[](int i)
{
    if (i < 0 || i >= n)
    {
        std::cerr << "Error in array limits: " << i
            << " is out of range\n";
        std::exit(EXIT_FAILURE);
    }
    return ar[i];
}

template <class T, int n>
T ArrayTP<T,n>::operator[](int i) const
{
    if (i < 0 || i >= n)
    {
        std::cerr << "Error in array limits: " << i
            << " is out of range\n";
        std::exit(EXIT_FAILURE);
    }
    return ar[i]; 
}
#endif
```

`template <class T, int n>` 是 C++ 中的一种模板声明方式，其中包含了两种不同类型的模板参数：

- **`T`**：这是一个**类型模板参数**（Type Template Parameter），表示一个类型，通常用来代表类、结构体、函数等的类型。
- **`n`**：这是一个**非类型模板参数**（Non-type Template Parameter），表示一个常量值，通常是整数、指针、引用或其他常量值。

![表达式参数](biaodashi.png)

缺点是，在`n`不相同的情况下，编译器会生成两种不同的模版（**哪怕非类型模版参数是相同的**）。非类型参数的使用本质是更改了内存的存储方式，在提升效率的同时牺牲了部分的通用性。（无法创建数组大小可变的类了）

#### 递归使用模板

`ArrayTP< ArrayTP<int,5>, 10> twodee;`

#### pair类

{% note info %}

`std::pair` 是 C++ 标准库中的一个模板类，用于存储两个不同类型的元素。它可以将两个数据组合成一个对象，这两个数据可以是任意类型，因此 `std::pair` 是一种非常常见的**通用容器**，特别适用于需要将两个相关数据关联在一起的场景。

`std::pair` 定义在 `<utility>` 头文件中，其模板定义如下：

```cpp
template <class T1, class T2>
struct pair {
    T1 first;   // 第一个元素
    T2 second;  // 第二个元素

    // 默认构造函数
    pair();
    
    // 带参数的构造函数
    pair(const T1& a, const T2& b);
    
    // 构造函数（使用移动语义）
    pair(T1&& a, T2&& b);

    // 比较运算符
    bool operator==(const pair& other) const;
    bool operator!=(const pair& other) const;
    bool operator<(const pair& other) const;
    bool operator<=(const pair& other) const;
    bool operator>(const pair& other) const;
    bool operator>=(const pair& other) const;
};
```

`std::pair` 存储两个值：`first` 和 `second`，它们可以是不同类型的数据。`T1` 是第一个元素的类型，`T2` 是第二个元素的类型。

- **默认构造函数**：`pair` 的两个成员 `first` 和 `second` 都会被默认初始化。

  ```cpp
  std::pair<int, double> p1;  // p1.first 和 p1.second 都是默认初始化
  ```

- **带参构造函数**：通过提供两个值初始化 `first` 和 `second`。

  ```cpp
  std::pair<int, double> p2(1, 3.14);  // p2.first = 1, p2.second = 3.14
  ```

- **拷贝构造函数**：允许使用另一个 `pair` 来初始化。

  ```cpp
  std::pair<int, double> p3 = p2;  // p3.first = p2.first, p3.second = p2.second
  ```

- **移动构造函数**：允许通过右值引用来初始化（C++11 引入的特性）。

  ```cpp
  std::pair<int, double> p4 = std::make_pair(10, 20.5);
  ```

**操作：**

- **访问成员**：`pair` 提供了两个公共成员变量 `first` 和 `second`，可以直接访问。

  ```cpp
  std::pair<int, double> p(1, 3.14);
  std::cout << p.first << ", " << p.second << std::endl;  // 输出: 1, 3.14
  ```

- **`std::make_pair`**：`make_pair` 是一个方便的函数模板，用于创建 `pair` 对象，避免手动指定类型。

  ```cpp
  auto p = std::make_pair(1, 3.14);  // p 是 pair<int, double>
  ```

- **比较操作符**：`pair` 支持常见的比较操作符（`==`, `!=`, `<`, `>`, `<=`, `>=`）。比较时，`pair` 会首先比较 `first`，如果相等，则再比较 `second`。

  ```cpp
  std::pair<int, double> p1(1, 3.14), p2(1, 2.718);
  if (p1 < p2) {
      std::cout << "p1 < p2" << std::endl;
  }
  ```

- **交换元素**：`std::pair` 提供了一个 `swap` 方法用于交换 `first` 和 `second`。

  ```cpp
  std::pair<int, double> p1(1, 3.14), p2(2, 2.718);
  p1.swap(p2);  // 交换 p1 和 p2 的值
  ```

**使用场景**

`std::pair` 经常用于以下场景：

- **返回多个值**：有时我们需要返回多个相关的值（例如，函数返回两个值），`std::pair` 是一个非常方便的方式来做到这一点。

  ```cpp
  std::pair<int, double> get_coordinates() {
      return std::make_pair(10, 20.5);
  }
  ```

- **在容器中存储键值对**：例如，`std::map` 和 `std::unordered_map` 等容器使用 `std::pair` 来存储键值对。

  ```cpp
  std::map<int, std::string> m;
  m[1] = "apple";
  m[2] = "banana";
  ```

- **配对数据**：在某些算法或数据结构中，两个相关的数据项经常被配对在一起。`std::pair` 允许你简洁地存储和访问它们。

```cpp
#include <iostream>
#include <utility>  // 引入 std::pair 和 std::make_pair

int main() {
    // 创建一个 pair
    std::pair<int, std::string> p1(1, "apple");

    // 访问 pair 的元素
    std::cout << "First: " << p1.first << ", Second: " << p1.second << std::endl;

    // 使用 std::make_pair 创建一个 pair
    auto p2 = std::make_pair(2, "banana");
    std::cout << "First: " << p2.first << ", Second: " << p2.second << std::endl;

    // 比较两个 pair
    if (p1 < p2) {
        std::cout << "p1 is less than p2" << std::endl;
    }

    // 交换两个 pair
    p1.swap(p2);
    std::cout << "After swap: " << "First: " << p1.first << ", Second: " << p1.second << std::endl;
    std::cout << "After swap: " << "First: " << p2.first << ", Second: " << p2.second << std::endl;

    return 0;
}
```

```
First: 1, Second: apple
First: 2, Second: banana
p1 is less than p2
After swap: First: 2, Second: banana
After swap: First: 1, Second: apple
```

{% endnote %}

{% note info %}

#### tuple和tie（补充知识）

##### 打包函数：`std::tie`

`std::tie` 是 C++11 引入的一个非常强大且方便的工具，它可以将多个值打包成一个元组，并使得这些值能够像元组一样被进行比较、解构或传递。它在多个方面提供了便利，以下是其强大之处：

1. **简化多重比较**

`std::tie` 可以将多个变量组合成一个元组，然后直接进行比较。这样，我们可以避免手动写出冗长的多重 `if` 语句来比较多个值。例如，比较结构体中的多个成员时，`std::tie` 可以将多个成员打包，按字典顺序（lexicographical order）进行比较。

```cpp
struct Point {
    int x, y, z;
};

// 使用 std::tie 对 Point 进行比较
bool compare(const Point& p1, const Point& p2) {
    return std::tie(p1.x, p1.y, p1.z) < std::tie(p2.x, p2.y, p2.z);
}
```

这里的比较会首先比较 `x`，如果 `x` 相同，再比较 `y`，如果 `y` 也相同，则比较 `z`。**这是一个典型的按字典顺序的比较**。

2. **简化排序操作**

通过 `std::tie`，你可以在排序操作中直接比较多个字段，而无需手动指定每个字段的比较方式。比如在 `std::sort` 中进行排序时，使用 `std::tie` 让代码更加简洁且易于维护。

```cpp
std::vector<Point> points = {{1, 2, 3}, {2, 1, 3}, {1, 1, 1}};
std::sort(points.begin(), points.end(), [](const Point& p1, const Point& p2) {
    return std::tie(p1.x, p1.y, p1.z) < std::tie(p2.x, p2.y, p2.z);
});
```

这样，排序时就不需要多个嵌套的 `if` 语句来分别比较 `x`, `y`, `z`。`std::tie` 会自动按字段顺序进行比较。

3. **方便的结构体解构**

`std::tie` 不仅可以用于比较，还可以用于解构，特别是在函数中返回多个值时，或者从一个元组中提取值时。它让你可以直接将多个值赋给多个变量。

```cpp
std::tuple<int, double, std::string> getValues() {
    return std::make_tuple(1, 3.14, "hello");
}

int main() {
    int i;
    double d;
    std::string s;
    std::tie(i, d, s) = getValues();  // 直接解构
    std::cout << i << " " << d << " " << s << std::endl;  // 输出: 1 3.14 hello
}
```

这样，你就可以将一个元组中的元素直接解构到不同的变量中，而不需要手动访问每个元素。

4. **元组和非元组类型的统一接口**

`std::tie` 的强大之处还在于它能够与普通类型（如 `int`，`double` 等）和标准库类型（如 `std::tuple`）无缝结合。这使得你在处理多个返回值时非常方便。

```cpp
int a = 5;
double b = 3.14;
std::tie(a, b) = std::make_tuple(10, 2.718);  // 可以直接解构元组
```

5. **更灵活的函数返回值**

当你需要一个函数返回多个值时，`std::tie` 可以帮助你轻松解构并同时返回多个值，而不需要使用 `std::pair` 或 `std::tuple` 来包裹它们。

```cpp
std::tuple<int, int> divide(int a, int b) {
    return std::make_tuple(a / b, a % b);
}

int main() {
    int quotient, remainder;
    std::tie(quotient, remainder) = divide(10, 3);
    std::cout << "Quotient: " << quotient << ", Remainder: " << remainder << std::endl;
}
```

#####  `std::tuple`

`std::tuple` 是 C++11 引入的一个标准库容器，它允许你存储多个不同类型的元素，类似于数组或结构体，但与这两者不同的是，`std::tuple` 的元素可以是不同类型的。换句话说，`tuple` 可以看作是一个异构的容器，即它可以存储多个不同类型的值，而不像数组那样只能存储相同类型的元素。

- **异构性**：`std::tuple` 可以包含不同类型的元素（如 `int`、`double`、`std::string` 等），而不像 `std::vector` 或 `std::array` 这样的容器只能包含相同类型的元素。
- **固定大小**：`std::tuple` 的大小在编译时就确定了，无法动态改变大小。也就是说，它的大小（即元素的个数）是固定的。
- **元素访问**：可以通过索引或者 `std::get` 来访问 `tuple` 中的元素。

你可以通过 `std::make_tuple` 或直接使用构造函数来创建 `tuple`。

```cpp
#include <tuple>
#include <iostream>

int main() {
    // 创建一个 tuple，包含不同类型的元素
    std::tuple<int, double, std::string> t(10, 3.14, "Hello");

    // 通过 std::get 访问 tuple 中的元素
    std::cout << "First element: " << std::get<0>(t) << std::endl;  // 输出 10
    std::cout << "Second element: " << std::get<1>(t) << std::endl; // 输出 3.14
    std::cout << "Third element: " << std::get<2>(t) << std::endl;  // 输出 Hello

    return 0;
}
```

在这个例子中，`t` 是一个包含三个元素的 `tuple`，它的元素分别是 `int`、`double` 和 `std::string` 类型。

**获取 `tuple` 中的元素：**

使用 `std::get<index>(tuple)` 来访问特定位置的元素。索引是编译时确定的，因此使用时需要指定元素的类型或位置。

```cpp
std::tuple<int, double, std::string> t(10, 3.14, "Hello");

// 使用 std::get 获取元素
int i = std::get<0>(t);           // 获取第一个元素
double d = std::get<1>(t);        // 获取第二个元素
std::string s = std::get<2>(t);   // 获取第三个元素
```

**获取 `tuple` 的大小：**

你可以使用 `std::tuple_size` 来获取 `tuple` 中元素的个数。

```cpp
std::tuple<int, double, std::string> t(10, 3.14, "Hello");
std::cout << "Tuple size: " << std::tuple_size<decltype(t)>::value << std::endl;  // 输出 3
```

**解构 `tuple`：**

你可以使用 `std::tie` 来解构 `tuple` 中的值，或者直接通过 `std::get` 提取多个值。

```cpp
std::tuple<int, double, std::string> t(10, 3.14, "Hello");

// 解构 tuple
int a;
double b;
std::string c;
std::tie(a, b, c) = t;

std::cout << "a: " << a << ", b: " << b << ", c: " << c << std::endl;
```

**scenario：**

1. **函数返回多个值**：使用 `tuple` 可以方便地在函数中返回多个不同类型的值，而无需创建自定义结构体。

   ```cpp
   std::tuple<int, double> getValues() {
       return std::make_tuple(10, 3.14);
   }
   
   int main() {
       auto values = getValues();
       std::cout << "First value: " << std::get<0>(values) << std::endl;
       std::cout << "Second value: " << std::get<1>(values) << std::endl;
       return 0;
   }
   ```

2. **存储异构数据**：当你需要存储异构数据（不同类型的元素）时，`tuple` 是一个非常方便的选择。例如，可以用它来存储一个人的年龄、名字和身高等不同类型的数据。

3. **元组解构和泛型编程**：`std::tuple` 常用于模板编程中，特别是在处理多类型的返回值或元组解构时，能够提供强大的灵活性。

{% endnote %}

#### 模版的实例化和具体化

##### 隐式实例化

函数模板的实例化通常由**编译器自动完成**， 编译器根据函数调用时的实际参数类型推断出模板参数的值，将模板参数的值代入函数模板，从而生成一个可执行的模板函数。类模板没这么幸运。编译器无法根据对象定义确定模板实际参数值。例如，定义 `Array` 类对象时给出的是代表数组下标范围的两个整数， 从中无法推断数组元素的类型，因而需要**定义对象时明确指出模板实际参数的值**。

以`std::vector`为例：

```C++
#include <iostream>
#include <vector>
using namespace std;
int main(){
    vector<int> thetest(5,3);
    for(auto num:thetest){
        cout<<num<<endl;
    }
    return 0;
}

/*输出结果
3
3
3
3
3
*/

```

在初始化一个`vector`对象的时候，我们需要**给出类模板的实际参数表**（即`class Type`）到底是什么，编译器才会**隐式的根据我们的提示生成对应的示例类和对象**。

如果使用`vector thetest(5,3);`的方式进行定义，会产生如下报错：

```bash
testcode_1.cpp: In function 'int main()':
testcode_1.cpp:15:12: error: missing template arguments before 'thetest'
     vector thetest(5,3);
            ^~~~~~~
testcode_1.cpp:16:18: error: 'thetest' was not declared in this scope
     for(auto num:thetest){
                  ^~~~~~~
testcode_1.cpp:16:18: note: suggested alternative: '_heapset'
     for(auto num:thetest){
                  ^~~~~~~
                  _heapset
```

##### 显示实例化

当使用关键词`template`并指出所需类型来声明类的时候，编译器会生成**类声明的显式实例化**。显式实例化通知编译器生成类模板的完整实例。

```C++
template <class T>
class Array{
    //模版类的声明
}


template class Array<double>;
//显式实例化生成具体的类
```

##### 显示具体化（Explicit Specialization）

显式具体化是指特定类型（用于**替换模版中的泛型**）的定义。这一种操作为泛型模版提供了更灵活的可操作性。

```C++
#include <iostream>
using namespace std;

template <typename T>
class MyClass {
public:
    void print() {
        cout << "Generic version" << endl;
    }
};

// 显式特化：针对int类型
template <>
class MyClass<int> {
public:
    void print() {
        cout << "Specialized version for int" << endl;
    }
};

int main() {
    MyClass<double> obj1; // 调用通用版本
    obj1.print();
    
    MyClass<int> obj2; // 调用针对int类型的特化版本
    obj2.print();

    return 0;
}

```

##### 部分具体化（Partial Specialization）

部分特化是指对**模板参数的某些部分进行特化**，而不需要完全特化整个模板。它允许针对一些特定的类型组合或模板参数条件进行特定实现。

```C++
#include <iostream>
using namespace std;

template <typename T, typename U>
class MyClass {
public:
    void print() {
        cout << "Generic version" << endl;
    }
};

// 部分特化：针对T是指针类型的情况
template <typename T>
class MyClass<T*, int> {
public:
    void print() {
        cout << "Specialized version for pointer and int" << endl;
    }
};

int main() {
    MyClass<double, int> obj1; // 调用通用版本
    obj1.print();
    
    MyClass<int*, int> obj2; // 调用部分特化版本
    obj2.print();

    return 0;
}

```

输出：

```Bash
Generic version
Specialized version for pointer and int
```

#### 成员模版

模板可用作**结构、类或模板类的成员**。要完全实现 STL 的设计，必须使用这项特性。  

**成员模板（Member Template）**是 C++ 中的一种功能，允许**在类中定义模板成员函数**。这样，类的成员函数可以根据调用时的类型自动实例化，具备模板的灵活性。

成员模板的主要作用是实现根据不同类型提供不同实现的功能，而不需要为每种类型手动编写多个成员函数。成员模板与类模板的结合使用，可以让类对不同类型的对象和数据进行通用处理。

```cpp
#include <iostream>
using namespace std;

template <typename T>
class MyClass {
public:
    // 成员模板函数
    template <typename U>
    void print(U value) {
        cout << "Value: " << value << endl;
    }
};

int main() {
    MyClass<int> obj;
    obj.print(42);       // 传入 int 类型
    obj.print(3.14);     // 传入 double 类型
    return 0;
}
```

- `MyClass` 是一个类模板，接受一个类型 `T`。
- `print` 是一个成员模板函数，接受任意类型 `U` 的参数。
- 在 `main` 函数中，分别用 `int` 和 `double` 类型调用 `print`，成员模板会根据传入类型进行实例化。

1. **模板参数化成员函数**：成员模板使得同一个成员函数可以根据调用的类型实例化不同的实现。
2. **灵活性**：可以使类的成员函数适应多种不同的数据类型，而不需要手动为每种类型编写重载函数。
3. **与类模板配合使用**：通常**成员模板**是与**类模板**配合使用的，使得类和其成员函数都可以泛化。

> 成员模版相当于对类中的成员函数使用**函数模版**。C++泛型的两种**模板机制**在此刻得到了统一。

我们来看一个更加复杂的例子：

```C++
// tempmemb.cpp -- template members
#include <iostream>
using std::cout;
using std::endl;

template <typename T>
class beta
{
private:
    template <typename V>  // nested template class member
    class hold
    {
    private:
        V val;
    public:
        hold(V v  = 0) : val(v) {}
        void show() const { cout << val << endl; }
        V Value() const { return val; }
    };
    hold<T> q;             // template object
    hold<int> n;           // template object
public:
    beta( T t, int i) : q(t), n(i) {}
    template<typename U>   // template method
    U blab(U u, T t) { return (n.Value() + q.Value()) * u / t; }
    void Show() const { q.show(); n.show();}
};

int main()
{
    beta<double> guy(3.5, 3);
    cout << "T was set to double\n";
    guy.Show();
    cout << "V was set to T, which is double, then V was set to int\n";
    cout << guy.blab(10, 2.3) << endl;
    cout << "U was set to int\n";
    cout << guy.blab(10.0, 2.3) << endl;
    cout << "U was set to double\n";
    cout << "Done\n";
    // std::cin.get();
    return 0; 
}

```
输出示例
```Bash
T was set to double
3.5
3
V was set to T, which is double, then V was set to int
28
U was set to int
28.2609
U was set to double
Done
```

模板类`hold`是`beta`类的私有成员，在此处一共定义了三个**模版参数**T，V和U，在main函数中，语句` beta<double> guy(3.5, 3);`将T设置为`double`类型，因此在guy（是一个beta类）中有两个数据成员：`hold<double>`和`hold<int>`。（在这里模版参数V先后被**隐式实例化**为`int`和`double`）接下来执行语句`guy.blab(10, 2.3)`，模版参数U被**隐式实例化**为`int`（`guy.blab(10, 2.3)`时也类似）。

#### 将模板用作参数

将模板用作参数（Template as a Parameter）是 C++ 中的一种技术，允许将模板本身作为函数、类或其他模板的参数。这种技术通常被称为 **模板模板参数**。它使得可以灵活地将一个模板传递给另一个模板，从而实现高度的泛化和定制化。

当你将模板用作参数时，你可以传递**一个模板的类型**，而不是实际的类型。这对于处理模板类或函数更加灵活，允许**模板参数不仅是类型，还可以是其他模板类型**。

> 有一点套娃的感觉

**示例 1：将模板作为函数参数**

下面的例子展示了如何将一个模板类作为另一个模板函数的参数：

```cpp
#include <iostream>
using namespace std;

// 模板类
template <typename T>
class MyClass {
public:
    T value;
    MyClass(T v) : value(v) {}
    void print() {
        cout << "Value: " << value << endl;
    }
};

// 一个函数模板，接受模板类作为参数
template <template <typename> class T>
    //这里模版参数T代表一个模版类(在后续main函数中，T被隐式实例化为MyClass这个模板类)
void callTemplateClass() {
    T<int> obj(42);  // 使用模板类 MyClass<int>
    obj.print();     // 输出 "Value: 42"
}

int main() {
    callTemplateClass<MyClass>();  // 调用时传递 MyClass 作为模板
    return 0;
}
```

```bash
Value: 42
```

- `MyClass` 是一个模板类，接受一个类型 `T`。
- `callTemplateClass` 是一个**模板函数，接受一个模板类作为参数**。`template <template <typename> class T>` 表示 `T` 是一个**模板模板参数**。
- 在 `main` 函数中，我们调用 `callTemplateClass<MyClass>()`，将 `MyClass` 作为模板传递给 `callTemplateClass` 函数。

> 这种规则本质上增添了**模版参数的多样性和灵活性**。

**示例 2：将模板作为类参数**

模板也可以作为类的成员，允许类在定义时接受模板作为参数。

```cpp
#include <iostream>
using namespace std;

// 定义一个模板类
template <typename T>
class MyClass {
public:
    T value;
    MyClass(T v) : value(v) {}
    void print() {
        cout << "Value: " << value << endl;
    }
};

// 定义一个类模板，接受模板作为参数
template <template <typename> class T>
class Wrapper {
public:
    T<int> obj;  // 用 T<int> 来实例化模板类
    Wrapper(int v) : obj(v) {}
    void print() {
        obj.print();
    }
};

int main() {
    Wrapper<MyClass> w(10);  // 传递 MyClass 作为模板
    w.print();  // 输出 "Value: 10"
    return 0;
}
```

- `Wrapper` 是一个模板类，接受一个模板类作为参数（`template <template <typename> class T>`）。
- `Wrapper` 类使用 `T<int>` 来实例化模板类，并通过 `obj.print()` 来调用它的成员函数。
- 在 `main` 函数中，我们创建了 `Wrapper<MyClass>` 对象，将 `MyClass` 作为模板传递给 `Wrapper`。

**使用模板作为参数的优点**

1. **高度灵活性**：允许传递不同的模板类或函数，增强代码的复用性。
2. **避免代码重复**：通过接受模板模板参数，可以减少对同类逻辑的多次实现，只需一个模板就能处理不同的类型或类。
3. **泛化**：通过使用模板模板参数，可以让代码对更多的类型和模板更加通用。

## Friend Functions and Friend Class

### 友元函数

友元函数（**Friend Function**）是 C++ 中的一种机制，允许一个函数（或类）访问另一个类的私有成员和保护成员。虽然友元函数可以访问类的私有和保护成员，但它本身并不是类的成员函数。友元函数通常用于操作一些类内部的细节，但它可能会引入一些需要注意的问题。

- 让函数成为类的友元，可以赋予该函数与类的成员函数相同的访问权限（**访问private**）
- 例：将运算符重载编写成一个非成员函数
- 友元函数具有成员函数的权限，但**作为非成员函数不能使用成员运算符进行调用**
  - 使用成员函数，可以使用构造函数，这更加高效
- **只有在函数声明的时候需要加上friend关键词，在函数定义时不可以**

#### 友元函数和在成员函数中运算符重载的区别

在成员函数中实现运算符重载

```C++
class Complex {
public:
    Complex operator+(const Complex& other) const {
        return Complex(real + other.real, imag + other.imag);
    }
    //成员函数重载运算符中，所需的参数数目比运算符使用的参数数目少一个，其中一个是被隐式传递的调用对象(this 指针)
    // 其它成员函数和数据成员
private:
    double real;
    double imag;
};

```

在非成员函数中实现运算符重载（友元函数）

```C++
class Complex {
private:
    double real;
    double imag;

public:
    Complex(double r = 0.0, double i = 0.0) : real(r), imag(i) {}

    // 声明友元函数
    friend Complex operator+(const Complex& left, const Complex& right);
};

```

#### 友元函数相比于成员函数的优势

友元函数相对于成员函数具有一些特定的优势，尽管它们打破了类的封装原则，但这些优势在某些情境下是非常有用的：

1. **操作符重载**

友元函数常用于操作符重载，特别是当操作符需要访问两个不同类或类型的对象时。成员函数只能通过 `this` 指针访问当前对象的成员，而友元函数可以直接访问两个对象的私有成员。例如：

```cpp
class Complex {
private:
    double real;
    double imag;

public:
    // ... 其他成员函数 ...
    friend Complex operator+(const Complex& a, const Complex& b);
};

// 友元函数定义
Complex operator+(const Complex& a, const Complex& b) {
    return Complex(a.real + b.real, a.imag + b.imag);
}
```

2. **提高代码的可读性和简洁性**

- **全局函数**：友元函数可以作为全局函数，这在某些情况下可以使代码更易于理解和维护。例如，上面的 `operator+` 作为友元函数，使得加法操作看起来更自然，不需要通过对象调用。
- **避免不必要的成员函数**：有时，某些操作并不适合作为类的一部分，但仍然需要访问类的私有成员。友元函数可以提供这种访问，而不需要增加类的成员函数。

3. **对称性**

- 友元函数可以提供操作的对称性。例如，在 `operator==` 的情况下，如果是成员函数，`a == b` 和 `b == a` 可能需要不同的实现，而友元函数可以使这两个操作等价。

4. **减少this指针的使用**

- 友元函数没有隐式的 `this` 指针，这在某些情况下可以提高效率，因为不需要额外的参数传递。

5. **访问私有成员而不改变类的接口**

- 如果你需要一个函数访问类的数据，但不想将这个函数作为类的成员（因为它不属于类的逻辑部分），友元函数可以让你实现这一点，而不改变类的公共接口。

6. **跨类访问**

- 友元函数可以被多个类声明为友元，从而允许**这些类之间共享数据，而不需要通过公共接口或继承**。

7. **设计模式的实现**

- 在一些设计模式中，如桥接模式、适配器模式等，友元函数可以帮助实现跨类协作，而无需暴露类的内部实现细节。

**注意事项**：

尽管友元函数有这些优势，但它们也有一些潜在的缺点：

- **打破封装**：友元函数允许非成员函数访问类的私有成员，可能会破坏类的封装性。
- **代码维护**：友元关系可能会使代码的维护变得复杂，因为它增加了类的依赖性。
- **滥用**：如果不谨慎使用，友元函数可能会导致代码的可读性和结构性下降。

因此，在使用友元函数时，应该谨慎考虑是否真的需要这种访问权限，并尽量限制友元函数的数量和范围，以保持类的封装性和代码的清晰度。

### 友元类

在 C++ 中，`friend` 是一种特殊的机制，它允许某些函数或类访问其他类的私有成员。`friend` 主要有两种形式：**友元函数** 和 **友元类**。这两种机制在设计某些复杂系统时非常有用，可以让不同类之间进行密切的合作，同时保持类内部的封装性。

**友元类** 是一个类，它被声明为某个类的友元类。友元类的成员函数可以访问该类的私有成员。这种机制通常用于设计两个紧密相关的类，它们需要互相访问私有数据，但又不希望暴露给外部。

**友元类的基本语法**

```cpp
class A;

class B {
public:
    void setA(A& obj);
};

class A {
private:
    int x;

public:
    A(int val) : x(val) {}

    // 声明 B 为友元类
    friend class B;
};

// B 是 A 的友元类，可以访问 A 的私有成员
void B::setA(A& obj) {
    obj.x = 100;  // 直接访问 A 的私有成员
}

int main() {
    A a(10);
    B b;
    b.setA(a);  // B 可以访问 A 的私有成员
    return 0;
}
```

**友元类的应用场景**

- **设计复杂系统**：友元类的使用可以让两个类之间共享数据和功能，而不暴露这些数据给其他类。例如，在某些库中，可能有一个类负责管理资源（如内存管理），另一个类负责使用这些资源。为了实现高效和紧密的合作，这两个类可能需要互相访问对方的私有成员，这时可以将其中一个类声明为友元类。
- **实现成员共享**：友元类通常用于需要相互访问成员数据的类，特别是在某些算法和数据结构的实现中，如链表、树、图等。

**友元类的设计示例**

```cpp
class Engine;  // 前向声明

class Car {
private:
    Engine* engine;  // Engine 对象是 Car 的私有成员

public:
    Car() : engine(nullptr) {}

    // 声明 Engine 为友元类
    friend class Engine;
};

class Engine {
private:
    int horsepower;

public:
    Engine(int hp) : horsepower(hp) {}

    void setCarEngine(Car& car) {
        car.engine = this;  // Engine 可以访问 Car 的私有成员
    }

    void showCarEngineInfo() {
        std::cout << "Car engine horsepower: " << horsepower << std::endl;
    }
};
```

在这个例子中，`Engine` 类被声明为 `Car` 类的友元类，使得 `Engine` 类能够访问 `Car` 类的私有成员。

#### **友元类与继承**

友元类与继承之间有一些特别的关系。虽然子类继承了父类的公共和保护成员，但**友元关系不被继承**。也就是说，如果**某个类是另一个类的友元类，它并不能自动成为其子类的友元类**。

```cpp
class A {
private:
    int value;
    
public:
    A(int val) : value(val) {}
    friend class B;  // B 是 A 的友元类
};

class C : public A {
public:
    C(int val) : A(val) {}
};

class B {
public:
    void showValue(A& obj) {
        std::cout << obj.value << std::endl;  // B 可以访问 A 的私有成员
    }
};

int main() {
    A a(10);
    B b;
    b.showValue(a);  // 正常，B 可以访问 A 的私有成员

    C c(20);
    // b.showValue(c);  // 错误，C 没有继承 B 对 A 的友元关系
    return 0;
}
```

在上面的例子中，`B` 是 `A` 的友元类，但 `C` 并没有继承 `B` 对 `A` 的友元关系，因此 `B` 无法访问 `C` 的私有成员。

#### **友元与封装**

尽管 `friend` 允许类外部的函数和类访问私有成员，但它仍然保持了一定的封装性。在设计时，应注意不要过度使用友元关系，因为过多的友元可能会破坏类的封装性，增加类之间的耦合度，导致维护困难。

- **适度使用友元**：只有在确实需要类之间紧密合作时，才应考虑使用友元关系。尤其是当某些函数需要访问类的内部细节时，友元函数和友元类可以提供非常强大的功能。
- **减少友元的使用**：不推荐随意将大量的类或函数声明为友元，尽量保持类的封装性，使其更具独立性和可维护性。

### 更加复杂的友元关系

在上文我们介绍了两种友元的使用：**友元类和友元函数**。友元函数具有作为外部函数访问类内私有数据成员的特权，而友元类具有**更多的特权**。可以实现**类之间的数据共享**。但是，**类友元的实现**在一定程度上也抹杀了类之间数据的安全性，不符合OOP中**封装**的基本理念。因此，我们可以不将整个类设置为友元，而是只将**另一个类的成员函数设置为友元，限制这种特权的使用**，保证安全性。

我们来看下面的示例，下文代码给出了电视机和遥控器的类实现。

```C++
// tvfm.h -- Tv and Remote classes using a friend member
#ifndef TVFM_H_
#define TVFM_H_

class Tv;                       // forward declaration

class Remote
{
public:
    enum State{Off, On};
    enum {MinVal,MaxVal = 20};
    enum {Antenna, Cable};
    enum {TV, DVD};
private:
    int mode;
public:
    Remote(int m = TV) : mode(m) {}
    bool volup(Tv & t);         // prototype only
    bool voldown(Tv & t);
    void onoff(Tv & t);
    void chanup(Tv & t);
    void chandown(Tv & t);
    void set_mode(Tv & t);
    void set_input(Tv & t);
    void set_chan(Tv & t, int c);
};

class Tv
{
public:
    friend void Remote::set_chan(Tv & t, int c);
    enum State{Off, On};
    enum {MinVal,MaxVal = 20};
    enum {Antenna, Cable};
    enum {TV, DVD};

    Tv(int s = Off, int mc = 125) : state(s), volume(5),
        maxchannel(mc), channel(2), mode(Cable), input(TV) {}
    void onoff() {state = (state == On)? Off : On;}
    bool ison() const {return state == On;}
    bool volup();
    bool voldown();
    void chanup();
    void chandown();
    void set_mode() {mode = (mode == Antenna)? Cable : Antenna;}
    void set_input() {input = (input == TV)? DVD : TV;}
    void settings() const;
private:
    int state;
    int volume;
    int maxchannel;
    int channel;
    int mode;
    int input;
};

// Remote methods as inline functions
inline bool Remote::volup(Tv & t) { return t.volup();}
inline bool Remote::voldown(Tv & t) { return t.voldown();}
inline void Remote::onoff(Tv & t) { t.onoff(); }
inline void Remote::chanup(Tv & t) {t.chanup();}
inline void Remote::chandown(Tv & t) {t.chandown();}
inline void Remote::set_mode(Tv & t) {t.set_mode();}
inline void Remote::set_input(Tv & t) {t.set_input();}
inline void Remote::set_chan(Tv & t, int c) {t.channel = c;} 
#endif

```

遥控器可以访问电视机的信息，因此我们自然想到可以把遥控器类设置为电视机类的友元类，但是这样的操作使安全性降低（毕竟电视机还有很多遥控器完成不了的操作），我们可以优化，将`Remote::set_chan`作为Tv类的友元函数，使其作为**唯一需要友元的方法实现**。

基于这个想法，我们需要对友元的定义和声明做一些修改。首先，就像函数声明一样，我们需要`class Tv`语句来实现**前置声明（forward declaration）**。同时，在`Remote`类的函数定义中包含`Tv`类的参数列表，因此**需要将Tv类的声明提前到Remote类的函数定义之前。**

内联函数的链接性是内部的，这意味着**函数定义必须在使用函数的文件**中。在这个例子中，内联定义位于头文件中，因此在使用函数的文件中包含头文件可确保将定义放在正确的地方。也可以将定义放在实现文件中，但必须删除关键字 `inline`，这样函数的链接性将是外部的  

> 总之就一个原则，**在使用对应的类的时候编译器应该已经看到对应的内容！**

#### 互为友元

除本章前面讨论的，还有其他友元和类的组合形式，下面简要地介绍其中的一些。

假设由于技术进步，出现了交互式遥控器。例如，交互式遥控器让您能够回答电视节目中的问题，如果回答错误，电视将在控制器上产生嗡嗡声。忽略电视使用这种设施安排观众进入节目的可能性，我们只看 C++的编程方面。新的方案将受益于相互的友情，一些 Remote 方法能够像前面那样影响 Tv 对象，而一些 Tv 方法也能影响 Remote 对象。这可以通过让**类彼此成为对方的友元**来实现，即除了 Remote 是 Tv 的友元外， Tv还是 Remote 的友元。需要记住的一点是，对于使用 Remote 对象的 Tv 方法，其原型可在 Remote 类声明之前声明，但必须在 Remote 类声明之后定义，以便编译器有足够的信息来编译该方法。

![互为友元](friend.png)

#### 共同的友元

需要使用友元的另一种情况是，**函数需要访问两个类的私有数据**。从逻辑上看，这样的函数应是每个类的成员函数，但这是不可能的。它可以是一个类的成员，同时是另一个类的友元，但有时**将函数作为两个类的友元更合理**。例如，假定有一个 Probe 类和一个 Analyzer 类，前者表示某种可编程的测量设备，后者表示某种可编程的分析设备。  

![共同的友元](frineds.png)

### 模板类和友元

模板类声明也可以有友元。模板的友元分 3 类：

- **非模板友元**；
- **约束（bound）模板友元**，即友元的类型取决于类被**实例化**时的类型；
- **非约束（unbound）模板友元**，即友元的所有具体化都是类的每一个具体化的友元。

##### 非模版友元

在 C++ 中，**模板类的非模板友元函数**是指一个**非模板函数**被声明为一个模板类的友元。这样，非模板友元函数可以访问模板类的私有和保护成员。与普通的友元函数类似，非模板友元函数允许特定的函数访问类的内部实现，但在模板类中，它们**并不依赖于模板参数**。

假设我们有一个模板类 `MyClass`，其中包含一个私有成员 `value`，并希望定义一个非模板函数来访问它。

```cpp
#include <iostream>
using namespace std;

template <typename T>
class MyClass {
private:
    T value;
    
public:
    MyClass(T v) : value(v) {}

    // 声明一个非模板友元函数
    friend void printValue(const MyClass<int>& obj);  // 只对 MyClass<int> 类型的对象有效
};

// 非模板友元函数
void printValue(const MyClass<int>& obj) {
    cout << "Value: " << obj.value << endl;  // 可以访问 MyClass<int> 的私有成员
}

int main() {
    MyClass<int> obj(42);  // 创建一个 MyClass<int> 类型的对象
    printValue(obj);       // 调用非模板友元函数，打印值
    return 0;
}
```

1. `MyClass` 是一个模板类，接受类型 `T` 作为模板参数，并包含一个私有成员 `value`。
2. `printValue` 是一个非模板函数，它被声明为 `MyClass<int>` 的友元函数。由于 `printValue` 只与 `MyClass<int>` 相关，因此它的参数类型为 `MyClass<int>`，而不是模板类型 `T`。
3. 在 `main` 函数中，我们创建了一个 `MyClass<int>` 类型的对象，并通过 `printValue` 访问其私有成员。

**注意点**

- **非模板友元函数的类型限定**：非模板友元函数只能访问**特定模板实例化的类的成员**。在上述例子中，`printValue` 只能作为 `MyClass<int>` 的友元函数，而无法访问其他类型实例化的 `MyClass` 对象（例如 `MyClass<double>`）。
- **模板类和非模板友元函数的结合**：非模板友元函数与模板类的结合需要对特定类型的模板实例进行访问，因此它通常不如模板友元函数灵活。但它在某些场景中非常有用，例如当**你只希望特定类型的模板类暴露内部实现时**。

> 一般来说，非模版友元函数和普通的友元函数没有区别（针对**特定实例化后的友元函数**），这样做一定程度上牺牲了泛型编程的**通用性**，但可以对特定类型做**更精细的操作**。

##### 约束友元

为约束模板友元作准备，要使**类的每一个具体化都获得与友元匹配的具体化**。这比非模板友元复杂些。包含以下三步：

- 在类定义的前面声明每个模板函数  
  - `template <typename T> void counts();`
  - `template <typename T> void report(T &);`
- 在函数中再次将模板声明为友元。这些语句根据类模板参数的类型声明具体化  
  - `template <typename TT>`（类模版的模版参数声明）
  - ` friend void counts<TT>();`
  - 声明中的`<>`指出这是模板具体化。对于`report()`， `<>`可以为空，因为可以从函数参数推断出。
- 为友元提供模板定义 
  - `template <typename T>
      void counts()`

```C++
// tmp2tmp.cpp -- template friends to a template class
#include <iostream>
using std::cout;
using std::endl;

// template prototypes

template <typename T> void report(T &);
template <typename T> void counts();

// template class
template <typename TT>
class HasFriendT
{
private:
    TT item;
    static int ct;
public:
    HasFriendT(const TT & i) : item(i) {ct++;}
    ~HasFriendT() { ct--; }
    friend void counts<TT>();
    friend void report<>(HasFriendT<TT> &);
};

template <typename T>
int HasFriendT<T>::ct = 0;

// template friend functions definitions
template <typename T>
void counts()
{
    cout << "template size: " << sizeof(HasFriendT<T>) << "; ";
    cout << "template counts(): " << HasFriendT<T>::ct << endl;
}

template <typename T>
void report(T & hf)
{
    cout << hf.item << endl;
}

int main()
{
    counts<int>();
    HasFriendT<int> hfi1(10);
    HasFriendT<int> hfi2(20);
    HasFriendT<double> hfdb(10.5);
    report(hfi1);  // generate report(HasFriendT<int> &)
    report(hfi2);  // generate report(HasFriendT<int> &)
    report(hfdb);  // generate report(HasFriendT<double> &)
    cout << "counts<int>() output:\n";
    counts<int>();
    cout << "counts<double>() output:\n";
    counts<double>();
    // std::cin.get();
    return 0; 
}

```

```powershell
10
20
10.5
counts<int>() output:
template size: 4; template counts(): 2
counts<double>() output:
template size: 8; template counts(): 1
```

##### 非约束友元

前一节中的约束模板友元函数是在**类外面声明的模板的具体化**。 int 类具体化获得 int 函数具体化，依此类推。通过在类内部声明模板，可以**创建非约束友元函数**，即每个函数具体化都是每个类具体化的友元。对于非约束友元，友元模板类型参数与模板类类型参数是不同的。

**非约束友元**指的是模板类的友元函数或类没有任何类型约束，它们可以接受任何类型的模板实例化，并且不对模板参数施加限制。

特点：

- 友元函数可以访问模板类的私有成员。
- 友元函数没有模板参数的约束，因此可以接受任何类型的模板实例化。
- 没有额外的限制条件，适用性广泛。

```C++
// manyfrnd.cpp -- unbound template friend to a template class
#include <iostream>
using std::cout;
using std::endl;

template <typename T>
class ManyFriend
{
private:
    T item;
public:
    ManyFriend(const T & i) : item(i) {}
    template <typename C, typename D> friend void show2(C &, D &);
};

template <typename C, typename D>
void show2(C & c, D & d)
{
    cout << c.item << ", " << d.item << endl;
}

int main()
{
    ManyFriend<int> hfi1(10);
    ManyFriend<int> hfi2(20);
    ManyFriend<double> hfdb(10.5);
    cout << "hfi1, hfi2: ";
    show2(hfi1, hfi2);
    cout << "hfdb, hfi2: ";
    show2(hfdb, hfi2);
    // std::cin.get();
    return 0;
}

```

```powershell
hfi1, hfi2: 10, 20
hfdb, hfi2: 10.5, 20
```

**非约束友元 vs 约束友元**

| 特性           | 非约束友元                                       | 约束友元                                                     |
| -------------- | ------------------------------------------------ | ------------------------------------------------------------ |
| **定义**       | 不对模板参数进行类型约束                         | 对**模板参数进行类型约束**（如通过概念、`enable_if`）        |
| **适用性**     | 可以接受任何类型的模板实例                       | 只能接受符合约束条件的模板实例                               |
| **类型安全性** | 没有类型限制，可能出现不安全的用法               | 强制类型安全，只有符合特定条件的类型才能使用                 |
| **灵活性**     | 更加灵活，不限制类型                             | 更加严格，只能与特定类型一起使用                             |
| **示例**       | `friend void printValue(const MyClass<T>& obj);` | `friend void printValue(const MyClass<T>& obj) requires IntegralType<T>;` |

## Conclusion

C++提供了几种重用代码的手段。第 13 章介绍的公有继承能够建立 is-a 关系，这样派生类可以重用基类的代码。私有继承和保护继承也使得能够重用基类的代码，但建立的是 has-a 关系。使用私有继承时，基类的公有成员和保护成员将成为派生类的私有成员；使用保护继承时，基类的公有成员和保护成员将成为派生类的保护成员。无论使用哪种继承，基类的公有接口都将成为派生类的内部接口。这有时候被称为继承实现，但并不继承接口，因为派生类对象不能显式地使用基类的接口。因此，不能将派生对象看作是一种基类对象。由于这个原因，在不进行显式类型转换的情况下，基类指针或引用将不能指向派生类对象。

还可以通过开发包含对象成员的类来重用类代码。这种方法被称为包含、层次化或组合，它建立的也是has-a 关系。与私有继承和保护继承相比，包含更容易实现和使用，所以通常优先采用这种方式。然而，私有继承和保护继承比包含有一些不同的功能。例如，继承允许派生类访问基类的保护成员；还允许派生类重新定义从基类那里继承的虚函数。因为包含不是继承，所以通过包含来重用类代码时，不能使用这些功能。另一方面，如果需要使用某个类的几个对象，则用包含更适合。例如， State 类可以包含一组 County对象。

多重继承（MI）使得能够在类设计中重用多个类的代码。私有 MI 或保护 MI 建立 has-a 关系，而公有MI 建立 is-a 关系。 MI 会带来一些问题，即多次定义同一个名称，继承多个基类对象。可以使用类限定符来解决名称二义性的问题，使用虚基类来避免继承多个基类对象的问题。但使用虚基类后，就需要为编写构造函数初始化列表以及解决二义性问题引入新的规则。

类模板使得能够创建通用的类设计，其中类型（通常是成员类型）由类型参数表示。

> END For OOP
