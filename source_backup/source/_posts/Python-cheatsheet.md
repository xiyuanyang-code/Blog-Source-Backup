---
title: Python-cheatsheet
date: 2025-01-15 21:15:00
index_img: /img/cover/python.jpg
excerpt: This Blog focuses on the basic grammatical usage of Python. Please ensure you are familiar at at least one programming language such as C.
categories:
  - Code
  - Python
tags:
  - Tutorial
  - Finished
  - Python
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Python Quick Tutorial

## The Zen of Python

<div style="text-align: center;">
Beautiful is better than ugly.<br>
Explicit is better than implicit.<br>
Simple is better than complex.<br>
Complex is better than complicated.<br>
Flat is better than nested.<br>
Sparse is better than dense.<br>
Readability counts.<br>
Special cases aren't special enough to break the rules.<br>
Although practicality beats purity.<br>
Errors should never pass silently.<br>
Unless explicitly silenced.<br>
In the face of ambiguity, refuse the temptation to guess.<br>
There should be one-- and preferably only one --obvious way to do it.<br>
Although that way may not be obvious at first unless you're Dutch.<br>
Now is better than never.<br>
Although never is often better than right now.<br>
If the implementation is hard to explain, it's a bad idea.<br>
If the implementation is easy to explain, it may be a good idea.<br>
Namespaces are one honking great idea -- let's do more of those!<br>
</div>
## Announcement

