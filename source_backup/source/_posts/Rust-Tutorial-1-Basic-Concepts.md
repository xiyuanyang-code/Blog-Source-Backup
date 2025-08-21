---
title: 'Rust Tutorial 1: Basic Concepts'
date: 2025-08-21 23:20:07
index_img: /img/cover/rust1.jpg
excerpt: Basic concepts of Rust programming language, including variables, data types, functions and control expressions.(while, if, loop, etc). Rust is a programming based on expressions. Finally, this blog includes a basic guessing-number game.
categories:
  - Code
  - Rust
tags:
  - tutorial
  - Finished
  - Rust
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Rust Tutorial 1: Basic Concepts

## `Hello, World!`与 Cargo

### `Hello, World!`

每个编程语言的学习都从 `Hello, World!` 开始。在 Rust 中，这简单而经典的程序揭示了一个核心概念：宏。

```rust
// A basic "Hello, World!" program in Rust
fn main() {
    println!("Hello, world!");
}
```

代码中的 `println!` 并非一个普通的函数，而是一个**宏**（Macro）。宏是一种元编程工具，允许开发者编写能够生成代码的代码。与运行时调用的函数不同，宏在编译时被展开，用生成的代码替换宏调用。

  - **编译时展开**：宏在编译阶段被处理，编译器根据宏定义生成新的 Rust 代码，并用其替换宏调用。这与 C/C++ 的预处理器宏（`#define`）有相似之处，但 Rust 的宏系统更为强大和安全，因为它是在抽象语法树（AST）级别上操作。
  - **作用**：宏的主要作用是减少重复代码。
  - **标识**：宏调用通常以感叹号 `!` 结尾，例如 `println!` 和 `vec!`，这是一种特殊的语法标记，用于将其与函数调用区分开来。
  - **宏与函数的区别**：宏可以接受**可变数量的参数**，而 Rust 的函数不能。此外，宏能够生成整个结构体或枚举的定义，这在实现类似序列化（如 Serde）的复杂功能时非常有用。

### Cargo：Rust 的包管理工具

**Cargo** 是 Rust 官方的包管理和构建系统。它简化了项目的编译、运行和依赖管理。

  - `cargo build`：编译项目。
  - `cargo run`：编译并运行项目。
  - `cargo check`：快速检查代码是否存在编译错误，但不生成可执行文件，非常适合快速迭代。
  - `cargo clean`：清理项目编译产生的中间文件。

Rust 的项目由 **crate** 构成，它是 Rust 代码的最小编译单元。

  - **Binary crate**：编译后产生一个可执行文件，必须包含 `main` 函数作为程序入口。
  - **Library crate**：编译后产生库文件，不包含 `main` 函数，可被其他 crate 引用。


## 变量

### 变量与常量

Rust 对变量的**不可变性**（immutability）有严格要求，这有助于编写更安全、更并发的代码。

#### 变量：默认不可变

```rust
// Example of an immutable variable
fn main() {
    let x = 3;
    // x = 5; // This would cause a compile-time error
}
```

默认情况下，变量是不可变的。若要允许变量的值在声明后被修改，必须在 `let` 关键字后添加 `mut` 关键字。

```rust
// Example of a mutable variable
fn main() {
    let mut x = 3;
    x = 5; // This is now allowed
}
```

另一种改变变量值的方式是**变量遮蔽（Shadowing）**。通过使用相同的变量名再次声明一个新变量，新变量会“遮蔽”旧变量。

```rust
fn main() {
    let x = 3;
    let x = 5; // The new 'x' shadows the old one
    let x = x + 1; // You can even use the old value to compute the new one
}
```

遮蔽比 `mut` 更强大，因为它允许改变变量的类型，并且可以限定在特定的作用域内。

#### 常量：编译时绑定

常量的声明比变量更为严格，核心在于**常量在编译时而不是运行时绑定**。

  - 声明使用 `const` 关键字。
  - 必须显式提供类型注解。
  - 无法被遮蔽，也无法在运行时进行计算。
  - 始终存在于全局作用域中，且在整个程序生命周期内有效。

## 数据类型

