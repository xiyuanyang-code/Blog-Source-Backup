---
title: Modern C++
date: 2025-04-13 18:32:26
index_img: /img/cover/moderncpp.jpg
excerpt: This blog focuses on more advanced modern C++ features,including std::move, type inference, smart pointer and other.
categories:
  - Code
  - Cpp
tags:
  - tutorial
  - Finished
  - Modern Cpp
  - C/C++
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Modern C++

## What is Modern Cpp?

现代C++通常指的是**C++11**及其后续版本（最新：C++23）。在这些C++版本中，引入了更多新的特性和改进，让C++更加的灵活并且高效。下面该文将具体介绍一些常见的并且强大的C++特性，包括：

- **移动语义和`std::move`**。
- **Type inference**和**完美转发**
- `auto`
- **智能指针**（重点）
- `std::any`
- `std::optional`
- `rust`初步介绍🤣

## `std::move` and value types

- 左值和右值
	- 对于赋值运算符的重载，只可以对左值使用。
	- **对于右值**？我们希望实现移动而非拷贝，来减少不必要的拷贝。

`std::move` 会让编译器相信**一个左值是一个右值**。（直接移动所有权而非拷贝。）

**注意**：在使用`std::move`之后对应的变量，不可以再使用。（因为所有权已经被移动）

- `const`变量不可以使用移动语义。
- 编译器有时会自动做优化。
- 因此，在设计自己的类的使用，需要设计对应的**Copy assignment for lvalue** and **Move assignment for rvalue**.

### 实例

#### 函数调用时避免复制

如果一个变量**我们在传参进入函数之后就不再使用**，可以使用`std::move()`来实现移动构造。

```cpp
void Fun(std::vector<int> v1, std::vector<int> v2) {
	// Use vector(const vector &other) as constructor
	Foo(v1);
	// v1 can still be used after this
	// Use vector(vector &&other) as constructor
	Foo(std::move(v2));
	// v2 cannot be used after this
}
```

#### Move assignment for rvalue

```cpp
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2025-04-13 18:54:39
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2025-04-13 23:35:26
 * @FilePath: /20250413_modern_cpp/move.cpp
 * @Description:
 * Do you code and make progress today?
 * Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved.
 */
#include <iostream>
#include <utility> // for std::move

class Buffer {
public:
    Buffer(size_t size) : data(new int[size]), size(size) {
        std::cout << "Buffer allocated\n";
    }

    /**
     * @brief Construct a new Buffer object
     * 
     * @param other 
     */
    Buffer(Buffer&& other) noexcept : data(other.data), size(other.size) {
        other.data = nullptr; 
        other.size = 0;
        std::cout << "Buffer moved\n";
    }

    /**
     * @brief Assignment for rvalue
     * 
     * @param other 
     * @return Buffer& 
     */
    Buffer& operator=(Buffer&& other) noexcept {
        if (this != &other) {
            delete[] data; // 释放当前资源
            data = other.data; // 转移所有权
            size = other.size;
            other.data = nullptr; // 让其他对象失去对资源的所有权
            other.size = 0;
            std::cout << "Buffer moved (assignment)\n";
        }
        return *this;
    }

    ~Buffer() {
        delete[] data; // 释放资源
        std::cout << "Buffer deallocated\n";
    }

private:
    int* data;
    size_t size;
};

int main() {
    Buffer buf1(10);

    Buffer buf2 = std::move(buf1); 
    

    Buffer buf3(20);
    buf3 = std::move(buf2); 

    return 0;
}

```

### 注意事项

- 我们没有必要**对本身就是右值**的数进行移动语义。
- 不要使用**const &&**。
	- 在 C++ 中，移动语义的核心思想是转移资源的所有权，而不是复制资源。移动操作通常涉及修改对象的内部状态（例如，转移指针、清空资源等）。因此，对于 `const` 对象，移动语义无法正常工作，因为`const` 关键字用于指示对象的值不能被修改。对 `const` 对象进行任何修改操作，包括移动操作，都是不允许的。这意味着你不能改变 `const` 对象的内部状态，比如将指针设置为 `nullptr`。
	- 移动构造函数和移动赋值运算符通常会执行以下操作：
		- 将源对象的资源（如指针）转移给目标对象。
		- 将源对象的资源状态清空（例如，将指针设置为 `nullptr`）。
	- 对于 `const` 对象，无法执行这些操作，因为它们的状态是不可变的。例如，尝试将一个 `const` 对象的指针设置为 `nullptr` 会导致编译错误。

### Applications

链接：https://acm.sjtu.edu.cn/OnlineJudge/problem/2641

{% note primary %}

stargazer 最近正在学习 Rust 语言的所有权机制与内存安全特性。

Rust 的所有权机制要求每个值都有唯一的所有者（通常是变量），并且在同一时间内只能有一个所有者。所有权的转移可以通过赋值、函数参数传递或返回值来实现。相当于 C++ 中的移动语义（`std::move()`）。

在一个值的所有者变量的作用域之外（例如在另外一个函数中）对该值的访问必须通过借用（相当于 C++ 中指向变量的指针）来实现。Rust 中的借用分为两种：

- 不可变借用（Immutable Borrow）：相当于 C++ 中的 `const T*`，允许读取但不允许修改
- 可变借用（Mutable Borrow）：相当于 C++ 中的 `T*`，允许读取和修改

Rust 对变量的借用有着严格的限制：

- 在同一时间内，只能有一个可变借用，或者多个不可变借用
- 不能同时存在可变借用和不可变借用
- 所有借用都必须在拥有变量的生命周期内有效 对以上规则的违反会导致编译错误。

