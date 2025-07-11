---
title: Jotting-References-and-Encapsulation-in-OOP
date: 2024-12-30 13:38:10
index_img: /img/cover/jotting.jpg
excerpt: This blog analyzes a vector class design, focusing on const and non-const references in member functions, operator overloading, and friend functions. It uses this example to discuss the balance between encapsulation and flexibility in OOP.
categories:
  - Code
  - Cpp
tags:
  - Finished
  - OOP
  - Jotting
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# [Jotting] References and Encapsulation in OOP

## About Jotting

{% note primary %}

"Jotting" means quickly writing down short notes or ideas. It’s often used for informal, brief records, like reminders or thoughts.

{% endnote %}

在**Jotting**栏目中，我将以**碎片化**的方式记录我在学习过程中遇到的一个个小问题，他们之间具有独立性，各自是一个个小专题，但是**有具有更深层次的共性**。因此，本栏目的初衷就是在**解决这一个个小专题的过程中，尝试提炼其最本质的“第一性原理”**，进而有助于构建在宏观上的知识框架。

## Abstract

In this blog, we will start with the design of a **vector** class, exploring the implementation details of defining member functions as **const references and non-const references**, as well as their practical applications in **operator overloading**, **setting friend functions**, **constructors**, and more. On a deeper level, this article uses this example to discuss the trade-offs between the **encapsulation philosophy and flexibility** in OOP.

## Introduction

在昨天的C++作业中，笔者做到了一道很经典的面向对象的设计题：**设计向量类并实现对应的功能**。下文贴出题目和已经通过测试样例的代码。

> **题目：**本关任务：定义一个向量类MyVect，分量为整数，向量维数作为其数据成员，除了实现构造函数、析构函数、拷贝构造函数，还能够 
>
> - 重载加法：对应分量相加，假设总是维数相同的两个向量相加 
> - 重载[]：取相应分量 
> - 重载输入输出 
> - 重载=、==、!= 
> - 重载++、–：对所有分量做++和--
> - 输出向量范数(L2范数) 若将向量赋值给一个double型数，则表示求其范数(L2范数) 
> - 能够知道程序目前存活的向量数 
> - 输出向量维数

```C++
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2024-11-11 21:10:22
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2024-12-30 14:56:27
 * @FilePath: \CODE_for_Vscode\C++_project\testcode_4.cpp
 * @Description: 
 * Do you code and make progress today?
 * Copyright (c) 2024 by Xiyuan Yang, All Rights Reserved. 
 */
#include <iostream>
#include <cmath>
using namespace std;
class MyVect{
    static int numbers;
    int size;
    int *array;
    public:
        MyVect(){
            numbers++;
        }
        MyVect(int n):size(n){
            array=new int[size];
            numbers++;
        }
        MyVect(const MyVect& x){
            size=x.size;
            array=new int[size];
            numbers++;
            for(int i=0;i<size;i++){
                array[i]=x.array[i];
            }
        }
        ~MyVect(){
            numbers--;
            delete[] array;
        }
        int getlength() const { return size;}
        int* getarray() const {return array;}
        double getnorm(){
            double sum=0;
            for(int i=0;i<size;i++){
                sum+=(array[i])*array[i];
            }
            sum=sqrt(sum);
            return sum;
        }
        static int getliving();
        int operator[](int x){return array[x];}
        MyVect& operator++(){
            for(int i=0;i<size;i++){
                array[i]++;
            }
            return *this;
        }
        MyVect operator++(int x){
            MyVect temp=*this;
            for(int i=0;i<size;i++){
                array[i]++;
            }
            return temp;
        }
        MyVect operator=(const MyVect &x);
        friend istream& operator>>(istream& is,MyVect& x);
        friend ostream& operator<<(ostream& os,const MyVect& x);
        friend bool operator==(MyVect&x1,MyVect&x2);
        friend MyVect operator+(const MyVect&x1,const MyVect&x2);
        double operator-(double minus){
            return this->getnorm()-minus;
        };
};

int MyVect::numbers=0;
int MyVect::getliving(){
    return numbers;
}

istream& operator>>(istream& is,MyVect& x){
    for(int i=0;i<x.size;i++){
        is>>x.array[i];
    }
    return is;
}

ostream& operator<<(ostream& os,const MyVect& x){
    for(int i=0;i<x.size;i++){
        os<<x.array[i];
        if(i!=x.size-1) os<<" ";
    }
    return os;
}

MyVect operator+(const MyVect&x1,const MyVect&x2){
    MyVect temp(x1.size);
    for(int i=0;i<x1.size;i++){
        temp.array[i]=x1.array[i]+x2.array[i];
    }
    return temp;
}


bool operator==(MyVect&x1,MyVect&x2){
    if(x1.size!=x2.size){
        return false;
    }
    for(int i=0;i<x1.size;i++){
        if(x1.array[i]!=x2.array[i]){
            return false;
        }
    }
    return true;
}

MyVect MyVect::operator=(const MyVect &x){
            if((this)==&x){
                return *this;
            }

            delete[] array;
            size=x.size;
            array=new int[size];
            for(int i=0;i<size;i++){
                array[i]=x.array[i];
            }
            return *this;
        }


int main()
{
    int n;
    cin>>n;
    MyVect v1(n);
    double x;
    cin >> v1;
    cout << "Now v1 is: "<< v1 << endl;
    cout << "The length of v1 is " << v1.getlength() << endl;
    cout << "The norm of v1 is " << v1.getnorm() << endl;
    x = v1 - 1.0;
    cout << "The norm of v1 minus 1 is " << x << endl;
    MyVect v2 = v1;
    cout << "Now v2 is: " << v2 << endl;
    //cout << "The number of vectors is: " << v1.getcount() << endl;
    cout << endl;
    {
        MyVect v3(n);
        v3= v1 + v2;
        cout << "The result of v1+v2 is: " << v3 << endl;
        cout << "The 1st element of v3 is " << v3[1] << endl;
        //cout << "The number of vectors is: " << MyVect::getcount() << endl;
    }
    cout << endl;
    cout << "The number of living vectors is: " << v1.getliving() << endl;
    //cout << "The number of total vector is: " << v1.getcount() << endl;
    cout << "The result of v1++ is: " << v1++ << endl;
    cout << "The result of v1==v2 is " << (v1==v2) << endl;
    cout << "The result of ++v2 is: " << ++v2 << endl;
    cout << "The result of v1==v2 is " << (v1==v2) << endl;
    return 0;
}

```

