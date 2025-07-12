---
title: File Management in Python
date: 2025-01-16 13:02:00
excerpt: An advanced tutorial focusing on file management in Python.
index_img: /img/cover/filespython.jpg
categories:
  - Code
  - Python
tags:
  - Tutorial
  - Files
  - Python
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Advanced Python Techniques: File Management

It is the learning lecture notes of Python tutorial (from scratch).

## Import Modules

在Python中，**模块**是一个包含Python代码的文件，通常以 `.py` 为扩展名。模块可以包含函数、类、变量和可执行代码。

### Basic Grammar

导入模块最常用的命令是`import`命令，我们可以使用`import`命令导入**Python中的第三方模块**。（类似于C++中的`#include`命令）

在导入模块的过程中，有如下的四种方法：

```Python
# 第一种导入方式：把整个模块全部导入
import math
# 使用 math 模块中的 sqrt 函数
result = math.sqrt(16)
print(result)  # 输出: 4.0


# 第二种导入方式：导入模块的特定内容
from math import sqrt
# 直接使用 sqrt 函数
result = sqrt(16)
print(result)  # 输出: 4.0


# 第三种导入方式：导入模块的全部内容
from math import *
# 直接使用 sqrt 函数
result = sqrt(16)
print(result)  # 输出: 4.0


# 第四种导入方式：起别名 
import math as m
# 使用别名 m 访问 sqrt 函数
result = m.sqrt(16)
print(result)  # 输出: 4.0

from math import sqrt as square_root
# 使用别名 square_root
result = square_root(16)
print(result)  # 输出: 4.0
```

### Import Class

为了提升代码的简洁性，**类的定义**被储存在特定的文件中，在开发者使用是可以被**特定的导入**

```python
# All_the_class.py
class Car:
    def __init__(self):
        pass


class Bike:
    def __init__(self):
        pass

class Truck:
    def __init__(self):
        pass

# main.py

import All_the_class

from All_the_class import Car

from All_the_class import Bike,Car
# 这三种导入方式都是合法的！
```

### Dive Deeper

{% note info %}

#### How do Python import modules?

当你导入一个模块时，Python 会按照以下顺序查找模块：

1. **内置模块**：Python 自带的标准库模块。
2. **当前目录**：运行脚本所在的目录。
3. **环境变量 `PYTHONPATH`**：包含目录列表的环境变量。
4. **安装的第三方库**：通过 `pip` 安装的第三方模块。

**当前目录**是指该文件所在的目录地址，例如下面的文件结构：

- `thefilefolder`
  - `testcode2.py`
  - `testcode3.py`
- `testcode1.py`

对于`testcode2.py`来说，当前目录下有文件`testcode3.py`，而没有`testcode1.py`。

**`PYTHONPATH` 允许你添加自定义的目录路径**，使得 Python 在导入模块时能够从这些目录中查找模块文件。这对于以下场景非常有用：

1. **自定义模块路径**：当你有一些自定义的模块不在当前目录或 Python 的标准库路径中时，可以通过 `PYTHONPATH` 指定这些模块所在的目录。
2. **项目结构管理**：在大型项目中，模块可能分布在不同的目录中，通过 `PYTHONPATH` 可以方便地管理这些模块的路径。

{% endnote %}

从上文可知，文件的导入并不局限于官方库文件的导入，你也可以导入**自己写的Python文件**！

#### `__name__`

我们来看下面的例子，这是在同一个目录下的两个Python文件`testcode4.py`和`testcode5.py`：

```python
# testcode5.py
print("Hello! This is the file testcode5.py!")

# testcode4.py
import testcode5
print("Hello! this is testcode4.py")
```

运行`testcode4.py`，输出结果如下：

```
Hello! This is the file testcode5.py!
Hello! this is testcode4.py
```

实际上，Python解释器运行代码的顺序和逻辑如下：

1. **运行 `testcode4.py`**：
   - Python 解释器开始执行 `testcode4.py` 文件。
   - 当遇到 `import testcode5` 时，Python 会查找并加载 `testcode5.py` 模块。
2. **加载并执行 `testcode5.py`**：
   - Python 会执行 `testcode5.py` 文件中的**所有顶层代码（即不在函数或类中的代码）**。
   - 在 `testcode5.py` 中，`print("Hello! This is the file testcode5.py!")` 是顶层代码，因此会被执行。
   - 执行完 `testcode5.py` 后，Python 会回到 `testcode4.py` 继续执行。
3. **继续执行 `testcode4.py`**：
   - 在 `testcode4.py` 中，`import testcode5` 已经完成，接下来执行 `print("Hello! this is testcode4.py")`。
   - 因此，`testcode4.py` 中的 `print` 语句会被执行。