Rust 的借用机制对编译器优化非常有帮助。由于不可变借用不能与可变借用共存，被不可变借用指向的值只需要从堆内存中获取一次，之后可以安全地存储在寄存器或栈上缓存中。相比之下，C++ 中也进行类似的优化，但其他函数修改 `const*` 指针指向的值是未定义行为，可能导致不安全的代码。

Rust 的编译器可以在编译时就能“静态”地检查所有权和借用关系，在运行时无需额外检查。然而，对于堆上对象，在编译期检查所有权和借用关系是非常困难的。因此，Rust 提供了 `RefCell<T>` 类型来在运行时检查所有权和借用关系。它有如下方法：

- `borrow()` 与 `try_borrow()`：获取一个不可变借用，返回 `Ref<T>` 类型。如果当前存在可变借用则失败。`borrow()` 会 panic，相当于 C++ 中的 abort，而 `try_borrow()` 返回一个 `Result<Ref<T>, BorrowError>`，相当于 C++ 中的 `std::optional<Ref<T>>`。
- `borrow_mut()` 与 `try_borrow_mut()`：获取一个可变借用，返回 `RefMut<T>` 类型。如果当前存在任何借用则会失败。
- 返回的 `Ref<T>` 和 `RefMut<T>` 包装器实现了解引用操作符，可以像使用普通引用一样使用
- 当 `Ref<T>` 和 `RefMut<T>` 的生命周期结束时，会自动减少或重置借用计数
- 当 `RefCell<T>` 的生命周期结束时，若仍有借用存在，则会 panic

请借鉴 Rust 中的借用机制，在 C++ 中实现一个 `RefCell` 类。

{% endnote %}

#### 代码实现

```cpp
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2025-04-06 20:47:27
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2025-04-07 09:51:11
 * @FilePath: /20250406_Test3/3.cpp
 * @Description: 
 * Do you code and make progress today?
 * Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved. 
 */
#include <iostream>
#include <optional>
#include <stdexcept>

class RefCellError : public std::runtime_error {
public:
    explicit RefCellError(const std::string& message) : std::runtime_error(message) {}
    virtual ~RefCellError() = default;
};

class BorrowError : public RefCellError {
public:
    explicit BorrowError(const std::string& message) : RefCellError(message) {}
};

class BorrowMutError : public RefCellError {
public:
    explicit BorrowMutError(const std::string& message) : RefCellError(message) {}
};

class DestructionError : public RefCellError {
public:
    explicit DestructionError(const std::string& message) : RefCellError(message) {}
};

template <typename T>
class RefCell {
private:
    T value;
    mutable size_t borrow_count = 0;
    mutable size_t borrow_mut_count = 0;
    mutable bool is_destroyed = false;

    void check_borrow() const {
        if (borrow_mut_count > 0) {
            throw BorrowError("Invalid");
        }
    }

    void check_borrow_mut() const {
        if (borrow_count > 0 || borrow_mut_count > 0) {
            throw BorrowMutError("Invalid");
        }
    }

    void check_destruction() const {
        if (borrow_count > 0 || borrow_mut_count > 0) {
            throw DestructionError("Invalid");
        }
    }

public:
    class Ref;
    class RefMut;

    explicit RefCell(const T& initial_value) : value(initial_value) {}
    explicit RefCell(T&& initial_value) : value(std::move(initial_value)) {}

    RefCell(const RefCell&) = delete;
    RefCell& operator=(const RefCell&) = delete;
    RefCell(RefCell&&) = delete;
    RefCell& operator=(RefCell&&) = delete;

    Ref borrow() const {
        check_borrow();
        return Ref(*this);
    }

    std::optional<Ref> try_borrow() const {
        if (borrow_mut_count > 0) {
            return std::nullopt;
        }
        return Ref(*this);
    }

    RefMut borrow_mut() {
        check_borrow_mut();
        return RefMut(*this);
    }

    std::optional<RefMut> try_borrow_mut() {
        if (borrow_count > 0 || borrow_mut_count > 0) {
            return std::nullopt;
        }
        return RefMut(*this);
    }

    class Ref {
    private:
        const RefCell* parent;
        bool valid = false;

    public:
        explicit Ref(const RefCell& p) : parent(&p) {
            parent->borrow_count++;
            valid = true;
        }

        ~Ref() {
            if (valid) {
                parent->borrow_count--;
            }
        }

        const T& operator*() const {
            if (!valid) throw BorrowError("Dangling reference");
            return parent -> value;
        }

        const T* operator->() const {
            if (!valid) throw BorrowError("Dangling reference");
            return &(parent -> value);
        }

        Ref(const Ref& other) : parent(other.parent), valid(other.valid) {
            if (valid) {
                parent->borrow_count++;
            }
        }

        Ref& operator=(const Ref& other) {
            if (this != &other) {
                if (valid) {
                    parent->borrow_count--;
                }
                parent = other.parent;
                valid = other.valid;
                if (valid) {
                    parent->borrow_count++;
                }
            }
            return *this;
        }

        Ref(Ref&& other) noexcept : parent(std::move(other.parent)), valid(std::move(other.valid)) {
            other.valid = false;
        }

        Ref& operator=(Ref&& other) noexcept {
            if (this != &other) {
                if (valid) {
                    parent->borrow_count--;
                }
                parent = std::move(other.parent);
                valid = std::move(other.valid);
                other.valid = false;
            }
            return *this;
        }
    };

    class RefMut {
    private:
        RefCell* parent;
        bool valid = false;

    public:
        explicit RefMut(RefCell& p) : parent(&p) {
            parent->borrow_mut_count++;
            valid = true;
        }

        ~RefMut() {
            if (valid) {
                parent->borrow_mut_count--;
            }
        }

        T& operator*() {
            if (!valid) throw BorrowMutError("Dangling mutable reference");
            return parent->value;
        }

        T* operator->() {
            if (!valid) throw BorrowMutError("Dangling mutable reference");
            return &parent->value;
        }

        RefMut(const RefMut&) = delete;
        RefMut& operator=(const RefMut&) = delete;

        RefMut(RefMut&& other) noexcept : parent(other.parent), valid(other.valid) {
            other.valid = false;
        }

        RefMut& operator=(RefMut&& other) noexcept {
            if (this != &other) {
                if (valid) {
                    parent->borrow_mut_count--;
                }
                parent = other.parent;
                valid = other.valid;
                other.valid = false;
            }
            return *this;
        }
    };

    ~RefCell() {
        check_destruction();
        is_destroyed = true;
    }
};
```

