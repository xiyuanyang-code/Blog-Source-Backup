---
title: Dynamic-Memory-and-Classes
date: 2024-12-05 15:16:05
index_img: /img/cover/dynamic.png
excerpt: This blog reveals common pitfalls when using dynamic memory in classes and more advanced skills in memory management, focusing on how to cautiously use dynamic memory allocation.
categories:
  - Code
  - Cpp
tags:
  - Finished
  - tutorial
  - OOP
  - C++ Primer Plus
  - Dynamic Memory
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# C++ Primer Plus Tutorial-12

# 面向对象编程教程——Section②

<center><p style="color: red;"><b><font size=6.5>Chapter 12 Dynamic-Memory-and-Classes</font></b></p></center>

<center><p style="color: red;"><b><font size=6.5>动态内存和类</font></b></p></center>

【写在前面的话】

[C++ Primer Plus Tutorial](https://xiyuanyang-code.github.io/posts/C-plus-plus-Primer-Plus-tutorial/)

[系列文章](https://xiyuanyang-code.github.io/tags/OOP/)

## Abstract

本章将重点放在如何对**自定义类和对象**谨慎地使用**动态内存分配**，以及内存管理的相关知识。首先从一个代码示例StringBad讲起，分析因为C++自带的隐式复制构造函数导致**按值传递**和**按址传递**发生冲突而导致程序无法正确输出，并以此为教材介绍了如何**显式**地定义**复制构造函数和赋值运算符**，并给出了修改后的String类的类定义和使用示范。接着，文章聚焦于在类中使用动态内存的易错点，包括new和delete的一一对应问题。最后，文章分析了使用动态内存分配在设计类和对象过程中的应用：**设置合理的返回对象**和**使用指向对象的指针**，同时介绍了动态内存管理的一些高级操作，包括**正确地使用析构函数**和**使用定位new运算符**。

> This chapter focuses on how to **cautiously use dynamic memory allocation with** **custom classes and objects**, along with related knowledge on memory management. It begins with a code example, StringBad, to analyze how the implicit copy constructor provided by C++ leads to conflicts between **pass-by-value** and **pass-by-reference**, resulting in incorrect program output. Using this example as a teaching tool, the chapter introduces how to **explicitly** define **copy constructors and assignment operators**, and provides an updated class definition and usage demonstration for the String class. Next, the article focuses on common pitfalls when using dynamic memory in classes, including the one-to-one correspondence issue between new and delete. Finally, the chapter discusses the application of dynamic memory allocation in the design of classes and objects: **setting appropriate return objects** and **using pointers to objects**, while also introducing some advanced operations in dynamic memory management, such as **correctly using destructors** and **using placement new operator**.
>
> **Key words: OOP, Dynamic Memory, C++, Classes**

## Introduction：C++中的特殊成员函数

### 示例代码（stringbad类的实现）

```C++
// strngbad.h -- flawed string class definition
#include <iostream>
#ifndef STRNGBAD_H_
#define STRNGBAD_H_
class StringBad
{
private:
    char * str;                // pointer to string
    int len;                   // length of string
    static int num_strings;    // number of objects
public:
    StringBad(const char * s); // constructor
    StringBad();               // default constructor
    ~StringBad();              // destructor
// friend function
    friend std::ostream & operator<<(std::ostream & os, const StringBad & st);
};
#endif

```

```C++
// strngbad.cpp -- StringBad class methods
#include <cstring>                    // string.h for some
#include "strngbad.h"
using std::cout;

// initializing static class member
int StringBad::num_strings = 0;

// class methods

// construct StringBad from C string
StringBad::StringBad(const char * s)
{
    len = std::strlen(s);             // set size
    str = new char[len + 1];          // allot storage
    std::strcpy(str, s);              // initialize pointer
    num_strings++;                    // set object count
    cout << num_strings << ": \"" << str
         << "\" object created\n";    // For Your Information
}

StringBad::StringBad()                // default constructor
{
    len = 4;
    str = new char[4];
    std::strcpy(str, "C++");          // default string
    num_strings++;
    cout << num_strings << ": \"" << str
         << "\" default object created\n";  // FYI
}

StringBad::~StringBad()               // necessary destructor
{
    cout << "\"" << str << "\" object deleted, ";    // FYI
    --num_strings;                    // required
    cout << num_strings << " left\n"; // FYI
    delete [] str;                    // required
}

std::ostream & operator<<(std::ostream & os, const StringBad & st)
{
    os << st.str;
    return os; 
}

```

```C++
// vegnews.cpp -- using new and delete with classes
// compile with strngbad.cpp
#include <iostream>
using std::cout;
#include "strngbad.h"

void callme1(StringBad &);  // pass by reference
void callme2(StringBad);    // pass by value

int main()
{
    using std::endl;
    {
        cout << "Starting an inner block.\n";
        StringBad headline1("Celery Stalks at Midnight");
        StringBad headline2("Lettuce Prey");
        StringBad sports("Spinach Leaves Bowl for Dollars");
        cout << "headline1: " << headline1 << endl;
        cout << "headline2: " << headline2 << endl;
        cout << "sports: " << sports << endl;
        callme1(headline1);
        cout << "headline1: " << headline1 << endl;
        callme2(headline2);
        cout << "headline2: " << headline2 << endl;
        cout << "Initialize one object to another:\n";
        StringBad sailor = sports;
        cout << "sailor: " << sailor << endl;
        cout << "Assign one object to another:\n";
        StringBad knot;
        knot = headline1;
        cout << "knot: " << knot << endl; 
        cout << "Exiting the block.\n";
    }
    cout << "End of main()\n";
    // std::cin.get();
    return 0;
}

void callme1(StringBad & rsb)
{
    cout << "String passed by reference:\n";
    cout << "    \"" << rsb << "\"\n";
}

void callme2(StringBad sb)
{
    cout << "String passed by value:\n";
    cout << "    \"" << sb << "\"\n";
}

```

以上代码实现了一个对字符串类`StringBad`类的声明与定义，代码示例参见注释，不做解释。

以下是原书给出的输出示例：

![](input1.png)

![Output](input2.png)

### 问题分析

通过输出可以大致判断`stringbad`类出现奇怪问题的原因可能如下：

- 静态变量`num_strings`出现了负值，说明在程序中**使用析构函数的次数**多于**使用构造函数的次数**
- 出现了**非标准字符**，说明字符串在传递过程中的值出现了问题。

### 问题分析1

在上述代码中，存在**两个构造函数**，分别对应有参数和无参数：

```C++
StringBad::StringBad(const char * s)
{
    len = std::strlen(s);             // set size
    str = new char[len + 1];          // allot storage
    std::strcpy(str, s);              // initialize pointer
    num_strings++;                    // set object count
    cout << num_strings << ": \"" << str
         << "\" object created\n";    // For Your Information
}

StringBad::StringBad()                // default constructor
{
    len = 4;
    str = new char[4];
    std::strcpy(str, "C++");          // default string
    num_strings++;
    cout << num_strings << ": \"" << str
         << "\" default object created\n";  // FYI
}
```

这是析构函数的定义：

```C++
StringBad::~StringBad()               // necessary destructor
{
    cout << "\"" << str << "\" object deleted, ";    // FYI
    --num_strings;                    // required
    cout << num_strings << " left\n"; // FYI
    delete [] str;                    // required
}
```

以上三个函数定义是都没有忘记对静态成员变量`num_strings`的操作（++/--）。因此，原因①可以被更加规范地表述为：**在程序运行中使用显式定义的构造函数的次数少于使用析构函数的次数**，换句话说，程序使用了**第三种构造函数**，这个“第三种构造函数”为程序自动生成，因此没有对`num_strings`的++操作，导致负值的出现。（毕竟在作用域中每个对象只能构造一次，析构一次，两者的数量应该是相等的。）

程序中一共涉及到5个对象，这是他们的构造方法：

```C++
StringBad headline1("Celery Stalks at Midnight");
StringBad headline2("Lettuce Prey");
StringBad sports("Spinach Leaves Bowl for Dollars");

StringBad sailor = sports;

StringBad knot;
knot = headline1;
```

前三个对象，`headline1`，`headline2`，`sports`都是有函数定义中`StringBad::StringBad(const char * s)`的构造函数进行构造；最后一个`knot`使用默认构造函数`StringBad::StringBad()`进行构造，因此，问题出现在`StringBad sailor = sports`上。

> sailor和knot的初始化很像，但程序处理的方式完全不同。knot在使用**默认构造函数**构造后用headline1进行赋值；而sailor对象使用了一种名叫**复制构造函数**的构造方式！

#### 复制构造函数：隐藏的“第三者”

##### 复制构造函数的声明

sailor的构造函数原型等价于如下语句：

```C++
StringBad sailor = StringBad (sports);
//相当于把sailor的副本拷贝给sailor
```

这里的`StringBad()`就是C++自动提供的**复制构造函数**，用于**将一个对象复制到一个新创建的对象的初始化中**，注意，是**初始化不是赋值！！！**knot对象的赋值操作和复制构造函数无关（后文会讲到，使用的是`赋值运算符`），因为对knot的初始化操作已经在上一行代码使用默认构造函数完成了。

下面是复制构造函数的原型，它**接受一个指向类对象的常量引用作为参数**。

```C++ 
Class_name(const Class_name &);

StringBad(const StringBad &);
```

##### 复制构造函数的使用场景

复制构造函数的使用非常常见，尤其在**将新对象与现有对象之间建立联系的时候**。

```C++ 
StringBad sailor(sports);
StringBad sailor=sports;
StringBad sailor = StringBad (sports);
StringBad *sailor = new StringBad(sports); 
//以上四种初始化方式等价，都会使用复制构造函数
```

更深入地讲，当函数**按值传递对象或返回对象时**，会使用复制构造函数创建原始对象的一个副本作为临时对象，然后将**临时对象的内容赋给要被初始化的对象**（第四种初始化方式所赋值的是所创建的匿名对象的指针）。

例如程序中的 `callme2()`函数使用按值传递，会创建一个副本（**这里和函数的形参实参传递本质是相同的**），因此会使用复制构造函数。

```C++
void callme2(StringBad sb)
{
    cout << "String passed by value:\n";
    cout << "    \"" << sb << "\"\n";
}
```

##### 复制构造函数的功能

默认的复制构造函数**逐个复制非静态成员（成员复制也称为浅复制）**，复制的是成员的值。  

- 补充：显式使用复制构造函数提供了对对象复制行为的精细控制，因此也被称作**深拷贝**。

  ```cpp
  class MyClass {
  public:
      int* data;
      MyClass() { data = new int(0); }
      ~MyClass() { delete data; }
      
      // 显式的复制构造函数
      MyClass(const MyClass& other) {
          // 深拷贝
          data = new int(*other.data);
      }
  };
  
  int main() {
      MyClass a;
      *a.data = 10;
      MyClass b = a; // 调用复制构造函数
      // 现在b.data指向一个新的int，其值为10
      return 0;
  }
  ```

  - **显式声明**：如果你不希望编译器生成默认的复制构造函数，可以通过在类中声明一个私有的复制构造函数并将其标记为`delete`来阻止默认复制构造函数的生成。

  ```cpp
  class MyClass {
  public:
      // 其他成员函数和变量...
  private:
      MyClass(const MyClass&) = delete; // 阻止默认复制构造函数的生成
  };
  ```


#### solution

在了解的复制构造函数后，就不难理解为何静态变量`num_strings`出现了负值了。在主程序 `vegnews.cpp` 中，有一下代码用到了隐式的复制构造函数：

```C++
callme2(headline2);
StringBad sailor = sports;
//使用隐式复制构造函数两次，因此计数器会出现-2，problems solved!
```

> 问题的解决方式已经在上文给出，构建显式构造函数。

### 问题分析2

接下来我们将重点放在 `字符串乱码`的问题上。

#### 还是复制构造函数的锅！

```C++
StringBad sailor = sports;
```

上文提到，隐式复制构造函数奉行**按值传递**的基本思想，而这对于字符串成员而言是非常危险。因为C风格字符串的本质就是`char`数组，或者说指向`char`类型的特殊指针。

因此，在使用隐式复制构造函数时，`sports.str`将自己的**值**复制给了`sailor.str`。**此时这两个字符串（指向char类型的指针）指向了同一块内存！**，因此，当释放其中一个对象时，被调用的析构函数会执行下面的操作：

```C++
delete [] str;
```

在这个程序中，`sailor`先于 `sports`被析构函数调用，因此，`sailor.str`会先被`delete`掉，这样，**`sports.str`就变成了“无家可归”的悬挂指针**，后续生成乱码也就很好理解了。

更严重地，在后续`delete``sports.str`的时候，相当于程序对同一块内存连续delete两次，这会导致程序的异常终止。

#### solution

按值传递更加直观，易于理解，但是，一旦程序涉及于指针和内存管理相关的操作时，**按值传递和按址传递（指针）会产生冲突**。因此，手动构建一个显式的复制构造函数迫在眉睫。

这是原书给出的显示复制构造函数（深拷贝），手动使用 `strcpy`函数实现字符串的拷贝（按址拷贝）。

![Explicit Copy Constructor](3.png)

### 问题还未被解决...

我们已经解决了因为**隐式复制构造函数**带来的一些问题，其中，问题②主要是因为浅拷贝的**赋值操作**导致的**按值传递与按址传递的冲突**。请看下面的代码：

```C++
StringBad sailor = sports;

StringBad knot;
knot = headline1;
```

`sailor`使用复制构造函数，将`sports`对象的临时拷贝**赋值**给`sailor`完成初始化。`knot`在初始化时使用了默认构造函数，但是也免不了`knot = headline1`中对其的**赋值操作**，**按值传递与按址传递的冲突**仍然存在！

![Output](input2.png)

输出示例验证了我们的猜想，在析构时，`knot`先被`delete`，因此在最后`delete` `headline1`时，字符串呈现乱码。也就是说，**C++默认的对对象的赋值运算符依旧使用按值传递**，即**类重载赋值运算符**。下面是这种运算符的函数原型：

```C++
Class_name & Class_name::operator=(const Class_name &)
```

因此，解决问题的方式也显而易见了：手动定义赋值运算符的重载，和复制构造函数的定义基本相同。书中实现了运算符重载的代码示例：

![Explicit Overloading of Assignment Operator](4.png)

### 特殊成员函数

StringBad 类的问题是由特殊成员函数引起的。这些成员函数是自动定义的，就 StringBad 而言，这些函数的行为与类设计不符。具体地说， C++自动提供了下面这些成员函数：

- **默认构造函数，如果没有定义构造函数；**
- **默认析构函数，如果没有定义；**
- **复制构造函数，如果没有定义；**
- **赋值运算符，如果没有定义；**
- **地址运算符，如果没有定义。**（读者可以自行查阅，就是取址运算符）

更准确地说，编译器将生成上述最后三个函数的定义——如果程序使用对象的方式要求这样做。例如，如果您将一个对象赋给另一个对象，编译器将提供赋值运算符的定义。

### From StringBad to String

这就是C++风格的string类的实现！请阅读教材的12.2节，其简单介绍了string类的相关成员函数的定义，受篇幅限制，本博客不再讨论本节内容。对于使用者而言，只需要了解string类的接口足矣，但对于一位优秀的OOP学习者而言，但阅读书中代码，甚至尝试自己实现是锻炼自己OOP能力的一大有力武器。

此处只给出书中的源代码：

```C++
// string1.h -- fixed and augmented string class definition

#ifndef STRING1_H_
#define STRING1_H_
#include <iostream>
using std::ostream;
using std::istream;

class String
{
private:
    char * str;             // pointer to string
    int len;                // length of string
    static int num_strings; // number of objects
    static const int CINLIM = 80;  // cin input limit
public:
// constructors and other methods
    String(const char * s); // constructor
    String();               // default constructor
    String(const String &); // copy constructor
    ~String();              // destructor
    int length () const { return len; }
// overloaded operator methods    
    String & operator=(const String &);
    String & operator=(const char *);
    char & operator[](int i);
    const char & operator[](int i) const;
// overloaded operator friends
    friend bool operator<(const String &st, const String &st2);
    friend bool operator>(const String &st1, const String &st2);
    friend bool operator==(const String &st, const String &st2);
    friend ostream & operator<<(ostream & os, const String & st);
    friend istream & operator>>(istream & is, String & st);
// static function
    static int HowMany();
};
#endif

```

```C++
// string1.cpp -- String class methods
#include <cstring>                 // string.h for some
#include "string1.h"               // includes <iostream>
using std::cin;
using std::cout;

// initializing static class member

int String::num_strings = 0;

// static method
int String::HowMany()
{
    return num_strings;
}

// class methods
String::String(const char * s)     // construct String from C string
{
    len = std::strlen(s);          // set size
    str = new char[len + 1];       // allot storage
    std::strcpy(str, s);           // initialize pointer
    num_strings++;                 // set object count
}

String::String()                   // default constructor
{
    len = 4;
    str = new char[1];
    str[0] = '\0';                 // default string
    num_strings++;
}

String::String(const String & st)
{
    num_strings++;             // handle static member update
    len = st.len;              // same length
    str = new char [len + 1];  // allot space
    std::strcpy(str, st.str);  // copy string to new location
}

String::~String()                     // necessary destructor
{
    --num_strings;                    // required
    delete [] str;                    // required
}

// overloaded operator methods    

    // assign a String to a String
String & String::operator=(const String & st)
{
    if (this == &st)
        return *this;
    delete [] str;
    len = st.len;
    str = new char[len + 1];
    std::strcpy(str, st.str);
    return *this;
}

    // assign a C string to a String
String & String::operator=(const char * s)
{
    delete [] str;
    len = std::strlen(s);
    str = new char[len + 1];
    std::strcpy(str, s);
    return *this;
}

    // read-write char access for non-const String
char & String::operator[](int i)
{
    return str[i];
}

    // read-only char access for const String
const char & String::operator[](int i) const
{
    return str[i];
}

// overloaded operator friends

bool operator<(const String &st1, const String &st2)
{
    return (std::strcmp(st1.str, st2.str) < 0);
}

bool operator>(const String &st1, const String &st2)
{
    return st2 < st1;
}

bool operator==(const String &st1, const String &st2)
{
    return (std::strcmp(st1.str, st2.str) == 0);
}

    // simple String output
ostream & operator<<(ostream & os, const String & st)
{
    os << st.str;
    return os; 
}

    // quick and dirty String input
istream & operator>>(istream & is, String & st)
{
    char temp[String::CINLIM];
    is.get(temp, String::CINLIM);
    if (is)
        st = temp;
    while (is && is.get() != '\n')
        continue;
    return is; 
}

```

```C++
// sayings1.cpp -- using expanded String class
// compile with string1.cpp
#include <iostream>
#include "string1.h" 
const int ArSize = 10;
const int MaxLen =81;
int main()
{
    using std::cout;
    using std::cin;
    using std::endl;
    String name;
    cout <<"Hi, what's your name?\n>> ";
    cin >> name;

    cout << name << ", please enter up to " << ArSize
        << " short sayings <empty line to quit>:\n";
    String sayings[ArSize];     // array of objects
    char temp[MaxLen];          // temporary string storage
    int i;
    for (i = 0; i < ArSize; i++)
    {
        cout << i+1 << ": ";
        cin.get(temp, MaxLen);
        while (cin && cin.get() != '\n')
            continue;
        if (!cin || temp[0] == '\0')    // empty line?
            break;              // i not incremented
        else
            sayings[i] = temp;  // overloaded assignment
    }
    int total = i;              // total # of lines read

    if ( total > 0)
    {
        cout << "Here are your sayings:\n";
        for (i = 0; i < total; i++)
            cout << sayings[i][0] << ": " << sayings[i] << endl;

        int shortest = 0;
        int first = 0;
        for (i = 1; i < total; i++)
        {
            if (sayings[i].length() < sayings[shortest].length())
                shortest = i;
            if (sayings[i] < sayings[first])
                first = i;
        }
        cout << "Shortest saying:\n" << sayings[shortest] << endl;;
        cout << "First alphabetically:\n" << sayings[first] << endl;
        cout << "This program used "<< String::HowMany() 
             << " String objects. Bye.\n";
    }
    else
        cout << "No input! Bye.\n";
// keep window open 
/*    if (!cin)
        cin.clear();
    while (cin.get() != '\n')
        continue; */ 
   return 0; 
}

```

## 类中动态内存的使用

通过string类的实现，我们不难发现**动态内存**在类中的使用既便捷，又危险。下文总结了在类与对象定义过程中应当注意的易错点：

- 使用`new`创建指针成员，应该在析构函数中使用`delete`

  - **注意点：尽量不要定义类中的静态指针成员，若需要请额外关注其生命周期！**

  - 在C++中，如果你在类中定义了一个静态指针成员变量，你需要特别注意其生命周期的管理，因为静态成员变量在程序的整个生命周期内只存在一个实例。这意味着：

    1. **初始化**：静态成员变量需要在类外进行初始化。
    2. **内存管理**：如果你使用了动态分配内存（例如，使用`new`关键字），你需要确保在程序结束之前手动释放这块内存。

    ```cpp
    class MyClass {
    public:
        static int* staticPointer;
    };
    
    // 类外初始化
    int* MyClass::staticPointer = nullptr;
    ```

    - **程序结束之前**：静态成员变量的生命周期与程序相同，因此在程序结束之前，你应该确保所有动态分配的内存被正确释放。这通常意味着在`main`函数结束之前或在某个全局清理函数中进行`delete`操作。

    - **自定义的清理函数**：如果你希望在程序结束之前明确地控制删除静态成员变量的时间，你可以创建一个函数来执行清理操作：

      ```cpp
      void cleanup() {
          delete MyClass::staticPointer;
          MyClass::staticPointer = nullptr;
      }

      int main() {
          // 程序运行...
          cleanup(); // 在程序结束前调用
          return 0;
      }
      ```

    - **使用智能指针**：为了避免手动管理内存，可以考虑使用智能指针（例如`std::unique_ptr`或`std::shared_ptr`）来管理静态成员变量的生命周期。这样，内存管理将由智能指针自动处理：

      ```cpp
      #include <memory>
      
      class MyClass {
      public:
          static std::unique_ptr<int> staticPointer;
      };
      
      // 初始化
      std::unique_ptr<int> MyClass::staticPointer = std::make_unique<int>(10);
      ```

      在这种情况下，你不需要手动`delete`，因为`unique_ptr`会在超出作用域时自动释放内存。

    - **避免重复删除**：确保你不会多次`delete`同一个指针，因为这会导致未定义行为。
    - **检查指针是否为`nullptr`**：在删除静态指针前，检查它是否已经指向了有效的内存地址，防止对`nullptr`进行`delete`操作。

  - 推荐使用C++中的新的关键字`nullptr`（来替代C风格的`NULL`）

- 注意C++中自动提供的若干**特殊成员函数**

  - 默认构造函数
  - 默认析构函数
  - （隐式）的复制构造函数
  - 赋值运算符
  - 地址运算符
  - **注意：如果你设计的类成员中存在指针等，请务必手动构建显式的成员函数！！！否则会因为按值传递和按址传递的冲突导致很多奇奇怪怪的问题（尤其在赋值和动态内存的手动管理上）**

- `new`和`delete`很危险，但请踏出使用他们的第一步！

例如，原书给出了三个定义构造函数的实例：

![](example1.png)

![Standard Practices for Using Dynamic Memory](example2.png)

## 返回对象

### 返回类的引用

在C++中，引用同样也是奉行“按址传递”思想的一大有力武器。在类中，返回引用不用拷贝一个类（这会消耗额外的内存），自然也不会调用复制构造函数。下文将介绍在类中返回引用的一些基础知识：

- 使用`const`引用，效率更高，但同时需要注意匹配问题：

  - 如果形式参数是`const`对象的话，返回值也应该是`const`对象！

- 返回指向非const对象的引用

  - 重载赋值运算符

  例如，我想实现一个连续赋值的操作：

  ```C++
  string s1="Hello world";
  string s2,s3;
  s3=s2=s1;
  //第三行代码等价于
  s3=(s2=s1);
  ```

  首先执行赋值语句 `s2=s1`，问题来了，表达式的返回值是什么？对于基本数据类型而言，赋值表达式的返回类型是左操作数（即被赋值变量）的引用类型。例如，如果`a`是一个`int`变量，那么`a = b`的返回类型就是`int&`。如果是自定义类，就需要我们 **关注赋值运算符重载的返回类型、即返回一个自定义类的非const引用**，示例代码如下：

  ```C++
  class MyClass {
  public:
      // 其他成员变量和成员函数...
  
      // 赋值运算符重载
      MyClass& operator=(const MyClass& other) {
          if (this != &other) { // 自赋值检查，防止自我复制
              // 执行实际的赋值操作
              // 例如：
              // this->data = other.data;
              // ...
          }
          return *this; // 返回当前对象的引用
      }
  
  private:
      // 私有成员变量
      int data;
  };
  
  int main() {
      MyClass a, b, c;
      // 链式赋值
      a = b = c; // 这将按预期工作
  
      return 0;
  }
  
  ```

  > 为什么是自身类型的非const引用？一方面，为了连续链式赋值的合法性，我们必须保证首先执行的赋值语句的返回值能够作为参数传入下一个赋值语句，例如在上面的代码中，首先执行的`s2=s1`，返回值是对s2的引用，而这正好能作为参数传入到下一个赋值语句（将`s2`的值传递给`s3`，在这里是s2的引用）。另一方面，由于赋值会改变对象的值，因此不可以使用const引用，故返回一个**自身类型的非const引用**。

  - `<<`运算符

  这其实和赋值运算符的本质差不多，运算符的本质还是一个**函数**，所以运算（表达式）一定会有返回值（**这个观点非常重要，对我们后续学习运算符重载有很大的帮助**）。我们知道，**流输出运算符的返回值是对流对象的引用**，例如`cout<<"Hello"<<"World";`返回的对象是`std::ostream&`。

  > 还记得第一堂C++课的时候笔者接触到cin和cout，感叹其功能强大之处。但在之后的coding过程中，各种花里胡哨的输入输出类型让笔者恼羞成怒，甚至在一段时间内换回了C风格的`printf()`和`scanf()`。但是随着学习的深入，当我们对**流**和引用，类，对象等C++的新概念有了更深刻的理解之后，我们便能体会到cin和cout的强大究竟是如何实现的！

### 返回局部变量：返回对象

考虑到局部变量的作用域，我们不应该在函数中返回一个局部变量的引用（否则会产生悬挂指针等非常严重的问题）。因此，我们需要**返回一个对象**。看下面的代码示例：

```C++
Vector Vector::operater+(const Vector & b) const{
	return Vector(x+b.x,y+b.y);
}
```

`Vector`（向量）相信大家不陌生，在这里重载了算数运算符`+`，将两个矢量的和储存在一个新的临时对象中，作为函数的返回值之后再进行相关的赋值操作，最后被丢弃。**在这里会不可避免的使用到复制构造函数，所以务必小心。**

为了保证安全性，可以返回一个**const 对象**，这样可以保证生成的临时对象的值不会被赋值运算符修改。

## 使用指向对象的指针

### 基本用法回顾

```C++
    // use pointers to keep track of shortest, first strings
        String * shortest = &sayings[0]; // initialize to first object
        String * first = &sayings[0];
        for (i = 1; i < total; i++)
        {
            if (sayings[i].length() < shortest->length())
                shortest = &sayings[i];
            if (sayings[i] < *first)     // compare values
                first = &sayings[i];     // assign address
        }
        cout << "Shortest saying:\n" << * shortest << endl;
        cout << "First alphabetically:\n" << * first << endl;

        srand(time(0));
        int choice = rand() % total; // pick index at random



    // use new to create, initialize new String object
        String * favorite = new String(sayings[choice]);
		//这里会调用相应的构造函数
        cout << "My favorite saying:\n" << *favorite << endl;
        delete favorite;
    }
    else
        cout << "Not much to say, eh?\n";
    cout << "Bye.\n";
```

代码的第一部分是指向对象指针的常见用法，包括`->`运算符，常见的取址和解引用运算。代码的第二部分涉及到`new`和`delete`操作。

### 一些高级用法

#### 何时使用析构函数？

![析构函数](析构函数.png)

在下述情况下析构函数将被调用（参见图 12.4）。

- 如果对象是动态变量，则当**执行完定义该对象的程序块**时，将调用该对象的析构函数。
- 如果对象是**静态变量（外部、静态、静态外部或来自名称空间）**，则在程序结束时将调用对象的析构函数。
- 如果对象是用 new 创建的，则仅当您**显式使用 delete 删除对象**时，其析构函数才会被调用。

例如在上面的例子中，一共定义了三个基于`Act`类的对象：`nice`（全局变量，与程序共存亡）、`pt`（使用new定义的一个指向Act类的指针）、`up`（在一个代码块中定义的自动局部变量）

**注意！**

- 如果析构函数通过对指针类成员使用 delete 来释放内存，则每个构造函数都应当使用 new 来初始化指针，或将它设置为空指针。

  > 如果类的析构函数使用`delete`来释放指针成员，那么意味着这个指针成员在对象的生命周期内被认为是指向动态分配的内存的。为了确保在对象被销毁时不会尝试删除一个未初始化的指针，每个构造函数都应该使用`new`来为指针成员分配内存，或者将指针成员初始化为`nullptr`（在C++11及以后的版本中使用`nullptr`，之前的版本使用`NULL`或`0`）

```C++
class MyClass {
public:
    MyClass() : data(new int(42)) {} // 初始化指针成员
    MyClass(int val) : data(new int(val)) {} // 另一种初始化方式
    explicit MyClass(bool flag) : data(nullptr) {} // 将指针设置为空指针
    ~MyClass() {
        delete data;
    }
private:
    int* data;
};

```

#### 定位new运算符

##### 什么是定位new运算符？

**定位new运算符**（Placement New Operator）是C++中一种特殊的`new`运算符，它允许你将对象构造在预先分配好的内存位置上，而不像常规的`new`那样动态分配内存。这在某些情况下特别有用：

```cpp
void* memory = ...; // 预先分配好的内存
SomeType* obj = new (memory) SomeType(arguments); // 构造对象在预先分配的内存上
```

这里的`memory`是一个指向已经分配好的内存的指针，`SomeType`是你的类或类型，`arguments`是传递给构造函数的参数。

1. **不分配内存**：定位new不会分配新的内存，它只是在**给定的内存位置上调用构造函数来初始化对象**。

2. **手动内存管理**：使用定位new意味着你必须手动管理内存的生命周期，包括确保内存是在定位new之前分配的，并且在对象不再需要时正确地调用析构函数。

3. **异常安全性**：如果构造函数抛出异常，定位new不会自动释放内存，因为它没有分配新的内存。你需要手动处理异常情况。

```cpp
#include <iostream>

class MyClass {
public:
    MyClass(int val) : value(val) {
        std::cout << "Constructing MyClass with value: " << value << std::endl;
    }
    ~MyClass() {
        std::cout << "Destructing MyClass with value: " << value << std::endl;
    }
    int value;
};

int main() {
    // 预先分配一块内存
    char* buffer = new char[sizeof(MyClass)];
    
    // 使用定位new在预先分配的内存上构造对象
    MyClass* obj = new (buffer) MyClass(42);
    
    // 使用对象
    std::cout << "Object value: " << obj->value << std::endl;
    
    // 手动调用析构函数
    obj->~MyClass();
    
    // 释放预先分配的内存
    delete[] buffer;

    return 0;
}
```

##### 定位new运算符的使用注意事项

下面的程序对普通的`new`使用和`定位new运算符`的使用进行了比较，并归纳出使用`定位new运算符`的常见易错点。

```C++
// placenew1.cpp  -- new, placement new, no delete
#include <iostream>
#include <string>
#include <new>
using namespace std;
const int BUF = 512;

class JustTesting
{
private:
    string words;
    int number;
public:
    JustTesting(const string & s = "Just Testing", int n = 0) 
    {words = s; number = n; cout << words << " constructed\n"; }
    //默认构造函数
    ~JustTesting() { cout << words << " destroyed\n";}
    //析构函数
    void Show() const { cout << words << ", " << number << endl;}
};
int main()
{
    char * buffer = new char[BUF];       // get a block of memory

    JustTesting *pc1, *pc2;

    pc1 = new (buffer) JustTesting;      // place object in buffer
    pc2 = new JustTesting("Heap1", 20);  // place object on heap（直接在堆上分配内存）
    
    cout << "Memory block addresses:\n" << "buffer: "<< (void *) buffer << "    heap: " << pc2 <<endl;
    //void*使buffer被强制转换成通用指针使其打印地址而不是字符串的值
    
    cout << "Memory contents:\n";
    cout << pc1 << ": ";
    pc1->Show();
    cout << pc2 << ": ";
    pc2->Show();

    JustTesting *pc3, *pc4;
    pc3 = new (buffer) JustTesting("Bad Idea", 6);
    pc4 = new JustTesting("Heap2", 10);
    cout << "Memory contents:\n";
    cout << pc3 << ": ";
    pc3->Show();
    cout << pc4 << ": ";
    pc4->Show();
    
    delete pc2;                          // free Heap1         
    delete pc4;                          // free Heap2
    delete [] buffer;                    // free buffer
    cout << "Done\n";
    // std::cin.get();
    return 0;
}

```

**输出示例：**

```
Just Testing constructed
Heap1 constructed
Memory block addresses:
buffer: 0xf61e80    heap: 0xf62090
Memory contents:
0xf61e80: Just Testing, 0
0xf62090: Heap1, 20
Bad Idea constructed
Heap2 constructed
Memory contents:
0xf61e80: Bad Idea, 6
0xf620c0: Heap2, 10
Heap1 destroyed
Heap2 destroyed
Done
```

**①覆盖问题**

尝试读懂代码的执行逻辑并观察输出示例，我们不难发现 `Just Testing`（pc1，调用了默认构造函数）和 `Bad Idea`（pc3，调用了显式构造函数和复制构造函数）两块存储的内存是相同的，都是 `buffer: 0xf61e80`的内存。

这会带来一个比较严重的问题，我们的初衷是在分配好的内存块上同时储存pc1和pc3两个指针指向的对象的内存，但是新对象（pc3指向的对象）**在程序中覆盖掉了原来pc1所指向的对象，导致值的丢失**。例如，如果我在代码的44行后加上`pc1->Show();`输出的结果会是`Bad Idea, 6`，原来pc1指向的对象的值已经被覆盖。

因此，程序员必须手动管理定位new运算符，使不同的指针指向不同区域的内存，互不冲突。修改方式如下：

```C++
pc1=new(buffer) JustTesting;
pc3=new(buffer+sizeof(JustTesting)) JustTesting("Better idea",6);
```

在定义pc3的时候，加上了`sizeof(JustTesting)`，保证了两块被分配的内存不会产生重叠。

**②何时delete？**

**使用定位new运算符分配的内存在delete时要格外的小心！**

```C++
delete pc2;                          // free Heap1         
delete pc4;                          // free Heap2
delete [] buffer;                    // free buffer
cout << "Done\n";
```

源代码中`delete`掉了所有动态分配的内存，包括p2，p4。p1，p3由于使用了定位new运算符，因此在delete掉buffer的内存指定区域后，p1，p3自然也就被delete掉了。这并不难理解，但是请注意以下操作是非法的！

```C++
delete pc2;                          // free Heap1         
delete pc4;                          // free Heap2
delete pc1;//INVALID!
delete pc3;//INVALID!
```

原书中这一段讲的非常清楚并且直白，直接贴上来：

原因在于 delete 可与常规 new 运算符配合使用，但**不能与定位 new 运算符配合使用**。例如，指针 pc3 没有收到 new 运算符返回的地址，因此 **delete pc3 将导致运行阶段错误**。在另一方面，指针pc1 指向的地址与 buffer 相同， 但 buffer 是使用 new []初始化的，因此必须使用 delete [ ]而不是 delete来释放。即使 buffer 是使用 new 而不是 new []初始化的， delete pc1 也将释放 buffer，而不是 pc1。这是因为 new/delete 系统知道已分配的 512 字节块 buffer，但对定位 new 运算符对该内存块做了何种处理一无所知。  

> 总之一句话：**不能混着用**！

那么，如何只delete我们定义的pc1和pc3而不delete掉整块buffer内存呢？我们可以**显示地为定位new运算符创建的对象调用析构函数，销毁指定的对象。**

```C++
    pc3 = new (buffer + sizeof (JustTesting))
                JustTesting("Better Idea", 6);
    pc4 = new JustTesting("Heap2", 10);
    
    cout << "Memory contents:\n";
    cout << pc3 << ": ";
    pc3->Show();
    cout << pc4 << ": ";
    pc4->Show();
    
    delete pc2;           // free Heap1         
    delete pc4;           // free Heap2
    // explicitly destroy placement new objects
    pc3->~JustTesting();  // destroy object pointed to by pc3
    pc1->~JustTesting();  // destroy object pointed to by pc1
    //这样的操作保证了我可以只delete两个指针指向的动态内存，但不delete掉buffer内存
    delete [] buffer;     // free buffer
```

## ADT for queue

因为篇幅限制，本博客不再转载这部分内容，而将其移动到`数据结构和算法部分`更新~

## References

- 《C++ Primer Plus》

> THE END 2024/12/7