[详情请点这里](https://xiyuanyang-code.github.io/posts/Python-tutorial/)

这是**Python快速教程系列**的第一篇，内容覆盖：**Python中的各种数据类型/循环/函数/面向对象编程**等基础的语法知识。 

## Table of Contents

- Data Types
- String
- Basic Data Structures
  - List
  - Tuple
  - Dictionaries
- Loops
- Functions
- OOP

## Data Types

在 Python 中，数据类型可以分为 **可变数据类型**（Mutable Data Types）和 **不可变数据类型**（Immutable Data Types）。它们的区别在于是否可以在创建后修改其内容。

### **Immutable Data Types**

不可变数据类型是指一旦创建，其内容就不能被修改的数据类型。如果尝试修改不可变对象，Python 会创建一个新的对象，而不是修改原对象。

1. **整数（`int`）**：
   ```python
   x = 10
   x = x + 5  # 创建一个新的整数对象，x 指向新对象
   ```

2. **浮点数（`float`）**：
   ```python
   y = 3.14
   y = y + 1.0  # 创建一个新的浮点数对象，y 指向新对象
   ```

3. **字符串（`str`）**：
   ```python
   s = "hello"
   s = s + " world"  # 创建一个新的字符串对象，s 指向新对象
   ```

4. **元组（`tuple`）**：
   ```python
   t = (1, 2, 3)
   t = t + (4,)  # 创建一个新的元组对象，t 指向新对象
   ```

5. **布尔值（`bool`）**：
   ```python
   b = True
   b = False  # 创建一个新的布尔对象，b 指向新对象
   ```

6. **冻结集合（`frozenset`）**：
   ```python
   fs = frozenset([1, 2, 3])
   fs = fs.union([4])  # 创建一个新的冻结集合对象，fs 指向新对象
   ```

### Mutable Data Types

可变数据类型是指可以在创建后修改其内容的数据类型。修改可变对象时，不会创建新的对象，而是直接修改原对象。

1. **列表（`list`）**：
   ```python
   lst = [1, 2, 3]
   lst.append(4)  # 直接修改原列表
   ```

2. **字典（`dict`）**：
   ```python
   d = {"a": 1, "b": 2}
   d["c"] = 3  # 直接修改原字典
   ```

3. **集合（`set`）**：
   ```python
   s = {1, 2, 3}
   s.add(4)  # 直接修改原集合
   ```

4. **字节数组（`bytearray`）**：
   ```python
   ba = bytearray(b"hello")
   ba[0] = 72  # 直接修改原字节数组
   ```

5. **自定义类的对象**：
   ```python
   class MyClass:
       def __init__(self, value):
           self.value = value
   
   obj = MyClass(10)
   obj.value = 20  # 直接修改对象的属性
   ```

## String in Python

在 C/C++ 中，字符串的实现的核心是`char*`（依靠指针操作内存）来实现的。但是在Python中，字符串在**使用**上相对来说更加灵活，方法也更丰富。

1. **大小写转换**

- `str.capitalize()`：将字符串的第一个字符大写，其余字符小写。
  
  ```python
  name = "hello world"
  print(name.capitalize())  # 输出: Hello world
  ```
  
- `str.casefold()`：将字符串转换为小写，支持更多语言（如德语）。
  
  ```python
  name = "HELLO WORLD"
  print(name.casefold())  # 输出: hello world
  ```
  
- `str.lower()`：将字符串转换为小写。
  
  ```python
  name = "HELLO WORLD"
  print(name.lower())  # 输出: hello world
  ```
  
- `str.upper()`：将字符串转换为大写。
  
  ```python
  name = "hello world"
  print(name.upper())  # 输出: HELLO WORLD
  ```
  
- `str.swapcase()`：将字符串中的大小写互换。
  
  ```python
  name = "Hello World"
  print(name.swapcase())  # 输出: hELLO wORLD
  ```
  
- `str.title()`：将字符串中每个单词的首字母大写。
  ```python
  name = "hello world"
  print(name.title())  # 输出: Hello World
  ```

2. **对齐与填充**

- `str.center(width[, fillchar])`：将字符串居中，并用指定字符填充至指定宽度。
  
  ```python
  name = "hello"
  print(name.center(10, "-"))  # 输出: --hello---
  ```
  
- `str.ljust(width[, fillchar])`：将字符串左对齐，并用指定字符填充至指定宽度。
  
  ```python
  name = "hello"
  print(name.ljust(10, "-"))  # 输出: hello-----
  ```
  
- `str.rjust(width[, fillchar])`：将字符串右对齐，并用指定字符填充至指定宽度。
  ```python
  name = "hello"
  print(name.rjust(10, "-"))  # 输出: -----hello
  ```

- `str.zfill(width)`：用 `0` 填充字符串至指定宽度。
  
  ```python
  num = "42"
  print(num.zfill(5))  # 输出: 00042
  ```

3. **查找与替换**

- `str.find(sub[, start[, end]])`：查找子字符串，返回第一次出现的索引，未找到返回 `-1`。
  ```python
  name = "hello world"
  print(name.find("world"))  # 输出: 6
  ```

- `str.rfind(sub[, start[, end]])`：从右向左查找子字符串，返回第一次出现的索引，未找到返回 `-1`。
  ```python
  name = "hello world"
  print(name.rfind("o"))  # 输出: 7
  ```

- `str.index(sub[, start[, end]])`：查找子字符串，返回第一次出现的索引，未找到抛出 `ValueError`。
  ```python
  name = "hello world"
  print(name.index("world"))  # 输出: 6
  ```

- `str.rindex(sub[, start[, end]])`：从右向左查找子字符串，返回第一次出现的索引，未找到抛出 `ValueError`。
  ```python
  name = "hello world"
  print(name.rindex("o"))  # 输出: 7
  ```

- `str.replace(old, new[, count])`：替换字符串中的子字符串。
  ```python
  name = "hello world"
  print(name.replace("world", "python"))  # 输出: hello python
  ```

4. **分割与连接**

- `str.split(sep=None, maxsplit=-1)`：根据分隔符分割字符串，返回列表。
  ```python
  name = "hello,world,python"
  print(name.split(","))  # 输出: ['hello', 'world', 'python']
  ```

- `str.rsplit(sep=None, maxsplit=-1)`：从右向左分割字符串，返回列表。
  ```python
  name = "hello,world,python"
  print(name.rsplit(",", 1))  # 输出: ['hello,world', 'python']
  ```

- `str.splitlines([keepends])`：按行分割字符串，返回列表。
  ```python
  text = "hello\nworld\npython"
  print(text.splitlines())  # 输出: ['hello', 'world', 'python']
  ```

- `str.join(iterable)`：将可迭代对象中的元素用字符串连接。
  ```python
  words = ["hello", "world", "python"]
  print("-".join(words))  # 输出: hello-world-python
  ```

5. **去除空白字符**

- `str.strip([chars])`：去除字符串两端的空白字符或指定字符。
  ```python
  name = "  hello world  "
  print(name.strip())  # 输出: hello world
  ```

- `str.lstrip([chars])`：去除字符串左端的空白字符或指定字符。
  ```python
  name = "  hello world  "
  print(name.lstrip())  # 输出: hello world  
  ```

- `str.rstrip([chars])`：去除字符串右端的空白字符或指定字符。
  ```python
  name = "  hello world  "
  print(name.rstrip())  # 输出:   hello world
  ```

6. **判断与验证**

- `str.startswith(prefix[, start[, end]])`：判断字符串是否以指定前缀开头。
  ```python
  name = "hello world"
  print(name.startswith("hello"))  # 输出: True
  ```

- `str.endswith(suffix[, start[, end]])`：判断字符串是否以指定后缀结尾。
  ```python
  name = "hello world"
  print(name.endswith("world"))  # 输出: True
  ```

- `str.isalnum()`：判断字符串是否只包含字母和数字。
  ```python
  name = "hello123"
  print(name.isalnum())  # 输出: True
  ```

- `str.isalpha()`：判断字符串是否只包含字母。
  ```python
  name = "hello"
  print(name.isalpha())  # 输出: True
  ```

- `str.isdigit()`：判断字符串是否只包含数字。
  ```python
  num = "123"
  print(num.isdigit())  # 输出: True
  ```

- `str.islower()`：判断字符串是否全部为小写。
  ```python
  name = "hello"
  print(name.islower())  # 输出: True
  ```

- `str.isupper()`：判断字符串是否全部为大写。
  ```python
  name = "HELLO"
  print(name.isupper())  # 输出: True
  ```

- `str.isspace()`：判断字符串是否只包含空白字符。
  ```python
  name = "   "
  print(name.isspace())  # 输出: True
  ```

7. **格式化**

- `str.format(*args, kwargs)`**：格式化字符串。
  ```python
  name = "world"
  print("hello {}".format(name))  # 输出: hello world
  ```

- `f-string`：Python 3.6 引入的**格式化方法**。
  
  ```python
  name = "world"
  print(f"hello {name}")  # 输出: hello world
  ```

8. **其他方法**

- `str.count(sub[, start[, end]])`：统计子字符串出现的次数。
  ```python
  name = "hello world"
  print(name.count("o"))  # 输出: 2
  ```

- `str.encode(encoding="utf-8", errors="strict")`：将字符串编码为字节。
  ```python
  name = "hello"
  print(name.encode())  # 输出: b'hello'
  ```

- `str.maketrans(x[, y[, z]])`：创建字符映射表，用于 `translate()`。
  ```python
  trans = str.maketrans("aeiou", "12345")
  print("hello".translate(trans))  # 输出: h2ll4
  ```

- `str.translate(table)`：根据映射表替换字符。
  ```python
  trans = str.maketrans("aeiou", "12345")
  print("hello".translate(trans))  # 输出: h2ll4
  ```

## Basic Data Structures in Python

### List in Python

在Python中，列表是动态的，可以随着列表的运行增删元素。

以下是 Python 列表（`list`）的常用操作整理，涵盖了列表的创建、访问、修改、排序、遍历、切片、列表推导式等操作。

> Python列表的底层实现本质上就是一个**C语言的动态数组**。

1. **列表的创建**

- 使用方括号 `[]` 创建列表。
- 使用 `list()` 函数将其他可迭代对象转换为列表。

```python
# 创建空列表
empty_list = []

# 创建包含元素的列表
fruits = ["apple", "banana", "cherry"]

# 使用 list() 函数创建列表
numbers = list(range(5))  # 输出: [0, 1, 2, 3, 4]
```

2. **访问列表元素**

- 使用索引访问列表元素（索引从 `0` 开始）。
- 使用负数索引从列表末尾访问元素。

```python
fruits = ["apple", "banana", "cherry"]
print(fruits[0])   # 输出: apple
print(fruits[-1])  # 输出: cherry
```

3. **修改列表元素**

- 通过索引直接修改列表元素。

```python
fruits = ["apple", "banana", "cherry"]
fruits[1] = "blueberry"
print(fruits)  # 输出: ['apple', 'blueberry', 'cherry']
```

4. **列表的常用方法**

**添加元素**

- `append()`：在列表末尾添加一个元素。
  ```python
  fruits = ["apple", "banana"]
  fruits.append("cherry")
  print(fruits)  # 输出: ['apple', 'banana', 'cherry']
  ```

- `extend()`：将另一个可迭代对象的元素添加到列表末尾。
  ```python
  fruits = ["apple", "banana"]
  fruits.extend(["cherry", "orange"])
  print(fruits)  # 输出: ['apple', 'banana', 'cherry', 'orange']
  ```

- `insert()`：在指定位置插入一个元素。
  ```python
  fruits = ["apple", "banana"]
  fruits.insert(1, "cherry")
  print(fruits)  # 输出: ['apple', 'cherry', 'banana']
  ```

**删除元素**

- `remove()`：删除列表中第一个匹配的元素。
  ```python
  fruits = ["apple", "banana", "cherry"]
  fruits.remove("banana")
  print(fruits)  # 输出: ['apple', 'cherry']
  ```

- `pop()`：删除并返回指定位置的元素（默认删除最后一个元素）。
  ```python
  fruits = ["apple", "banana", "cherry"]
  fruits.pop(1)  # 删除索引为 1 的元素
  print(fruits)  # 输出: ['apple', 'cherry']
  ```

- `clear()`：清空列表。
  ```python
  fruits = ["apple", "banana", "cherry"]
  fruits.clear()
  print(fruits)  # 输出: []
  ```

**查找元素**

- `index()`：返回指定元素的索引（如果存在）。
  
  ```python
  fruits = ["apple", "banana", "cherry"]
  print(fruits.index("banana"))  # 输出: 1
  ```
  
- `count()`：返回指定元素在列表中出现的次数。
  ```python
  fruits = ["apple", "banana", "cherry", "banana"]
  print(fruits.count("banana"))  # 输出: 2
  ```

**排序与反转**

- `sort()`：对列表进行排序（默认升序）。
  ```python
  numbers = [3, 1, 4, 1, 5, 9]
  numbers.sort()
  print(numbers)  # 输出: [1, 1, 3, 4, 5, 9]
  ```

- `reverse()`：反转列表中的元素。
  ```python
  fruits = ["apple", "banana", "cherry"]
  fruits.reverse()
  print(fruits)  # 输出: ['cherry', 'banana', 'apple']
  ```

- `sorted()`：对列表进行临时排序

```python
fruits = ["banana","apple", "cherry"]
print(sorted(fruits))
print(fruits)
'''
['apple', 'banana', 'cherry']
['banana', 'apple', 'cherry']
'''
```

{% note info %}

**Python 中的自定义比较函数**

```python
students = [
    {"name": "Alice", "age": 25},
    {"name": "Bob", "age": 22},
    {"name": "Charlie", "age": 28}
]

# 按年龄升序排序
students.sort(key=lambda x: x["age"])
print(students)
# 输出: [{'name': 'Bob', 'age': 22}, {'name': 'Alice', 'age': 25}, {'name': 'Charlie', 'age': 28}]
```

```python
from functools import cmp_to_key

students = [
    {"name": "Alice", "age": 25, "grade": 88},
    {"name": "Bob", "age": 22, "grade": 92},
    {"name": "Charlie", "age": 22, "grade": 85}
]

# 自定义比较函数
def compare(a, b):
    if a["age"] != b["age"]:
        return a["age"] - b["age"]  # 按年龄升序
    else:
        return b["grade"] - a["grade"]  # 按成绩降序

# 使用 cmp_to_key 将比较函数转换为 key 函数
students.sort(key=cmp_to_key(compare))
print(students)
# 输出: [{'name': 'Charlie', 'age': 22, 'grade': 85}, {'name': 'Bob', 'age': 22, 'grade': 92}, {'name': 'Alice', 'age': 25, 'grade': 88}]
```

{% endnote %}

5. **列表的遍历**

- 使用 `for` 循环遍历列表。

```python
fruits = ["apple", "banana", "cherry"]
for fruit in fruits:
    print(fruit)
```

6. **列表的切片**

- 使用切片操作获取列表的子集。
- 语法：`list[start:stop:step]`

```python
numbers = [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
print(numbers[2:5])    # 输出: [2, 3, 4]
print(numbers[::2])    # 输出: [0, 2, 4, 6, 8]
print(numbers[::-1])   # 输出: [9, 8, 7, 6, 5, 4, 3, 2, 1, 0]
```

7. **列表的复制**

- 使用切片或 `copy()` 方法复制列表。

```python
fruits = ["apple", "banana", "cherry"]
fruits_copy = fruits[:]  # 使用切片复制
fruits_copy2 = fruits.copy()  # 使用 copy() 方法复制
```

8. **列表推导式**

- 使用列表推导式快速生成列表。

```python
# 生成平方数列表
squares = [x**2 for x in range(10)]
print(squares)  # 输出: [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]

# 生成偶数列表
evens = [x for x in range(10) if x % 2 == 0]
print(evens)  # 输出: [0, 2, 4, 6, 8]
```

9. **列表的嵌套**

- 列表可以包含其他列表，形成嵌套列表。

```python
matrix = [
    [1, 2, 3],
    [4, 5, 6],
    [7, 8, 9]
]
print(matrix[1][2])  # 输出: 6
```

10. **列表的其他操作**

- `len()`：获取列表的长度。
  ```python
  fruits = ["apple", "banana", "cherry"]
  print(len(fruits))  # 输出: 3
  ```

- `in` 和 `not in`：检查元素是否在列表中。
  
  ```python
  fruits = ["apple", "banana", "cherry"]
  print("banana" in fruits)  # 输出: True
  ```
  
- `+` 和 `*`：列表的拼接和重复。
  ```python
  list1 = [1, 2, 3]
  list2 = [4, 5, 6]
  print(list1 + list2)  # 输出: [1, 2, 3, 4, 5, 6]
  print(list1 * 2)      # 输出: [1, 2, 3, 1, 2, 3]
  ```

### Tuple in Python

在 Python 中，**元组（Tuple）** 是一种不可变的序列类型，用于存储一组有序的元素。元组与列表（`list`）类似，但元组一旦创建，其内容不可修改。

- **不可变性**：元组一旦创建，其元素不能被修改、添加或删除。
- **有序性**：元组中的元素是有序的，可以通过索引访问。
- **异构性**：元组可以存储不同类型的元素（如整数、字符串、列表等）。
- **支持嵌套**：元组可以嵌套其他元组或列表。

元组使用圆括号 `()` 定义，元素之间用逗号 `,` 分隔。

**元组的创建**

```python
# 创建一个元组
my_tuple = (1, 2, 3)
print(my_tuple)  # 输出: (1, 2, 3)

# 创建包含不同类型元素的元组
mixed_tuple = (1, "hello", 3.14, [1, 2, 3])
print(mixed_tuple)  # 输出: (1, 'hello', 3.14, [1, 2, 3])

# 创建空元组
empty_tuple = ()
print(empty_tuple)  # 输出: ()
```

- 如果元组只有一个元素，需要在元素后面加一个逗号 `,`，否则会被认为是普通括号。
```python
single_element_tuple = (42,)  # 这是一个元组
not_a_tuple = (42)            # 这是一个整数
```
**元组的遍历**

```python
my_tuple = (10, 20, 30, 40, 50)

# 访问第一个元素
print(my_tuple[0])  # 输出: 10

# 访问最后一个元素
print(my_tuple[-1])  # 输出: 50

# 访问切片
print(my_tuple[1:4])  # 输出: (20, 30, 40)
```

元组是不可变的，因此不能修改、添加或删除元素。

```python
my_tuple = (1, 2, 3)

# 尝试修改元素
my_tuple[0] = 10  # 会抛出 TypeError: 'tuple' object does not support item assignment

# 尝试添加元素
my_tuple.append(4)  # 会抛出 AttributeError: 'tuple' object has no attribute 'append'

# 尝试删除元素
del my_tuple[0]  # 会抛出 TypeError: 'tuple' object doesn't support item deletion

# 但是可以给表示元组的变量赋值
my_tuple=(4,5,6) #VALID
```

```python
tuple1 = (1, 2, 3)
tuple2 = (4, 5, 6)
combined_tuple = tuple1 + tuple2
print(combined_tuple)  # 输出: (1, 2, 3, 4, 5, 6)
```

**元组的基本操作**

```python
# 元组的基本操作
# 元组的拼接
tuple1 = (1, 2, 3)
tuple2 = (4, 5, 6)
combined_tuple = tuple1 + tuple2
print(combined_tuple)  # 输出: (1, 2, 3, 4, 5, 6)

# 元组的重复
my_tuple = (1, 2, 3)
repeated_tuple = my_tuple * 3
print(repeated_tuple)  # 输出: (1, 2, 3, 1, 2, 3, 1, 2, 3)、

# 元组的长度
my_tuple = (1, 2, 3, 4, 5)
print(len(my_tuple))  # 输出: 5

# 元组的遍历
my_tuple = (1, 2, 3, 4, 5)
for item in my_tuple:
    print(item)
    
# 元组的解包
my_tuple = (10, 20, 30)
a, b, c = my_tuple
print(a, b, c)  # 输出: 10 20 30

# 元组嵌套其他元组或列表
nested_tuple = ((1, 2), (3, 4), [5, 6])
print(nested_tuple)  # 输出: ((1, 2), (3, 4), [5, 6])
```

**元组与列表的区别**

| 特性         | 元组 (`tuple`)                   | 列表 (`list`)                |
| ------------ | -------------------------------- | ---------------------------- |
| **可变性**   | 不可变                           | 可变                         |
| **语法**     | 使用圆括号 `()`                  | 使用方括号 `[]`              |
| **性能**     | 访问速度更快，占用内存更少       | 访问速度较慢，占用内存较多   |
| **适用场景** | 存储不可变数据（如常量、配置等） | 存储可变数据（如动态集合等） |

### Dictionaries in Python

在Python中，**字典**是一种基于键值对的数据结构，底层原理是**哈希表（Hash Table）**。字典中的键必须是唯一的，而值可以是任意类型的数据。字典是无序的（在 Python 3.7 及以上版本中，字典保持了插入顺序），并且通过键来快速查找对应的值。以下是字典的详细介绍：

**字典的创建**

```python
# 创建一个字典
my_dict = {"name": "Alice", "age": 25, "city": "New York"}
# 注意：需要使用花括号！
print(my_dict)  # 输出: {'name': 'Alice', 'age': 25, 'city': 'New York'}

# 创建空字典
empty_dict = {}
print(empty_dict)  # 输出: {}

# 使用 dict() 函数创建字典
my_dict = dict(name="Alice", age=25, city="New York")
print(my_dict)  # 输出: {'name': 'Alice', 'age': 25, 'city': 'New York'}
```

**通过键来访问字典的值**

```python
my_dict = {"name": "Alice", "age": 25, "city": "New York"}

# 访问键对应的值
print(my_dict["name"])  # 输出: Alice

# 访问不存在的键会抛出 KeyError
# print(my_dict["gender"])  # KeyError: 'gender'

# 使用get访问，一种更安全的方法
print(my_dict.get("name"))      # 输出: Alice
print(my_dict.get("gender"))    # 输出: None
print(my_dict.get("gender", "unknown"))  # 输出: unknown
```

**修改，插入，删除**

```python
my_dict = {"name": "Alice", "age": 25, "city": "New York"}

# 修改值
my_dict["age"] = 26
print(my_dict)  # 输出: {'name': 'Alice', 'age': 26, 'city': 'New York'}

# 添加新的键值对
my_dict["gender"] = "female"
print(my_dict)  # 输出: {'name': 'Alice', 'age': 26, 'city': 'New York', 'gender': 'female'}

# 使用 del 删除键值对
del my_dict["city"]
print(my_dict)  # 输出: {'name': 'Alice', 'age': 25}

# 使用 pop() 删除键值对并返回值
age = my_dict.pop("age")
print(age)      # 输出: 25
print(my_dict)  # 输出: {'name': 'Alice'}

# 清空字典
my_dict.clear()
print(my_dict)  # 输出: {}
```

**其他操作**

```python
my_dict = {"name": "Alice", "age": 25, "city": "New York"}

# 遍历键
for key in my_dict:
    print(key)

# 遍历值
for value in my_dict.values():
    print(value)

# 遍历键值对
for key, value in my_dict.items():
    print(f"{key}: {value}")
    
# 使用in检查是否存在
my_dict = {"name": "Alice", "age": 25, "city": "New York"}
print("name" in my_dict)  # 输出: True
print("gender" in my_dict)  # 输出: False


# 获取所有键
print(my_dict.keys())  # 输出: dict_keys(['name', 'age', 'city'])

# 获取所有值
print(my_dict.values())  # 输出: dict_values(['Alice', 25, 'New York'])

# 获取所有键值对
print(my_dict.items())  # 输出: dict_items([('name', 'Alice'), ('age', 25), ('city', 'New York')])

dict1 = {"name": "Alice", "age": 25}
dict2 = {"city": "New York", "gender": "female","age":15}
dict1.update(dict2)
print(dict1)  # 输出: {'name': 'Alice', 'age': 15, 'city': 'New York', 'gender': 'female'}
# 合并冲突的键值对时会自动覆盖成新的
```

---

**字典推导式**

```python
# 创建一个字典，键为数字，值为数字的平方（使用花括号{}）
squares = {x: x**2 for x in range(5)}
print(squares)  # 输出: {0: 0, 1: 1, 2: 4, 3: 9, 4: 16}
```

## Loops in Python

### While-loops

在Python中，`while`循环的基本用法和C/C++基本相同，唯一需要注意的是**Python中不加花括号并且要注意缩进**。

```python
count = 0
while count < 5:
    print(f"Count is: {count}")
    count += 1
```

`while` 循环可以有一个可选的 `else` 子句。当循环正常结束（即条件变为 `False`）时，`else` 子句会被执行。如果循环是通过 `break` 退出的，`else` 子句不会执行。

```python
count = 0
while count < 5:
    print(f"Count is: {count}")
    count += 1
else:
    print("Loop finished normally.")
```

### For-loops

```python
# 遍历列表
fruits = ['apple', 'banana', 'cherry']
for fruit in fruits:
    print(fruit)

# 遍历字符串
for char in "hello":
    print(char)

# 遍历字典的键值对
my_dict = {'a': 1, 'b': 2}
for key, value in my_dict.items():
    print(key, value)
```

range **的使用**

在 Python 中，`range` 是一个内置函数，用于生成一个整数序列。它通常用于 `for` 循环中，也可以用于创建列表或其他需要整数序列的场景。`range` 函数有三种调用方式：

（1）`range(stop)`

生成从 `0` 开始到 `stop-1` 的整数序列。

```python
for i in range(5):
    print(i)
'''
0
1
2
3
4
'''
```

（2）`range(start, stop)`

生成从 `start` 开始到 `stop-1` 的整数序列。

```python
for i in range(2, 6):
    print(i)
'''
2
3
4
5
'''
```

（3）`range(start, stop, step)`

生成从 `start` 开始到 `stop-1` 的整数序列，步长为 `step`。

```python
for i in range(1, 10, 2):
    print(i)
'''
1
3
5
7
9
'''
```

- **惰性求值**：`range` 返回的是一个 `range` 对象，而不是一个列表。它只在需要时生成值，因此非常节省内存。
- **不可变性**：`range` 对象是不可变的，不能直接修改其内容。
- **支持负步长**：`step` 可以为负数，表示反向生成序列。

可以通过 `list()` 函数将 `range` 对象转换为列表。

```python
numbers = list(range(5))
print(numbers)  # 输出: [0, 1, 2, 3, 4]
```

1. **遍历字典**：

   - 你可以使用 `for` 循环直接遍历字典的键、值，或者键值对。

   ```python
   my_dict = {'a': 1, 'b': 2, 'c': 3}
   for key in my_dict:
       print(key, my_dict[key])
   
   for key, value in my_dict.items():
       print(key, value)
   ```

2. **使用 `enumerate()`**：

   - `enumerate()` 函数可以在遍历列表时获取元素的索引。

   ```python
   my_list = ['apple', 'banana', 'cherry']
   for index, value in enumerate(my_list):
       print(index, value)
   '''
   0 apple
   1 banana
   2 cherry
   '''
   ```

3. 使用 `zip()`：

   - `zip()` 函数可以同时遍历多个序列。

   ```python
   names = ['Alice', 'Bob', 'Charlie']
   scores = [85, 90, 95]
   for name, score in zip(names, scores):
       print(f'{name} scored {score}')
   ```

4. **列表推导式**：

   - 列表推导式是一种简洁的方式来生成列表。

   ```python
   squares = [x**2 for x in range(10)]
   print(squares)
   ```

5. **生成器表达式**：

   - 与列表推导式类似，但生成器表达式使用圆括号，生成元素时不会立即存储在内存中。

   ```python
   squares_gen = (x**2 for x in range(10))
   for square in squares_gen:
       print(square)
   ```

6. **条件过滤**：

   - 你可以在 `for` 循环中使用条件语句来过滤元素。

   ```python
   even_squares = [x**2 for x in range(10) if x % 2 == 0]
   print(even_squares)
   '''
   [0, 4, 16, 36, 64]
   '''
   ```

7. **嵌套循环**：

   - 列表推导式和生成器表达式中可以包含嵌套循环。

   ```python
   pairs = [(x, y) for x in range(3) for y in range(3)]
   print(pairs)
   '''
   [(0, 0), (0, 1), (0, 2), (1, 0), (1, 1), (1, 2), (2, 0), (2, 1), (2, 2)]
   '''
   ```

## Functions in Python

在Python中，自定义函数的语法格式和运行逻辑有较大的不同。

### Basic Usage

```python
# example one
def greet_users():
    '''for the greetings to new users'''
    print("Hello!")

    
# example two
def greet_users2(username):
    '''functions with input'''
    print(f"Hello! {username.title()}")
    
# example three
def greet_users3(username):
    '''functions with outpput'''
    print(f"Hello! {username.title()}")
    return username


greet_users()
greet_users2("wowo")
output=greet_users3("wowO")
print (output)
'''
Hello!
Hello! Wowo
Hello! Wowo
wowO
'''
```

这是一个最基本的**函数定义**，包括**函数名，文档字符串（Optional）和具体的函数代码**。

> 值得注意的是，python中的函数不需要显式声明返回类型。

**python中也存在形参和实参的传递**，但并不完全是拷贝传递（可变对象和不可变对象）

- 不可变对象（如整数、字符串、元组）在传递时，形参和实参指向同一个对象。但如果形参尝试修改对象，Python 会创建一个新的对象，形参会指向这个新对象，而实参仍然指向原来的对象。

- 可变对象（如列表、字典、集合）在传递时，形参和实参指向同一个对象。如果形参修改了对象的内容，实参也会受到影响。

  - 可以使用切片的方法实现**拷贝传参**

  ```python
  function_name(list_name[:])
  ```

### Advanced Usage

- 关键词实参（参数列表的一一对应）

- 返回值的多样性

  - Python 函数可以返回 **字典**、**列表** 或 **元组**，甚至可以返回它们的组合。
  - Python 的函数非常灵活，可以返回任意类型的对象，包括复杂的数据结构。

- 使用`def function(*args)`可以创建一个名为`nums`的元组。

  - 在函数定义中，`*` 和 `**` 可以用于接收任意数量的位置参数和关键字参数。

    ```python
    def print_args(*args, **kwargs):
        print("Positional arguments:", args)
        print("Keyword arguments:", kwargs)
    
    # 调用函数
    print_args(1, 2, 3, name="Alice", age=25)
    ```

    ```python
    Positional arguments: (1, 2, 3)
    Keyword arguments: {'name': 'Alice', 'age': 25}
    ```

    - `*args` 接收所有位置参数，并将其存储为一个元组。
    - `**kwargs` 接收所有关键字参数，并将其存储为一个字典。

## OOP in Python

在Python中，也可以实现面向对象编程。下文笔者将从C++的视角出发，向读者展示Python中相对应的用法。

### A simple class for Matrix

下文给出一个简单的矩阵类实现（C++语言）

```cpp
#include <iostream>
using namespace std;
class Matrix{
    private:
        int row,col;
        int** thenumlist;
    public:
        Matrix(int row_,int col_):row(row_),col(col_){
            thenumlist=new int*[row];
            for(int i=0;i<row;i++){
                thenumlist[i]=new int [col];
            }
        };

        ~Matrix(){
            for(int i=0;i<row;i++){
                delete[] thenumlist[i];
            }
            delete[] thenumlist;
        }

        Matrix(const Matrix& x){
            row=x.row;
            col=x.col;
            thenumlist=new int*[row];
            for(int i=0;i<row;i++){
                thenumlist[i]=new int [col];
            }
            for(int i=0;i<row;i++){
                for(int j=0;j<col;j++){
                    thenumlist[i][j]=x.thenumlist[i][j];
                }
            }
        }

        int operator()(int x,int y);
        bool operator==(const Matrix &x){
            //bool judge=true;
            if(x.row!=this->row) return false;
            if(x.col!=this->col) return false;
            for(int i=0;i<x.row;i++){
                for(int j=0;j<x.col;j++){
                    if(x.thenumlist[i][j]!=(this->thenumlist)[i][j]){
                        return false;
                    }
                }
            }
            return true;
        }
        Matrix operator=(const Matrix &x){
            if(*this==x){
                return *this;
            }

            for(int i=0;i<row;i++){
                delete[] thenumlist[i];
            }
            delete[] thenumlist;

            row=x.row;col=x.col;
            thenumlist=new int*[row];
            for(int i=0;i<row;i++){
                thenumlist[i]=new int [col];
            }

            for(int i=0;i<row;i++){
                for(int j=0;j<col;j++){
                    thenumlist[i][j]=x.thenumlist[i][j];
                }
            }
            return *this;
        }

        friend istream& operator>>(istream& is,Matrix& x);
        friend ostream& operator<<(ostream& os,Matrix& x);
        friend Matrix operator+(const Matrix&x1,const Matrix&x2);
};

int Matrix::operator()(int x,int y){
    return thenumlist[x][y];
}

istream& operator>>(istream& is,Matrix& x){
    for(int i=0;i<x.row;i++){
        for(int j=0;j<x.col;j++){
            is>>x.thenumlist[i][j];
        }
    }
    return is;
}

ostream& operator<<(ostream& os,Matrix& x){
    for(int i=0;i<x.row;i++){
        for(int j=0;j<x.col;j++){
            os<<x.thenumlist[i][j];
            if(j!=x.col-1) os<<" ";
        }
        os<<endl;
    }
    return os;
}

Matrix operator+(const Matrix&x1,const Matrix&x2){
    Matrix temp(x1.row,x1.col);
    for(int i=0;i<x1.row;i++){
        for(int j=0;j<x1.col;j++){
            temp.thenumlist[i][j]=x1.thenumlist[i][j]+x2.thenumlist[i][j];
        }
    }
    return temp;
}

int main()
{
    int m, n, x, y;
    cin >> m >> n >> x >> y;
    Matrix a(m, n), b(m, n);
    cin >> a >> b;
    Matrix c = a+b;
    cout << c;
    a = b+c;
    // print element at row-x column-y of Matrix a, row and column count from 0
    cout << a(x, y) << endl; 
    return 0;
}
```

下面改写成Python的版本进行迁移学习：

```Python
class Matrix:
    def __init__(self, row=1, col=1):
        """
        Default constructor for the Matrix class.
        Initializes a matrix with the given number of rows and columns.
        If no dimensions are provided, defaults to a 1x1 matrix.
        """
        self.row = row
        self.col = col
        # Create a 2D list (matrix) filled with zeros
        self.thenumlist = [[0 for _ in range(col)] for _ in range(row)]

    def __del__(self):
        """
        Destructor for the Matrix class.
        In Python, this is rarely used because memory management is handled by the garbage collector.
        """
        print("Matrix object destroyed.")

    def __copy__(self):
        """
        Copy constructor for the Matrix class.
        Creates a new Matrix object that is a copy of the current object.
        """
        new_matrix = Matrix(self.row, self.col)
        for i in range(self.row):
            for j in range(self.col):
                new_matrix.thenumlist[i][j] = self.thenumlist[i][j]
        return new_matrix

    def __call__(self, x, y):
        """
        Overloads the () operator to access elements of the matrix.
        Returns the element at the specified row and column.
        """
        return self.thenumlist[x][y]

    def __eq__(self, other):
        """
        Overloads the == operator to compare two matrices.
        Returns True if the matrices have the same dimensions and elements, otherwise False.
        """
        if self.row != other.row or self.col != other.col:
            return False
        for i in range(self.row):
            for j in range(self.col):
                if self.thenumlist[i][j] != other.thenumlist[i][j]:
                    return False
        return True

    def __assign__(self, other):
        """
        Overloads the = operator to assign one matrix to another.
        Performs a deep copy of the elements.
        """
        if self == other:
            return self
        self.row = other.row
        self.col = other.col
        self.thenumlist = [[0 for _ in range(self.col)] for _ in range(self.row)]
        for i in range(self.row):
            for j in range(self.col):
                self.thenumlist[i][j] = other.thenumlist[i][j]
        return self

    def __add__(self, other):
        """
        Overloads the + operator to add two matrices.
        Returns a new Matrix object that is the sum of the two matrices.
        """
        if self.row != other.row or self.col != other.col:
            raise ValueError("Matrix dimensions do not match for addition.")
        result = Matrix(self.row, self.col)
        for i in range(self.row):
            for j in range(self.col):
                result.thenumlist[i][j] = self.thenumlist[i][j] + other.thenumlist[i][j]
        return result

    def __str__(self):
        """
        Overloads the str() function to provide a string representation of the matrix.
        Returns the matrix as a formatted string.
        """
        return "\n".join([" ".join(map(str, row)) for row in self.thenumlist])

    def __repr__(self):
        """
        Overloads the repr() function to provide a detailed string representation of the matrix.
        Useful for debugging.
        """
        return f"Matrix({self.row}, {self.col}, data={self.thenumlist})"

    @staticmethod
    def from_input(rows, cols):
        """
        Static method to create a Matrix object from user input.
        """
        matrix = Matrix(rows, cols)
        for i in range(rows):
            row = list(map(int, input().split()))
            if len(row) != cols:
                raise ValueError("Invalid number of elements in row.")
            matrix.thenumlist[i] = row
        return matrix


# Main function to test the Matrix class
if __name__ == "__main__":
    # Read matrix dimensions and element indices
    m, n, x, y = map(int, input().split())

    # Create two matrices from user input
    print("Enter elements for matrix A:")
    a = Matrix.from_input(m, n)
    print("Enter elements for matrix B:")
    b = Matrix.from_input(m, n)

    # Perform matrix addition
    c = a + b
    print("Matrix C (A + B):")
    print(c)

    # Assign the result of (B + C) to A
    a = b + c

    # Print the element at row-x, column-y of matrix A
    print(f"Element at ({x}, {y}) in matrix A: {a(x, y)}")
```

### Data and functions？

在C++中，类的定义主要包括两个方面的定义：**数据成员和方法**（即数据类型和成员函数）。但是在Python的代码中，我们没有看到明确的一块区域来专门定义数据成员。

实际上，Python中的类实现更加的灵活，数据成员的定义可以直接在构造函数中实现，也可以在**后续的使用中添加新的数据成员**。

```Python
class MyClass:
    def __init__(self):
        self.data_member = 42  # 定义数据成员
        self.name = "Python"   # 定义另一个数据成员

obj = MyClass()
print(obj.data_member)  # 输出: 42
print(obj.name)         # 输出: Python

obj.new_data_member = "Dynamic"  # 动态添加数据成员
print(obj.new_data_member)       # 输出: Dynamic

# 使用slots可以显式地规定不可以添加新的数据成员
class MyClass:
    __slots__ = ['data_member', 'name']  # 限制只能有这两个数据成员

    def __init__(self):
        self.data_member = 42
        self.name = "Python"

obj = MyClass()
print(obj.data_member)  # 输出: 42
print(obj.name)         # 输出: Python

# obj.new_member = "Dynamic"  # 报错: AttributeError
```

值得注意的是，Python中存在**类一级的数据成员**，类似于C++中的静态数据成员。

```Python
class MyClass:
    class_data_member = 100  # 定义类变量（类数据成员）

    def __init__(self):
        self.instance_data_member = 42  # 定义实例变量

obj1 = MyClass()
obj2 = MyClass()

print(MyClass.class_data_member)  # 输出: 100（通过类名访问）
print(obj1.class_data_member)     # 输出: 100（通过对象访问）
print(obj1.instance_data_member)  # 输出: 42

# 修改类变量
MyClass.class_data_member = 200
print(obj2.class_data_member)     # 输出: 200（所有对象共享类变量）
```

### Public and Private？

继续观察Python代码，发现**Python中没有private和public**的区别了！

> 这实际上是一个很严重的问题，因为OOP的核心精神之一就是封装，而不同访问权限的设置是封装精神实现的基础。

在 Python 中，确实没有像 C++ 或 Java 那样严格的 `private` 和 `public` 访问控制关键字。Python 采用了一种更加灵活的方式来控制成员的访问权限，主要通过**命名约定**和**特殊机制**来实现类似的功能。

默认情况下，**类的所有成员（属性和方法）**都是公有的。没错，Python中的类实现牺牲了封装性。也没有友元的实现。

{% note primary %}

**Python为什么要这么做？**请看这个链接：  [Pythonic](https://mail.python.org/pipermail/tutor/2003-October/025932.html)

I came across a quote "**we are all consenting adults here**" I think in explaining why it is not necessary to have type declaration statements in Python, in contrast to other strongly-typed languages.

This expression is also used in the object-oriented python literature to explain python's attitudes about private class members, which python doesn't have. 

When you create an instance of some class there is nothing to prevent you from poking around inside and using various internal,
private methods that are (a) necessary for the class to function, BUT (b) not intended for direct use/access.

Nothing is really private in python. No class or class instance can keep you away from all what's inside (this makes introspection possible and powerful). Python trusts you. It says "hey, if you want to go poking around in dark places, I'm gonna trust that you've got a good reason and you're not making trouble."

After all, **we're all consenting adults here**. C++ and Java don't have this philosophy (not to the same extent). They allow you create private methods and static members. Perl culture is like python in this respect, but Perl expresses the sentiment a bit differently. As the Camel book puts it,

  "a Perl module would prefer that you stayed out of its living room because you weren't invited, not because it has a shotgun." But the sentiment is identical.

--karl

我偶然看到一句话：“**we are all consenting adults here**”（我们都是成年人），这句话通常用来解释为什么 Python 不需要像其他强类型语言那样有类型声明语句。

这个表达也用在 Python 面向对象编程的文献中，用来解释 Python 对于**私有类成员**的态度——Python 并没有真正的私有成员。当你创建某个类的实例时，没有什么能阻止你去探索类的内部并使用各种内部的、私有的方法，这些方法（a）是类正常运行所必需的，但（b）并不打算让你直接使用或访问。

在 Python 中，**没有什么东西是真正私有的**。没有任何类或类的实例能阻止你访问内部的所有内容（这使得 Python 的内省机制变得可能且强大）。Python 信任你。它说：“嘿，如果你想深入探索那些黑暗的角落，我会相信你有充分的理由，而不是在捣乱。”

毕竟，**我们都是成年人**。C++ 和 Java 并没有这种哲学（至少不像 Python 这样）。它们允许你创建私有方法和静态成员。Perl 的文化在这方面与 Python 类似，但 Perl 的表达方式略有不同。正如《骆驼书》（Perl 的经典书籍）中所说：

> “Perl 模块更希望你远离它的客厅，因为你没有被邀请，而不是因为它有一把猎枪。”

但两者的理念是相同的。

——Karl

{% endnote %}

不过实际上，Python中也允许一些**类似私有成员**的实现方式，不过并没有像C++一样那么严格。

#### **Protected Members**

- **定义**：通过在成员名前加一个下划线 `_` 来表示受保护成员。

- **特点**：

  - 这是一种命名约定，表示该成员**不应该**在类的外部直接访问，但 Python 并不会阻止你访问。
  - 主要用于提示开发者：“这是一个内部实现细节，请谨慎使用”。

  ```Python
  class MyClass:
      def __init__(self):
          self._protected_var = 20  # 受保护变量
  
      def _protected_method(self):
          print("This is a protected method.")  # 受保护方法
  
  obj = MyClass()
  print(obj._protected_var)  # 可以访问，但不推荐
  obj._protected_method()    # 可以调用，但不推荐
  ```

#### **Private Members**

- **定义**：通过在成员名前加两个下划线 `__` 来表示私有成员。

- **特点**：

  - Python 会对私有成员进行名称改写（Name Mangling），使其在类的外部无法直接访问。
  - 名称改写规则：`__var` 会被改写为 `_ClassName__var`。
  - 这是一种更严格的访问控制，但仍然可以通过改写后的名称访问（不推荐）。

  ```Python
  class MyClass:
      def __init__(self):
          self.__private_var = 30  # 私有变量
  
      def __private_method(self):
          print("This is a private method.")  # 私有方法
  
  obj = MyClass()
  # print(obj.__private_var)  # 直接访问会报错：AttributeError
  # obj.__private_method()    # 直接调用会报错：AttributeError
  
  # 通过名称改写访问（不推荐）
  print(obj._MyClass__private_var)  # 输出：30
  obj._MyClass__private_method()    # 输出：This is a private method
  ```

### Constructor

#### **Default Constructor and parameterized Constructor**

```python
def __init__(self, row=1, col=1):
        """
        Default constructor for the Matrix class.
        Initializes a matrix with the given number of rows and columns.
        If no dimensions are provided, defaults to a 1x1 matrix.
        """
        self.row = row
        self.col = col
        # Create a 2D list (matrix) filled with zeros
        self.thenumlist = [[0 for _ in range(col)] for _ in range(row)]
```

```C++
Matrix(int row_=1,int col_=1):row(row_),col(col_){
            thenumlist=new int*[row];
            for(int i=0;i<row;i++){
                thenumlist[i]=new int [col];
            }
        };
```

讲面向对象编程肯定得从构造函数讲起，下面着重分析两种语言中在语法上的差异：

- `self`：`self` 是类的方法（包括构造函数 `__init__`）中的第一个参数，用于表示**当前对象的实例**。有点类似于C++中的**隐式this指针**！
- Python中的数据成员的声明主要都是在构造函数中进行的，并且没有提供访问权限的控制。

#### Copy Constructor

在C++中，由于涉及到指针，使用**复制构造函数**是非常有必要的！在 Python 中，**没有显式的拷贝构造函数**（Copy Constructor），因为 Python 的内存管理和对象复制机制与 C++ 不同。但是在Python的`copy`库中，也有类似的实现方式。

##### Shallow Copy
   - **定义**：浅拷贝创建一个新对象，但不会递归复制对象内部的子对象。

   - **实现方式**：
     - 使用 `copy.copy()` 函数。
     - 使用对象的 `copy()` 方法（如果对象支持）。
     
     ```python
     import copy
     
     class MyClass:
         def __init__(self, value,valist):
             self.value = value
             self.valist= valist
     
     obj1 = MyClass(42,[1,2,3,4])
     obj2 = copy.copy(obj1)
     
     print(obj1.value)  
     print(obj2.value)
     
     print(obj1.valist)
     print(obj2.valist)
     
     # obj2.valist=[1,2,3,3,4] 这样会让obj2.valist指向一个新的列表对象
     obj2.value+=1
     obj2.valist.append(33)
     
     print(obj1.valist)
     print(obj2.valist)
     
     print(obj1.value)  
     print(obj2.value)
     
     ```
     
     ```Bash
     42
     42
     [1, 2, 3, 4]
     [1, 2, 3, 4]
     [1, 2, 3, 4, 33]
     [1, 2, 3, 4, 33]
     42
     43
     ```
     
     - 如果对象包含可变子对象（如列表、字典），**浅拷贝**会共享这些子对象。
     
     **分析**：在上述代码中，`obj2 = copy.copy(obj1)`让两个对象的`valist`数据成员指向了**同一个列表！**因此对obj2进行修改的时候obj1也会修改。
     
     > 这一点和C++一样，相当于**一条绳上的蚂蚱**！

##### Deep Copy
   - **定义**：深拷贝创建一个新对象，并递归复制对象内部的所有子对象。

   - **实现方式**：
     - 使用 `copy.deepcopy()` 函数。
     
     ```python
     import copy
     
     class MyClass:
         def __init__(self, value,valist):
             self.value = value
             self.valist= valist
     
     obj1 = MyClass(42,[1,2,3,4])
     obj2 = copy.deepcopy(obj1) #仅修改这一处
     
     print(obj1.value)  
     print(obj2.value)
     
     print(obj1.valist)
     print(obj2.valist)
     
     # obj2.valist=[1,2,3,3,4] 这样会让obj2.valist指向一个新的列表对象
     obj2.value+=1
     obj2.valist.append(33)
     
     print(obj1.valist)
     print(obj2.valist)
     
     print(obj1.value)  
     print(obj2.value)
     
     ```
     
   - **注意**：
     - 深拷贝会递归复制所有子对象，因此不会共享任何可变子对象。

##### Define Copy Constructor **explicitly**
```python
def __copy__(self):
        """
        Copy constructor for the Matrix class.
        Creates a new Matrix object that is a copy of the current object.
        """
        new_matrix = Matrix(self.row, self.col)
        for i in range(self.row):
            for j in range(self.col):
                new_matrix.thenumlist[i][j] = self.thenumlist[i][j]
        return new_matrix
```

{% note danger %}

**注意！在这里本质上还是进行浅拷贝（和C++有很大不同）**。因为Python允许`thenumlist[i][j]`是任何数据类型（甚至是**可变数据，例如列表**）。如果是不可变数据例如一个`int`整数，深拷贝和浅拷贝的行为没有差别，但如果是一个可变数据类型例如列表，那么其本质上还是指向同一个列表，是一条绳上的蚂蚱！

{% endnote %}

不过，Python也支持自定义深拷贝函数。**但我们为什么不用`copy`库中给好的函数呢？**
```python
import copy

class MyClass:
    def __init__(self, value):
        self.value = value

    def __copy__(self):
        """自定义浅拷贝行为"""
        return MyClass(self.value)

    def __deepcopy__(self, memo):
        """自定义深拷贝行为"""
        return MyClass(copy.deepcopy(self.value, memo))

obj1 = MyClass(42)
obj2 = copy.copy(obj1)      # 调用 __copy__
obj3 = copy.deepcopy(obj1)  # 调用 __deepcopy__

print(obj1.value)  # 输出: 42
print(obj2.value)  # 输出: 42
print(obj3.value)  # 输出: 42
```

| 特性           | Python 中的对象复制                     | C++ 中的拷贝构造函数     |
| -------------- | --------------------------------------- | ------------------------ |
| **实现方式**   | 通过 `copy.copy()` 或 `copy.deepcopy()` | 通过显式的拷贝构造函数   |
| **默认行为**   | 浅拷贝（共享子对象）                    | 深拷贝（递归复制子对象） |
| **自定义行为** | 通过 `__copy__` 和 `__deepcopy__`       | 通过自定义拷贝构造函数   |
| **内存管理**   | 自动垃圾回收                            | 手动内存管理             |

### Destructor

Python中不用实现具体的动态内存分配的回收，不过可以实现一些其他的功能。（例如输出特定的信息）

```Python
class MyClass:
    def __del__(self):
        # 析构函数的代码
        print("Object is being destroyed")
```

### Operator Overloading

在 Python 中，**运算符重载**（Operator Overloading）是通过定义类的特殊方法（Magic Methods 或 Dunder Methods）来实现的。这些特殊方法以双下划线 `__` 开头和结尾，例如 `__add__`、`__sub__` 等。通过实现这些方法，可以让自定义类的对象支持 Python 的内置运算符（如 `+`、`-`、`*` 等）。

| 运算符   | 方法名         | 描述                       |
| :------- | :------------- | :------------------------- |
| `+`      | `__add__`      | 加法运算                   |
| `-`      | `__sub__`      | 减法运算                   |
| `*`      | `__mul__`      | 乘法运算                   |
| `/`      | `__truediv__`  | 除法运算                   |
| `//`     | `__floordiv__` | 整除运算                   |
| `%`      | `__mod__`      | 取模运算                   |
| `**`     | `__pow__`      | 幂运算                     |
| `==`     | `__eq__`       | 等于运算                   |
| `!=`     | `__ne__`       | 不等于运算                 |
| `<`      | `__lt__`       | 小于运算                   |
| `<=`     | `__le__`       | 小于等于运算               |
| `>`      | `__gt__`       | 大于运算                   |
| `>=`     | `__ge__`       | 大于等于运算               |
| `[]`     | `__getitem__`  | 索引操作                   |
| `[]=`    | `__setitem__`  | 索引赋值操作               |
| `len()`  | `__len__`      | 获取对象长度               |
| `str()`  | `__str__`      | 返回对象的字符串表示       |
| `repr()` | `__repr__`     | 返回对象的官方字符串表示   |
| `in`     | `__contains__` | 检查元素是否在对象中       |
| `()`     | `__call__`     | 使对象可调用（像函数一样） |

以上文的矩阵类为例，看一看具体是如何实现的：

```Python
def __call__(self, x, y):
        """
        Overloads the () operator to access elements of the matrix.
        Returns the element at the specified row and column.
        """
        return self.thenumlist[x][y]

    def __eq__(self, other):
        """
        Overloads the == operator to compare two matrices.
        Returns True if the matrices have the same dimensions and elements, otherwise False.
        """
        if self.row != other.row or self.col != other.col:
            return False
        for i in range(self.row):
            for j in range(self.col):
                if self.thenumlist[i][j] != other.thenumlist[i][j]:
                    return False
        return True

    def __assign__(self, other):
        """
        Overloads the = operator to assign one matrix to another.
        Performs a deep copy of the elements.
        """
        if self == other:
            return self
        self.row = other.row
        self.col = other.col
        self.thenumlist = [[0 for _ in range(self.col)] for _ in range(self.row)]
        for i in range(self.row):
            for j in range(self.col):
                self.thenumlist[i][j] = other.thenumlist[i][j]
        return self

    def __add__(self, other):
        """
        Overloads the + operator to add two matrices.
        Returns a new Matrix object that is the sum of the two matrices.
        """
        if self.row != other.row or self.col != other.col:
            raise ValueError("Matrix dimensions do not match for addition.")
        result = Matrix(self.row, self.col)
        for i in range(self.row):
            for j in range(self.col):
                result.thenumlist[i][j] = self.thenumlist[i][j] + other.thenumlist[i][j]
        return result

    def __str__(self):
        """
        Overloads the str() function to provide a string representation of the matrix.
        Returns the matrix as a formatted string.
        """
        return "\n".join([" ".join(map(str, row)) for row in self.thenumlist])

    def __repr__(self):
        """
        Overloads the repr() function to provide a detailed string representation of the matrix.
        Useful for debugging.
        """
        return f"Matrix({self.row}, {self.col}, data={self.thenumlist})"
```

### Static Members

对于**静态数据成员**来说，可以通过类一级的数据成员实现，在上文有讲。[跳转链接](#Data and functions？)

对于静态成员函数来说，使用 `@staticmethod` 装饰器定义静态方法。

```Python
class MyClass:
    @staticmethod
    def static_method():
        print("This is a static method")

# 通过类名调用静态方法
MyClass.static_method()  # 输出: This is a static method

# 通过实例调用静态方法
obj = MyClass()
obj.static_method()      # 输出: This is a static method
```

### Class Inheritance

在Python中，也可以实现类的继承。

```Python
class ParentClass:
    # 父类的属性和方法
    pass

class ChildClass(ParentClass):
    # 子类的属性和方法
    pass
```

下文以一个简化的矩阵类为示例，演示一下类的继承：

> 是**公有继承**，即一种**is-a**关系。

```Python
class Matrix:
    def __init__(self, rows, cols):
        self.rows = rows
        self.cols = cols
        self.data = [[i+j for i in range(cols)] for j in range(rows)]  # 初始化矩阵

    def __str__(self):
        """返回矩阵的字符串表示"""
        return "\n".join(" ".join(map(str, row)) for row in self.data)

    def add(self, other):
        """矩阵加法"""
        if self.rows != other.rows or self.cols != other.cols:
            raise ValueError("矩阵维度不匹配")
        result = Matrix(self.rows, self.cols)
        for i in range(self.rows):
            for j in range(self.cols):
                result.data[i][j] = self.data[i][j] + other.data[i][j]
        return result

    
class SquareMatrix(Matrix):
    def __init__(self, size):
        super().__init__(size, size)  # 调用父类的构造函数

    def is_identity(self):
        """检查是否是单位矩阵"""
        for i in range(self.rows):
            for j in range(self.cols):
                if i == j and self.data[i][j] != 1:
                    return False
                if i != j and self.data[i][j] != 0:
                    return False
        return True
    
obj1=Matrix(1,2)
obj2=SquareMatrix(6)
print(obj2)
```

- **`super()` 函数**：

  - 在 `SquareMatrix` 的构造函数中，使用 `super().__init__(size, size)` 调用父类的构造函数。

- 派生类对象可以使用基类对象的方法

  - 上述代码的输出：

  ```
  0 1 2 3 4 5
  1 2 3 4 5 6
  2 3 4 5 6 7
  3 4 5 6 7 8
  4 5 6 7 8 9
  5 6 7 8 9 10
  ```

  > 本质上是使用了基类重载的`def __str__(self)`

- 在派生类中实现和基类同名的方法可以实现**对基类方法的覆盖**。

### Advanced Usage

#### Property Decorator

**Property Decorator**（属性装饰器）是 Python 中用于将方法转换为属性的机制。它允许你像访问属性一样访问方法，同时可以在访问时执行额外的逻辑（如验证、计算等）。属性装饰器通过 `@property`、`@属性名.setter` 和 `@属性名.deleter` 来实现。

1. **将方法转换为只读属性**：
   - 使用 `@property` 装饰器，可以将一个方法转换为只读属性。
   - 这样，你可以像访问属性一样调用方法，而不需要使用 `()`。

2. **实现属性的写操作**：
   - 使用 `@属性名.setter` 装饰器，可以为属性添加写操作逻辑。
   - 这样，你可以在赋值时执行额外的逻辑（如验证、计算等）。

3. **实现属性的删除操作**：
   - 使用 `@属性名.deleter` 装饰器，可以为属性添加删除操作逻辑。

1. **只读属性**

```python
class Circle:
    def __init__(self, radius):
        self.radius = radius

    @property
    def area(self):
        """计算圆的面积"""
        return 3.14 * self.radius ** 2

# 使用
c = Circle(5)
print(c.area)  # 输出: 78.5（像访问属性一样调用方法）
# c.area = 100  # 报错：AttributeError（只读属性）
```

2. **可读写属性**

```python
class Temperature:
    def __init__(self, celsius):
        self._celsius = celsius

    @property
    def celsius(self):
        """获取摄氏温度"""
        return self._celsius

    @celsius.setter
    def celsius(self, value):
        """设置摄氏温度"""
        if value < -273.15:
            raise ValueError("Temperature cannot be below absolute zero.")
        self._celsius = value

    @property
    def fahrenheit(self):
        """获取华氏温度"""
        return self._celsius * 9 / 5 + 32

    @fahrenheit.setter
    def fahrenheit(self, value):
        """设置华氏温度"""
        self._celsius = (value - 32) * 5 / 9

# 使用
t = Temperature(25)
print(t.celsius)      # 输出: 25
print(t.fahrenheit)   # 输出: 77.0

t.fahrenheit = 100
print(t.celsius)      # 输出: 37.777...
```

3. **删除属性**

```python
class MyClass:
    def __init__(self):
        self._value = 42

    @property
    def value(self):
        return self._value

    @value.setter
    def value(self, new_value):
        self._value = new_value

    @value.deleter
    def value(self):
        print("Deleting value")
        del self._value

# 使用
obj = MyClass()
print(obj.value)  # 输出: 42
del obj.value     # 输出: Deleting value
# print(obj.value)  # 报错：AttributeError（属性已被删除）
```

| 特性         | 属性装饰器 (`@property`)      | 普通方法                 |
| ------------ | ----------------------------- | ------------------------ |
| **访问方式** | 像访问属性一样（无括号）      | 需要调用方法（带括号）   |
| **逻辑封装** | 可以在访问时执行额外逻辑      | 需要显式调用方法         |
| **只读性**   | 可以通过 `@property` 实现只读 | 需要额外逻辑实现只读     |
| **写操作**   | 通过 `@setter` 实现写操作     | 需要额外方法实现写操作   |
| **删除操作** | 通过 `@deleter` 实现删除操作  | 需要额外方法实现删除操作 |

> The END
>
> 更多Python的高级操作的教程见后续的更新。