这就出现了很尴尬的问题，我们设置`print`语句的初衷是告知程序员现在在运行哪一个文件，我们**不希望模块被导入的输出相同的结果**，换句话说，我们希望将一个程序**作为模块被导入运行和直接运行**的行为产生差异。

> 这是一个很实际的问题，模块的使用者直接导入模块，而模块的开发者需要调试信息，就需要这样一种**分离**的策略。

一种方法是将所有的代码写成函数，**这样不存在顶层代码一说了**，但是这对模块开发和调试的功能有增添了不便之处。实际上，每个文件都有一个**`__name__` 属性**，灵活使用`__name__`可以很好的解决这个问题。

我们略微修改刚刚的代码：

```python
# testcode5.py
print("Hello! This is the file testcode5.py!")
print(f"testcode5 __name__: {__name__}")

# testcode4.py
import testcode5
print("Hello! this is testcode4.py")
print(f"testcode4 __name__: {__name__}")

'''
when you run testcode5.py:
Hello! This is the file testcode5.py!
testcode5 __name__: __main__

when you run testcode4.py:
Hello! This is the file testcode5.py!
testcode5 __name__: testcode5
Hello! this is testcode4.py
testcode4 __name__: __main__
'''
```

我们很惊喜的发现，**被导入的文件**和**直接被运行的文件**的`__name__`属性是不一样的！当一个文件被导入的时候，`__name__`字符串的值就是其文件名（例如`testcode5`），而被直接运行的时候，`__name__`的值变成了`__main__`。

因此，我们可以使用`if __name__ == "__main__"`语句来封装**顶层代码**，实现我们上文的需求！

## File Reading and Writing

### What is path?

**路径（path）**是指文件或文件夹在系统中的准确位置，**类似于每家每户的门牌号**。当Python想要读取一个文件时，首先需要获得**文件的地址（即路径）**，这样文件才能被找到并被成功读取。

路径分为两类，**相对路径**和**绝对路径**。

- 绝对路径是从文件系统的根目录开始的完整路径，它唯一地标识一个文件或目录的位置。
- 相对路径是相对于当前工作目录的路径，它依赖于当前的工作环境。

> 通俗一点来说，如果我们要描述小明的绝对路径，我们要从**根目录**（不妨假设为地球）开始说，小明住在哪个大洲哪个国家哪个区域哪个街道哪个门牌号...但如果我站在**当前工作目录**（不妨假设为`地球/亚洲/中国`）,我们可以直接从小明住在哪个区域开始说起就行了。但是如果远在美洲的朋友就无法识别这个相对路径（因为**当前工作目录发生了改变**）。