例如，在这段代码中：

```cpp
Ref& operator=(Ref&& other) noexcept {
            if (this != &other) {
                if (valid) {
                    parent->borrow_count--;
                }
                parent = std::move(other.parent);
                valid = std::move(other.valid);
                other.valid = false;
            }
            return *this;
        }
```

对于借用，我们要严格保证**所有权的独立性和严格管理**，因此使用移动语义来保证所有权的移动而非复制。

## Type inference and `std::forward`

对于模版函数，编译器会自动识别对应的模版参数T（但是，不可以识别右值）。

如何改进？可以使用 `const T&`，并且，**当一个右值被绑定到 `const` 引用时，右值的生命周期会被延长到引用的生命周期结束**。这意味着在引用存在期间，右值不会被销毁。

> 最简单的例子：我们写了很多遍的copy constructor.

### Universal Reference

**Universal References**（通用引用）是 C++11 引入的一个重要概念，它允许编写可以接受左值和右值的模板参数。这个概念通常与完美转发（perfect forwarding）一起使用，使得函数模板能够有效地转发参数而不丢失其值类别（lvalue 或 rvalue）。

- C++的引用折叠：`int& &&` -> `int &`（对于左值）

> 换句话说，左值还是左值，右值还是右值。

```cpp
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2025-04-14 00:03:41
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2025-04-14 00:13:32
 * @FilePath: /20250413_modern_cpp/universal.cpp
 * @Description:
 * Do you code and make progress today?
 * Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved.
 */
#include <iostream>
#include <type_traits>

template <typename T> void test(T &&param) {
  if constexpr (std::is_lvalue_reference<T>::value) {
    std::cout << "param is an lvalue" << std::endl;
  } else {
    std::cout << "param is an rvalue" << std::endl;
  }
}

int main() {
  int a = 10;
  int *b = &a;
  const int *c = b;
  const int d = 10;

  std::cout << "Test for Universal reference" << std::endl;
  test(a);
  test(a + 1);
  test(b);
  test(c);
  test(d);
  test(8);
  return 0;
}
```

程序输出：

```
Test for Universal reference
param is an lvalue
param is an rvalue
param is an lvalue
param is an lvalue
param is an lvalue
param is an rvalue
```

### `std::forward`

`std::forward`可以实现**完美转发**，即在模版参数中**将参数原封不动地转发给其他函数，保持其原有的左值或者右值属性**。

- Passes an rvalue: Ensures that an rvalue reference is passed.
- Passes a const lvalue: Ensures that a const lvalue reference is passed.
- Passes a non-const lvalue: Ensures that a non-const lvalue.

有关`std::forward`的应用，我们在下面的例子中加以说明：

#### 为什么全是左值？

请看下面的示例代码：

```cpp
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2025-04-13 19:07:20
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2025-04-14 00:18:51
 * @FilePath: /20250413_modern_cpp/forward.cpp
 * @Description:
 * Do you code and make progress today?
 * Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved.
 */
#include <iostream>
#include <string>
class A {
public:
  A(const std::string &s) : s_(s) {
    std::cout << "const string &" << std::endl;
  }
  A(std::string &&s) : s_(std::move(s)) {
    std::cout << "string &&" << std::endl;
  }

private:
  std::string s_;
};
template <class T> void foo(T &&s) {
  if constexpr (std::is_lvalue_reference<T>::value) {
    std::cout << "param is an lvalue" << std::endl;
  } else {
    std::cout << "param is an rvalue" << std::endl;
  }
  A tmp(s);
}

int main() {
  std::string a = "111";
  std::string b = "222";
  foo(a);
  foo(a + b);
  foo(std::string{"111222"});
}
```

很惊讶的是，程序的输出竟然是这样：

```
param is an lvalue
const string &
param is an rvalue
const string &
param is an rvalue
const string &
```

为什么**左值和右值都选择了使用const string&**？原因在于`const T&`在构造函数中相比于右值构造具有更高的优先级。也就是说在const T&存在的情况下，传递一个右值会将参数**传递给左值const引用**并且延长他的生命周期。但我们可以使用`std::forward`来强制保证 **Passes an rvalue: Ensures that an rvalue reference is passed.**

具体而言，我们可以修改代码：`A tmp(std::forward<T>(s));`

得到下面的输出示例：

```
param is an lvalue
const string &
param is an rvalue
string &&
param is an rvalue
string &&
```

## `auto` reference

自动类型推断：

- `auto` 可以推出变量类型
- `auto &` 可以推出引用类型
- `const auto &` 可以推出常量引用

同时，auto还支持对数组，元组的自动解包和打包。

同样的，我们也可以结合for循环中的代码使用auto：

```cpp
std::map<int, std::string> GetMap();
int main() {
	auto map = GetMap();
	for (const auto &[key, value] : map) {}
	// Do something
}
```

## Smart Pointer

指针的使用经常伴随着**内存泄漏**的出现，例如如果我希望在堆内存上构造一个对象，我可以使用new来手动分配，但是**我必须要在使用完成之后手动delete**掉这段内存。这在代码逻辑比较复杂的时候经常会存在**没有被delete的漏网之鱼**出现。因此，现在C++提出了**智能指针**，实现了**自动内存回收**，保证了便捷性和安全性。