Rust 是一种静态类型的语言，即**在程序编译阶段**所有变量的类型都必须被确定并且检查通过。但是和 C++ 不同的是，Rust 引入了 `let` 关键字，这样创建新变量的过程中不需要显示地规定变量类型。因此，在做类型转换的时候，需要**显示定义类型注解**来防止编译器陷入困惑。（尤其在类型转换的结果有很多可能的时候）

Rust 的数据类型可以分为两大类：**标量类型 (Scalar Types)** 和 **复合类型 (Compound Types)**。

### 标量类型 (Scalar Types)

标量类型代表一个单一的值。Rust 内置了四种主要的标量类型：整数、浮点数、布尔值和字符。

#### 整数 (Integers)

整数类型用于存储没有小数部分的数字。Rust 提供了多种整数类型，每种都具有指定的大小（位数）和符号（有符号或无符号）。

  * **有符号整数 (`i`)**: 可以是正数、负数或零。类型包括 `i8`, `i16`, `i32`, `i64`, `i128`。
  * **无符号整数 (`u`)**: 只能是正数或零。类型包括 `u8`, `u16`, `u32`, `u64`, `u128`。
  * **架构相关类型 (`isize`, `usize`)**: 它们的位数取决于你的电脑架构。在 64 位系统上，它们是 64 位；在 32 位系统上，它们是 32 位。`usize` 主要用于数组索引和大小，因为它能保证在任何系统上都能表示内存中的所有位置。

#### 浮点数 (Floating-Point Numbers)

浮点数用于存储有小数部分的数字。Rust 有两种浮点类型：

  * `f32`: 32 位单精度浮点数。
  * `f64`: 64 位双精度浮点数，是默认的浮点类型。

#### 布尔值 (Booleans)

布尔类型只有两个可能的值：`true` 或 `false`。它通常用于条件判断。

#### 字符 (Characters)

Rust 的 `char` 类型是一个 Unicode 字符，而不是简单的 ASCII 字符。这意味着它可以表示任何语言的字母、数字、标点符号，甚至是表情符号。`char` 类型占 4 个字节。

```rust
let my_char = '❤️';
```


### 复合类型 (Compound Types)

复合类型可以将多个值组合成一个类型。Rust 提供了两种基本的复合类型：元组和数组。

#### 元组 (Tuples)

元组是一种将多个不同类型的值打包成一个复合类型的方法。一旦创建，它的长度就不能改变。

  * 你可以使用索引来访问元组的元素。
  * 元组的类型由其内部元素的类型和顺序决定。

<!-- end list -->

```rust
let my_tuple = (500, 6.4, "hello");
let (x, y, z) = my_tuple; // 解构
let first_item = my_tuple.0; // 使用索引访问
```

#### 数组 (Arrays)

数组是一种将多个**相同类型**的值放入一个固定长度的集合中的方式。

  * **数组的长度是固定的，不能改变**。
  * 数组的类型由**元素的类型和长度**共同决定，例如 `[i32; 5]` 代表一个包含 5 个 32 位整数的数组。
  * 数组的元素在内存中是连续存储的。
  * 数组的内存在栈上分配。

<!-- end list -->

```rust
let my_array: [i32; 5] = [1, 2, 3, 4, 5];
let first_element = my_array[0]; // 使用索引访问
```

除了这些基本的数据类型，Rust 还提供了更高级的类型，比如 **切片 (`&[T]`)**、**字符串 (`String` 和 `&str`)**、**枚举 (`enum`)** 和 **结构体 (`struct`)** 等。这些类型构成了 Rust 强大而安全的类型系统的基础。

> 一个复合类型的大小是其所有字段大小的总和，加上为了满足对齐要求而产生的填充（padding）。这一点和 C++ 保持一致。

```text
--- 标量类型 ---
i8:    1 bytes
u8:    1 bytes
i16:   2 bytes
u16:   2 bytes
i32:   4 bytes
u32:   4 bytes
i64:   8 bytes
u64:   8 bytes
i128:  16 bytes
u128:  16 bytes
isize: 8 bytes
usize: 8 bytes
f32:   4 bytes
f64:   8 bytes
bool:  1 bytes
char:  4 bytes

--- 复合类型 ---
():      0 bytes
(i32, f64): 16 bytes
[i32; 3]: 12 bytes
```

### 溢出 

