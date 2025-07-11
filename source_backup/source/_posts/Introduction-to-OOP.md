---
title: Introduction-to-OOP
date: 2024-12-04 19:36:23
index_img: /img/cover/OOP.jpg
excerpt: In this article, you will learn about the basic ideas and methods of object-oriented programming, including creating new classes and objects, constructors, destructors, friends, etc.
categories:
  - Code
  - Cpp
tags:
  - tutorial
  - Finished
  - OOP
  - C++ Primer Plus
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Introduction to OOP

# C++ Primer Plus Tutorial-10/11

# 面向对象编程教程——Section①

<center><p style="color: red;"><b><font size=6.5>Chapter 10/11 Object Oriented Programming</font></b></p></center>

<center><p style="color: red;"><b><font size=6.5>面向对象编程引论</font></b></p></center>

【写在前面的话】

[C++ Primer Plus Tutorial](https://xiyuanyang-code.github.io/posts/C-plus-plus-Primer-Plus-tutorial/)

[系列文章](https://xiyuanyang-code.github.io/tags/OOP/)

## Abstract

欢迎来到**面向对象编程**的世界！首先文章将会向你介绍在面向对象编程中四个最核心的精神：**封装、继承、多态和抽象**，并对面向对象所涉及的知识做一个总体性的概览。接下来，你将系统学习到什么是**类和对象**，以及如何创建并使用自己的类和对象。文章的内容涵盖**构造函数**的四种定义、**析构函数**的使用、**静态和动态**数据成员以及成员函数、**友元函数**、**const函数**的用法、**this指针**以及类的自动类型转换等知识。

Welcome to the world of **Object-Oriented Programming (OOP)**! This article will first introduce you to the four core principles of OOP: **Encapsulation, Inheritance, Polymorphism, and Abstraction**, and provide an overview of the knowledge involved in OOP. Next, you will systematically learn what **classes and objects** are, and how to create and use your own classes and objects. The article covers topics such as the four definitions of **constructors**, the use of **destructors**, **static** and **dynamic** data members and member functions, **friend functions**, the usage of const functions, the **`this`** pointer, and **automatic type conversion of classes**, among others.

**Key words: OOP, constructors, destructors, this pointer, friend functions**

## 常见知识点概览

> 在这个部分，我们将先对OOP的基本概念做一个系统性的梳理和总结。如果你是第一次接触面向对象，建议看完 `The Art of Object Oriented Programming`后跳转到对应内容处学习，最后再来看总结。

### 1. The Art of Object Oriented Programming

什么是面向对象编程？这是C++引入的一个**新的特性**。它是一种程序设计范式，它将现实世界的事物抽象成**对象**，通过封装、继承、多态和抽象等机制，使得代码更加模块化、易维护和可扩展。在OOP中，**对象**是类的实例，代表了具体的实体，它包含了属性（数据）和方法（操作）。这些对象通过交互来实现程序的功能。

初学者在入门OOP时，往往会感觉到非常的不适应，因为面向对象的语法规则艰深晦涩，并且创建类和对象需要注意到很多很多的细节，稍不留神便报错满天飞。**经过2个月的OOP学习**，笔者认为初学OOP最忌讳的就是将重点放在记忆语法规则上，而应该**将重点放在面向对象的四个基本精神：封装、继承、多态、抽象**，以此为出发点，尝试在实践中创建自己的类，并运用的过程中同步熟悉语法规则。

{% note primary %}

**封装（Encapsulation）**：

- 封装是将数据和操作数据的方法（成员函数）包装在一个单一的单元（类）中，隐藏类的内部实现细节，只暴露必要的**接口**。
- 封装在保证数据安全性的同时，也允许了代码的后续生长。

**继承（Inheritance）**：

- 继承允许一个类（子类）获得另一个类（基类或父类）的属性和方法，从而实现代码的复用和层次结构的建立。

**多态（Polymorphism）**：
- 多态指的是同一个接口可以有多种不同的实现方式。C++通过虚函数和重载实现多态。

**抽象（Abstraction）**：
- 抽象是将复杂的现实世界简化成计算机程序可以处理的模型。通过定义抽象类（含有纯虚函数的类），可以强制子类实现某些方法。

{% endnote %}

### 2. 类和对象

**类（Class）**：

```cpp
class MyClass {
private:
    int m_value; // 私有成员变量

public:
    MyClass(int val) : m_value(val) {} // 构造函数
    ~MyClass() {} // 析构函数
    void setValue(int val) { m_value = val; } // 成员函数
    int getValue() const { return m_value; } // const成员函数
};
```

- **构造函数**：初始化对象的成员变量。
- **析构函数**：当对象被销毁时执行清理工作。
- **成员函数**：操作类的数据成员。

**对象（Object）**：
- 对象是类的实例化。通过类定义来创建对象。

```cpp
MyClass obj(10); // 创建一个MyClass的对象
```

### 3. 继承

```cpp
class Base {
public:
    void display() { std::cout << "Base"; }
};

class Derived : public Base { // 公有继承
public:
    void display() { std::cout << "Derived"; }
};
```

- **公有继承**：子类可以继承基类的公有和保护成员。
- **私有继承**：子类只能继承基类的保护成员，基类的公有成员变为私有。
- **保护继承**：子类可以继承基类的保护成员，基类的公有成员变为保护。

### 4. 多态

**虚函数（Virtual Functions）**：
```cpp
class Base {
public:
    virtual void display() { std::cout << "Base"; } // 虚函数
};

class Derived : public Base {
public:
    void display() override { std::cout << "Derived"; } // 重写虚函数
};

int main() {
    Base* b = new Derived();
    b->display(); // 输出：Derived
    delete b;
    return 0;
}
```

- **纯虚函数**：`virtual void func() = 0;` 定义了接口，但不提供实现，必须在派生类中实现。

### 5. 抽象类和接口

- 包含纯虚函数的类称为抽象类，不能直接实例化，必须通过继承来实现。

```cpp
class Shape {
public:
    virtual double area() = 0; // 纯虚函数
};

class Circle : public Shape {
private:
    double radius;
public:
    Circle(double r) : radius(r) {}
    double area() override { return 3.14159 * radius * radius; }
};
```

### 6. 封装和访问控制

- **公有（public）**：任何代码都可以访问。
- **私有（private）**：只能在类内部访问。
- **保护（protected）**：在类和派生类中可以访问。

### 7. 构造函数和析构函数

- **构造函数**：用于初始化对象。
- **析构函数**：用于清理对象资源。

### 8. 友元（Friend）

- 友元函数或类可以访问类的私有和保护成员，但这打破了封装性，应谨慎使用。

### 9. 静态成员

- **静态成员变量**：共享所有对象的单一实例。
- **静态成员函数**：不与任何对象实例关联，只能访问静态成员。

```cpp
class MyClass {
public:
    static int count;
    static void incrementCount() { count++; }
};

int MyClass::count = 0; // 静态成员变量的定义和初始化
```

### 10. 运算符重载

- 允许自定义类支持标准运算符。

```cpp
class Complex {
public:
    double real, imag;
    Complex operator+(const Complex& other) {
        return Complex{real + other.real, imag + other.imag};
    }
};
```

# Cha 10 对象和类

## 数据成员

- 一般来说，私有数据成员存储信息，共有成员函数（方法）提供访问数据的唯一途径。（除了**友元函数**）

## 成员函数

### 静态成员和静态成员函数

{% note primary %}

静态成员函数（`static` member function）是类中的一种特殊成员函数，它与类的对象无关，而是与类本身相关。静态成员函数的主要作用和特点有：

1. **不依赖于类的对象**

静态成员函数可以在没有实例化对象的情况下调用，因为它不需要访问类的实例成员（非静态成员）。它只能访问类的静态成员变量和其他静态成员函数。静态成员函数不隐式地接收 `this` 指针，这使得它不能访问非静态成员。

2. **访问和操作静态成员**

静态成员函数只能访问静态成员变量和其他静态成员函数。静态成员变量属于类本身，而不是类的某个特定对象。因此，静态成员函数通常用于操作或管理类的静态数据。

3. **实例化时不需要对象**

静态成员函数可以通过类名直接调用，而不需要创建类的对象。例如：

```cpp
class MyClass {
public:
    static int staticVar;

    static void staticMethod() {
        std::cout << "Static method called, staticVar = " << staticVar << std::endl;
    }
};

int MyClass::staticVar = 10;//静态数据成员需要再类外定义

int main() {
    MyClass::staticMethod();  // 调用静态成员函数
    return 0;
}
```

在这个例子中，`staticMethod()` 是静态成员函数，我们通过 `MyClass::staticMethod()` 直接调用它，而没有创建 `MyClass` 的对象。

4. **用于实现类级别的功能**

静态成员函数适用于那些不依赖于类对象的功能，通常用于实现类级别的操作。例如：

- **工具函数**：可以作为与类的对象无关的实用功能，如数学计算、日志记录等。
- **工厂方法**：用于创建对象的工厂方法，通常返回类的实例（但不一定需要依赖于实例化对象）。
- **共享资源的管理**：静态成员函数可以用于管理类的静态资源，如数据库连接池、缓存等。

5. **内存优化**

静态成员函数可以在程序中只存在一个副本，而不需要为每个对象都创建一份。这对内存管理有一定优化作用。

```cpp
#include <iostream>

class Counter {
private:
    static int count;  // 静态成员变量

public:
    Counter() {
        count++;
    }

    static int getCount() {  // 静态成员函数
        return count;
    }
};

// 静态成员变量定义
int Counter::count = 0;

int main() {
    Counter c1, c2;
    std::cout << "Object count: " << Counter::getCount() << std::endl;  // 输出 2
    return 0;
}
```

在这个例子中，`count` 是一个静态成员变量，它用于记录 `Counter` 类的对象的数量。`getCount()` 是静态成员函数，它通过类名调用，返回静态变量 `count` 的值。

静态成员函数主要用于：

- 不依赖于对象的类级别操作。
- 操作和访问静态成员数据。
- 在不实例化对象的情况下执行类相关的功能。

它们是面向类而不是面向实例的，通常用于实现与类的对象无关的逻辑。

{% endnote %}

{% note primary %}

### 关于const成员函数

##### 关于const 成员函数的参数问题

在C++中，`const`成员函数是一个特别的成员函数，**它保证了该函数不会修改对象的成员变量**。以下是关于`const`成员函数的一些关键点：

- `const`成员函数在函数声明末尾加上`const`关键字。例如：

```cpp
class MyClass {
public:
    void doSomething() const {
        // 函数体
    }
};
```

**目的**

- **保证不修改对象状态**：`const`成员函数确保函数不会修改调用它的对象的非静态数据成员。这有助于确保对象在调用这些函数时保持不变。
- **增强代码的可读性和维护性**：通过明确表示函数不会改变对象的状态，提高了代码的清晰度，使开发者更容易理解和使用这些函数。
- **支持const对象**：`const`成员函数可以被`const`对象调用，因为它保证不会修改对象。

**使用场景**

- **访问器（Getter）**：通常，`const`成员函数用于只读访问对象的成员变量。

```cpp
int getValue() const {
    return m_value;
}
```

- **常量成员函数**：任何不修改对象状态的操作都可以被声明为`const`。

```cpp
bool isPositive() const {
    return m_value > 0;
}
```

**注意事项**

- **不能修改非静态成员变量**：在`const`成员函数内，尝试修改非静态成员变量会导致编译错误。
- **可以修改`mutable`成员**：`mutable`关键字可以用于声明一个成员变量，即使在`const`成员函数内也可以修改它。
- **隐式`this`指针的类型**：在`const`成员函数中，`this`指针的类型是`const MyClass*`，因此不能通过`this`来修改对象。
- **重载**：`const`和非`const`版本的同名成员函数可以共存，编译器会根据调用对象的`const`性来选择调用哪个版本。

```cpp
class MyClass {
public:
    int getValue() const {
        return m_value;
    }

    int& getValue() {
        return m_value;
    }

private:
    int m_value;
};

int main() {
    MyClass obj;
    const MyClass constObj;

    // 调用非const版本
    obj.getValue() = 10;

    // 调用const版本
    int val = constObj.getValue();

    return 0;
}
```

- **指针和引用**：在`const`成员函数中，返回成员变量的指针或引用也必须是`const`的，以防止通过这些指针或引用修改对象。

```cpp
const int* getPointer() const {
    return &m_value;
}
```

- **静态成员函数**：`static`成员函数不涉及`this`指针，因此它们天生就是`const`的。

`const`成员函数是C++中增强代码安全性和可读性的重要工具，通过它们，开发者可以明确地表达函数对对象状态的影响，帮助避免不必要的副作用，同时也使代码更易于维护和使用。

是的，`const`成员函数可以有形参。`const`关键字只影响函数对对象自身状态的修改能力，并不限制函数是否可以接受参数。以下是一些关于`const`成员函数如何处理参数的说明：

##### 接受参数的`const`成员函数

```cpp
class MyClass {
public:
    void doSomething(int x, const std::string& str) const {
        // 函数体
    }
};
```

在这个例子中：

- `doSomething`是一个`const`成员函数。
- 它接受两个参数：一个`int`类型的`x`和一个`const std::string`类型的引用`str`。
- 函数体中不能修改`MyClass`对象的任何非静态成员变量。

**参数的类型**

- **非`const`参数**：你可以传递非`const`类型的参数给`const`成员函数，因为这些参数是函数的局部副本或引用，不影响对象的状态。

```cpp
void doSomething(int x) const {
    x = 10; // 修改的是局部变量，不影响对象状态
}
```

- **引用和指针参数**：`const`成员函数可以接受`const`或非`const`的引用或指针作为参数，但如果是`const`引用或指针，则不能通过它们修改所指向的对象。

```cpp
void doSomething(const std::string& str) const {
    // str不能被修改，但可以通过非const成员函数修改str指向的对象
}

void doSomething(std::string* str) const {
    // str可以被重新赋值，但不能通过str修改str指向的对象
}
```

##### `const`成员函数的限制

虽然`const`成员函数可以有形参，但有以下限制：

- **不能修改对象的非静态成员变量**：这是`const`成员函数的核心原则。
- **不能调用非`const`成员函数**：因为非`const`成员函数可能修改对象的状态。
- **可以修改`mutable`成员变量**：`mutable`关键字允许在`const`成员函数中修改特定的成员变量。

```cpp
class MyClass {
public:
    void doSomething() const {
        m_mutableVar = 1; // 可以修改mutable变量
    }
private:
    mutable int m_mutableVar;
};
```

- **可以修改局部变量**：在`const`成员函数内定义的局部变量可以被修改，因为它们不影响对象的状态。

##### 总结

`const`成员函数可以接受任何类型的参数，包括`const`和非`const`的参数。`const`关键字的作用是确保函数不会修改对象的状态，而不影响函数如何处理其参数。通过这种方式，`const`成员函数既能提供只读的接口，又能灵活地处理传入的参数。

{% endnote %}

## 类的构造函数

### 构造函数的初始化列表

```C++
Test::Test(int lh, int rh):low(lh),high(rh){
//其他成员的初始化
}
```

{% note primary %}

**必须使用初始化列表的情况：**

在 C++ 中，初始化列表通常用于在类的构造函数中初始化成员变量。虽然可以在构造函数的主体中赋值，但在某些情况下，必须使用初始化列表。以下是几个常见的情况：

1. **常量成员变量（`const`）**

常量成员变量必须在对象构造时初始化，并且只能通过初始化列表进行初始化。因为常量成员变量在构造函数体内不能被赋值。

```cpp
class MyClass {
private:
    const int x;
public:
    MyClass(int val) : x(val) {}  // 必须使用初始化列表
};
```

2. **引用成员变量**

引用成员变量也只能通过初始化列表来初始化，因为引用一旦绑定到某个对象，就不能再改变。

```cpp
class MyClass {
private:
    int& ref;
public:
    MyClass(int& r) : ref(r) {}  // 引用成员必须通过初始化列表初始化
};
```

3. **基类的构造函数**

如果你的类有一个继承自基类的成员，那么在派生类的构造函数中，必须通过初始化列表来调用基类的构造函数。

```cpp
class Base {
public:
    Base(int x) {}
};

class Derived : public Base {
public:
    Derived(int x) : Base(x) {}  // 必须通过初始化列表调用基类构造函数
};
```

4. **初始化动态分配的资源（如智能指针）**

对于一些成员变量（如`std::unique_ptr`或`std::shared_ptr`），你可以在初始化列表中进行初始化，避免多次赋值，保证对象在构造时即拥有正确的资源。

```cpp
class MyClass {
private:
    std::unique_ptr<int> ptr;
public:
    MyClass(int val) : ptr(std::make_unique<int>(val)) {}  // 使用初始化列表
};
```

5. **成员变量的非默认构造**

如果类的成员变量没有提供默认构造函数，则必须在初始化列表中进行初始化。

```cpp
class MyClass {
private:
    std::vector<int> v;
public:
    MyClass() : v(10, 5) {}  // 初始化列表用于指定成员变量的初始化
};
```

尽管你可以在构造函数体内初始化一些成员变量，但对于常量成员、引用成员、基类构造函数调用以及没有默认构造函数的成员变量，必须通过初始化列表进行初始化。使用初始化列表能提高性能，并且在某些情况下（例如常量或引用成员）是唯一有效的选择。

{% endnote %}

### 类内初始化

设置对象的初值是由构造函数完成的。如果类中没有定义构造函数，编译器会提供一个默认构造函数，即用数据成员类型的默认构造函数初始化数据成员，一般情况下都是随机数。

C++11 提供了一个称为类内初始化的功能，可以在类定义时为数据成员指定初值。如果构造函数没有为这个数据成员赋值，那么该数据成员的初值即为类定义时指定的初值。类内初始化可以使用=的初始化形式，也可以使用大括号括起来的直接初始化形式，但不能使用小括号。

**类内初始化只适用于动态成员函数，静态成员函数的定义必须在类外！**

### 委托构造

有时某个构造函数的一部分工作与另外一个构造函数完全相同，那么完成这部分工作的语句必须在两个构造函数中都出现。例如，希望 DoubleArray 对象定义时能同时给出数组的初值，则需要增加一个如下的构造函数。

```C++
DoubleArray(int lh, double a[], int size): low(lh), high(lh + size -1) 
{
    storage = new double[size];
    for (int i = 0; i < size; ++i)
        storage[i] = a[i];
}
```

这个构造函数除了 for 语句之外， 其他工作与 DoubleArray(int lh, int rh)完全相同。 为了避免重复写这些语句， C++11 **允许一个构造函数调用另一个构造函数**，即委托构造。

委托构造函数有一个**初始化列表**和一个**函数体**。初始化列表只有唯一的入口，即被调用的构造函数。函数体完成额外的初始化工作。上面的构造函数可以定义如下：

```C++
DoubleArray(int lh, double a[], int size):DoubleArray(lh, lh + size - 1)
{
    for (int i = 0; i < size; ++i)
    storage[i] = a[i];
}
```

> 在类的继承中，派生类的构造函数的基本原理就是**使用委托构造调用基类的构造函数**。

### 复制构造函数

笔者花了一整篇博客的篇幅讲解复制构造函数，包括**隐式**的复制构造函数和**显式定义**的复制构造函数。

[博客地址](https://xiyuanyang-code.github.io/posts/Dynamic-Memory-and-Classes/)

### 移动构造函数

{% note primary %}

**移动构造函数**（Move Constructor）是 C++11 引入的一种特殊构造函数，用于实现对象的"移动语义"。它的作用是通过转移资源所有权，而不是复制资源，来高效地构造一个新对象。移动构造函数通常用于在对象的生命周期结束时，避免不必要的资源复制，从而提高程序的性能。

移动构造函数的主要任务是将一个**临时对象的资源**（如动态分配的内存、文件句柄等）“转移”到新的对象中，而不是进行深拷贝(**这是复制构造函数的工作**)。通过这种方式，移动构造函数能够避免重复的资源分配和复制操作，从而提高效率。

移动构造函数通常具有以下形式：

```cpp
class MyClass {
public:
    MyClass(MyClass&& other) noexcept {
        // 转移资源所有权
        this->data = other.data;
        other.data = nullptr;  // 将原对象的资源置为空（防止析构时释放）
    }
};
```

其中 `MyClass&& other` 是一个**右值引用**，表示传入的对象是一个临时对象（或可以被安全地移动的对象）。`noexcept` 表示该函数不会抛出异常，通常对于移动构造函数是一个良好的实践。

 **何时使用移动构造函数**

- **当对象是临时对象时**：临时对象可以安全地进行资源转移（移动），而不需要进行复制。
- **当对象的资源不再需要时**：比如，在函数返回时，一个对象的资源可以被“移走”，避免无谓的复制。

**与拷贝（复制）构造函数的区别**

- **拷贝构造函数**：会创建新对象并复制源对象的所有数据（深拷贝），这对于某些数据结构（如动态内存分配）可能非常昂贵。
- **移动构造函数**：通过转移资源的所有权来构造对象，不需要进行昂贵的复制操作，效率更高。

**移动构造函数的实现细节**

- 通常，移动构造函数会转移所有资源，并将原对象的资源置为空或有效的"无资源"状态，以防止析构函数错误地释放资源。
- 移动构造函数的实现通常需要保证“资源管理”的正确性，避免双重释放等问题。

```cpp
#include <iostream>
#include <vector>

class MyClass {
private:
    std::vector<int> data;
public:
    MyClass() = default;  // 默认构造函数

    // 移动构造函数
    MyClass(MyClass&& other) noexcept : data(std::move(other.data)) {
        std::cout << "Move constructor called!" << std::endl;
    }

    void addData(int value) {
        data.push_back(value);
    }

    void printData() const {
        for (int v : data) {
            std::cout << v << " ";
        }
        std::cout << std::endl;
    }
};

int main() {
    MyClass obj1;
    obj1.addData(1);
    obj1.addData(2);

    // 使用移动构造函数创建新对象
    MyClass obj2 = std::move(obj1);  // 调用移动构造函数
    obj2.printData();  // 打印 obj2 的数据
    obj1.printData();  // obj1 现在是空的

    return 0;
}
```

**输出**：

```
Move constructor called!
1 2
```

- 在 `obj2 = std::move(obj1)` 语句中，`obj1` 被“移动”到 `obj2` 中，调用了移动构造函数。
- `obj1` 之后没有持有原来数据的所有权，因此它的内容变为空。

 **使用 `std::move`**

`std::move` 不是一个真正的“移动”操作，它只是将其参数转换为右值引用，使得移动构造函数或移动赋值运算符可以被调用。因此，当你希望使用移动构造函数时，通常会显式调用 `std::move`。

 **何时自动调用移动构造函数**

移动构造函数通常在以下情况下自动调用：

- **返回值优化（RVO）**：函数返回局部对象时。
- **标准容器的元素转移**：例如，在将对象插入 `std::vector`、`std::list` 等容器时，容器可以通过移动构造函数来高效地管理内存。

**移动构造函数的最佳实践**

- **确保移动构造函数不会抛出异常**：通常应该声明为 `noexcept`，以便提高编译器的优化能力。
- **使被移动的对象处于有效但空的状态**：例如，将指针置为空或清空容器中的元素。

{% endnote %}

### 构造函数相关小结

- 可以通过函数重载创建多个同名函数，条件是每个函数的特征标（参数列表）都不同。
- 构造函数没有声明类型。
- 默认构造函数：①无参数（系统默认）② 手动为**每一个形参**添加默认值

## 类的析构函数

析构函数没有返回值也没有返回类型！

### 类析构的顺序

在 C++ 中，对象的析构顺序由其作用域（或生命周期）决定。析构过程通常遵循**逆构造顺序**，即从最内层的局部对象开始，依次向外层对象进行析构。下面是一些常见的情况和详细的析构顺序说明。

1. **局部对象的析构顺序**

在函数中，局部对象的析构顺序通常遵循**后创建先销毁**的原则。也就是说，局部对象按照它们创建的反向顺序进行析构。

```cpp
#include <iostream>

class A {
public:
    A() { std::cout << "A created" << std::endl; }
    ~A() { std::cout << "A destroyed" << std::endl; }
};

class B {
public:
    B() { std::cout << "B created" << std::endl; }
    ~B() { std::cout << "B destroyed" << std::endl; }
};

void example() {
    A a;
    B b;
    // 在这个函数结束时，a 和 b 的析构顺序是：b -> a
}

int main() {
    example();
    return 0;
}
```

**输出**：

```
A created
B created
B destroyed
A destroyed
```

**解析**：

- 在 `example` 函数中，`a` 和 `b` 是局部对象。`a` 在 `b` 之前创建，但 `b` 在 `a` 之后被销毁。这是因为析构顺序遵循“后创建，先销毁”的原则。

2. **类的成员变量的析构顺序**

类的成员变量的析构顺序遵循它们在类定义中声明的顺序，而**不是在构造函数中初始化的顺序**。也就是说，无论成员变量的初始化顺序如何，它们的析构顺序总是按它们在类定义中的声明顺序进行。

```cpp
class MyClass {
private:
    A a;  // a 在类中声明顺序较前
    B b;  // b 在类中声明顺序较后
public:
    MyClass() {}
    ~MyClass() {}
};

int main() {
    MyClass obj;
    return 0;
}
```

**输出**：

```
A created
B created
B destroyed
A destroyed
```

**解析**：

- 在 `MyClass` 的析构函数中，成员变量 `a` 会先析构，而 `b` 后析构，尽管 `b` 在构造函数中是后初始化的。

3. **栈上对象与堆上对象**

- **栈上对象**：在栈上分配的对象（即局部对象）会在它们的作用域结束时自动析构，顺序遵循“后创建，先销毁”。
- **堆上对象**：在堆上通过 `new` 创建的对象，需要**显式调用 `delete` 来销毁**。堆上的对象不会自动析构，它们的析构顺序完全取决于程序员手动调用 `delete` 的时机。

```cpp
class MyClass {
public:
    MyClass() { std::cout << "MyClass created" << std::endl; }
    ~MyClass() { std::cout << "MyClass destroyed" << std::endl; }
};

int main() {
    MyClass *ptr = new MyClass();  // 堆上对象
    delete ptr;  // 手动销毁堆上对象
    return 0;
}
```

**输出**：

```
MyClass created
MyClass destroyed
```

**解析**：

- 堆上对象 `ptr` 只有在调用 `delete` 时才会被销毁。

4. **静态和全局对象的析构顺序**

- **静态变量**：程序中所有静态变量（包括全局静态变量和局部静态变量）会在**程序结束时**析构。它们的析构顺序通常是**与定义顺序相反**，也就是从后定义的静态变量先析构。

  静态对象的析构时机通常由编译器的运行时环境管理（如 C++ 的静态对象析构）。

```cpp
class A {
public:
    A() { std::cout << "A created" << std::endl; }
    ~A() { std::cout << "A destroyed" << std::endl; }
};

A a;  // 静态对象

int main() {
    std::cout << "Inside main" << std::endl;
    return 0;
}
```

**输出**：

```
A created
Inside main
A destroyed
```

**解析**：

- 静态对象 `a` 在 `main` 函数执行之前创建，在 `main` 执行完毕后销毁。静态对象的析构发生在程序退出时，通常在 `main` 返回之后。

5. **全局对象的析构顺序**

全局对象的析构顺序遵循**反向构造顺序**，即程序的最后一个全局对象会最早被销毁。

```cpp
class A {
public:
    A() { std::cout << "A created" << std::endl; }
    ~A() { std::cout << "A destroyed" << std::endl; }
};

A a;  // 全局对象

int main() {
    std::cout << "Inside main" << std::endl;
    return 0;
}
```

**输出**：

```
A created
Inside main
A destroyed
```

**解析**：

- `a` 在 `main` 执行之前创建，并在程序退出时销毁。

6. **局部静态变量的析构顺序**

局部静态变量的析构顺序在程序结束时由运行时系统管理，通常是在 `main` 执行完毕之后销毁。

```cpp
void func() {
    static A a;  // 局部静态变量
}

int main() {
    func();  // a 在第一次调用 func 时构造
    return 0;
}
```

**总结**

- **局部对象**：后创建，先销毁（按作用域顺序）。
- **成员变量**：按照类定义中的声明顺序析构，而非构造顺序。
- **堆上对象**：需要显式使用 `delete` 销毁。
- **静态/全局对象**：按程序退出时的逆构造顺序销毁。

对象的析构顺序**遵循“后创建，先销毁”的原则**，但具体情况会根据对象的类型（局部对象、成员变量、静态变量等）以及它们的生命周期有所不同。

## this 指针

- `this` 指针在 C++ 中是一个非常重要的概念，用于指向当前对象的内存地址。以下是关于 `this` 指针的一些关键点：

  1. **指向当前对象**：

     - `this` 是一个指向当前对象的指针。每个非静态成员函数都隐式地包含一个 `this` 指针，它指向调用该成员函数的对象。

  2. **隐式传递**：

     - 当调用一个对象的成员函数时，`this` 指针会自动作为第一个参数传递给该函数。实际上，编译器会在函数调用时将对象的地址作为参数传递给成员函数的 `this` 参数。

  3. **用途**：

     - **区分形参和成员变量**：当成员函数的参数名与成员变量名相同的时候，可以使用 `this` 来明确地引用成员变量。例如：

       ```cpp
       class MyClass {
           int value;
       public:
           void setValue(int value) {
               this->value = value; // 使用 this 指针来访问成员变量
           }
       };
       ```

     - **返回当前对象的引用**：在成员函数中返回 `*this` 可以返回当前对象的引用，常用于链式调用。

  4. **注意事项**：

     - `this` 指针在类内成员函数中是隐式的，不需要显式声明。
     - `this` 指针的类型是 `类名* const`，意味着 `this` 本身是常量指针，即不能改变 `this` 指针指向的地址，但可以通过 `this` 修改对象的内容。

  5. **示例**：

     ```cpp
     class A {
     public:
         void display() {
             std::cout << "The object's address is: " << this << std::endl;
         }
     };
     
     int main() {
         A obj;
         obj.display(); // 打印对象 obj 的内存地址
         return 0;
     }
     ```

  6. **静态成员函数**：

     - 静态成员函数没有 `this` 指针，因为它们不与任何对象实例相关联，而是直接与类相关联。

  `this` 指针提供了一种方式，使得**成员函数能够访问其所属的对象的成员变量和成员函数**，增强了代码的可读性和可维护性，同时也提供了解决名称冲突的便利方法。

- 例：P297

![This pointer](https://ooo.0x0.ooo/2025/01/23/OG7ceI.png)

即需要一个指向“自身”的隐式指针——**this 指针**！

故缺失的代码为：`return *this`

### 有关this 指针的相关注意点

- this指针指向调用对象，也可以使用const限定符

- 在C++中，`this`并不是一个关键词（keyword），而是一个保留字（reserved word）。这意味着：

  - **`this`的用途**：`this`是一个指向当前对象的指针。在非静态成员函数中，`this`隐式地指向调用该函数的对象实例。它的主要用途包括：
    - 区分局部变量和成员变量。例如，如果成员变量和函数参数同名，可以使用`this->`来明确指代成员变量。
    - 返回当前对象的引用或指针。例如，`return *this;`用于链式调用。

  - **保留字**：虽然`this`不是关键词，但它是保留给编译器使用的，不能用作其他用途（如变量名或函数名）。这确保了`this`在C++代码中具有特定的含义和用法。

  - **不可重新定义**：由于`this`是保留字，程序员不能定义一个名为`this`的变量、函数或其他标识符。

  - **编译器支持**：编译器会自动在成员函数中插入`this`指针的使用，使得成员函数知道它们是哪个对象的成员。

  因此，虽然`this`在C++中不是关键词，但它的用法和保留性质使得它在语言中扮演了一个特殊的角色，确保了面向对象编程中的对象身份的明确性。

## 对象数组

- 创建对象数组的格式和基本数组相同

![Constructor](https://ooo.0x0.ooo/2025/01/23/OG7s3F.png)

也可以使用不同的构造函数

- **要创建类对象数组，必须应有类的默认构造函数（显示&隐式）**，因为在初始化对象数组的时候首先使用默认构造函数创建数组元素，接着再创建临时对象并对值进行覆盖。
- 针对对象数组，也可以使用指针

```C++
top = &(top->topval(stocks[st]));
```

成员访问运算符的优先级高于取址运算符

## 类作用域

- 要调用共有成员函数，必须通过对象！（静态成员除外）
- 可以使用成员访问运算符或作用域解析运算符（主要对于库函数）

### 类作用域运算符`::`

在C++中，作用域解析运算符（`::`）用于多种场景，以下是几种常见使用情况：

1. **访问全局变量或函数**：

   - 当局部变量与全局变量同名时，可以使用`::`来明确指定使用全局变量。例如：

     ```cpp
     int a = 10;  // 全局变量
     void func() {
         int a = 20;  // 局部变量
         std::cout << ::a << std::endl;  // 输出全局变量a的值
     }
     ```

2. **访问命名空间中的成员**：

   - 用于明确指定命名空间中的成员，避免命名冲突。例如：

     ```cpp
     namespace MyNamespace {
         void myFunction();
     }
     MyNamespace::myFunction();
     ```

3. **访问类的静态成员**：

   - 访问类的静态成员变量或静态成员函数时：

     ```cpp
     class MyClass {
     public:
         static int count;
         static void staticMethod();
     };
     int MyClass::count = 0;  // 定义并初始化静态成员变量
     MyClass::staticMethod();  // 调用静态成员函数
     ```

4. **基类成员的访问**：

   - 当派生类与基类有同名的成员时，使用作用域解析运算符可以明确访问基类的成员：

     ```cpp
     class Base {
     public:
         void method() { std::cout << "Base::method()" << std::endl; }
     };
     class Derived : public Base {
     public:
         void method() { 
             Base::method();  // 调用基类的同名方法
             std::cout << "Derived::method()" << std::endl; 
         }
     };
     ```

5. **模板类中的静态成员**：

   - 在模板类中，静态成员的定义需要使用作用域解析运算符：

     ```cpp
     template<typename T>
     class TemplateClass {
     public:
         static T value;
     };
     template<typename T>
     T TemplateClass<T>::value;  // 定义静态成员
     ```

6. **访问外部链接的变量或函数**：

   - 当在不同的源文件中声明和定义变量或函数时，可以使用作用域解析运算符来明确链接到外部定义：

     ```cpp
     // file1.cpp
     extern int globalVar;
     
     // file2.cpp
     int ::globalVar = 10;  // 定义全局变量
     ```

作用域解析运算符在C++中是一个非常重要的工具，它帮助程序员明确指定变量、函数或类的作用域，避免命名冲突，增强代码的可读性和可维护性。

- 有关静态成员

  - 访问类的静态成员可以不通过对象进行访问。静态成员是属于类本身而不是类实例的，因此可以直接通过类名访问。

    ```C++
    class MyClass {
    public:
        static int staticVar;
        static void staticMethod();
    };
    int MyClass::staticVar = 10;  // 静态成员变量的定义
    
    MyClass::staticVar = 20;  // 直接通过类名访问静态成员变量
    MyClass::staticMethod();  // 直接通过类名调用静态成员函数
    
    ```

  - 静态成员（包括静态成员变量和静态成员函数）在C++中有一些常见的用途：

静态成员变量

1. **计数器**：

   - 用于跟踪类的实例数量。

   ```cpp
   class MyClass {
   public:
       static int instanceCount;
       MyClass() { ++instanceCount; }
       ~MyClass() { --instanceCount; }
   };
   int MyClass::instanceCount = 0;  // 定义并初始化静态成员变量
   ```

2. **共享数据**：

   - 当需要在所有实例之间共享某些数据时。

   ```cpp
   class Logger {
   public:
       static std::string logFile;
       static void log(const std::string& message) {
           // 向静态成员变量logFile记录日志
       }
   };
   std::string Logger::logFile = "log.txt";
   ```

3. **常量数据**：

   - 定义类相关的常量信息。

   ```cpp
   class MathConstants {
   public:
       static constexpr double PI = 3.14159265358979323846;
   };
   ```



# Cha 11 使用类

## 友元函数

友元函数（**Friend Function**）是 C++ 中的一种机制，允许一个函数（或类）访问另一个类的私有成员和保护成员。虽然友元函数可以访问类的私有和保护成员，但它本身并不是类的成员函数。友元函数通常用于操作一些类内部的细节，但它可能会引入一些需要注意的问题。

- 让函数成为类的友元，可以赋予该函数与类的成员函数相同的访问权限（**访问private**）
- 例：将运算符重载编写成一个非成员函数
- 友元函数具有成员函数的权限，但**作为非成员函数不能使用成员运算符进行调用**
  - 使用成员函数，可以使用构造函数，这更加高效
- **只有在函数声明的时候需要加上friend关键词，在函数定义时不可以**

![Vector](https://ooo.0x0.ooo/2025/01/23/OG7Yh6.png)

### 友元函数和在成员函数中运算符重载的区别

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

### 友元函数相比于成员函数的优势

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

## 友元类

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

> 我们会在 [这篇博客](https://xiyuanyang-code.github.io/posts/Code-Reuse-in-OOP/) 中花更多的篇幅介绍友元类。

## 运算符重载

```C++ 
operator[op](argument-list){
    //运算符重载的具体实现细节
}
```

> 运算符重载也可以看做是OOP**多态**精神的一部分，标准库中每个运算符都有自己的定义，现在我们在此基础之上新定义新的语法规则。

![Operator](https://ooo.0x0.ooo/2025/01/23/OG7ftP.png)

- 可以实现运算符重载的运算符

- 重载运算符的性质
  - 必须有一个操作数是用户定义的类型
  - 不能违反运算符原来的句法规则（包括优先级）
  - 不能创建新的运算符

下文给出了对`Rational`类的相关运算符重载：

```C++
class Rational {
    private:
        int num;
        int den;
            void ReductFraction();
    public:
        Rational(int n = 0, int d = 1) { 
            num = n; den = d; 
            ReductFraction();
        }
        Rational operator + (const Rational &r1) const; //+运算符重载
        Rational operator * (const Rational &r1) const; //*运算符重载
        bool operator < (const Rational &r1) const; //<运算符重载
        bool operator == (const Rational &r1) const; //==运算符重载
        void display() const { cout << num << '/' << den;}
};
//重载了+ *运算，返回一个本类型的对象
//这里不用返回一个对本类型对象的引用，因为是临时变量，进行按值传递（如果涉及到指针需要重载赋值运算符，稍后会讲到）
Rational Rational::operator+(const Rational &r1) const
{
    Rational tmp;
    tmp.num = num * r1.den + r1.num * den;
    tmp.den = den * r1.den;
    tmp.ReductFraction();
    return tmp;
}
Rational Rational::operator*(const Rational &r1) const
{
    Rational tmp;
    tmp.num = num * r1.num;
    tmp.den = den * r1.den;
    tmp.ReductFraction();
    return tmp;
}
//重载了关系运算符，需要返回一个bool值
bool Rational::operator < (const Rational &r1) const
{
    return num * r1.den < den * r1.num;
}
bool Rational::operator == (const Rational &r1) const
{
    return num == r1.num && den == r1.den;
}
```

运算符重载本质上是一个**函数**，他有对应的返回值。（例如重载赋值运算符需要返回一个bool值，重载加法运算符需要返回一个类）编译时， 编译器将 `r3 = r1 + r2` 解释成 `r3 = r1.operator+(r2)`， `r3 = r1 * r2` 解释成 `r3 = r1.operator*(r2)`， `r1 == r2` 解释成 `r1.operator==(r2)`， `r1 < r3` 解释成 `r1.operator<(r3)`。   

如何理解`r3 = r1.operator+(r2)`？你可以把`operator+`当成是一种函数名，对象`r1`调用自己的方法`operator+()`函数，函数的参数列表是`r2`（一个对本类型对象的`const`引用）。

这 4 个运算符也可以重载成全局函数。由于重载函数主要是对对象的数据成员进行操作，而在一般的类定义中，数据成员都被定义成私有的。因此，当运算符被重载成全局函数时，通常将**此重载函数设为类的友元函数，便于访问类的私有数据成员**。  （在后文关于友元函数的章节会涉及）

大多数运算符都可以重载成成员函数或全局函数。但是**赋值运算符（ =）、下标运算符（ []）、函数调用运算符（ ()）必须重载成成员函数**，因为这些运算符的**第一个运算对象必须是相应类的对象**，定义成成员函数可以保证第一个运算对象的正确性。如果第一个运算对象不是相应类的对象，编译器能检查出此错误。 具有**赋值意义的运算符（如复合的赋值运算符以及++和--） 不一定非要重载为成员函数，但建议重载为成员函数**。具有两个运算对象且计算结果会产生一个新对象的运算符建议重载为**全局函数**，如 +、 -、 >等，这样可以使应用更加灵活。  

### 赋值运算符的重载

**[类和动态内存分配](https://xiyuanyang-code.github.io/posts/Dynamic-Memory-and-Classes/)**，建议阅读以获得更好的上下文体验。

简单来说，在类中的默认赋值运算符执行的是**浅拷贝**的过程，将每一个数据成员的**值**拷贝给另一个类中对应的数据成员。而对于**指针成员**，指针成员的值是指向的一块**内存地址**，而并非**指向变量的值**！这就会导致赋值的两个对象的指针数据成员指向了同一块内存，都具有对这块内存的**修改权限**。同时，如果使用动态内存分配，很有可能对delete掉同一块内存两次，产生错误。

**因此，赋值运算符的重载本质上就是对指针等特殊情况特殊处理，进行“深拷贝”**。

```C++
MyClass& operator=(const MyClass& other) {
        if (this == &other)  // 防止自我赋值
            return *this;

        // 先释放当前对象的资源
        delete data;

        // 分配新的内存并复制数据
        data = new int(*(other.data));

        return *this;  // 返回左操作数的引用
    }
```

在C++中，**赋值是一种运算**，赋值运算语句的返回值是左边（左值）被赋值的对象的值。因此，**赋值运算符必须返回对左边对象的引用，即返回一个左值。**

> 这样做允许了`a=b=c`的链式赋值操作，在流输入输出运算过程中也是如此。

与移动构造类似， C++11 提出了**移动赋值**的概念，让左边的对象直接接管右边临时对象的资源，以提高赋值过程的时间性能。移动赋值的右值是临时对象，因此它的**形式参数是同类对象的右值引用**，返回值是**当前对象的引用**。例如，为 DoubleArray 类增加一个移动赋值运算符重载函数，其定义如下：

```C++
DoubleArray &DoubleArray::operator=(DoubleArray && a) {
    delete [] storage;
    low = a.low;
    high = a.high;
    storage = a.storage;
    a.storage = nullptr;
    return *this;
}

```

函数首先释放左边对象的空间，然后直接接管右边对象的空间，就不再需要复制数组元素了。

### 下标运算符的重载

下标运算符是一个二元运算符，第一个运算数是**操作对象**，第二个运算数是**下标值**。

```C++
#include <iostream>
#include <stdexcept>  // 为了抛出异常

class MyArray {
private:
    int* data;//一个int指针（当数组用）
    size_t size;

public:
    // 构造函数
    MyArray(size_t s) : size(s) {
        data = new int[size];
    }

    // 析构函数
    ~MyArray() {
        delete[] data;
    }

    // 非常量版本的下标运算符重载（允许修改元素）
    int& operator[](size_t index) {
        if (index >= size) {
            throw std::out_of_range("Index out of range");
            //进行越界检查
        }
        return data[index];
    }

    // 常量版本的下标运算符重载（不允许修改元素）
    const int& operator[](size_t index) const {
        if (index >= size) {
            throw std::out_of_range("Index out of range");
        }
        return data[index];
    }

    // 获取数组大小
    size_t getSize() const {
        return size;
    }
};

int main() {
    MyArray arr(5);  // 创建一个包含 5 个整数的数组

    // 使用下标运算符修改元素
    for (size_t i = 0; i < arr.getSize(); ++i) {
        arr[i] = i * 10;  // 设置 arr[0] = 0, arr[1] = 10, arr[2] = 20, ...
    }

    // 使用下标运算符读取元素
    for (size_t i = 0; i < arr.getSize(); ++i) {
        std::cout << "arr[" << i << "] = " << arr[i] << std::endl;
    }

    // 访问常量对象
    const MyArray constArr(3);
    // constArr[0] = 100;  // 错误：常量对象不能修改
    std::cout << "constArr[0] = " << constArr[0] << std::endl;

    return 0;
}

```

### 函数调用运算符的重载

C++将函数调用也作为一种运算。 函数调用运算符()是一个二元运算符。第一个运算对象是**函数名**，第二个参数是**函数的形式参数表**，运算结果是**函数的返回值**。如果一个类重载了函数调用运算符，就可以把**这个类的对象当作函数**来使用。因为是把当前类的对象当作函数使用，所以()运算的第一个运算数是当前类的对象， C++规定函数调用运算符必须重载成成员函数。

例如，我有数组类`array`和数组类对象`arr`，如果我在`array`中重载了函数调用运算符，那么`arr(1,2)`这种操作就是合法的，即**对象名就是函数名！**

```C++
#include <iostream>

class Multiplier {
private:
    int factor;  // 乘数

public:
    // 构造函数：初始化 factor
    Multiplier(int f) : factor(f) {}

    // 重载函数调用运算符
    int operator()(int value) const {
        return factor * value;  // 返回值乘以 factor
    }
};

int main() {
    // 创建一个 Multiplier 对象，乘数为 5
    Multiplier multiplyBy5(5);

    // 使用重载的函数调用运算符进行计算
    int result = multiplyBy5(10);  // 相当于 multiplyBy5.operator()(10)
    std::cout << "Result: " << result << std::endl;  // 输出：50

    return 0;
}

```

### `++`和`--`运算符的重载

 `++`和`--`都是一元运算符，它们可被重载成成员函数或友元函数。但因为这两个运算符改变了运算对象的状态，所以更倾向于将它们作为成员函数。在考虑重载

`++`和`--`运算符时，必须注意一个问题。 `++`和`--`既可以作为前缀使用，也可以作为后缀使用。而且这两种用法的结果是不一样的：作为前缀使用时，运算结果是修改以后的对象引用；作为后缀使用时，运算结果是修改以前的对象值。为了与内置类型一致，重载后的`++`和`--`也应具有这个特性。为此，对于`++`和`--`运算，每个运算符必须**提供两个重载函数：一个处理前缀运算；另一个处理后缀运算**。

但问题是，处理++的两个重载函数的原型除了返回类型不同之外，其他是完全相同的，处理--的两个重载函数也是如此。而仅返回类型不同的两个函数无法形成重载函数。

为了解决这个问题， C++规定**后缀运算符重载函数接收一个额外的（即无用的） int 型的形式参数**。使用后缀运算符时，编译器用 0 作为这个参数的值。当编译器看到一个前缀表示的++或--时，调用正常重载的这个函数。如果看到的是一个后缀表示的++或--，则**调用有一个额外参数的重载函数**。这样就把前缀和后缀的重载函数区分开了。

#### 前缀

```C++
#include <iostream>
using namespace std;

class MyClass {
private:
    int value;

public:
    // 构造函数
    MyClass(int v) : value(v) {}

    // 前缀 ++ 运算符重载
    MyClass& operator++() {
        ++value;  // 增加成员变量 value
        return *this;  // 返回当前对象的引用
    }

    // 前缀 -- 运算符重载
    MyClass& operator--() {
        --value;  // 减少成员变量 value
        return *this;  // 返回当前对象的引用
    }

    // 显示值
    void display() const {
        cout << "Value: " << value << endl;
    }
};

int main() {
    MyClass obj(10);
    obj.display();

    ++obj;  // 前缀++ 运算符
    obj.display();

    --obj;  // 前缀-- 运算符
    obj.display();

    return 0;
}

```

{% note primary %}

**注意：前缀运算符的返回值是一个对本对象的引用**，这样可以使运算符在连续调用时进行链式操作（例如：`++(++obj)`）

{% endnote %}

#### 后缀

```C++
#include <iostream>
using namespace std;

class MyClass {
private:
    int value;

public:
    // 构造函数
    MyClass(int v) : value(v) {}

    // 后缀 ++ 运算符重载
    MyClass operator++(int) {
        MyClass temp = *this;  // 保存当前值
        ++value;  // 增加成员变量 value
        return temp;  // 返回原对象
    }

    // 后缀 -- 运算符重载
    MyClass operator--(int) {
        MyClass temp = *this;  // 保存当前值
        --value;  // 减少成员变量 value
        return temp;  // 返回原对象
    }

    // 显示值
    void display() const {
        cout << "Value: " << value << endl;
    }
};

int main() {
    MyClass obj(10);
    obj.display();

    obj++;  // 后缀++ 运算符
    obj.display();

    obj--;  // 后缀-- 运算符
    obj.display();

    return 0;
}

```

{% note primary %}

注意：

- 后缀运算符的参数列表多了一个`int`，这是C++用于区分前缀后缀的方式
- 后缀运算符重载的返回类型是**值**（会使用到赋值运算符），因此并不能返回对一个局部变量的**引用**。
  - **实际上，编译器在执行`i++`的时候，也是先创建一个副本作为i的初始值，然后执行自增操作后返回副本的值，接着副本被销毁**。
  - 这种机制导致了**`i++`所需的计算开销会高于`++i`**，尤其当i是自己定义的对象或其他内存占比比较大的数据类型时。

{% endnote %}

### 输入/输出运算符的重载

#### 重新认识`<<`和`>>`运算符

在笔者的第一堂C++启蒙课上，笔者便体会到了**流操作**的精妙和便捷。（相比于`printf()`和`scanf()`的冗长语法）但是，当时笔者只是把`cin/cout`当做一个普通的函数来使用，并不知道其背后深层次的原理。下面，我们先来重新认识一下cin/cout的真面目。

##### `istream`和`ostream`

在 C++ 中，**流类（如 `std::istream` 和 `std::ostream`）是处理输入输出操作的核心类**，它们为程序提供了与数据流进行交互的功能。这些类是 C++ 标准库的一部分，用于简化与文件、控制台、字符串等设备的交互。

![IOstream](https://ooo.0x0.ooo/2025/01/23/OG7l2b.png)

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

## 矢量类的实现

在下文给出了**矢量类vector**的实现（不是STL的那个vector）。

```C++
// vect.h -- Vector class with <<, mode state
#ifndef VECTOR_H_
#define VECTOR_H_
#include <iostream>
namespace VECTOR
{
    class Vector
    {
    public:
        enum Mode {RECT, POL};
    // RECT for rectangular, POL for Polar modes
    private:
        double x;          // horizontal value
        double y;          // vertical value
        double mag;        // length of vector
        double ang;        // direction of vector in degrees
        Mode mode;         // RECT or POL
        //状态成员
    // private methods for setting values
        void set_mag();
        void set_ang();
        void set_x();
        void set_y();
    public:
        Vector();
        Vector(double n1, double n2, Mode form = RECT);
        void reset(double n1, double n2, Mode form = RECT);
        ~Vector();
        double xval() const {return x;}       // report x value
        double yval() const {return y;}       // report y value
        double magval() const {return mag;}   // report magnitude
        double angval() const {return ang;}   // report angle
        //这四个报告函数是内联函数
        void polar_mode();                    // set mode to POL
        void rect_mode();                     // set mode to RECT
    // operator overloading
        Vector operator+(const Vector & b) const;
        Vector operator-(const Vector & b) const;
        Vector operator-() const;
        Vector operator*(double n) const;
    // friends
        friend Vector operator*(double n, const Vector & a);
        friend std::ostream & operator<<(std::ostream & os, const Vector & v);
    };

}   // end namespace VECTOR
#endif


```

```C++
// vect.cpp -- methods for the Vector class
#include <cmath>
#include "vect.h"   // includes <iostream>
using std::sqrt;
using std::sin;
using std::cos;
using std::atan;
using std::atan2;
using std::cout;

namespace VECTOR
{
    // compute degrees in one radian
    const double Rad_to_deg = 45.0 / atan(1.0);
    // should be about 57.2957795130823

    // private methods
    // calculates magnitude from x and y
    void Vector::set_mag()
    {
        mag = sqrt(x * x + y * y);
    }

    void Vector::set_ang()
    {
        if (x == 0.0 && y == 0.0)
            ang = 0.0;
        else
            ang = atan2(y, x);
    }

    // set x from polar coordinate
    void Vector::set_x()
    {
        x = mag * cos(ang);
    }

    // set y from polar coordinate
    void Vector::set_y()
    {
        y = mag * sin(ang);
    }

    // public methods
    Vector::Vector()             // default constructor
    {
        x = y = mag = ang = 0.0;
        mode = RECT;
    }

    // construct vector from rectangular coordinates if form is r
    // (the default) or else from polar coordinates if form is p
    Vector::Vector(double n1, double n2, Mode form)
    {
        mode = form;
        if (form == RECT)
         {
             x = n1;
             y = n2;
             set_mag();
             set_ang();
        }
        else if (form == POL)
        {
             mag = n1;
             ang = n2 / Rad_to_deg;
             set_x();
             set_y();
        }
        else
        {
             cout << "Incorrect 3rd argument to Vector() -- ";
             cout << "vector set to 0\n";
             x = y = mag = ang = 0.0;
             mode = RECT;
        }
    }

    // reset vector from rectangular coordinates if form is
    // RECT (the default) or else from polar coordinates if
    // form is POL
    void Vector:: reset(double n1, double n2, Mode form)
    {
        mode = form;
        if (form == RECT)
         {
             x = n1;
             y = n2;
             set_mag();
             set_ang();
        }
        else if (form == POL)
        {
             mag = n1;
             ang = n2 / Rad_to_deg;
             set_x();
             set_y();
        }
        else
        {
             cout << "Incorrect 3rd argument to Vector() -- ";
             cout << "vector set to 0\n";
             x = y = mag = ang = 0.0;
             mode = RECT;
        }
    }

    Vector::~Vector()    // destructor
    {
    }

    void Vector::polar_mode()    // set to polar mode
    {
        mode = POL;
    }

    void Vector::rect_mode()     // set to rectangular mode
    {
        mode = RECT;
    }

    // operator overloading
    // add two Vectors
    Vector Vector::operator+(const Vector & b) const
    {
        return Vector(x + b.x, y + b.y);
    }

    // subtract Vector b from a
    Vector Vector::operator-(const Vector & b) const
    {
        return Vector(x - b.x, y - b.y);
    }

    // reverse sign of Vector
    Vector Vector::operator-() const
    {
        return Vector(-x, -y);
    }

    // multiply vector by n
    Vector Vector::operator*(double n) const
    {
        return Vector(n * x, n * y);
    }

    // friend methods
    // multiply n by Vector a
    Vector operator*(double n, const Vector & a)
    {
        return a * n;
    }

    // display rectangular coordinates if mode is RECT,
    // else display polar coordinates if mode is POL
    std::ostream & operator<<(std::ostream & os, const Vector & v)
    {
        if (v.mode == Vector::RECT)
             os << "(x,y) = (" << v.x << ", " << v.y << ")";
        else if (v.mode == Vector::POL)
        {
             os << "(m,a) = (" << v.mag << ", "
                 << v.ang * Rad_to_deg << ")";
        }
        else
             os << "Vector object mode is invalid";
        return os; 
    }

}  // end namespace VECTOR

```



## 类的自动转换和强制类型转换

对于基本数据类型：使用自动转换和强制类型转换。

### 类的自动类型转换

类之间的自动类型转换通常通过 **构造函数** 或 **转换运算符** 来实现。这种转换在 C++ 中是自动发生的，当目标类型的构造函数或转换运算符能接收源类型时，编译器会自动进行转换。

例如，如果一个类具有一个接受 `int` 类型的构造函数或转换运算符，当你尝试将 `int` 类型的对象赋值给该类的对象时，编译器会自动调用该构造函数或转换运算符。

```C++
#include <iostream>
using namespace std;

class MyClass {
public:
    int value;

    // 构造函数，可以从 int 转换为 MyClass
    MyClass(int val) : value(val) {}
};

int main() {
    MyClass obj = 10;  // 自动调用 MyClass(int) 构造函数
    cout << obj.value << endl;  // 输出 10
    return 0;
}
```

在上面的代码中，`int` 被自动转换为 `MyClass` 对象，这依赖于 `MyClass` 的构造函数。

- **可以将类定义成基本类型或与另一个类相关，使得从一种类型强制转换到另一种类型是有意义的**
  - 例：利用构造函数（但只有一个参数，其余均默认）

- 类的隐式转换

  - int可以先转换成double类型，再被转换成自定义的类（可能会带来安全隐患）：**二步转换**（**无二义性**）

- 使用`exlpicit`关键词可以关闭自动转换（只允许显示转换）

```C++ 
explicit Stonewt(double lbs)
```

![Explicit](https://ooo.0x0.ooo/2025/01/23/OG7jWl.png)

- 转换函数：将类转换为基本的数据类型

  - 声明：

    ```C++ 
    operator typeName()
    operator int()
    operator double()
    ```

  - 定义

  ![Definition](https://ooo.0x0.ooo/2025/01/23/OG72gg.png)

  - 优化：进行显示转换

  ![Modofication](https://ooo.0x0.ooo/2025/01/23/OG76VB.png)

  - **警告：使用过多的转换函数可能会导致二义性冲突**。

{% note info %}

### 补充知识：五种强制类型转换

在 C++ 中，强制类型转换（也称为显式类型转换）允许程序员显式地转换一种类型到另一种类型。强制类型转换提供了比自动类型转换更大的灵活性，但也带来一定的风险，因为不当的转换可能会导致程序出错或行为未定义。

C++ 提供了 **四种类型转换操作符**，它们比传统的 C 风格转换更具类型安全性和可维护性。具体来说，C++ 强制类型转换包括：

1. **C 风格类型转换**（`(type)expression`）
2. **`static_cast`**
3. **`dynamic_cast`**
4. **`const_cast`**
5. **`reinterpret_cast`**

下面分别介绍这五种类型转换方式。

#### 1. **C 风格类型转换（C-style Cast）**

**语法**：`(new_type)expression`

这是最基本、最传统的类型转换方式，类似于 C 语言中的类型转换方式。它可以进行大多数类型转换，包括基本类型之间的转换和类类型之间的转换。

```cpp
int a = 10;
double b = (double)a;  // 强制将 int 转换为 double
std::cout << b << std::endl;  // 输出 10.0
```

- **优点**：语法简单，易于使用。
- **缺点**：没有类型安全检查，不容易发现错误。由于它能进行多种转换（例如基本类型转换、类类型转换等），可能会导致意外的转换行为。

------

#### 2. **`static_cast`**

**语法**：`static_cast<new_type>(expression)`

`static_cast` 是 C++ 提供的一种类型安全的强制类型转换方式。它通常用于：

- 基本类型之间的转换。
- **类层次结构中，向上或向下转换（在没有多态的情况下）**；
- 其他兼容类型之间的转换。

`static_cast` 是 **类型安全的**，它会在编译时检查类型是否兼容，因此可以避免一些常见的转换错误。

```cpp
double a = 3.14;
int b = static_cast<int>(a);  // 强制将 double 转换为 int
std::cout << b << std::endl;  // 输出 3
class Base {
public:
    virtual void show() { std::cout << "Base" << std::endl; }
};

class Derived : public Base {
public:
    void show() override { std::cout << "Derived" << std::endl; }
};

int main() {
    Base* basePtr = new Derived;
    Derived* derivedPtr = static_cast<Derived*>(basePtr);  // 向下转型
    derivedPtr->show();  // 输出 "Derived"
    return 0;
}
```

- **优点**：类型检查在编译时完成，能确保类型兼容性。
- **缺点**：如果转换不合理，`static_cast` 会导致编译错误。

------

#### 3. **`dynamic_cast`**

**语法**：`dynamic_cast<new_type>(expression)`

`dynamic_cast` 用于**处理类类型的转换，尤其是在类之间存在继承关系时**。它通常用于：

- 向下转型（从基类指针或引用转换为派生类指针或引用）。
- 进行 **RTTI（运行时类型识别）** 检查，用于多态类（具有虚函数的类）之间的转换。
- 对于指针或引用，`dynamic_cast` 会在运行时检查类型是否安全，如果类型不兼容，则会返回 `nullptr`（对于指针）或抛出 `std::bad_cast` 异常（对于引用）。

```cpp
class Base {
public:
    virtual void show() { std::cout << "Base" << std::endl; }
};

class Derived : public Base {
public:
    void show() override { std::cout << "Derived" << std::endl; }
};

int main() {
    Base* basePtr = new Derived;
    Derived* derivedPtr = dynamic_cast<Derived*>(basePtr);  // 向下转型
    if (derivedPtr) {
        derivedPtr->show();  // 输出 "Derived"
    } else {
        std::cout << "Conversion failed" << std::endl;
    }
    return 0;
}
```

对于不安全的转换，`dynamic_cast` 会返回 `nullptr`（指针转换）或抛出异常（引用转换）。


- **优点**：提供了运行时类型检查，避免不安全的类型转换。
- **缺点**：只能用于类类型（具有虚函数的类），并且运行时有一定的性能开销。

------

#### 4. **`const_cast`**

**语法**：`const_cast<new_type>(expression)`

`const_cast` 用于添加或移除对象的 **const** 属性。它允许程序修改一个原本是 `const` 的对象，或者从 `const` 类型的指针/引用获取非 `const` 类型的指针/引用。

- 移除 `const` 属性：通常用于修改本来是常量的对象，或者修改 `const` 指针所指向的内容。
- 添加 `const` 属性：较少使用，通常用于将非 `const` 对象传递给接受 `const` 类型参数的函数。


```cpp
const int a = 10;
int* p = const_cast<int*>(&a);  // 移除 const 属性
*p = 20;  // 修改常量对象的值
std::cout << a << std::endl;  // 输出 20
```

> **注意**：这种转换非常危险，因为它绕过了 `const` 修饰符的保护，可能会导致未定义行为。如果原对象是 `const`，通过 `const_cast` 进行修改是不合法的。


- **优点**：可以显式地修改 `const` 对象的 `const` 属性，满足特殊需求。
- **缺点**：滥用 `const_cast` 可能会破坏程序的 **const-correctness**，导致潜在的 bug 和未定义行为。

------

#### 5. **`reinterpret_cast`**

**语法**：`reinterpret_cast<new_type>(expression)`

`reinterpret_cast` 用于极低级别的转换，它允许程序员强制转换几乎任何类型到任何其他类型，甚至是完全不相关的类型。它是最强大的类型转换方式，但也最危险，因为它可能破坏内存布局并导致未定义行为。

- 常用于指针类型的转换。
- 对于完全不相关的类型，`reinterpret_cast` 可能会导致程序崩溃。


```cpp
int a = 10;
void* ptr = reinterpret_cast<void*>(&a);  // 将 int* 转换为 void*
std::cout << *reinterpret_cast<int*>(ptr) << std::endl;  // 输出 10
```


- **优点**：极其灵活，能够在底层进行类型转换，适用于底层操作和与硬件、操作系统接口的交互。
- **缺点**：几乎没有类型安全保障，可能导致程序崩溃，或者出现未定义行为，使用时要非常小心。

------

#### **总结**

C++ 提供了五种类型转换方式，各自有其特定的使用场景和优缺点：

1. **C 风格类型转换**：简单但缺乏类型安全，适合快速开发，但不推荐用于复杂的程序中。
2. **`static_cast`**：类型安全，适用于基本类型和兼容类型的转换，是常见的类型转换方式。
3. **`dynamic_cast`**：主要用于类类型转换，并进行运行时类型检查，确保安全，适合多态类型。
4. **`const_cast`**：用于移除或添加 `const` 属性，通常在需要修改 `const` 对象时使用，但要小心避免滥用。
5. **`reinterpret_cast`**：低级别的转换，允许对几乎所有类型进行转换，但极其危险，应该谨慎使用。

在实际开发中，尽量避免过度使用强制类型转换，特别是 `reinterpret_cast` 和 `const_cast`，它们可能破坏类型安全。更推荐使用 `static_cast` 和 `dynamic_cast`，它们提供了更好的类型安全性和可维护性。

{% endnote %}

# Conclusion

终于结束啦！本章主要聚焦在类和对象的使用上，在下一篇博客中，我们将会将重点转移到一个令人又爱又恨的东西上：**动态内存分配**！