具体的实现其实并不麻烦，只是代码的工程量很大。不过，笔者在重载++运算符的时候遇到了一些麻烦。

回顾**[笔者之前写的博客](https://xiyuanyang-code.github.io/posts/Introduction-to-OOP/)**，这是**重载++/--运算符的方法**：

```C++
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2024-11-11 21:10:21
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2024-12-30 14:04:15
 * @FilePath: \CODE_for_Vscode\C++_project\testcode_5.cpp
 * @Description: 
 * Do you code and make progress today?
 * Copyright (c) 2024 by Xiyuan Yang, All Rights Reserved. 
 */
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
    MyClass obj1(10),obj2(10);
    cout<<"++ in the front:"<<endl;
    (++obj1).display();

    cout<<"++ in the back:"<<endl;
    (obj2++).display();

    return 0;
}

/*
输出：
++ in the front:
Value: 11
++ in the back:
Value: 10
*/
```

如果仔细比较`++/--`运算符的重载过程，我们会发现在函数定义的时候主要有以下几点的不同：

- 后缀运算符重载的时候需要`(int)`作为一个参数列表，方便编译器区分。
- 后缀运算符返回的是一个**`MyClass`对象**，而前缀运算符返回的是对一个**`MyClass`对象的引用**。

下面我们来重点讨论第二条，即这篇博客的第一个重点内容：**关于对象的引用。**

## The References of an object

为什么前缀和后缀运算符的**返回值不相同**？因为编译器实现这两种运算的操作不同。前缀运算是返回自增之后的

值，需要返回对对象的引用，这是为了支持**链式操作**和**一致性**。例如`(++(++obj));`

那**后置运算符**呢？在我们重载的后置运算符中，我们先新建一个temp对象来储存原来对象的值，之后对对象做**自增运算**，然后返回temp对象的值，这样就实现了**返回递增前对象的值**。

所以，在使用**后置自增运算符的过程中**，我们实际上返回的是**临时变量的值**，因此**不可以返回对对象的引用，因为对临时对象的引用会导致悬挂指针等严重的问题**。（临时对象在传完值后就会被销毁，此时在对他进行引用操作是没有意义且极其危险的。）

如果读者还对这一部分有些疑惑的话，不妨看看下面的代码示例，以下的代码通过**显式定义三种构造函数**来监控临时对象的构造和析构。

```C++
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2024-11-11 21:10:21
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2024-12-30 14:24:24
 * @FilePath: \CODE_for_Vscode\C++_project\testcode_5.cpp
 * @Description: 
 * Do you code and make progress today?
 * Copyright (c) 2024 by Xiyuan Yang, All Rights Reserved. 
 */
#include <iostream>
using namespace std;

class MyClass {
private:
    int value;

public:
    // 构造函数
    MyClass(){
        cout<<"Create a new object with default constructor"<<endl;
    }
    MyClass(int v) : value(v) {
        cout<<"Create a new object!"<<endl;
    }
    MyClass(const MyClass& x){
        value=x.value;
        cout<<"Create a new object with copy constructor"<<endl;
    }
    ~MyClass(){
        cout<<"The object destroyed"<<endl;
    }

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
    MyClass obj3(10);
    obj3.display();
    (obj3++).display();
    ((obj3++)++).display();
    return 0;
}
/*
Create a new object!
Value: 10
Create a new object with copy constructor
Value: 10
The object destroyed
Create a new object with copy constructor
Create a new object with copy constructor
Value: 11
The object destroyed
The object destroyed
The object destroyed
*/
```

### Facing the `<<` operator

因为临时变量对**等待赋值运算符将值拷贝成功后再被销毁**（如果涉及到指针对象，需要重载赋值运算符），因此仅仅在**值**的使用上，上文的代码能够相安无事。（输出结果的正确性也印证了这一点）。不过，只要我们对代码做一些小小的修改，我们就会发现很多有趣的bug（或者说是**冲突**）就出现了。

做什么修改呢？我们重载**流输出运算符**：

```C++
friend ostream& operator<<(ostream& os,MyClass& x){
        os<<x.value;
        return os;
    }
//这是使用非 const引用
```

接下来我们修改main函数：

```C++
int main() {
    MyClass obj3(10);
    obj3.display();
    (obj3++).display();
    ((obj3++)++).display();

    cout<<"Now lets look at the obj4"<<endl;
    MyClass obj4(100);
    cout<<"Normal use "<<obj4<<endl;
    cout<<"++ in the front "<<(++obj4)<<endl;
    //cout<<"++ in the back "<<(obj4++)<<endl;
    //上面这一行先注释掉，之后再讨论
    return 0;
}
/*
Create a new object!
Value: 10
Create a new object with copy constructor
Value: 10
The object destroyed
Create a new object with copy constructor
Create a new object with copy constructor
Value: 11
The object destroyed
The object destroyed
Now lets look at the obj4
Create a new object!
Normal use 100
++ in the front 101
The object destroyed
The object destroyed
*/
```

现在我们可以把`cout<<"++ in the back "<<(obj4++)<<endl;`这一行代码的注释删掉，**不出意外的话，我们的代码出现了报错**。

```Bash
testcode_5.cpp:81:35: error: cannot bind non-const lvalue reference of type 'MyClass&' to an rvalue of type 'MyClass'
     cout<<"++ in the back "<<(obj4++)<<endl;
                              ~~~~~^~~
                              
Translate:无法将类型为 MyClass& 的非 const 左值引用绑定到类型为 MyClass 的右值。
```

### Explanations

仔细分析一下原因其实很简单：**问题出现在后置++运算符的返回类型上**。因为后置++运算符返回的是一个临时对象的值（**MyClass 的右值**），这与流输出运算符的重载参数列表（**MyClass& 的非 const 左值引用**）不匹配。

这样就有冲突产生了，我们设计代码的初衷是希望对象能返回他在执行自增操作之前的值，如果我们修改++后置运算符的返回类型，会产生悬挂指针的严重问题：

![Dangling pointer](references.png)

> 引用找不到对应的内存地址，无法正常输出。

**因此，解决这个问题的唯一途径就是修改流输出运算符的重载函数定义！**

### Use of const references

在这里，我们需要更改流输出运算符的引用类型：**将非const引用更改为const引用**。

因为**const 引用有一个非常重要的特性**：**const 引用可以绑定右值**。这是 C++ 中的一个重要特性，允许将临时对象（右值）传递给接受 `const` 引用的函数或参数。

在更改参数列表后，代码就可以正常的运行了！

**const vs non-const**

| 特性         | const 引用           | 非 const 引用        |
| :----------- | :------------------- | :------------------- |
| **修改权限** | 不能修改对象         | 可以修改对象         |
| **绑定规则** | 可以绑定左值和右值   | 只能绑定左值         |
| **使用场景** | 只读访问，避免拷贝   | 需要修改对象         |
| **安全性**   | 更安全，避免意外修改 | 需要谨慎使用         |
| **性能**     | 可以优化右值处理     | 无法直接优化右值处理 |

{% note info %}

笔者写到这里才发现自己的辅导书上在重载流输出运算符的时候本来就是使用const引用的 🤡💔。所以改了那么久的代码就是因为没有仔细看书。。。吃一堑长一智吧。。。

{% endnote %}

## Encapsulation in OOP

写完上面的代码，读者的心中冒出了两个有关引用和封装的问题。

**Question1 **回顾上面的代码，成员函数和友元函数可以**直接修改引用类的私有数据成员**，这样做是否有违OOP的**封装**的安全性精神？

封装的核心思想是**隐藏对象的内部实现细节**，只暴露必要的接口给外部使用。通过封装，可以确保对象的内部状态不会被外部代码随意修改，从而提高代码的安全性和可维护性。

身为**成员函数**或者**友元函数**，其本身就具有修改本对象的私有成员的权限，但是值得注意的是，这些函数在**非const引用下具有访问甚至修改其他对象的私有成员的权限**。

这是很危险的事情，因此，我们**再次感受到了const引用的强大优势：在节省效率的同时保证了对象不被修改的安全性。**

因此，Question1 的问题确实是存在的，解决办法也非常的简单，将引用修改为const引用，只在需要修改的时候暴露必要的权限。同时，将修改的操作封装成函数也能一定程度上保证其安全性。



**Question 2** 如果我希望任何外部代码都不能访问一个特地的私有成员（例如密钥），我该如何实现？

**这个问题比较复杂**，具体而言需要使用**嵌套类实现**。

```C++
#include <iostream>
class MyClass {
private:
    int value;
    class PrivateValue{
        private:int privatevalue;
        public:
            int getprivatevalue() const{return privatevalue;}
            PrivateValue(int x):privatevalue(x){}
    };
    PrivateValue pri;
    // 只有特定成员函数可以访问 privatevalue
    
public:
    MyClass(int v, int pv) : value(v),pri(pv){}

    // 公共接口
    int getValue() const {
        return pri.getprivatevalue();
    }
};
```

在这里，方法`int getprivatevalue() const{return privatevalue;}`成为了获得密钥的唯一途径，哪怕是成员函数，也不可以直接调用`pri.privatevalue`。

![safety](safety.png)



{% note primary %}

**封装和安全性**是OOP中非常重要的一个精神，在实现公用接口的同时隐藏数据成员和具体的函数代码，提高了用户使用的安全性和便捷性。但是，在本科C++的教学中，受困于**题目**的限制，这一点被大大忽略了。（笔者身边甚至有同学将所有的数据成员全部设置为public，这样少写很多函数也可以通过测试样例。）

{% endnote %}

例如在下面的类设计中，`privatevalue`作为密钥，**哪怕是MyClass的成员函数都不可以调用其值**，只能通过唯一的接口`int getprivatevalue() const`实现。

> 这样有什么好处？好处时实现了安全性的**层级分离**。显然value的安全等级低于`privatevalue`，开发者放开`MyClass`的权限也不会影响到`PrivateValue`类。保证了高级安全数据的安全性。
>
> 例如，任何`MyClass`的成员函数都可以访问value的值，但是只有掌握password的成员函数才能通过接口访问`privatevalue`的值。

```C++
#include <iostream>
using namespace std;
class MyClass {
private:
    int value;
    class PrivateValue{
        private:int privatevalue;
        public:
            int getprivatevalue() const{
                int password;
                cout<<"Please enter the password: ";
                cin>>password;
                if(password==123456){
                    return privatevalue;
                }
                return 0;
            }
            PrivateValue(int x):privatevalue(x){}
    };
    PrivateValue pri;
    // 只有特定成员函数可以访问 privatevalue
    
public:
    MyClass(int v, int pv) : value(v),pri(pv){}

    // 公共接口
    int getValue() const {
        return pri.getprivatevalue();
    }
};


int main(){
    MyClass obj1(23,3456);
    cout<<obj1.getValue();
}
```



## Conclusion

- 多用const引用，多多益善。
- 对于没有访问权的变量使用**嵌套类**实现。

> The END