Rust 在处理整数溢出方面比 C/C++ 更安全。

  - 在 **Debug 模式**下，整数溢出会导致程序 `panic`（程序终止）。
  - 在 **Release 模式**下，溢出会发生“环绕”（Wrapping），从最大值重新回到最小值。

为了显式控制溢出行为，Rust 提供了如下方法：

  - `wrapping_add`：环绕加法，用于模块化算术。
  - `checked_add`：检查加法，溢出时返回 `None`，否则返回 `Some(result)`。
  - `saturating_add`：饱和加法，溢出时将结果饱和到类型的最大值或最小值。
  - `overflowing_add`：返回一个元组，包含计算结果和是否溢出的布尔值。


#### `wrapping_add`（环绕加法）

**功能**：当计算结果超出整数类型的范围时，它会**环绕**（或称“回卷”），从最小值或最大值重新开始。

**用途**：

  * 处理需要模块化算术（Modular Arithmetic）的场景，比如哈希函数、加密算法、循环数组索引等。
  * 在发布模式下，这是 Rust 整数溢出的默认行为。

**示例**：

```rust
let x: u8 = 250;
let y = 10;
let result = x.wrapping_add(y); // 250 + 10 = 260
                                // 260 超过了 u8 的最大值 255
                                // 结果会环绕：260 - 256 = 4
println!("Result: {}", result); // 输出：4
```

#### `checked_add`（检查加法）

**功能**：执行加法，并在发生溢出时返回一个 `Option` 类型。如果结果没有溢出，返回 `Some(result)`；如果溢出，返回 `None`。

**用途**：

  * 这是最安全的处理溢出的方法，让你有机会在运行时优雅地处理错误，而不是让程序直接恐慌。
  * 非常适合需要确保计算结果不会溢出的场景。

**示例**：

```rust
let x: u8 = 250;
let y = 10;
let result = x.checked_add(y);

if let Some(sum) = result {
    println!("Sum is {}", sum);
} else {
    println!("Overflow occurred!"); // 发生了溢出，打印此行
}
```


#### `saturating_add`（饱和加法）

**功能**：当计算结果超出整数类型的范围时，它会将结果\*\*“饱和”\*\*到该类型的最大值或最小值。

**用途**：

  * 防止意外的环绕行为，将结果限制在可接受的范围内。
  * 常用于图形处理、音频处理或任何需要防止数据“回卷”的场景。

**示例**：

```rust
let x: u8 = 250;
let y = 10;
let result = x.saturating_add(y); // 250 + 10 = 260
                                  // 260 超过了 u8 的最大值 255
                                  // 结果会被“饱和”到 u8 的最大值 255
println!("Result: {}", result); // 输出：255
```


#### `overflowing_add`（溢出加法）

**功能**：执行加法，并返回一个包含**计算结果**和**是否溢出**的元组。

**用途**：

  * 需要同时获取计算结果和溢出状态的场景。
  * 通常用于自定义溢出逻辑或调试。

**示例**：

```rust
let x: u8 = 250;
let y = 10;
let (result, overflowed) = x.overflowing_add(y);

println!("Result: {}, Overflowed: {}", result, overflowed);
// 250 + 10 会环绕到 4，并标记为溢出
// 输出：Result: 4, Overflowed: true
```

> Panic in Rust
> 在 Rust 中，Panic 是一种不可恢复的错误状态，它表明程序遇到了一个非常严重的问题，无法继续安全地执行下去。当 Rust 程序发生 panic 时，它会：

- 打印一条错误信息。
- 清理栈（unwind the stack），释放所有函数栈帧所拥有的资源。
- 最终，程序会直接终止。
- 可以把 panic 理解为程序对自己说：“我不知道该如何处理这个致命的错误，继续运行下去只会让情况更糟，所以我选择干净利落地退出。”


## 函数

函数的定义使用 `fn` 关键字，参数需要显式提供类型注解。

```rust
// A function that returns a value
fn add(x: i32, y: i32) -> i32 {
    x + y // No semicolon, this is an expression and will be returned
}
```

### 语句与表达式

这是 Rust 与 C/C++ 哲学上的一个显著区别。

  - **语句（Statements）**：执行指令，但不返回值。例如 `let x = 5;`。
  - **表达式（Expressions）**：求值后产生一个值。例如 `x + y`。