### `unique_ptr`

一个**Move Only**的智能指针，只可以拥有一个拥有者。

请看下面的代码示例：

```cpp
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2025-04-13 19:33:09
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2025-04-13 20:45:07
 * @FilePath: /20250413_modern_cpp/ptr.cpp
 * @Description:
 * Do you code and make progress today?
 * Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved.
 */
#include <iostream>
#include <memory>
#include <string>

class test_unique {
private:
  int storage;
  std::string name;

public:
  test_unique(int a = 1, std::string name_ = "Helloworld")
      : storage(a), name(name_) {
    std::cout << "Constructed" << std::endl;
    std::cout << "Name: " << name << std::endl;
  }

  ~test_unique() {
    std::cout << "This object's name is " << name << std::endl;
    std::cout << "Destructed" << std::endl;
  }
};

int main() {
  // for unique ptr
  std::unique_ptr<test_unique> ptr1 =
      std::make_unique<test_unique>(4, "Unique_ptr");
  // std::unique_ptr<test_unique> ptr2 = ptr1;

  if (ptr1 == nullptr) {
    std::cout << "ptr1 is null now!" << std::endl;
  } else {
    std::cout << "ptr1 is not null now!" << std::endl;
  }

  std::unique_ptr<test_unique> ptr3 = std::move(ptr1);

  if (ptr1 == nullptr) {
    std::cout << "ptr1 is null now!" << std::endl;
  } else {
    std::cout << "ptr1 is not null now!" << std::endl;
  }
}
```

程序的输出：

```
Constructed
Name: Unique_ptr
ptr1 is not null now!
ptr1 is null now!
This object's name is Unique_ptr
Destructed
```

代码的37行被注释掉了，因为如果不注释的话会在编译时报错。由上文代码可见，**unique_ptr**的关键在于它只有一个“主人”，因此它的所有权是独一无二的（就像rust一样）。因此，这会带来极大的安全性，并且可以实现内存的自动回收。

#### RAII (Resource Acquisition is Initialization)

RAII（Resource Acquisition Is Initialization）是一种管理资源的编程技术，广泛应用于 C++ 中。它的核心思想是将**资源的生命周期与对象的生命周期**绑定在一起，从而确保资源在对象创建时获取，并在对象销毁时自动释放。这种方法可以有效地避免**资源泄漏和悬空指针**等问题。

`std::unique_ptr`就很好的满足了RAII的思想，它实现了 RAII 的原则，自动管理动态分配的内存。具体来说：

1. **自动释放**：当 `std::unique_ptr` 的生命周期结束时（即它被销毁），它会自动调用其管理的对象的析构函数，释放内存。
2. **独占所有权**：`std::unique_ptr` 只能有一个所有者，防止多个指针同时管理同一块内存，避免了悬空指针和双重释放的问题。
3. **移动语义**：`std::unique_ptr` 支持移动语义，可以通过 `std::move` 将所有权从一个指针转移到另一个指针，而不会发生复制。这符合 RAII 的设计理念。

### `shared_ptr`

`unique_ptr`非常高效地解决了安全问题（其具有极小的额外开销），但是安全性的保证势必会带来在便捷性上的约束。（**这本质上就是一种权衡，不是吗（rust👀）**）。而`shared_ptr`**允许多个指针共享同一资源**，通过引用计数管理资源的生命周期。当最后一个指向资源的 `shared_ptr` 被销毁时，资源才会被释放。

我们实现相似的实例代码，看看这一次会怎么样。

```cpp
/*
 * @Author: Xiyuan Yang   xiyuan_yang@outlook.com
 * @Date: 2025-04-13 19:33:09
 * @LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
 * @LastEditTime: 2025-04-13 21:02:38
 * @FilePath: /20250413_modern_cpp/ptr.cpp
 * @Description:
 * Do you code and make progress today?
 * Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved.
 */

#include <iostream>
#include <memory>
#include <string>

class test {
private:
  int storage;
  std::string name;

public:
  test(int a = 1, std::string name_ = "Helloworld") : storage(a), name(name_) {
    std::cout << "Constructed" << std::endl;
    std::cout << "Name: " << name << std::endl;
  }

  ~test() {
    std::cout << "This object's name is " << name << std::endl;
    std::cout << "Destructed" << std::endl;
  }

  std::string getname() const { return name; }
};

void test_uniqued_ptr() {
  // for unique ptr
  std::unique_ptr<test> ptr1 = std::make_unique<test>(4, "Unique_ptr");
  // std::unique_ptr<test_unique> ptr2 = ptr1;

  if (ptr1 == nullptr) {
    std::cout << "ptr1 is null now!" << std::endl;
  } else {
    std::cout << "ptr1 is not null now!" << std::endl;
  }

  std::unique_ptr<test> ptr3 = std::move(ptr1);

  if (ptr1 == nullptr) {
    std::cout << "ptr1 is null now!" << std::endl;
  } else {
    std::cout << "ptr1 is not null now!" << std::endl;
  }
}

void test_shared_ptr() {
  // for shared ptr
  std::shared_ptr<test> ptr1 = std::make_shared<test>(4, "Shared_ptr");

  std::shared_ptr<test> ptr2 = ptr1;

  std::cout << "ptr1's name " << ptr1->getname() << std::endl;
  std::cout << "ptr2's name " << ptr2->getname() << std::endl;

  if (ptr1 == nullptr) {
    std::cout << "ptr1 is null now!" << std::endl;
  } else {
    std::cout << "ptr1 is not null now!" << std::endl;
  }

  std::shared_ptr<test> ptr3 = std::move(ptr1);
  

  if (ptr1 == nullptr) {
    std::cout << "ptr1 is null now!" << std::endl;
  } else {
    std::cout << "ptr1 is not null now!" << std::endl;
  }

  std::cout << "ptr3's name " << ptr3->getname() << std::endl;
}

int main() {
//   test_uniqued_ptr();
  test_shared_ptr();
  return 0;
}
```