![The Absolute Path and the Relative Path](https://ooo.0x0.ooo/2025/01/19/OGkztG.png)

### `pathlib`

`pathlib` 是 Python 3.4 引入的一个模块，提供了一种面向对象的方式来处理文件系统路径。

`pathlib` 的核心类是 `Path`，它表示文件系统路径。`Path` 类有两种主要子类：
- **`PurePath`**：纯路径类，只处理路径字符串，不涉及实际文件系统操作。
- **`Path`**：具体路径类，继承自 `PurePath`，可以执行实际的文件系统操作（如创建文件、读取文件等）。

1. 创建 `Path` 对象

```python
from pathlib import Path

# 创建 Path 对象
p = Path('example.txt')
print(p)  # 输出: example.txt
```

2. 路径拼接

使用 `/` 运算符可以轻松拼接路径。
```python
p = Path('/path/to')
p = p / 'subdir' / 'file.txt'
print(p)  # 输出: /path/to/subdir/file.txt
```

3. 获取路径的组成部分

```python
p = Path('/path/to/file.txt')

print(p.name)       # 输出: file.txt（文件名）
print(p.stem)       # 输出: file（文件名不带后缀）
print(p.suffix)     # 输出: .txt（文件后缀）
print(p.parent)     # 输出: /path/to（父目录）
print(p.absolute()) # 输出: 绝对路径
```

4. 检查路径是否存在

```python
p = Path('example.txt')
if p.exists():
    print("文件存在")
else:
    print("文件不存在")
# 这里查找的是相对目录下的文件
```

5. 创建文件和目录

```python
# 创建文件
p = Path('new_file.txt')
p.touch()  # 创建空文件

# 创建目录
p = Path('new_directory')
p.mkdir(exist_ok=True)  # exist_ok=True 避免目录已存在时报错
```

6. 读取和写入文件

```python
# 写入文件
p = Path('example.txt')
p.write_text('Hello, World!')

# 注意！原来文件中的内容会被write_text()所覆盖
path.open(mode='a').write("Hello")
# 可以使用追加模式


# 读取文件
content = p.read_text()
print(content)  # 输出: Hello, World!
```

我们还可以实现一些更高级的操作：

```python
from pathlib import Path

path = Path("example.txt")
contents = path.read_text()

lines = contents.splitlines()
# 会返回一个列表
for i,line in enumerate(lines):
    print(f"The {i+1} line is {line}")
```

```
The 1 line is hello my name is van
The 2 line is i am an artist
The 3 line is a performance artist
```

7. 遍历目录

```python
p = Path('.')
for file in p.iterdir():
    print(file)  # 打印当前目录下的所有文件和目录
```

8. 查找文件

```python
# 查找当前目录下的所有 .txt 文件
for file in p.glob('*.txt'):
    print(file)
```

9. 删除文件或目录

```python
# 删除文件
p = Path('example.txt')
p.unlink()

# 删除目录（目录必须为空）
p = Path('empty_directory')
p.rmdir()
```

   10.获取当前文件目录（这里使用了**os**库）

```Python
import os
from pathlib import Path

# 获取当前工作目录
cwd = os.getcwd()
print(f"当前工作目录: {cwd}")

# 使用 Path.cwd() 获取当前工作目录
cwd_path = Path.cwd()
print(f"当前工作目录: {cwd_path}")
# 获取的是根目录
```

## `FileNotFoundError`

当我们进行文件操作时，一不小心可能会拼错或者搞错了文件的路径，导致**程序无法找到对应的文件**。

例如，如下程序的目录中有文件`example.txt`，但没有`exammple.txt`。

```Python
from pathlib import Path

# for a neater traceback information
from rich import traceback
traceback.install(show_locals=True)


correct_path = Path("example.txt")
wrong_path = Path("exammple.txt")

contents_correct = correct_path.read_text()
contents_wrong = wrong_path.read_text()
```

程序的输出结果如下：

![FileNotfound Error](https://ooo.0x0.ooo/2025/01/20/OGktrC.png)

归根结底就一句话：`FileNotFoundError: [Errno 2] No such file or directory: 'exammple.txt'`。

和C++一样，Python也有**异常处理**的机制。`FileNotFoundError` 是 Python 中的一个**内置异常类**，用于表示程序尝试访问一个不存在的文件或目录时抛出的错误。它是 `OSError` 的子类（在 Python 3.3 之前是 `IOError` 的子类），专门用于处理文件未找到的情况。

在C++中，我们可以使用`try-catch`块来捕获异常，保证其他程序的正常运行，在Python中，`try-except`块也发挥了类似的作用。

```Python
from pathlib import Path

# for a neater traceback information
from rich import traceback
traceback.install(show_locals=True)


correct_path = Path("example.txt")
wrong_path = Path("exammple.txt")

contents_correct = correct_path.read_text()

try:
    contents_wrong = wrong_path.read_text()
except FileNotFoundError:
    print("HaHa, File not found!")
```

再次运行程序，就不会出现`traceback`报错，而只会输出：`HaHa,File not found!`

## Data Storage：`JSON`

### What is `JSON` ?

**JSON 文件** 是一种用于**存储和交换数据的文件格式**，全称是 **JavaScript Object Notation**（JavaScript 对象表示法）。它的设计目标是易于人类阅读和编写，同时也易于机器解析和生成。

JSON 文件的内容是由 **“键值对”** 组成的，就像字典一样。是一种轻量级的数据格式。

```json
{
  "name": "Alice",
  "age": 25,
  "isStudent": false,
  "courses": ["Math", "Science", "History"],
  "address": {
    "city": "Beijing",
    "postalCode": "100000"
  }
}
```

`JSON`文件最强大的用处在于**数据的传输和存储**，例如前后端之间API的调用、软件的配置信息、存储结构化数据（日志，用户信息）等等。

### `JSON` in Python

在Python中，也可以使用`json`模块来进行文件管理。

1. **将 Python 对象转换为 JSON 字符串**

使用 `json.dumps()` 可以将 Python 对象（如字典、列表）转换为 JSON 格式的字符串。

```python
from pathlib import Path
import json

# 准备数据
data = {
    "name": "小红",
    "age": 20,
    "isStudent": False,
    "favoriteFoods": ["火锅", "冰淇淋"]
}

# 将 Python 对象转换为 JSON 字符串
json_string = json.dumps(data, indent=4, ensure_ascii=False)
# 这一步会在当前目录下生成一个data.json文件
print(json_string)


# 使用 pathlib 写入文件
file_path = Path("data.json")
file_path.write_text(json_string, encoding="utf-8")
```

```json
{
    "name": "小明",
    "age": 18,
    "isStudent": true,
    "hobbies": [
        "打篮球",
        "听音乐"
    ]
}
```

- **`indent=4`**：让 JSON 字符串格式化输出，缩进 4 个空格。
- **`ensure_ascii=False`**：确保非 ASCII 字符（如中文）正常显示。

2. **将 JSON 字符串转换为 Python 对象**

使用 `json.loads()` 可以将 JSON 格式的字符串转换为 Python 对象（如字典、列表）。

```python
from pathlib import Path
import json

# 使用 pathlib 读取文件
file_path = Path("data.json")
json_string = file_path.read_text(encoding="utf-8")

# 将 JSON 字符串转换为 Python 对象
data = json.loads(json_string)
print(data["name"])  # 输出：小红
print(data["favoriteFoods"])  # 输出：['火锅', '冰淇淋']
```

### Usage: User_info management

学会了`json`的基本操作后，我们可以实现一个**迷你数据库**：用来存储用户的数据，进行读写等操作。

```Python
from pathlib import Path
import json

# 获取用户输入的名字
user_name = input("Hello! What is your name? ")
user_name_path = Path("user_info.json")

# 初始化用户数据
all_user_data = []

# 如果文件存在，读取文件内容
if user_name_path.exists():
    all_user_contents = user_name_path.read_text(encoding="utf-8")
    all_user_data = json.loads(all_user_contents)

# 检查用户是否已经存在
user_exists = False
user_info = None

for user in all_user_data:
    if user.get("name") == user_name:
        user_exists = True
        user_info = user
        break

if user_exists:
    # 用户已经注册过
    print(f"Welcome Back, {user_name}!")
    print(f"Your favourite food is {user_info.get('food')}")
else:
    # 新用户
    print(f"Hello {user_name}! It seems that you are new here.")
    fav_food = input("What is your favourite food? ")

    # 创建新用户数据
    new_user = {
        "name": user_name,
        "food": fav_food
    }

    # 将新用户添加到用户列表中
    all_user_data.append(new_user)

    # 将更新后的用户列表写入文件
    with open("user_info.json", "w", encoding="utf-8") as json_file:
        json.dump(all_user_data, json_file, ensure_ascii=False, indent=4)

    print("Your information has been saved. Welcome!")
```

**这样的Python代码习惯实在是太差了！**下面是对函数体封装后的优化写法：

```Python
from pathlib import Path
import json

def load_user_data(file_path):
    """
    从 JSON 文件加载用户数据。
    如果文件不存在，返回空列表。
    """
    if file_path.exists():
        try:
            with open(file_path, "r", encoding="utf-8") as file:
                return json.load(file)
        except json.JSONDecodeError:
            print(f"错误：文件 {file_path} 不是有效的 JSON 文件！")
            return []
    else:
        return []

def save_user_data(file_path, data):
    """
    将用户数据保存到 JSON 文件。
    """
    with open(file_path, "w", encoding="utf-8") as file:
        json.dump(data, file, ensure_ascii=False, indent=4)

def find_user_by_name(users, name):
    """
    在用户列表中查找指定名字的用户。
    如果找到，返回用户信息；否则返回 None。
    """
    for user in users:
        if user.get("name") == name:
            return user
    return None

def register_new_user(users, name):
    """
    注册新用户并返回更新后的用户列表。
    """
    print(f"Hello {name}! It seems that you are new here.")
    fav_food = input("What is your favourite food? ")
    new_user = {"name": name, "food": fav_food}
    users.append(new_user)
    print("Your information has been saved. Welcome!")
    return users

def main():
    """
    主程序逻辑。
    """
    # 文件路径
    user_file_path = Path("user_info.json")

    # 加载用户数据
    users = load_user_data(user_file_path)

    # 获取用户输入的名字
    user_name = input("Hello! What is your name? ")

    # 查找用户是否已存在
    user_info = find_user_by_name(users, user_name)

    if user_info:
        # 用户已存在
        print(f"Welcome Back, {user_name}!")
        print(f"Your favourite food is {user_info.get('food')}")
    else:
        # 新用户注册
        users = register_new_user(users, user_name)
        # 保存更新后的用户数据
        save_user_data(user_file_path, users)

# 运行主程序
if __name__ == "__main__":
    main()
```

## Project: Data Visualization

### Chapter 15 Data_Creation

#### `Matplotlib`

Python的强大之处之一在于**其活跃的社区生态**，通过`pip install`他人的第三方库，我们可以实现更多强大的功能，接下来的章节笔者将重点介绍`Matplotlib`的使用。

> 这一章的学习非常有趣，强烈建议初学者一起上手操作。

`Matplotlib` 是 Python 中最流行的数据可视化库之一，广泛用于创建静态、动态和交互式的图表。它提供了类似于 MATLAB 的绘图接口，简单易用且功能强大。以下是 `Matplotlib` 的简单介绍和基本用法：

(1) **绘制简单的折线图**

```python
import matplotlib.pyplot as plt

# 数据
x = [1, 2, 3, 4, 5]
y = [2, 3, 5, 7, 11]

# 创建图表
plt.plot(x, y)

# 添加标题和标签
plt.title("Simple Line Plot")
plt.xlabel("X-axis")
plt.ylabel("Y-axis")

# 显示图表
plt.show()
```

<img src="https://matplotlib.org/stable/_images/sphx_glr_pyplot_001.png" alt="Simple Line Plot" style="zoom:67%;" />

(2) **绘制散点图**

```python
import matplotlib.pyplot as plt

# 数据
x = [1, 2, 3, 4, 5]
y = [2, 3, 5, 7, 11]

# 创建散点图
plt.scatter(x, y, s=10)
# s代表点的大小

# 添加标题和标签
plt.title("Scatter Plot")
plt.xlabel("X-axis")
plt.ylabel("Y-axis")

# 显示图表
plt.show()
```

<img src="https://matplotlib.org/stable/_images/sphx_glr_scatter_001.png" alt="Scatter Plot" style="zoom:67%;" />

(3) **绘制柱状图**

```python
import matplotlib.pyplot as plt

# 数据
categories = ['A', 'B', 'C', 'D']
values = [10, 20, 15, 25]

# 创建柱状图
plt.bar(categories, values)

# 添加标题和标签
plt.title("Bar Chart")
plt.xlabel("Categories")
plt.ylabel("Values")

# 显示图表
plt.show()
```



![Bar Chart](https://matplotlib.org/stable/_images/sphx_glr_bar_001.png)

(4) **绘制饼图**

```python
import matplotlib.pyplot as plt

# 数据
labels = ['A', 'B', 'C', 'D']
sizes = [15, 30, 45, 10]

# 创建饼图
plt.pie(sizes, labels=labels, autopct='%1.1f%%')

# 添加标题
plt.title("Pie Chart")

# 显示图表
plt.show()
```

![Pie Chart](https://matplotlib.org/stable/_images/sphx_glr_pie_001.png)

使用 `plt.subplots` 可以在一个窗口中绘制多个子图。

```python
import matplotlib.pyplot as plt

# 数据
x = [1, 2, 3, 4, 5]
y1 = [2, 3, 5, 7, 11]
y2 = [1, 4, 9, 16, 25]

# 创建子图
fig, (ax1, ax2) = plt.subplots(1, 2, figsize=(10, 5))

# 子图 1：折线图
ax1.plot(x, y1)
ax1.set_title("Line Plot")

# 子图 2：散点图
ax2.scatter(x, y2)
ax2.set_title("Scatter Plot")

# 显示图表
plt.show()
```

![Subplots](https://ooo.0x0.ooo/2025/01/20/OGkwSP.png)

`Matplotlib` 支持丰富的自定义选项，例如颜色、线型、标记等。

```python
import matplotlib.pyplot as plt

# 数据
x = [1, 2, 3, 4, 5]
y = [2, 3, 5, 7, 11]

# 创建图表
plt.plot(x, y, color='red', linestyle='--', marker='o', label='Data')

# 添加标题、标签和图例
plt.title("Customized Line Plot")
plt.xlabel("X-axis")
plt.ylabel("Y-axis")
plt.legend()

# 显示图表
plt.show()
```

![Customized Line Plot](https://matplotlib.org/stable/_images/sphx_glr_line_demo_dash_control_001.png)

使用 `plt.savefig` 可以将图表保存为图片文件。

```python
import matplotlib.pyplot as plt

# 数据
x = [1, 2, 3, 4, 5]
y = [2, 3, 5, 7, 11]

# 创建图表
plt.plot(x, y)

# 保存图表
plt.savefig("line_plot.png")
```

也可以使用**内置样式**：

```Python
import matplotlib.pyplot as plt

# 查看所有内置样式
print(plt.style.available)
```

```Python
import matplotlib.pyplot as plt

# 应用内置样式
plt.style.use('seaborn-v0_8-dark')

# 数据
x = [1, 2, 3, 4, 5]
y = [2, 3, 5, 7, 11]

# 创建图表
plt.plot(x, y, label="Data")

# 添加标题和标签
plt.title("Line Plot with Seaborn Darkgrid Style")
plt.xlabel("X-axis")
plt.ylabel("Y-axis")

# 添加图例
plt.legend()

# 显示图表
plt.show()
```

> Matplotlib提供了很多自定义的设置，例如`ticklable_format(style=)`来设置**刻度标记**，自定义RGB颜色`color=(0,0.8,0)`

#### Application：Random Walk

下文给出实现`Random Walk` 的Python代码：

```python
from random import choice
import matplotlib.pyplot as plt
from tqdm import tqdm

class RandomWalk:
    '''A class represents the random walk'''

    def __init__(self, num_points=500):
        self.num_points = num_points

        self.x_values=[0]
        self.y_values=[0]

    def fill_walk(self):
        while len((self.x_values))<self.num_points:
            x_direction = choice([1,-1])
            x_distance = choice([0,1,1,1,1,1,1,2,3,4])
            x_step = x_distance * x_direction

            y_direction = choice([1,-1])
            y_distance = choice([0,1,1,1,1,1,1,2,3,4])
            y_step = y_distance * y_direction

            if x_step==0 and y_step==0:
                continue

            x_new = self.x_values[-1] + x_step
            y_new = self.y_values[-1] + y_step

            self.x_values.append(x_new)
            self.y_values.append(y_new)


def imitate_walk(index):
    Total_steps= 10000
    rw_orange = RandomWalk(Total_steps)
    rw_red = RandomWalk(Total_steps)
    rw_blue = RandomWalk(Total_steps)

    rw_orange.fill_walk()
    rw_blue.fill_walk()
    rw_red.fill_walk()

    plt.style.use('classic')
    fig, ax = plt.subplots()
    point_numbers = list(range(Total_steps))

    ax.scatter(rw_orange.x_values, rw_orange.y_values, c=point_numbers,cmap=plt.cm.Oranges,edgecolors='none', s=2)
    ax.scatter(rw_blue.x_values, rw_blue.y_values, c=point_numbers,cmap=plt.cm.Blues,edgecolors='none', s=2)
    ax.scatter(rw_red.x_values, rw_red.y_values, c=point_numbers,cmap=plt.cm.Reds,edgecolors='none', s=2)


    ax.set_aspect('equal')

    ax.get_xaxis().set_visible(False)
    ax.get_yaxis().set_visible(False)


    plt.suptitle(f'Random Walk{index+1}', fontsize=16, fontweight='bold')
    plt.savefig(f'Random_walk{index+1}.png', bbox_inches='tight')
    plt.close()

def main():
    for i in tqdm(range(1000)):
        imitate_walk(i)

if __name__ == '__main__':
    '''The main function'''
    main()

```

这个代码会实现1000次的Random_Walk，其中每一次的步长为10000。最后画出的图片效果还是非常震撼的！

### Chapter 16 Download Data

#### CSV

CSV（Comma-Separated Values，逗号分隔值）是一种简单的文件格式，用于存储和交换表格数据。它以纯文本形式存储数据，每行代表一条记录，每个字段用逗号分隔。CSV 文件易于生成和解析，广泛应用于数据存储、导入和导出。

```
Name,Age,City
Alice,30,New York
Bob,25,Los Angeles
Charlie,35,Chicago
```

- **表头（Header）**：第一行通常是字段名称（如 `Name, Age, City`）。
- **数据行**：每行代表一条记录，字段之间用逗号分隔。
- **分隔符**：默认是逗号（`,`），但也可以是其他字符（如分号 `;` 或制表符 `\t`）。

我们会发现，CSV数据文件阅读起来还是比较麻烦的，（作为存储便捷性的牺牲）我们可以使用Python来帮助我们解析CSV数据并且完成数据的可视化。

### CSV in Python：Data Processing

在原书中，作者使用了天气数据作为示范，我们可以来一点更好玩的~

在接下来的篇幅中，笔者将使用**Python完成一个简单的机器学习项目——Titanic二分类问题**

数据集来源：[Kaggle Titanic](https://www.kaggle.com/c/titanic/overview)

在Vscode中新建一个文件夹作为项目主目录，并且下载官方提供的数据集：`gender_submission.csv`,`test.csv`,`train.csv`

先看看官方给我们的要求是什么：

> **The training set** should be used to build your machine learning models. For the training set, we provide the outcome (also known as the “ground truth”) for each passenger. Your model will be based on “features” like passengers’ gender and class. You can also use [feature engineering ](https://triangleinequality.wordpress.com/2013/09/08/basic-feature-engineering-with-the-titanic-data/)to create new features.
>
> **The test set** should be used to see how well your model performs on unseen data. For the test set, we do not provide the ground truth for each passenger. It is your job to predict these outcomes. For each passenger in the test set, use the model you trained to predict whether or not they survived the sinking of the Titanic.
>
> We also include **gender_submission.csv**, a set of predictions that assume all and only female passengers survive, as an example of what a submission file should look like.

通俗来说，就是给了泰坦尼克号上部分成员的数据信息，然后建立自己的机器学习模型预测**“ground truth”**,最后提交一份类似于`gender_submission.csv`的报告（第三份文件只是输出示例，假设所有女人幸存而所有男人牺牲）

##### Loading Data

我们首先需要**读取csv文件中的数据**，可以使用Python中的`csv`模块。

```Python
import csv
from pathlib import Path
import json

test_data_path = Path('test.csv')
train_data_path = Path('train.csv')

train_lines = train_data_path.read_text().splitlines()
test_lines = test_data_path.read_text().splitlines()
# 将文本字符串按行拆分成列表

train_reader = csv.reader(train_lines)
test_reader = csv.reader(test_lines)

header_row_train = next(train_reader)
header_row_test = next(test_reader)
#得到第一行题头

print(header_row_train)
print(header_row_test)

```

```
['PassengerId', 'Survived', 'Pclass', 'Name', 'Sex', 'Age', 'SibSp', 'Parch', 'Ticket', 'Fare', 'Cabin', 'Embarked']
['PassengerId', 'Pclass', 'Name', 'Sex', 'Age', 'SibSp', 'Parch', 'Ticket', 'Fare', 'Cabin', 'Embarked']
```

接下来，我们将要将数据处理成方便获取的JSON格式。

```Python
train_data_json = []
test_data_json = []

# 将训练集数据转换为 JSON
for row in train_reader:
    # 将每一行数据与题头组合成字典
    row_dict = {header: value for header, value in zip(header_row_train, row)}
    train_data_json.append(row_dict)

# 将测试集数据转换为 JSON
for row in test_reader:
    # 将每一行数据与题头组合成字典
    row_dict = {header: value for header, value in zip(header_row_test, row)}
    test_data_json.append(row_dict)

# 打印结果
print("训练集 JSON 数据：")
print(train_data_json[:2])

print("\n测试集 JSON 数据：")
print(test_data_json[:2])

# 保存训练集 JSON 数据
with open('train_data.json', 'w', encoding='utf-8') as f:
    json.dump(train_data_json, f, indent=2)  # indent=2 用于美化输出

# 保存测试集 JSON 数据
with open('test_data.json', 'w', encoding='utf-8') as f:
    json.dump(test_data_json, f, indent=2)
```

运行程序，我们就成功的把csv格式的文件存储成为`JSON`文件！

##### Data Washing

接下来，我们需要进行数据的预处理，包括**处理空缺的数据**等等。

```python
import json
import pandas as pd
import numpy as np

'''Data washing'''

# 加载训练集 JSON 数据
with open('train_data.json', 'r', encoding='utf-8') as f:
    train_data = json.load(f)

# 加载测试集 JSON 数据
with open('test_data.json', 'r', encoding='utf-8') as f:
    test_data = json.load(f)


# 将 JSON 数据转换为 DataFrame
train_df = pd.DataFrame(train_data)
test_df = pd.DataFrame(test_data)

# 将字符串类型的数值转换为数值类型
train_df['Age'] = train_df['Age'].replace('', np.nan)
train_df['Fare'] = train_df['Fare'].replace('', np.nan)
test_df['Age'] = test_df['Age'].replace('', np.nan)
test_df['Fare'] = test_df['Fare'].replace('', np.nan)

train_df['Age'] = train_df['Age'].astype(float)
train_df['Fare'] = train_df['Fare'].astype(float)

test_df['Age'] = test_df['Age'].astype(float)
test_df['Fare'] = test_df['Fare'].astype(float)


# 处理缺失值
train_df['Age'] = train_df['Age'].fillna(train_df['Age'].median())
train_df['Embarked'] = train_df['Embarked'].fillna(train_df['Embarked'].mode()[0])
train_df['Cabin'] = train_df['Cabin'].fillna('Unknown')

test_df['Age'] = test_df['Age'].fillna(test_df['Age'].median())
test_df['Fare'] = test_df['Fare'].fillna(test_df['Fare'].median())
test_df['Cabin'] = test_df['Cabin'].fillna('Unknown')

# check
# 检查训练集的缺失值
print("The loss of train set:")
print(train_df.isnull().sum())

# 检查测试集的缺失值
print("\nThe loss of test set:")
print(test_df.isnull().sum())
```

检查输出，说明数据预处理完成。

```bash
The loss of train set:
PassengerId    0
Survived       0
Pclass         0
Name           0
Sex            0
Age            0
SibSp          0
Parch          0
Ticket         0
Fare           0
Cabin          0
Embarked       0
dtype: int64

The loss of test set:
PassengerId    0
Pclass         0
Name           0
Sex            0
Age            0
SibSp          0
Parch          0
Ticket         0
Fare           0
Cabin          0
Embarked       0
dtype: int64
```

> 这就是第一步储存在`JSON`的好处，模块化的处理保证了程序员可以独立地执行各部分的程序，如果出错了也可以重新进行Loading Data获得原始数据。

##### Machine Learning

接下来就是**机器学习**的专场了，笔者使用的是**随机森林RandomBoost**。

```Python
# %%
import json
import pandas as pd
import numpy as np

# %%

'''Data washing'''

# 加载训练集 JSON 数据
with open('train_data.json', 'r', encoding='utf-8') as f:
    train_data = json.load(f)

# 加载测试集 JSON 数据
with open('test_data.json', 'r', encoding='utf-8') as f:
    test_data = json.load(f)


# 将 JSON 数据转换为 DataFrame
train_df = pd.DataFrame(train_data)
test_df = pd.DataFrame(test_data)

# 将字符串类型的数值转换为数值类型
train_df['Age'] = train_df['Age'].replace('', np.nan)
train_df['Fare'] = train_df['Fare'].replace('', np.nan)
test_df['Age'] = test_df['Age'].replace('', np.nan)
test_df['Fare'] = test_df['Fare'].replace('', np.nan)

train_df['Age'] = train_df['Age'].astype(float)
train_df['Fare'] = train_df['Fare'].astype(float)

train_df['Parch'] = train_df['Parch'].astype(int)
train_df['SibSp'] = train_df['SibSp'].astype(int)
train_df['Pclass'] = train_df['Pclass'].astype(int)


test_df['Age'] = test_df['Age'].astype(float)
test_df['Fare'] = test_df['Fare'].astype(float)

test_df['Parch'] = test_df['Parch'].astype(int)
test_df['SibSp'] = test_df['SibSp'].astype(int)
test_df['Pclass'] = test_df['Pclass'].astype(int)


# 处理缺失值
train_df['Age'] = train_df['Age'].fillna(train_df['Age'].median())
train_df['Embarked'] = train_df['Embarked'].fillna(train_df['Embarked'].mode()[0])
train_df['Cabin'] = train_df['Cabin'].fillna('Unknown')

test_df['Age'] = test_df['Age'].fillna(test_df['Age'].median())
test_df['Fare'] = test_df['Fare'].fillna(test_df['Fare'].median())
test_df['Cabin'] = test_df['Cabin'].fillna('Unknown')



# check
# 检查训练集的缺失值
print("The loss of train set:")
print(train_df.isnull().sum())

# 检查测试集的缺失值
print("\nThe loss of test set:")
print(test_df.isnull().sum())

# %%
# 从姓名中提取称呼（如 Mr, Miss, Mrs）
train_df['Title'] = train_df['Name'].apply(lambda x: x.split(',')[1].split('.')[0].strip())
test_df['Title'] = test_df['Name'].apply(lambda x: x.split(',')[1].split('.')[0].strip())

# 将性别转换为数值
train_df['Sex'] = train_df['Sex'].map({'male': 0, 'female': 1})
test_df['Sex'] = test_df['Sex'].map({'male': 0, 'female': 1})

# 创建家庭大小特征
train_df['FamilySize'] = train_df['SibSp'] + train_df['Parch'] + 1
test_df['FamilySize'] = test_df['SibSp'] + test_df['Parch'] + 1

# 将登船港口转换为数值
train_df['Embarked'] = train_df['Embarked'].map({'S': 0, 'C': 1, 'Q': 2})
test_df['Embarked'] = test_df['Embarked'].map({'S': 0, 'C': 1, 'Q': 2})

# %%
# 选择特征
features = ['Pclass', 'Sex', 'Age', 'SibSp', 'Parch', 'Fare', 'Embarked', 'FamilySize']
X_train = train_df[features]
y_train = train_df['Survived']

X_test = test_df[features]

# %%
print(X_train.dtypes)

# %%
from sklearn.ensemble import RandomForestClassifier

# 初始化模型
model = RandomForestClassifier(n_estimators=100, random_state=42)

# 训练模型
model.fit(X_train, y_train)

# %%
from sklearn.metrics import accuracy_score, confusion_matrix

# 在训练集上进行预测
y_train_pred = model.predict(X_train)

# 计算准确率
accuracy = accuracy_score(y_train, y_train_pred)
print(f"训练集准确率: {accuracy:.2f}")

# 绘制混淆矩阵
conf_matrix = confusion_matrix(y_train, y_train_pred)
print("混淆矩阵:")
print(conf_matrix)

# %%
# 对测试集进行预测
test_predictions = model.predict(X_test)

# 将预测结果保存到 CSV 文件
output = pd.DataFrame({
    'PassengerId': test_df['PassengerId'],
    'Survived': test_predictions
})
output.to_csv('submission.csv', index=False)


```