Rust 是一门**基于表达式**的语言。`if` 块、`match` 块以及用大括号 `{}` 括起来的代码块都是表达式，它们会产生一个值。函数的最后一行如果是一个表达式，并且没有分号结尾，其值将作为函数的返回值。

```rust
// An if expression
let x = if 1 == 2 { 5 } else { 10 };

// A block expression
let y = {
    let z = 1;
    z + 2 // The value of this expression is 3
};
```

例如下面的一段代码：

```rust
fn main() {
    println!("Hello world!");
    expression();
}

fn expression() {
    let x = if 1 == 2 { 5 } else { 10 };
    let y = {
        let z = 1;
        z + 2
    };
    println!("x value is {}", x);
    println!("y value is {}", y);
}
```

- 使用 let 实现变量的创建过程是一个语句而不是表达式（因为这个过程本身不会产生返回值，并且最后使用分号结尾）
- 但是使用 `{}` 括起来的部分是一个表达式（同时，他还是限定范围的作用域的开始），他的返回值是该代码块中最后一个表达式的返回值。
    - 因此如果在上面的代码中多加了一个分号，写成了 `z + 2;`，那这个代码块的返回值就是**空**，是一个空元组，会导致编译失败。
    - 这也可以作为函数返回值的设计，同样，if else 块也遵循相同的返回值返回原理。
    ```rust
    fn pow(x: i32, p: u32) -> i64 {
    let mut result: i64 = 1;
    for _i in 0..p {
        result = result * (x as i64);
    }
    result
    }
    ```

## 控制流：循环与分支

### 条件语句：`if`

Rust 的 `if` 表达式的条件**必须是布尔值**。它不像 C/C++ 那样允许整数自动转换为布尔值（非零即真）。

### 循环：`loop`, `while`, `for`

  - `loop`：创建无限循环，可使用 `break` 语句退出。
  - `while`：当条件为真时执行循环。
  - `for`：遍历迭代器（如数组、切片或范围）中的每个元素。

<!-- end list -->

```rust
// A for loop iterating over a range
for number in (1..4).rev() {
    println!("{number}!");
}
```

此外，`loop` 循环可以使用**循环标签**（loop labels），允许 `break` 或 `continue` 跳出或跳过特定层级的嵌套循环，这在处理复杂循环逻辑时非常有用。

```rust
fn main() {
    println!("Hello world!");
    expression();
    println!("Demo for pow function: {}", pow(10, 2));

    if_else();

    println!("{}", always_running());

    loop_label();
    loop_label_2();
    loop_label_3();

    println!("Fibonacci: {}", fibonaci(5));
}

fn expression() {
    let x = if 1 == 2 { 5 } else { 10 };
    let y = {
        let z = 1;
        z + 2
    };
    println!("x value is {}", x);
    println!("y value is {}", y);
}

fn pow(x: i32, p: u32) -> i64 {
    let mut result: i64 = 1;
    for _i in 0..p {
        result = result * (x as i64);
    }
    result
}

fn if_else() {
    let x = 1;
    if x == 1 {
        println!("It is True");
    }
}

fn always_running() -> i32 {
    let mut counter = 0;
    let result = loop {
        counter += 1;
        if counter == 10 {
            break counter * 2;
        }
    };
    result
}

fn loop_label() {
    let mut count = 0;
    'counting_up: loop {
        println!("count = {count}");
        let mut remaining = 10;
        loop {
            println!("remaining = {remaining}");
            if remaining == 9 {
                break;
            }
            if count == 2 {
                break 'counting_up;
            }
            remaining -= 1;
        }
        count += 1;
    }
    println!("Counting up end.")
}

fn loop_label_2() {
    let mut i = 0;

    'outer_while: while i < 5 {
        println!("外层循环: i = {}", i);

        let mut j = 0;
        'inner_while: while j < 5 {
            println!("  内层循环: j = {}", j);

            if j == 2 {
                // 这个 break 语句只会退出内层循环
                break 'inner_while;
            }
            if i == 3 {
                // 使用循环标签，直接跳出外层循环
                break 'outer_while;
            }
            j += 1;
        }

        i += 1;
    }

    println!("循环结束");
}

fn loop_label_3() {
    'outer_for: for i in 1..=3 {
        println!("外层循环: i = {}", i);

        'inner_for: for j in 1..=3 {
            println!("  内层循环: j = {}", j);

            if i == 2 && j == 2 {
                // 这个 `break` 语句会直接跳出外层的 'outer_for 循环
                // 整个程序将提前结束
                break 'outer_for;
            }

            if i == 3 {
                break 'inner_for;
            }
        }
    }

    println!("所有循环已结束");
}

fn fibonaci(index: i32) -> i32 {
    if index == 0 {
        return 0;
    };
    if index == 1{
        return 1;
    }

    let mut pre = 1;
    let mut pre_pre = 0;
    for _ in 2..=index {
        let next = pre + pre_pre;
        pre_pre = pre;
        pre = next;
    };

    pre
}
```

