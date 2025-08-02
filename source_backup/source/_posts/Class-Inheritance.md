---
title: Class-Inheritance
date: 2024-12-08 14:02:57
index_img: /img/cover/inheritance.jpg
excerpt: In this article, you will learn another core concept in object-oriented programming which are inheritance and polymorphism, including polymorphic public inheritance, virtual functions and ABC.
categories:
  - Code
  - Cpp
tags:
  - tutorial
  - Finished
  - OOP
  - C++ Primer Plus
  - Inheritance
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# C++ Primer Plus Tutorial-13

# 面向对象编程教程——Section③

<center><p style="color: red;"><b><font size=6.5>Chapter 13 Class Inheritance</font></b></p></center>

<center><p style="color: red;"><b><font size=6.5>类的继承</font></b></p></center>

【写在前面的话】

[C++ Primer Plus Tutorial](https://xiyuanyang-code.github.io/posts/C-plus-plus-Primer-Plus-tutorial/)

[系列文章](https://xiyuanyang-code.github.io/tags/OOP/)

## Abstract

在本文中，你将学到面向对象编程中的又一个核心思想：继承与多态。本文从一个基本的基类讲起并延伸到派生类的定义和基本使用，探讨派生类和基类的特殊关系。接下来本文深入继承的原理，介绍在多态公有继承中的**is-a**关系和虚函数，已经动态联编和静态联编的基本知识。接着本文介绍了抽象基类的使用提高效率以及在派生类中使用动态内存分配的注意事项。最后本文对OOP的基本精神——封装、继承与多态和相关基础知识做了梳理。

> In this article, you will learn another core concept in object-oriented programming: inheritance and polymorphism. The article begins with a basic base class and extends to the definition and basic usage of derived classes, exploring the special relationship between derived and base classes. Next, the article delves into the principles of inheritance, introducing the **is-a** relationship in polymorphic public inheritance, virtual functions, and the basics of dynamic and static binding. Then, the article discusses the use of abstract base classes to improve efficiency, as well as considerations for using dynamic memory allocation in derived classes. Finally, the article summarizes the fundamental principles of OOP—encapsulation, inheritance, and polymorphism—along with related foundational knowledge.

## Table of Contents

- 基类函数和派生类函数的简单示例
  - 派生类的构造函数
  - 使用派生类
  - 派生类和基类的特殊关系
- 继承
  - 多态公有继承
  - 虚函数
  - 静态联编和动态联编
- 抽象基类
- 继承和动态内存分配
- 对面向对象基本知识的总结

## Introduction

在程序设计中，代码的重用是非常重要的，这一点在面向对象编程也不例外。一种最简单的“代码重用”是最简单的`Ctrl-C,Ctrl-V`程序员，手动修改代码实现重用（乐）。那么，我们如何在不修改代码的基础上实现代码（对于OOP而言，主要是类和对象）的重用呢？这便是**类的继承**：从已有的类（基类）将特征和方法继承给一个新的类（派生类）。同时，类的继承也保证了安全性，派生类可以提供新特性，甚至不需要访问源代码就可以派生出类，实现**数据的封装**。（这不正是OOP的核心和精髓吗）

接下来的一章，让我们走进类继承的世界。

## Example: Base class and derived class

文章首先给出一个比较简单的基类

```C++
// tabtenn0.h -- a table-tennis base class
#ifndef TABTENN0_H_
#define TABTENN0_H_
#include <string>
using std::string;
// simple base class
class TableTennisPlayer
{
private:
    string firstname;
    string lastname;
    bool hasTable;
public:
    TableTennisPlayer (const string & fn = "none",const string & ln = "none", bool ht = false);
    //默认构造函数
    void Name() const;
    //输出会员的姓名
    bool HasTable() const { return hasTable; };
    //判断会员是否有球桌
    void ResetTable(bool v) { hasTable = v; };
    //重设球桌状态
};
#endif

```

```C++
//tabtenn0.cpp -- simple base-class methods
#include "tabtenn0.h"
#include <iostream>

TableTennisPlayer::TableTennisPlayer (const string & fn, 
    const string & ln, bool ht) : firstname(fn),lastname(ln), hasTable(ht) {}
    
void TableTennisPlayer::Name() const
{
    std::cout << lastname << ", " << firstname;
}

```

```C++
// usett0.cpp -- using a base class
#include <iostream>
#include "tabtenn0.h"

int main ( void )
{
    using std::cout;
    TableTennisPlayer player1("Chuck", "Blizzard", true);
    TableTennisPlayer player2("Tara", "Boomdea", false);
    player1.Name();
    if (player1.HasTable())
        cout << ": has a table.\n";
    else
        cout << ": hasn't a table.\n";
    player2.Name();
    if (player2.HasTable())
        cout << ": has a table";
    else
        cout << ": hasn't a table.\n";
    // std::cin.get();
    return 0;
}

```

现在，我要在这个类的基础之上派生出一个新的类，来记录乒乓球运动员在比赛中的得分。

先来看写好的头文件，再来逐行解析：

```C++
// tabtenn1.h -- a table-tennis base class
#ifndef TABTENN1_H_
#define TABTENN1_H_
#include <string>
using std::string;
// simple base class
class TableTennisPlayer
{
private:
    string firstname;
    string lastname;
    bool hasTable;
public:
    TableTennisPlayer (const string & fn = "none",const string & ln = "none", bool ht = false);
    void Name() const;
    bool HasTable() const { return hasTable; };
    void ResetTable(bool v) { hasTable = v; };
};

// simple derived class
class RatedPlayer : public TableTennisPlayer
{
private:
    unsigned int rating;
public:
    RatedPlayer (unsigned int r = 0, const string & fn = "none",
                 const string & ln = "none", bool ht = false);
    RatedPlayer(unsigned int r, const TableTennisPlayer & tp);
    unsigned int Rating() const { return rating; }
    void ResetRating (unsigned int r) {rating = r;}
};

#endif

```

很容易的看出，`RatedPlayer`是从`TableTennisPlayer`派生出来的派生类。最基本的语法定义如下：

```C++
class RatedPlayer : public TableTennisPlayer
{
private:
//your Declaration here
public:
//your Declaration here
};

```

冒号指出 `RatedPlayer` 类的基类是 `TableTennisplayer` 类。上述特殊的声明头表明 `TableTennisPlayer` 是一个**公有基类**，这被称为**公有派生**。**派生类对象包含基类对象**。使用公有派生，**基类的公有成员将成为派生类的公有成员**；**基类的私有部分也将成为派生类的一部分**，但只能通过基类的公有和保护方法访问（稍后将介绍保护成员）。

`Ratedplayer`对象将具有以下特征：

- 派生类对象存储了基类的数据成员（派生类继承了基类的实现）；
- 派生类对象可以使用基类的方法（派生类继承了基类的接口）。

> 派生类就像基类的“儿子”，子承父业，可以直接继承父亲的所有遗产。

这是**继承**的部分，同时，派生类也可以实现自己的新方法和新成员，这也在3~6行的代码处定义。

- 派生类**需要**自己的构造函数（**这很重要！**）
- 派生类可以添加额外的数据方法和数据成员。

### 派生类的构造函数：访问权限的考虑

```C++
    RatedPlayer (unsigned int r = 0, const string & fn = "none",
                 const string & ln = "none", bool ht = false);
    RatedPlayer(unsigned int r, const TableTennisPlayer & tp);
```

上文代码是派生类中实现的两个构造函数，可以看出，构造函数要同时定义**原有基类的数据成员和新添加的数据成员**（第一个构造函数）。当然，偷懒一点也是可以的，可以在构造函数中使用到**基类的对象**（第二个构造函数）。

但是，构造函数的设计并不是像想象的那么简单。前文的`Introduction`讲过，派生类甚至可以在不访问基类的情况下实现继承。实际上，**派生类不可以直接访问基类的私有成员，而必须通过基类定义的方法进行访问（和外部的函数一样，只能通过接口进行访问）**，换句话来说，**派生类构造函数必须使用基类构造函数**。

#### 第一种构造函数

这点就显得非常矛盾了，因为从上文第一个构造函数的视角看过去，我们貌似**给派生类的每一个私有成员提供了数据，貌似可以直接访问所有的私有成员，包括基类和派生类**。实际上，并不是这样，下面我们来详细解释这一点。

我们更进一步，给出第一个构造函数的声明和定义：

```C++
//函数声明
RatedPlayer (unsigned int r = 0, const string & fn = "none", const string & ln = "none", bool ht = false);

//函数定义
RatedPlayer::RatedPlayer(unsigned int r, const string & fn,const string & ln, bool ht) : TableTennisPlayer(fn, ln, ht)
{
    rating = r;
}
```

在创建派生类对象时，程序首先会创建**基类对象**。因此，在使用派生类对象的构造函数时，对应的基类对象已经存在。

> 这很好理解，儿子出生之前，爸爸肯定得出生。

理解了这一点后，我们再来看函数的定义，我们会发现很独特的一行，`TableTennisPlayer(fn, ln, ht)`。这行代码叫**成员初始化列表**，是可执行的代码，用来调用基类`TableTennisPlayer`的构造函数。`fn, ln, ht`作为三个形式参数，接受实参并将其本身传递给`TableTennisPlayer`构造函数的形式参数，后者将创建一个**嵌套的基类对象**（先创建爸爸），接着，进入派生类函数的函数体，完成对派生类对象的创建。（再创建儿子）

如果省略了成员初始化列表，那么程序会调用**默认的构造函数**创建一个基类对象，而无法使用显式的构造函数，程序就无法读取到我们希望赋给对象的数据。

```C++
//没有成员初始化列表的情况
RatedPlayer::RatedPlayer(unsigned int r, const string & fn,const string & ln, bool ht) : 
{
    rating = r;
}

//与下面的代码等效
RatedPlayer::RatedPlayer(unsigned int r, const string & fn,const string & ln, bool ht) : 
TableTennisPlayer()
//使用无参数的默认构造函数
{
    rating = r;
}
```

#### 第二种构造函数

接下来，我们来看第二种更加简洁的构造函数：

```C++
//函数声明
RatedPlayer(unsigned int r, const TableTennisPlayer & tp);

//函数定义
RatedPlayer::RatedPlayer(unsigned int r, const TableTennisPlayer & tp)
    : TableTennisPlayer(tp)
{
        rating=r;
}

//函数定义的等价形式
RatedPlayer::RatedPlayer(unsigned int r, const TableTennisPlayer & tp)
    : TableTennisPlayer(tp), rating(r)
{
}

```

来看这里的初始化成员列表，传入的参数是一个`const TableTennisPlayer &`类型，因此，系统会调用默认的**复制构造函数**（这是个很有趣的问题，我们在[第十二章](https://xiyuanyang-code.github.io/posts/Dynamic-Memory-and-Classes/)的时候重点讨论了，这里使用默认的复制构造函数是不会产生任何问题的）。

当然，我们也可以对**派生类成员使用初始化列表**，在这种情况下，应该使用成员名而不是类名。

> 释放对象的顺序与创建对象的顺序相反，即首先执行派生类的析构函数，然后自动调用基类的析构函数。  

{% note success %}
**小总结**： 创建派生类对象时，程序首先**调用基类构造函数**，然后再**调用派生类构造函数**。基类构造函数负责初始化继承的数据成员；派生类构造函数主要用于初始化新增的数据成员。派生类的构造函数总是调用一个基类构造函数。可以使用初始化器列表语法指明要使用的基类构造函数，否则将使用默认的基类构造函数。派生类对象过期时，程序将首先调用派生类析构函数，然后再调用基类析构函数。
{% endnote %}

现在，我们可以给出派生类的全部函数定义：

```C++
//tabtenn1.cpp -- simple base-class methods
#include "tabtenn1.h"
#include <iostream>

TableTennisPlayer::TableTennisPlayer (const string & fn, 
    const string & ln, bool ht) : firstname(fn),lastname(ln), hasTable(ht) {}
    
void TableTennisPlayer::Name() const
{
    std::cout << lastname << ", " << firstname;
}

// RatedPlayer methods
RatedPlayer::RatedPlayer(unsigned int r, const string & fn,
    const string & ln, bool ht) : TableTennisPlayer(fn, ln, ht)
{
    rating = r;
}

RatedPlayer::RatedPlayer(unsigned int r, const TableTennisPlayer & tp)
    : TableTennisPlayer(tp), rating(r)
{
}

```

### 使用派生类

```C++
// usett1.cpp -- using base class and derived class
#include <iostream>
#include "tabtenn1.h"

int main ( void )
{
    using std::cout;
    using std::endl;
    TableTennisPlayer player1("Tara", "Boomdea", false);
    RatedPlayer rplayer1(1140, "Mallory", "Duck", true);
    rplayer1.Name();          // derived object uses base method
    if (rplayer1.HasTable())
        cout << ": has a table.\n";
    else
        cout << ": hasn't a table.\n";
    player1.Name();           // base object uses base method
    if (player1.HasTable())
        cout << ": has a table";
    else
        cout << ": hasn't a table.\n";
    cout << "Name: ";
    rplayer1.Name();
    cout << "; Rating: " << rplayer1.Rating() << endl;
// initialize RatedPlayer using TableTennisPlayer object
    RatedPlayer rplayer2(1212, player1);
    cout << "Name: ";
    rplayer2.Name();
    cout << "; Rating: " << rplayer2.Rating() << endl;
    // std::cin.get();
    return 0;
}

```

### 派生类和基类的特殊关系

用形象的话说，派生类是儿子，继承自基类（爸爸）的方法和数据成员。但是，基类又有自己的“隐私”，派生类是能通过公共接口实现对基类的访问，即无法直接访问其私有成员。

> 派生类相当于一种**在定义和结构上**获得“权限”的类，权限在于可以直接继承基类所定义好的成员和方法（在定义层面的**继承**），但是**在操作上**，派生类并没有从基类处获得任何特权，不能访问的还是不能访问。

从这条出发，我们可以归纳派生类和基类之间的特殊关系：

- 派生类对象可以使用基类的方法，条件是**方法不是私有的**。
- **基类指针可以在不进行显示转换的情况下指向派生类对象。**
- **基类引用可以在不进行显示转换的情况下引用派生类对象。**

> 二、三两条非常的重要，**指针和引用类型的兼容性**确保了其可以在基类和派生类之间反复横跳，更加的灵活。（同时也更加的复杂和危险）

#### 基类指针可以在不进行显示转换的情况下指向派生类对象

```C++
class Base {
public:
    virtual void display() { cout << "Base class" << endl; }
};

class Derived : public Base {
public:
    void display() override { cout << "Derived class" << endl; }
};

int main() {
    Derived d;
    Base* basePtr = &d; // 基类指针指向派生类对象
    basePtr->display(); // 输出: Derived class (多态行为)
    return 0;
}

```

#### 基类引用可以在不进行显示转换的情况下引用派生类对象。

```C++
class Base {
public:
    virtual void display() { cout << "Base class" << endl; }
};

class Derived : public Base {
public:
    void display() override { cout << "Derived class" << endl; }
};

int main() {
    Derived d;
    Base& baseRef = d; // 基类引用绑定到派生类对象
    baseRef.display(); // 输出: Derived class (多态行为)
    return 0;
}

```

> 注意关键词`virtual`，之后会讲到，否则会发生静态绑定现象。

- 基类指针和引用只能调用基类方法，而不可以调用派生类的方法。
  - 因为派生类允许添加新的数据成员，如果允许调用会产生很多奇怪的问题。
- 基类指针和应用可以指向派生类的对象
  - [这篇讲的很清楚](https://blog.csdn.net/goodgoodstudy___/article/details/124905482)
  - 这里涉及到一些有关虚函数的知识点，暂时先跳过。
- **基类对象也可以被派生类对象初始化（或赋值）**

```C++
RatedPlayer player1(1840,"o1","Loaf",true);
TableTennisPlayer player2(player1);    //VALID
```

可以用**引用兼容性**的属性来解释这个问题，调用基类的构造函数时不存在匹配的构造函数，故会调用隐式复制构造函数`TableTennisPlayer(const TableTennisPlayer&)`，形参是**对基类的引用**，**可以在不进行显式转换的**情况下引用派生类对象`player1`。因此，复制构造函数将嵌套在`player1`基类中的`TableTennisPlayer`赋值给了`player2`。

## Inheritance

派生类和基类的特殊关系在本质上基于C++继承的底层模型。C++有常见的三种继承方式：

- 公有继承（**is-a关系**）

  - 派生类也是一个基类对象，可以执行基类对象执行的任何操作。
  - **is-a：is a kind of**（往往是抽象与具象的关系）

  > 如果一个派生类选择以**公有继承**的方式，那么应该可以被改写成**XX is a kind of XXX**的形式。例如，香蕉类公有继承水果类（香蕉类可以使用水果类的所有数据和方法，同时香蕉也可以定义自己的数据成员和方法），可以说Banana is a kind of fruit.

  > 除了**is a**还有什么关系？有很多，比如**has-a, is-like-a, is-implemented-as-a, uses-a**等等关系，不过，这些都不适合使用公有继承的方式实现。

- 保护继承

- 私有继承

### 多态公有继承

在传统的继承关系中，派生类可以使用基类的成员函数（只要他不是私有成员）。但有时，派生类希望个性化地修改基类成员函数的方法来实现个性化的目的，因此有必要对基类成员函数进行修改，而非**完全实现**。这种思想被称作**多态**，这种继承方式也被称作多态公有继承。

#### 例子：`Brass Plus类`和`Brass类`

书上给出了另一个有关银行的例子，有关类的实现原理请自行阅读。

> 现在来看另一个例子。由于 Webtown 俱乐部的工作经历，您成了 Pontoon 银行的首席程序员。银行要求您完成的第一项工作是开发两个类。一个类用于表示基本支票账户—Brass Account，另一个类用于表示代表 Brass Plus 支票账户，它添加了透支保护特性。也就是说，如果用户签出一张超出其存款余额的支票——但是超出的数额并不是很大，银行将支付这张支票，对超出的部分收取额外的费用，并追加罚款。可以根据要保存的数据以及允许执行的操作来确定这两种账户的特征。
>
> 下面是用于 Brass Account 支票账户的信息：
>
> - 客户姓名；
> - 账号；
> - 当前结余。
>
> 下面是可以执行的操作：
>
> - 创建账户；
> - 存款；
> - 取款；
> - 显示账户信息。
>
> Pontoon 银行希望 Brass Plus 支票账户包含 Brass Account 的所有信息及如下信息：
>
> - 透支上限；
> - 透支贷款利率；
> - 当前的透支总额。
>
> 不需要新增操作，但有两种操作的实现不同：
>
> - 对于取款操作，必须考虑透支保护；
> - 显示操作必须显示 Brass Plus 账户的其他信息。
>
> 假设将第一个类命名为 Brass，第二个类为 BrassPlus。应从 Brass 公有派生出 BrassPlus 吗？要回答这个问题，必须先回答另一个问题： BrassPlus 类是否满足 is-a 条件？当然满足。对于 Brass 对象是正确的事情，对于 BrassPlus 对象也是正确的。它们都将保存客户姓名、账号以及结余。使用这两个类都可以存款、取款和显示账户信息。请注意， is-a 关系通常是不可逆的。也就是说，水果不是香蕉；同样， Brass 对象不具备 BrassPlus 对象的所有功能。

我们直接来看代码，以下是有关两个类实现的头文件声明：

```C++
// brass.h  -- bank account classes
#ifndef BRASS_H_
#define BRASS_H_
#include <string>
// Brass Account Class
class Brass
{
private:
    std::string fullName;
    long acctNum;
    double balance;
public:
    Brass(const std::string & s = "Nullbody", long an = -1,
                double bal = 0.0);
    void Deposit(double amt);
    virtual void Withdraw(double amt);
    double Balance() const;
    virtual void ViewAcct() const;
    virtual ~Brass() {}
};

//Brass Plus Account Class
class BrassPlus : public Brass
{
private:
    double maxLoan;
    double rate;
    double owesBank;
public:
    BrassPlus(const std::string & s = "Nullbody", long an = -1,
            double bal = 0.0, double ml = 500,
            double r = 0.11125);
    BrassPlus(const Brass & ba, double ml = 500, 
		                        double r = 0.11125);
    virtual void ViewAcct()const;
    virtual void Withdraw(double amt);
    void ResetMax(double m) { maxLoan = m; }
    void ResetRate(double r) { rate = r; };
    void ResetOwes() { owesBank = 0; }
};

#endif
```

我们发现有以下几点：

- `BrassPlus`类和`Brass`类都定义了同名函数`Withdraw()`和`Viewacct()`。
  - 编译器将根据对象的类型自动选择使用哪个类定义中的函数。
- 出现了新关键词`virtual`**（虚方法）**

#### 虚函数

函数`Withdraw()`和`Viewacct()`不同于一般的方法 ，在基类和派生类中都有自己的定义（而且定义一般是不同的），这些函数在面向对象编程中被称为**虚函数**。

{% note info %}

虚函数是指一个**在基类中声明并可以在派生类中重写的成员函数**。虚函数的主要作用是实现**运行时多态**（Runtime Polymorphism），即程序在执行过程中根据对象的实际类型调用相应的函数版本，而不是**根据指针或引用的类型来决定**。

{% endnote %}

下面我们来详细解释一下这个定义，请看如下代码：

```C++
#include <iostream>
using namespace std;

class Base {
public:
    virtual void show() {
        cout << "Base class show" << endl;
    }
};

class Derived : public Base {
public:
    void show() override {
        cout << "Derived class show" << endl;
    }
};

int main() {
    Base* basePtr;  // 基类指针
    Derived derivedObj;  // 派生类对象
    
    basePtr = &derivedObj;  // 基类指针指向派生类对象
    
    // 虚函数的调用
    basePtr->show();  // 运行时会调用派生类的 show()，输出: Derived class show
    
    return 0;
}

```

在这里，指针`basePtr`的**定义类型**是Base（基类），但是指向一个派生类的对象（上文提到过这种行为是合法的）。那么在下一行调用`show`函数的时候是应该**优先考虑指针本身的类型**，还是考虑**指针指向对象的类型**呢？因此，C++引入了关键词`virtual`来声明一个虚函数，对于虚函数而言，在调用时会**根据对象的实际类型来调用相对应的同名函数**，比如在上文由于baseptr指向的**实际对象**是一个派生类的对象，所以会**调用派生类的函数**。

> 如果不加virtual关键词，编译器会报错（这个需要更加深入的知识了）

有意思的是，基类`Brass`的虚构函数同样使用了`virtual`关键词来定义，这样做是为了确保释放派生对象时，按照正确的顺序调用析构函数。（这形成了一种惯例，后文会再提到）

接下来，我们来看两个类的具体定义。

```C++
// brass.cpp -- bank account class methods
#include <iostream>
#include "brass.h"
using std::cout;
using std::endl;
using std::string;

// formatting stuff
typedef std::ios_base::fmtflags format;
typedef std::streamsize precis;
format setFormat();
void restore(format f, precis p);

// Brass methods

Brass::Brass(const string & s, long an, double bal)
{
    fullName = s;
    acctNum = an;
    balance = bal;
}

void Brass::Deposit(double amt)
{
    if (amt < 0)
        cout << "Negative deposit not allowed; "
             << "deposit is cancelled.\n";
    else
        balance += amt;
}

void Brass::Withdraw(double amt)
{
    // set up ###.## format
    format initialState = setFormat();
    precis prec = cout.precision(2);

    if (amt < 0)
        cout << "Withdrawal amount must be positive; "

             << "withdrawal canceled.\n";
    else if (amt <= balance)
        balance -= amt;
    else
        cout << "Withdrawal amount of $" << amt
             << " exceeds your balance.\n"
             << "Withdrawal canceled.\n";
    restore(initialState, prec);
}
double Brass::Balance() const
{
    return balance;
}

void Brass::ViewAcct() const
{
     // set up ###.## format
    format initialState = setFormat();
    precis prec = cout.precision(2);
    cout << "Client: " << fullName << endl;
    cout << "Account Number: " << acctNum << endl;
    cout << "Balance: $" << balance << endl;
    restore(initialState, prec); // Restore original format
}

// BrassPlus Methods
BrassPlus::BrassPlus(const string & s, long an, double bal,
           double ml, double r) : Brass(s, an, bal)
{
    maxLoan = ml;
    owesBank = 0.0;
    rate = r;
}

BrassPlus::BrassPlus(const Brass & ba, double ml, double r)
           : Brass(ba)   // uses implicit copy constructor
{
    maxLoan = ml;
    owesBank = 0.0;
    rate = r;
}

// redefine how ViewAcct() works
void BrassPlus::ViewAcct() const
{
    // set up ###.## format
    format initialState = setFormat();
    precis prec = cout.precision(2);

    Brass::ViewAcct();   // display base portion
    //注意：这里必须要使用作用域解析运算符，否则会发生无终止递归。（因为编译器会默认认为这里的ViewAcct是调用BrassPlus类中的方法）
    cout << "Maximum loan: $" << maxLoan << endl;
    cout << "Owed to bank: $" << owesBank << endl;
    cout.precision(3);  // ###.### format
    cout << "Loan Rate: " << 100 * rate << "%\n";
    restore(initialState, prec); 
}

// redefine how Withdraw() works
void BrassPlus::Withdraw(double amt)
{
    // set up ###.## format
    format initialState = setFormat();
    precis prec = cout.precision(2);

    double bal = Balance();
    if (amt <= bal)
        Brass::Withdraw(amt);
    else if ( amt <= bal + maxLoan - owesBank)
    {
        double advance = amt - bal;
        owesBank += advance * (1.0 + rate);
        cout << "Bank advance: $" << advance << endl;
        cout << "Finance charge: $" << advance * rate << endl;
        Deposit(advance);
        Brass::Withdraw(amt);
    }
    else
        cout << "Credit limit exceeded. Transaction cancelled.\n";
    restore(initialState, prec); 
}

format setFormat()
{
    // set up ###.## format
    return cout.setf(std::ios_base::fixed, 
                std::ios_base::floatfield);
} 

void restore(format f, precis p)
{
    cout.setf(f, std::ios_base::floatfield);
    cout.precision(p);
}

```

> 代码除了注意在定义同名函数的时候**使用作用域运算符以防混淆**，其他都和类的继承的语法知识点没有太多的联系，可自行跳过。

### 静态联编和动态联编

**函数名联编：**将源代码的函数调用解释为执行特定的函数代码块。

如果编译器在**编译时**完成了这种联编，那么被称为**静态联编**。但是，由于虚函数的存在，OOP的**多态性**让这种行为变得困难（虚函数保证了调用的函数和对象的实际类型相匹配，而基类指针即可以指向派生类也可以指向基类，这就让编译器很难确定调用哪一个函数）。因此，编译器生成一种**在程序运行时选择正确的虚方法的代码**，这种方法被称为**动态联编**。

> 和**动态内存分配**有着异曲同工之妙！如果需要再程序运行时确定数组的大小N，则需要使用动态内存分配的方法在程序运行时动态地在**堆**上分配相匹配的内存容量。

#### 指针和引用类型的兼容性

（这一块有点复杂并且无聊，笔者直接给出结论）

- **向上强制转换**：将派生类应用或指针转换为基类引用或指针
  - 不需要进行显式类型转换（is-a关系可以看做一种子集关系）
  - 具有可传递性和兼容性
- **向下强制转换**：将基类应用或指针转换为派生类引用或指针
  - 必须使用显式类型转换

```C++
#include <iostream>
using namespace std;

class Base {
public:
    virtual void display() {  // 基类的虚函数
        cout << "Base class display" << endl;
    }
    virtual ~Base() {}  // 虚析构函数，确保派生类对象能够被正确销毁
};

class Derived : public Base {
public:
    void display() override {  // 派生类重写 display 函数
        cout << "Derived class display" << endl;
    }
    void show() {  // 派生类的特有函数
        cout << "Derived class show" << endl;
    }
};

int main() {
    // 向上转换
    Derived derivedObj;
    Base* basePtr = &derivedObj;  // Derived* -> Base* 向上转换

    // 使用 basePtr 调用 display()，实际调用的是 Derived 的 display()
    cout << "Using basePtr (upcasting):" << endl;
    basePtr->display();  // 输出: Derived class display

    // 向下转换
    Derived* derivedPtr = dynamic_cast<Derived*>(basePtr);  // Base* -> Derived* 向下转换
    if (derivedPtr) {
        cout << "Using derivedPtr (downcasting):" << endl;
        derivedPtr->show();  // 输出: Derived class show
    } else {
        cout << "Downcasting failed" << endl;
    }

    // 向下转换失败的例子
    Base* anotherBasePtr = new Base();  // 创建一个纯 Base 类型的对象
    Derived* failedCastPtr = dynamic_cast<Derived*>(anotherBasePtr);  // Base* -> Derived* 向下转换（尝试转换但是转换失败）
    if (failedCastPtr) {
        failedCastPtr->show();  // 不会执行到这里
    } else {
        cout << "Failed downcasting Base* to Derived*" << endl;  // 输出: Failed downcasting Base* to Derived*
    }

    delete anotherBasePtr;  // 释放内存
    return 0;
}

```

#### 虚函数成员和动态联编

> 在原书中，此节介绍了三部分知识：
>
> - 为什么有两种类型的联编
> - 动态联编为何不设置为默认的联编方式
> - 动态联编的工作原理
>   - 虚函数表

在本文中，我们暂且跳过这一部分，把重点放在虚函数使用的相关注意事项：

{% note info %}

- 在基类方法的声明中使用关键字 `virtual` 可使该方法在基类以及所有的派生类（包括从派生类派生出来的类）中是虚的。

- 如果使用指向对象的引用或指针来调用虚方法，程序将使用为对象类型定义的方法，而不使用为引用或指针类型定义的方法。这称为动态联编或晚期联编。这种行为非常重要，因为这样**基类指针或引用可以指向派生类对象**。

- 如果定义的类将被用作基类，则应将那些要在派生类中重新定义的类方法声明为虚的。

- 构造函数不能是虚函数。

  - 创建派生类对象时会调用基类的构造函数，所以没啥意义。（反正肯定会被调用）

- **析构函数应当是虚函数。**

  - 如果派生类新定义了动态内存分配，那么在调用时如果基类的析构函数不是虚函数，那么如果一个基类指针指向了派生类对象，在调用析构函数的时候会**调用基类的析构函数**，这在派生类中新定义的成员不会被`delete`掉，产生内存泄漏。

- 友元不能是虚函数（友元不是类成员）

- 重新定义的问题

  - 如果派生类没有重新定义函数，就直接使用基类的版本。

  - 如果派生类位于派生链中（一串派生类），那么使用**最新的虚函数版本**。

  - **重新定义基类的方法不是重载。**如果在派生类中重新定义函数，将不是使用相同的函数特征标覆盖基类声明，而是隐藏同名的基类方法，不管参数特征标如何。

    - 如果重新定义继承的方法，应确保与原来的原型完全相同，但如果返回类型是基类引用或指针，则可以修改为指向派生类的引用或指针（这种例外是新出现的）。这种特性被称为返回类型协变（covariance of return type），因为允许返回类型随类类型的变化而变化。

    - 如果基类声明被重载，应该在派生类中重新定义所有的基类版本。

{% endnote %}

![Covirance of Return Type](https://ooo.0x0.ooo/2025/01/11/OEfRai.png)

![基类声明被重载](https://ooo.0x0.ooo/2025/01/11/OEftNX.png)

## Protected

我们使用`public`和`private`来实现对类成员访问权限的控制。同时，我们存在`protected`关键词。

- 与`private`相比，`protected`可以被派生类成员直接访问。
- 与`public`相比，`protected`**只能**被派生类成员直接访问，而不能在外部直接访问。

> 通过protected的实现，基类终于给派生类赋予了一些在访问上的**特权**！

## Abstract Base Class

在is-a关系中，我们希望**派生类 is a kind of 基类**，因为这样派生类使用基类的成员方法才有意义。但是有些时候使用基类的方法会成为累赘。

> 例如圆是一种特殊的椭圆。在基类椭圆类中，我们可能会定义椭圆的一些参数和方法，比如长轴短轴离心率，在继承给圆的时候，当然可以把**圆看做一种特殊的椭圆**。但是实际上如果从零开始设计圆类，我们可能只需要圆心和半径即可，这样基类的方法反而是一种使问题变的更加复杂的累赘。

因此，我们可以**从圆和椭圆中提取出抽象的共性，放在一个抽象基类（ABC）中，比如计算面积等方法**，实现代码的重用。如果抽象基类没有包含必要的数据成员来实现有共性的函数，C++使用**纯虚函数（pure virtual function）**提供未实现的函数。

> ABC是一种很高深的思想，万事万物的本质原理是什么？亚里士多德提出了**第一性原理**的阐释。在C++中，抽象基类便像第一性原理，后续的派生类都建立在抽象基类的基础之上。真可谓一生二，二生三，三生万物。这便是ABC的哲学！

我们来看代码的示例：

```C++
// acctabc.h  -- bank account classes
#ifndef ACCTABC_H_
#define ACCTABC_H_
#include <iostream>
#include <string>

// Abstract Base Class
class AcctABC
{
private:
    std::string fullName;
    long acctNum;
    double balance;
protected:
    struct Formatting 
    {
        std::ios_base::fmtflags flag;
        std::streamsize pr;
        //用来设置和恢复格式
    };
    const std::string & FullName() const {return fullName;}
    long AcctNum() const {return acctNum;}
    Formatting SetFormat() const;
    void Restore(Formatting & f) const;
public: 
    AcctABC(const std::string & s = "Nullbody", long an = -1,
                double bal = 0.0);
    void Deposit(double amt) ;
    virtual void Withdraw(double amt) = 0; // pure virtual function
    double Balance() const {return balance;};
    virtual void ViewAcct() const = 0;     // pure virtual function
    virtual ~AcctABC() {}
    //提供纯虚函数接口
};

// Brass Account Class
class Brass :public AcctABC
{
public:
    Brass(const std::string & s = "Nullbody", long an = -1,
           double bal = 0.0) : AcctABC(s, an, bal) { }
    virtual void Withdraw(double amt);
    virtual void ViewAcct() const;
    virtual ~Brass() {}
};

//Brass Plus Account Class
class BrassPlus : public AcctABC
{
private:
    double maxLoan;
    double rate;
    double owesBank;
public:
    BrassPlus(const std::string & s = "Nullbody", long an = -1,
            double bal = 0.0, double ml = 500,
            double r = 0.10);
    BrassPlus(const Brass & ba, double ml = 500, double r = 0.1);
    virtual void ViewAcct()const;
    virtual void Withdraw(double amt);
    void ResetMax(double m) { maxLoan = m; }
    void ResetRate(double r) { rate = r; };
    void ResetOwes() { owesBank = 0; }
};

#endif

```

以下是一些对代码和抽象基类的解读：

- 纯虚函数在声明时需要再尾部加上"0"。
- ABC的主要作用是为**派生类提供一些共性的纯虚函数接口**。

我们来看一看函数定义中纯虚函数是怎么定义的。

```C++
// acctabc.cpp -- bank account class methods
#include <iostream>
#include "acctabc.h"
using std::cout;
using std::ios_base;
using std::endl;
using std::string;

// Abstract Base Class
AcctABC::AcctABC(const string & s, long an, double bal)
{
    fullName = s;
    acctNum = an;
    balance = bal;
}

void AcctABC::Deposit(double amt)
{
    if (amt < 0)
        cout << "Negative deposit not allowed; "<< "deposit is cancelled.\n";
    else
        balance += amt;
}

void AcctABC::Withdraw(double amt)
{
    balance -= amt;
}

// protected methods for formatting
AcctABC::Formatting AcctABC::SetFormat() const
{
 // set up ###.## format
    Formatting f;
    f.flag = 
        cout.setf(ios_base::fixed, ios_base::floatfield);
    f.pr = cout.precision(2);
    return f; 
}

void AcctABC::Restore(Formatting & f) const
{
    cout.setf(f.flag, ios_base::floatfield);
    cout.precision(f.pr);
}

// Brass methods
void Brass::Withdraw(double amt)
{
    if (amt < 0)
        cout << "Withdrawal amount must be positive; "
             << "withdrawal canceled.\n";
    else if (amt <= Balance())
        AcctABC::Withdraw(amt);
    else
        cout << "Withdrawal amount of $" << amt
             << " exceeds your balance.\n"
             << "Withdrawal canceled.\n";
}

void Brass::ViewAcct() const
{
   
    Formatting f = SetFormat();
    cout << "Brass Client: " << FullName() << endl;
    cout << "Account Number: " << AcctNum() << endl;
    cout << "Balance: $" << Balance() << endl;
    Restore(f);
}

// BrassPlus Methods
BrassPlus::BrassPlus(const string & s, long an, double bal,
           double ml, double r) : AcctABC(s, an, bal)
{
    maxLoan = ml;
    owesBank = 0.0;
    rate = r; 
}

BrassPlus::BrassPlus(const Brass & ba, double ml, double r)
           : AcctABC(ba)   // uses implicit copy constructor
{
    maxLoan = ml;
    owesBank = 0.0;
    rate = r;
}

void BrassPlus::ViewAcct() const
{
    Formatting f = SetFormat();

    cout << "BrassPlus Client: " << FullName() << endl;
    cout << "Account Number: " << AcctNum() << endl;
    cout << "Balance: $" << Balance() << endl;
    cout << "Maximum loan: $" << maxLoan << endl;
    cout << "Owed to bank: $" << owesBank << endl;
    cout.precision(3);
    cout << "Loan Rate: " << 100 * rate << "%\n";
    Restore(f);
}

void BrassPlus::Withdraw(double amt)
{
    Formatting f = SetFormat();

    double bal = Balance();
    if (amt <= bal)
        AcctABC::Withdraw(amt);
    else if ( amt <= bal + maxLoan - owesBank)
    {
        double advance = amt - bal;
        owesBank += advance * (1.0 + rate);
        cout << "Bank advance: $" << advance << endl;
        cout << "Finance charge: $" << advance * rate << endl;
        Deposit(advance);
        AcctABC::Withdraw(amt);
    }
    else
        cout << "Credit limit exceeded. Transaction cancelled.\n";
    Restore(f); 
}

```

## 继承和动态内存分配

在阅读这一部分之前，建议读者先阅读[第十二章](https://xiyuanyang-code.github.io/posts/Dynamic-Memory-and-Classes/)了解有关C++中的复制构造函数，析构函数，赋值运算符等等相关知识。本部分将继续围绕在类的继承背景下，如何实现动态内存分配。

### 派生类不使用`new`

先来看这样一个使用动态内存分配的基类：

![baseDMA](https://ooo.0x0.ooo/2025/01/11/OEfJdL.png)

这个基类动态分配内存给一个指向字符的指针（**涉及到了按址传递，故使用默认的复制构造函数会存在风险**），同时也手动定义了显式的赋值运算符，复制构造函数和析构函数。那么，如果以此为基类的派生类没有使用动态内存分配，便**不需要再为派生类显式地定义显式析构函数，复制构造函数和重载赋值运算符。**

> 为什么？你当然可以显式定义，但没有任何的必要。因为默认的赋值运算符和复制构造函数都是在使用动态内存时会产生冲突，基类的动态内存已经手动显示定义，故不存在隐患。
>
> 在复制类所继承的类组件时，会优先使用被继承类的显式构造函数。（对赋值同样也是如此）

### 派生类使用`new`

这便是更加复杂的情况，我们来看一个使用动态内存的派生类：

![Derived Class with DMA.png](https://ooo.0x0.ooo/2025/01/11/OEfJdL.png)

{% note danger %}

**在这种情况下，必须为派生类定义显式析构函数、复制构造函数和赋值运算符 。**因为派生类添加了新的数据成员使用动态内存分配，而基类的方法中并不包含这个成员的操作。因此需要额外定义来防止内存泄漏等严重问题。

{% endnote %}

### 使用友元

来看下面的综合代码：

```C++
// dma.h  -- inheritance and dynamic memory allocation
#ifndef DMA_H_
#define DMA_H_
#include <iostream>

//  Base Class Using DMA
class baseDMA
{
private:
    char * label;
    int rating;
    
public:
    baseDMA(const char * l = "null", int r = 0);
    baseDMA(const baseDMA & rs);
    virtual ~baseDMA();
    //使用虚函数
    baseDMA & operator=(const baseDMA & rs);
    friend std::ostream & operator<<(std::ostream & os, 
                                     const baseDMA & rs);
    //友元函数（流输出运算符）
};

// derived class without DMA
// no destructor needed
// uses implicit copy constructor
// uses implicit assignment operator
class lacksDMA :public baseDMA
{
private:
    enum { COL_LEN = 40};
    char color[COL_LEN];
public:
    lacksDMA(const char * c = "blank", const char * l = "null",
              int r = 0);
    lacksDMA(const char * c, const baseDMA & rs);
    friend std::ostream & operator<<(std::ostream & os, 
                                     const lacksDMA & rs);
};

// derived class with DMA
class hasDMA :public baseDMA
{
private:
    char * style;
public:
    hasDMA(const char * s = "none", const char * l = "null",
              int r = 0);
    hasDMA(const char * s, const baseDMA & rs);
    hasDMA(const hasDMA & hs);
    ~hasDMA();
    hasDMA & operator=(const hasDMA & rs);  
    friend std::ostream & operator<<(std::ostream & os, 
                                     const hasDMA & rs);
};

#endif

```

```C++
// dma.cpp --dma class methods

#include "dma.h"
#include <cstring>

// baseDMA methods
baseDMA::baseDMA(const char * l, int r)
{
    label = new char[std::strlen(l) + 1];
    std::strcpy(label, l);
    rating = r;
}

baseDMA::baseDMA(const baseDMA & rs)
{
    label = new char[std::strlen(rs.label) + 1];
    std::strcpy(label, rs.label);
    rating = rs.rating;
}

baseDMA::~baseDMA()
{
    delete [] label;
}

baseDMA & baseDMA::operator=(const baseDMA & rs)
{
    if (this == &rs)
        return *this;
    delete [] label;
    label = new char[std::strlen(rs.label) + 1];
    std::strcpy(label, rs.label);
    rating = rs.rating;
    return *this;
}
    
std::ostream & operator<<(std::ostream & os, const baseDMA & rs)
{
    os << "Label: " << rs.label << std::endl;
    os << "Rating: " << rs.rating << std::endl;
    return os;
}

// lacksDMA methods
lacksDMA::lacksDMA(const char * c, const char * l, int r)
    : baseDMA(l, r)
{
    std::strncpy(color, c, 39);
    color[39] = '\0';
}

lacksDMA::lacksDMA(const char * c, const baseDMA & rs)
    : baseDMA(rs)
{
    std::strncpy(color, c, COL_LEN - 1);
    color[COL_LEN - 1] = '\0';
}

std::ostream & operator<<(std::ostream & os, const lacksDMA & ls)
{
    os << (const baseDMA &) ls;
    os << "Color: " << ls.color << std::endl;
    return os;
}

// hasDMA methods
hasDMA::hasDMA(const char * s, const char * l, int r)
         : baseDMA(l, r)
{
    style = new char[std::strlen(s) + 1];
    std::strcpy(style, s);
}

hasDMA::hasDMA(const char * s, const baseDMA & rs)
         : baseDMA(rs)
{
    style = new char[std::strlen(s) + 1];
    std::strcpy(style, s);
}

hasDMA::hasDMA(const hasDMA & hs)
         : baseDMA(hs)  // invoke base class copy constructor
{
    style = new char[std::strlen(hs.style) + 1];
    std::strcpy(style, hs.style);
}

hasDMA::~hasDMA()
{
    delete [] style;
}

hasDMA & hasDMA::operator=(const hasDMA & hs)
{
    if (this == &hs)
        return *this;
    baseDMA::operator=(hs);  // copy base portion，赋值基类的数据成员
    //这个语句等价于 *this = hs;
    delete [] style;         // prepare for new style
    style = new char[std::strlen(hs.style) + 1];
    std::strcpy(style, hs.style);
    return *this;
}
    
std::ostream & operator<<(std::ostream & os, const hasDMA & hs)
{
    os << (const baseDMA &) hs;
    //使用强制类型转换，使得该行可以调用基类的友元函数
    os << "Style: " << hs.style << std::endl;
    return os;
}

```

在 C++ 中，**友元函数**的行为与普通成员函数不同。友元函数是 **根据它所声明的类** 来确定是否有权限访问该类的私有和保护成员的，而与对象的动态类型无关。因此，友元函数并不遵循 **多态性** 规则。

例如有以下代码：

```C++
#include <iostream>
using namespace std;

class Base;  // 前置声明

class Base {
private:
    int baseValue;
public:
    Base() : baseValue(10) {}

    // 友元函数声明
    friend void printBaseValue(Base& b);
};

class Derived : public Base {
private:
    int derivedValue;
public:
    Derived() : derivedValue(20) {}

    // 友元函数声明，同名函数
    friend void printBaseValue(Derived& d);
};

// 基类的友元函数定义
void printValue(Base& b) {
    cout << "Base Value: " << b.baseValue << endl;
}

// 派生类的友元函数定义
void printValue(Derived& d) {
    cout << "Derived Value: " << d.derivedValue << endl;
}

int main() {
    Derived d;
    Base* basePtr = &d;  // 基类指针指向派生类对象
    // 使用基类指针调用基类的友元函数
    printValue(*basePtr);  // 将会调用 Base 类的友元函数
    return 0;
}

```

输出结果：`Base Value: 10`

`basePtr`指针的类型是基类指针，但是指向了一个派生类对象。对于成员函数使用**虚函数声明**保证了调用的函数与实际指向的对象类型相符。（会调用派生类的函数）**但友元函数虽然有访问私有数据成员的特权**，但他不是成员函数，因此友元函数根据类的声明位置（即函数特征标的匹配程度）来决定调用的。在这里编译器判定`basePtr`指针的类型是基类指针，因此会调用**基类的`printValue()`**函数，哪怕它指向的是一个派生类对象。

这就是为什么派生类指针如果想使用基类的友元函数必须先**强制类型转换成**一个基类的指针（就像代码的第105~110行）。

## Conclusion

**恭喜你！你已经掌握OOP的基本知识，可以进行一些实操锻炼了！**笔者自己在阅读10,11,12,13四章时非常的痛苦，感觉非常的不适应，但是一路走来，在掌握了OOP的哲学和最基本的原则之后，一切纷繁复杂的语法原理似乎都有理可依。

接下来，我们将系统梳理这四章的一些内容，受篇幅限制，本部分主要以思维导图和关键词的形式呈现，详细内容大家可以看原书的13.8节。

OOP的基本精神：

- 封装（保证操作的安全性和便利性）

  - `private`
    - 储存私有的数据成员
  - `public`
    - 一些方法（函数）
      - 构造函数
        - 默认构造函数
        - 复制构造函数
        - 显示定义的构造函数和复制构造函数（深拷贝）
      - 析构函数（显示定义）
        - 在12节重点介绍了这些函数和**动态内存分配**
      - 运算符重载
        - 使用友元函数，this指针
        - 显示定义赋值运算符
    - 提供了访问私有数据成员的接口
    - 友元函数的声明
  - `protected`
    - 基类成员给予派生类成员的一些特权

- 继承与多态

  - 基类——派生类
  - 公有继承的**is-a**关系
  - 继承函数的体系就是这一章节的目录（不再归纳）

  ![Conclusion](https://ooo.0x0.ooo/2025/01/11/OEf4Et.png)

当然，OOP的精神远不止于此！在接下来的第十四章，我们将更加深入的探寻**C++中的其他继承关系和代码重用问题。**

{% note info %}

无论如何，请你记住，面向对象编程是一种精神，而不是繁文缛节的语法。

{% endnote %}

> THE END                     ——2024/12/11