```
3_modern_cpp/"ptr
Constructed
Name: Shared_ptr
ptr1's name Shared_ptr
ptr2's name Shared_ptr
ptr1 is not null now!
ptr1 is null now!
ptr3's name Shared_ptr
This object's name is Shared_ptr
Destructed
```

我们发现在这里`std::shared_ptr<test> ptr2 = ptr1;`的操作是合法的。

### `weak_ptr`

在使用智能指针`shared_ptr`的时候，可能会存在**循环引用**的问题，例如智能指针a指向智能指针b，智能指针b指向智能指针a。此时两个智能指针的引用计数都不为1，此时存在内存泄露，两个指针指向的内存不会被释放。

具体而言使用代码实现就是：

```cpp
class Node {
public:
  std::shared_ptr<Node> next;

  Node() { std::cout << "Node created\n"; }

  ~Node() { std::cout << "Node destroyed\n"; }
};


void test_bug_for_shared_ptr() {
  std::shared_ptr<Node> ptr1 = std::make_shared<Node>();
  std::shared_ptr<Node> ptr2 = std::make_shared<Node>();
  ptr1->next = ptr2;
  ptr2->next = ptr1;
}
```

程序的结果：

```
Node created
Node created
```

我们可以看到**在程序终止运行的时候**，没有调用对应的析构函数，也就是说发生了类型泄漏。使用`valgrind`等工具进行检查可以看到更详细的日志信息：

```
❯ valgrind --leak-check=full ./ptr 
==13994== Memcheck, a memory error detector
==13994== Copyright (C) 2002-2022, and GNU GPL'd, by Julian Seward et al.
==13994== Using Valgrind-3.22.0 and LibVEX; rerun with -h for copyright info
==13994== Command: ./ptr
==13994== 
Node created
Node created
==13994== 
==13994== HEAP SUMMARY:
==13994==     in use at exit: 64 bytes in 2 blocks
==13994==   total heap usage: 4 allocs, 2 frees, 74,816 bytes allocated
==13994== 
==13994== 64 (32 direct, 32 indirect) bytes in 1 blocks are definitely lost in loss record 2 of 2
==13994==    at 0x4846FA3: operator new(unsigned long) (in /usr/libexec/valgrind/vgpreload_memcheck-amd64-linux.so)
==13994==    by 0x10CAB1: std::__new_allocator<std::_Sp_counted_ptr_inplace<Node, std::allocator<void>, (__gnu_cxx::_Lock_policy)2> >::allocate(unsigned long, void const*) (in /home/xiyuanyang/ACM_course_DS/20250413_modern_cpp/ptr)
==13994==    by 0x10C4A3: std::__allocated_ptr<std::allocator<std::_Sp_counted_ptr_inplace<Node, std::allocator<void>, (__gnu_cxx::_Lock_policy)2> > > std::__allocate_guarded<std::allocator<std::_Sp_counted_ptr_inplace<Node, std::allocator<void>, (__gnu_cxx::_Lock_policy)2> > >(std::allocator<std::_Sp_counted_ptr_inplace<Node, std::allocator<void>, (__gnu_cxx::_Lock_policy)2> >&) (in /home/xiyuanyang/ACM_course_DS/20250413_modern_cpp/ptr)
==13994==    by 0x10C0E4: std::__shared_count<(__gnu_cxx::_Lock_policy)2>::__shared_count<Node, std::allocator<void>>(Node*&, std::_Sp_alloc_shared_tag<std::allocator<void> >) (in /home/xiyuanyang/ACM_course_DS/20250413_modern_cpp/ptr)
==13994==    by 0x10BE3D: std::__shared_ptr<Node, (__gnu_cxx::_Lock_policy)2>::__shared_ptr<std::allocator<void>>(std::_Sp_alloc_shared_tag<std::allocator<void> >) (in /home/xiyuanyang/ACM_course_DS/20250413_modern_cpp/ptr)
==13994==    by 0x10BC5E: std::shared_ptr<Node>::shared_ptr<std::allocator<void>>(std::_Sp_alloc_shared_tag<std::allocator<void> >) (in /home/xiyuanyang/ACM_course_DS/20250413_modern_cpp/ptr)
==13994==    by 0x10B571: std::shared_ptr<std::enable_if<!std::is_array<Node>::value, Node>::type> std::make_shared<Node>() (in /home/xiyuanyang/ACM_course_DS/20250413_modern_cpp/ptr)
==13994==    by 0x10A936: test_bug_for_shared_ptr() (in /home/xiyuanyang/ACM_course_DS/20250413_modern_cpp/ptr)
==13994==    by 0x10A9F1: main (in /home/xiyuanyang/ACM_course_DS/20250413_modern_cpp/ptr)
==13994== 
==13994== LEAK SUMMARY:
==13994==    definitely lost: 32 bytes in 1 blocks
==13994==    indirectly lost: 32 bytes in 1 blocks
==13994==      possibly lost: 0 bytes in 0 blocks
==13994==    still reachable: 0 bytes in 0 blocks
==13994==         suppressed: 0 bytes in 0 blocks
==13994== 
==13994== For lists of detected and suppressed errors, rerun with: -s
==13994== ERROR SUMMARY: 1 errors from 1 contexts (suppressed: 0 from 0)
```

因此，我们有必要实现`weak_ptr`: 即**弱引用计数**。