## Guessing Number Game

这个简单的猜数字游戏项目可以帮助我们理解 Rust 的许多核心概念。

### 变量绑定与关联函数

```rust
let mut guess = String::new();
```

这里的 `String::new()` 是一个**关联函数**。关联函数是与特定类型（如 `String`）关联的函数，但不需要 `self` 参数。它们通过类型本身使用双冒号 `::` 调用，类似于 C++ 中的静态成员函数。`String::new()` 是一个典型的构造函数，用于创建 `String` 类型的新实例。

### 错误处理：`Result` 类型与 `expect`

`std::io::stdin().read_line()` 方法返回一个 `Result` 类型的值。`Result` 是一个枚举（enum），它有两个成员：`Ok` 和 `Err`，代表成功或失败。

  - 如果 `read_line` 调用成功，它返回一个 `Ok` 成员，其中包含读取的字节数。
  - 如果失败（例如底层操作系统错误），它返回一个 `Err` 成员，其中包含错误信息。

`Result` 类型拥有 `expect` 方法。如果 `Result` 是 `Err`，`expect` 会导致程序 `panic` 并打印传递给它的错误信息；如果 `Result` 是 `Ok`，`expect` 会解封 `Ok` 中的值并返回它。这是一种快速处理可恢复错误的机制。

### 类型转换与变量遮蔽

```rust
let guess: u32 = guess.trim().parse().expect("Please type a number.");
```

这行代码展示了类型转换和变量遮蔽。`guess.trim()` 去除字符串首尾的空白，`parse()` 将字符串解析成一个数字，但因为 `parse` 可以解析多种数字类型，编译器无法推断类型，因此需要**显式类型注解** `guess: u32`。

新的 `guess` 变量遮蔽了旧的 `guess` 字符串变量，这在 Rust 中是一种常见的模式，允许我们在同一作用域内重用变量名，同时改变其类型。

### Traits：Rust 的接口

**Trait** 是 Rust 的一个核心概念，它定义了类型可以共享的功能。你可以将其理解为一种接口或协议，规定了**如果一个类型实现了某个 Trait，它就必须提供特定的方法**。

  - **Trait 设计**：Rust 的作用域规则要求，即使一个类型（例如 `rand::rng()` 返回的类型）实现了某个 trait，你也不能直接调用该 trait 定义的方法，除非你显式地将这个 trait 引入到你的作用域中（使用 `use` 语句）。
  - **孤儿规则**：这是为了防止不同库对同一个类型实现同一个 trait 时发生冲突，从而保证了类型和 trait 实现的一致性。

### Source Code

```rust

use rand::Rng;
use std::cmp::Ordering;

fn main() {
    println!("Hello World, this is a guessing number game");

    let secret_number = rand::rng().random_range(1..=100);
    // println!("The secret number is {secret_number}");

    loop {
        println!("please input your guess");
        let mut guess = String::new();
        std::io::stdin()
            .read_line(&mut guess)
            .expect("Fail to read lines");

        if guess.trim() == "quit" {
            break;
        }

        let guess: u32 = match guess.trim().parse() {
            Ok(num) => num,
            Err(_) => {
                println!("Error, please type a number");
                continue;
            }
        };

        match guess.cmp(&secret_number) {
            Ordering::Less => println!("Too small!"),
            Ordering::Equal => {
                println!("Exactly the same!");
                break;
            }
            Ordering::Greater => {
                println!("Too big!");
            }
        }
    }
}
```