```cpp
void test_weak_ptr() {
  std::weak_ptr<test> weakPtr;
  {
    std::shared_ptr<test> ptr1 = std::make_shared<test>(4, "Weak_ptr");

    weakPtr = ptr1;

    std::cout << "Reference count: " << ptr1.use_count() << "\n";

    {
      std::shared_ptr<test> ptr2 = ptr1;
      std::cout << "Reference count: " << ptr1.use_count() << "\n";
    }
    std::cout << "Reference count: " << ptr1.use_count() << "\n";
    if (auto sharedPtr = weakPtr.lock()) {
      std::cout << "Weak pointer is valid\n";
    } else {
      std::cout << "Weak pointer is expired\n";
    }
  }

  if (auto sharedPtr = weakPtr.lock()) {
    std::cout << "Weak pointer is valid\n";
  } else {
    std::cout << "Weak pointer is expired\n";
  }
}
```

我们发现`weak_ptr`不会增加智能指针的引用计数，但是会随着智能指针一起被销毁。这样精巧的设计就可以保证循环引用的问题被解决，只需要让一个指针使用`weak_ptr`就可以了。

```cpp
class NodeFix {
public:
  std::shared_ptr<NodeFix> next;
  std::weak_ptr<NodeFix> prev; 

  NodeFix() { std::cout << "NodeFix created\n"; }

  ~NodeFix() { std::cout << "NodeFix destroyed\n"; }
};

void fix_problem() {
  std::shared_ptr<NodeFix> ptr1 = std::make_shared<NodeFix>();
  std::shared_ptr<NodeFix> ptr2 = std::make_shared<NodeFix>();
  ptr1->next = ptr2;
  ptr2->prev = ptr1;//ptr2->prev是一个weak指针，因此不会增加ptr1的引用计数
}
```

```
NodeFix created
NodeFix created
NodeFix destroyed
NodeFix destroyed
```

使用`Valgrind`也是安然无恙！😊

```
==17644== Memcheck, a memory error detector
==17644== Copyright (C) 2002-2022, and GNU GPL'd, by Julian Seward et al.
==17644== Using Valgrind-3.22.0 and LibVEX; rerun with -h for copyright info
==17644== Command: ./ptr
==17644== 
NodeFix created
NodeFix created
NodeFix destroyed
NodeFix destroyed
==17644== 
==17644== HEAP SUMMARY:
==17644==     in use at exit: 0 bytes in 0 blocks
==17644==   total heap usage: 4 allocs, 4 frees, 74,848 bytes allocated
==17644== 
==17644== All heap blocks were freed -- no leaks are possible
==17644== 
==17644== For lists of detected and suppressed errors, rerun with: -s
==17644== ERROR SUMMARY: 0 errors from 0 contexts (suppressed: 0 from 0)
```

### Applications

来看下面的例题，题源：https://acm.sjtu.edu.cn/OnlineJudge/problem/2640

{% note primary %}

stargazer 最近正在学习 Rust 语言的所有权机制与内存安全特性。

Rust 的所有权机制要求每个值都有唯一的所有者（通常是变量），并且在同一时间内只能有一个所有者。当变量的所有权被转移或其生命周期结束后，所有对该变量的借用（相当于 C++ 中指向变量的指针）都不能再被使用，否则会造成编译错误。Rust 的编译器可以在编译时就能“静态”地检查所有权和借用关系，从而在运行时无需额外检查的情况下保证了内存安全。

这种所有权机制虽然保证了内存安全，但是也使得一些数据结构的实现变得困难。因此，对于堆上对象的所有权，Rust 引入了类似于 C++ 中智能指针的机制，包括：

- `Rc<T>`：相当于 C++ 中的 `std::shared_ptr`，允许多个所有者共享实际对象，通过引用计数管理内存
- `Weak<T>`：相当于 C++ 中的 `std::weak_ptr`，不增加强引用计数，用于解决循环引用问题
- `Weak<T>` 必须先通过 `upgrade()` 方法转换为 `Rc<T>` 才能访问实际对象。若实际对象已被释放，则 `upgrade()` 会返回 `None`。C++ 中的 `std::weak_ptr` 也有类似的 `lock()` 方法。

在具体实现中：

- `Rc<T>` 与 `Weak<T>` 并不直接指向对象本身，而是指向一个“引用计数块”，内含三个成员：“强引用计数”、“弱引用计数”、“指向对象的指针”
- 强引用计数：指向该对象的 `Rc<T>` 的数量
- 弱引用计数：指向该对象的 `Weak<T>` 的数量
- 当强引用计数为 0 时，实际对象会被释放
- 当弱引用计数也为 0 时，引用计数块会被释放

`Weak<T>` 避免了循环引用问题。在双链表中，每个节点需要指向前一个和后一个节点。如果所有这些指针都是强引用（如 `Rc<T>` 或 `std::shared_ptr`），就会形成循环引用：节点A持有指向节点B的强引用，节点B也持有指向节点A的强引用，这会导致两者的引用计数永远不会降为0，即使外部不再引用这些节点，它们也不会被释放，从而造成内存泄漏。使用 `Weak<T>`（或 `std::weak_ptr`）可以打破这种循环。

请借鉴 Rust 中的所有权思想，在 C++ 中实现一个无需手动管理内存的双链表。

{% endnote %}

给出我的示例代码：

```cpp
#include <iostream>
#include <memory>
#include <optional>

template <typename T>
class DoublyLinkedList {
private:
    // Node structure definition
    struct Node {
        T value;
        std::shared_ptr<Node> next;
        std::weak_ptr<Node> prev;

        Node(const T& val = T()) : value(val), next(nullptr) {}
    };

    std::shared_ptr<Node> head;
    std::weak_ptr<Node> tail;
    size_t length;

public:
    // Iterator class
    class iterator {
    private:
        std::shared_ptr<Node> current;

    public:
        iterator(std::shared_ptr<Node> node) : current(node) {}

        // Dereference operator to get value
        T& operator*() {
            return current->value;
        }

        // Post-increment operator
        iterator operator++(int) {
            auto tmp = *this;
            current = current->next;
            return tmp;
        }

        // Equality operators
        bool operator==(const iterator& other) const {
            return current == other.current;
        }

        bool operator!=(const iterator& other) const {
            return !(*this == other);
        }
    };

    // Constructor
    DoublyLinkedList() : head(nullptr), length(0) {}

    // No destructor needed, memory managed by shared_ptr

    // Check if list is empty
    bool empty() const {
        return length == 0;
    }

    // Get the size of the list
    size_t size() const {
        return length;
    }

    // Add element to the front
    void push_front(const T& value) {
        auto new_node = std::make_shared<Node>(value);
        if (head) {
            new_node->next = head;
            head->prev = new_node;
        } else {
            tail = new_node; // If list was empty, update tail
        }
        head = new_node;
        length++;
    }

    // Add element to the back
    void push_back(const T& value) {
        auto new_node = std::make_shared<Node>(value);
        if (tail.lock()) {
            auto tail_node = tail.lock();
            tail_node->next = new_node;
            new_node->prev = tail_node;
        } else {
            head = new_node; // If list was empty, update head
        }
        tail = new_node;
        length++;
    }

    // Remove and return the first element
    std::optional<T> pop_front() {
        if (!head) {
            return std::nullopt;
        }
        auto value = head->value;
        head = head->next;
        if (head) {
            head->prev.reset(); // Clear the weak_ptr reference
        } else {
            tail.reset(); // If list becomes empty, clear tail
        }
        length--;
        return value;
    }

    // Remove and return the last element
    std::optional<T> pop_back() {
        if (empty()) {
            return std::nullopt;
        }
        auto tail_node = tail.lock();
        auto value = tail_node->value;
        if (tail_node->prev.lock()) {
            auto prev_node = tail_node->prev.lock();
            prev_node->next.reset();
            tail = prev_node;
        } else {
            head.reset(); // If list becomes empty, clear head
            tail.reset();
        }
        length--;
        return value;
    }

    // Iterators
    iterator begin() {
        return iterator(head);
    }

    iterator end() {
        return iterator(nullptr);
    }
};
```

> 本质上就是把使用`weak_ptr`破解的过程转化为**一个具体的双链表的实现**。

## `std::any`

C++作为一个强类型语言，对类型转换有着很高的要求，但是使用`std::any`，我们可以将C++用作弱类型语言。

```cpp
void foo() {
    std::any x = 114514; // 1
    if (auto ptr = std::any_cast<int>(&x); ptr != nullptr) { // 2
        std::cout << *ptr << std::endl; // 3
    }
    x = "qwerty"; // 4
    if (auto ptr = std::any_cast<int>(&x); ptr != nullptr) { // 5
        std::cout << (*ptr) + 114514 << std::endl; // 6
    }
}

```

- 一开始x被赋值为整数类型，此时for循环中的代码：`auto ptr = std::any_cast<int>(&x)`会判断ptr**是否可以转化为一个int*类型的指针**，如果可以就做取地址，如果不可以就变成`nullptr`

## `std::optional` and `std::variant`

### `std:optional`

`std::optional` 是一个封装类型，用于表示一个值可能存在或不存在。它可以用来替代指针或其他机制，以更安全地处理缺失值。

- **可选值**：`std::optional<T>` 可以存储类型 `T` 的值，或者什么都不存储（即空）。
- **安全性**：使用 `std::optional` 可以避免使用空指针，减少空指针解引用的风险。
- **简单的接口**：提供了简单的方法来检查值是否存在，并访问该值。

```cpp
#include <iostream>
#include <optional>

std::optional<int> getValue(bool returnValue) {
    if (returnValue) {
        return 42; // 返回一个值
    }
    return std::nullopt; // 返回空
}

int main() {
    auto value = getValue(true);
    if (value) {
        std::cout << "Value: " << *value << std::endl; // 输出 42
    } else {
        std::cout << "No value" << std::endl;
    }
    return 0;
}

```

### `std::variant`

`std::variant` 是一个类型安全的联合体，可以存储多种不同类型中的一种。它允许在运行时选择存储的类型，并提供安全的访问方式。

- **多态性**：`std::variant<T1, T2, ...>` 可以存储 `T1`、`T2` 等类型之一。
- **类型安全**：访问存储的值时，必须确保使用正确的类型，避免类型错误。
- **易于使用**：通过 `std::get` 和 `std::visit` 等函数，可以方便地访问和处理存储的值。

```cpp
#include <iostream>
#include <variant>

std::variant<int, std::string> getValue(bool returnInt) {
    if (returnInt) {
        return 42; // 返回整数
    }
    return "Hello"; // 返回字符串
}

int main() {
    auto value = getValue(false);
    
    // 使用 std::visit 处理不同类型
    std::visit([](auto&& arg) {
        std::cout << arg << std::endl; // 输出 "Hello"
    }, value);
    
    return 0;
}

```

## Rust简介

### Rust历史简介

**Rust**作为一门新的编程语言，创始与2006年（我出生了）。Rust 的创始人 Graydon Hoare 在 Mozilla 开始了这个项目。最初，Rust 是作为一个个人项目进行开发，目的是解决 **C++ 中的一些问题，特别是在内存管理和并发方面**。

- **2010年**：Rust 的第一个公开版本（0.1）发布，标志着语言的初步成型。此时，Rust 开始吸引更多的关注和开发者。

- **2010年**：Mozilla 正式加入 Rust 项目，提供资金和资源支持。Rust 被视为 Mozilla 的未来编程语言，尤其是在开发 **Firefox** 浏览器时。

- **2015年**：Rust 1.0 发布，标志着语言的稳定版本。此后，Rust 的生态系统和社区迅速发展，越来越多的库和工具相继推出。
- **2024年**：Rust不断发展壮大，自从 2022 年 Linux 内核宣布引入 Rust 语言以来，这个社区内部掀起了一场意料之外的风波。不久前，Rust for Linux 项目的核心维护者 Wedson Almeida Filho 决定退出该项目，他坦言已厌倦了社区中不断增多的、与技术无关的争论，其中不乏涉及 Rust 与 C 的语言之争。

Rust 的设计理念强调内存安全和并发性，**采用所有权（ownership）、借用（borrowing）和生命周期（lifetimes）等概念**，旨在避免常见的内存错误（如空指针解引用和数据竞争）。

### Hello Rust！

#### 简单的猜数字小游戏

- Rust是强类型编程语言，在做类型转换的时候需要显式声明。
	- 但是类似于C++的auto，rust编译器也可以实现自动判断类型，但并不妨碍其**严格的类型检查**。
- Rust的变量**默认不可变**，如果是可变借用的话需要声明`mut`。

```rust
// use std::io;
use rand::Rng;

fn main() {
    println!("For the guessing game.");
    let secret_number = rand::rng().random_range(1..=100);
    println!("The secret number: {}", secret_number);

    loop {
        println!("Please input your guess:");
        let mut guess = String::new();

        std::io::stdin().read_line(&mut guess).expect("Fuck!");
        let guess = guess.trim();
        println!("Your guess is {}", guess);
        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => {
                println!("Invalid input, try again.");
                continue;
            }
        };
        match guess.cmp(&secret_number) {
            std::cmp::Ordering::Less => println!("Too small!"),
            std::cmp::Ordering::Greater => println!("Too big!"),
            std::cmp::Ordering::Equal => {
                println!("Wow! Your guess is right!");
                break;
            }
        }
    }
}

```

#### 变量与常量

- 常量不可以使用`mut`。

![image](https://s1.imagehub.cc/images/2025/04/16/6c90737d12544166af6ca30d8f7bd454.png)

- shadowing
	- 声明**同名变量**覆盖原来的变量。
	- 如果不使用let关键字，对非mut原来变量进行赋值会产生报错。
	- 使用let声明的新变量可以与原来不同。

#### 数据类型

**Rust**是静态编译语言，需要再编译的时候知道**所有变量的数据类型**。

- 基于使用的值，Rust的编译器可以自动推断出使用的类型。
- **在有必要时需要加类型标注**。

**标量类型**：

- 整数类型（u32（无符号），**i32**（有符号），i64，u64...）
	- 存在溢出现象，但是编译器会检查溢出。（panic）
	- 在发布模式下，和C++类似，会发生环绕现象。
- 浮点数类型 （默认 **f64**）
- 波尔类型：true 和 false
- 字符类型：（**char**）
	- Rust的字符类型是4字节（C++是1字节），因此可以表示更多的含义，例如中日韩文字和emoji等。（**Unicode**）
	- 这不来一个恶搞？

```rust
// test for emoji
let emoji = '👀';
println!("{}", emoji);
```

**复合类型**

- Tuple

	- 每个位置对应一个类型，每一个类型不一定要相同。


```rust
// test tuple
let tip: (i32, i32, i32) = (599, 44, 33);
let (x, y, z) = tip;
println!("{} {} {}", x, y, z);
```

- 数组
	- 数组越界会在运行时报错。
	- 和C/C++不同的时，Rust中**不允许访问数组内存的下一个元素**（这在C++中是被允许的）。
	- **Rust**的安全性不容质疑！

```rust
// test array
let x = [1, 2, 2, 3, 4, 4];
let b = [3;5];
println!("{}", x[1]);
println!("{}", b[1]);
```

#### 函数

- 使用**snake_case**作为命名规范。
- **Rust不太关注函数声明的顺序**。
- 函数形式参数的类型必须要**显示声明**！
- Rust也是一个**基于表达式的语言**。但是和C语言不同的是，Rust严格区分了**语句和表达式**。
	- 例如`x + 1`是一个表达式（具有返回值），但是`x + 1;`是一个语句（带分号）
- 在Rust里面，函数的返回值就是函数里面最后一个表达式的值。（可以使用return提前返回）

```rust
fn test_function(x: i32, y: i32) -> i32{
    let z = x + y;
    z
}
```

#### if else

- 分支（arm）
- **if后面的表达式的类型必须严格是布尔类型**！！！
- **可以使用match**来重构代码。
- 因为rust要保证安全性，因此必须要使用**强类型语言**，而if-else语句本身就是一个表达式（需要判断**其类型**），因此需要if和else块中返回值的类型是完全相同的。

```rust
fn test_if (x: i32) -> i32{
    let number = 3;
    if number < x {
        5
    } else if number == x  {
        6
    }else {
        7
    }
}
```

#### 循环

##### loop循环

相当于`while(true)`，可以使用break退出。

##### while条件循环

和C++非常类似。

##### for循环遍历集合

```rust
fn test_for_loop (a: [i32; 5]) {
    for element in a.iter() {
        println!("{}", element);
    }
}
```

###### Range

这里又很像Python...

```rust
fn test_for_loop (a: [i32; 5]) {
    for element in a.iter() {
        println!("{}", element);
    }
    
    for number in (1..10).rev() {
        println!("Wow{}", number);
    }
}
```

{% note primary %}

**至此，Rust的所有语法的基本逻辑已经全部涉及了**，但是到现在，我们还没有接触Rust最核心且最精华的部分：**所有权的控制**，相信我，这也是相当精彩的一章！笔者需要开一个新坑来完成这一个教程！

{% endnote %}
