---
title: 'Python Architecture Patterns: Multi-file Programming'
date: 2025-07-12 17:09:37
excerpt: Tutorials on managing import paths when programming in Python with multiple files, including understanding import priority, absolute imports, and relative imports.
index_img: /img/cover/filespython.jpg
categories:
  - Code
  - Architecture Design
tags:
  - finished
  - Tutorial
  - Modules
  - Python
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# PAP series: Multi-file Programming

## Introduction

In Python, `import` command is frequently used to import **modules**, **classes**, **functions**, etc. 

> Make sure you have access to the basic concepts of modules in Python and several basic commands to importing a module!

## 识别和优先级

import的识别文件范围非常广泛，包括：

- `.py`的模块文件

- 编译后的Python文件 `.pyc` & `.pyo`，可以提高运行速度

- Packages: 含有`__init__.py`文件的含有多个模块的目录

但是在import的顺序上，Python有自己设计的一套优先级：


- 当前模块：（运行脚本）所在的目录。注意，当前目录是指该文件所在的目录地址，**而不是运行脚本的地址**。

搜索优先级如下：

- 内置模块 (Built-in Modules): 首先，Python 会查找内置模块。这些模块直接编译到 Python 解释器中，无需文件查找。例如 `sys`, `os`, `math` 等。运行bash命令 `ls /usr/lib/python3.12/` 就可以查看相当多的Python自带的标准库模块。

- `sys.modules` 缓存: 接下来，Python 会检查 sys.modules 字典。这是一个缓存，存储了所有已经被导入过的模块。如果模块已经在 sys.modules 中，Python 会直接返回缓存中的模块对象，避免重复导入和重复执行模块代码。

- `sys.path` 中的路径: 如果模块不在 sys.modules 中，Python 会按照 sys.path 列表中的顺序，逐个目录地查找模块。sys.path 的默认组成部分通常包括：

    - 当前工作目录 (Current Working Directory): 脚本运行时的当前目录。(**通常这是第一个条目**)

    - `PYTHONPATH` 环境变量: 如果设置了 PYTHONPATH 环境变量，它会包含在 sys.path 中。

    - 标准库目录: Python 安装时自带的标准库模块的路径。

    - 第三方库目录 (site-packages): 通过 pip 等工具安装的第三方库通常会被安装到 site-packages 目录中。

下面我们逐一介绍之。

### `sys.modules`

缓存机制设计的最大好处就是**快速导入之前已经重复导入的库**，例如，在该进程中两处Python文件中都有代码`import torch`，在第二次导入的时候会使用首次导入的缓存机制，快速导入。

例如下面的代码：

```python
import time
import sys


def test_import(index):
    start_time = time.time()
    import torch

    end_time = time.time()
    print(f"Spend time {index}: {(end_time - start_time):.8f} seconds")


if __name__ == "__main__":
    cache_modules = sys.modules.keys()
    print(f"numbers of modules in cache: {len(cache_modules)}")
    print(f"Is torch module in the cache? {("torch" in cache_modules)}")

    test_import(1)

    cache_modules = sys.modules.keys()
    print(f"numbers of modules in cache: {len(cache_modules)}")
    print(f"Is torch module in the cache? {("torch" in cache_modules)}")

    test_import(2)
```

Output:
```text
numbers of modules in cache: 44
Is torch module in the cache? False
Spend time 1: 1.36842155 seconds
numbers of modules in cache: 1053
Is torch module in the cache? True
Spend time 2: 0.00000262 seconds
```

从代码中很容易可以看见，**缓存机制**的设计使得第二次导入torch库所花的时间远低于第一次。

### `sys.path`

如果未在缓存中寻找到相关文件，解释器就会**寻找特定的路径**，这些路径会在`sys.path`中给出。

#### Current Directory and Absolute Path of files

```python
import os

if __name__ == "__main__":
    print(f"Current Working Directory: {os.getcwd()}")
    print(f"Absolute path of this file: {os.path.abspath(__file__)}")
```

运行这段代码，笔者的命令是`python "/home/xiyuanyang/python/test/test_inner/test_inner_inner/test.py"`，在`/home/xiyuanyang/python/test`目录下运行，得到结果：

```text
Current Working Directory: /home/xiyuanyang/python/test
Absolute path of this file: /home/xiyuanyang/python/test/test_inner/test_inner_inner/test.py
```

这就涉及到两个**极易被混淆的概念：当前工作目录和文件目录**：

- **Current Working Directory** (CWD) - `os.getcwd()`：当前工作目录 (CWD) 是你执行 Python 脚本命令时所处的那个目录。无论你的脚本文件实际存储在哪里，os.getcwd() 都会返回你敲下 python 命令并按下回车键时，终端光标所在的目录。**CWD 是 Python 解析相对路径的起点**。（这一点很重要，并且在**任何一种调用情况下都成立**！）。例如，如果你在脚本中尝试打开一个文件，比如 with open("my_data.txt", "r") as f:，Python 会首先在 CWD (/home/xiyuanyang/python/test) 中查找 my_data.txt。

- **Absolute path of this file** - `os.path.abspath(__file__)`：这是正在执行的 Python 脚本文件本身在文件系统中的完整、绝对路径。它与你从哪个目录启动脚本无关，而是文件在磁盘上的固定位置。


在Python的多文件编程中，因为不同的代码文件具有不同的层次关系，在代码树中的位置自然不同，因此**当前工作目录**和**文件位置**不一致是非常常见的事情。

回到`sys.path`，下面我们来尝试两种不同的调用方式：

```python
import os
import sys

if __name__ == "__main__":
    print(f"Current Working Directory: {os.getcwd()}")
    print(f"Absolute path of this file: {os.path.abspath(__file__)}")
    print(f"sys.path: {sys.path}")
```

- run as scripts: **prepend the script’s directory**

```bash
# run in ~/python/test 
python "/home/xiyuanyang/python/test/test_inner/test_inner_inner/test.py" 
```

```text
Current Working Directory: /home/xiyuanyang/python/test
Absolute path of this file: /home/xiyuanyang/python/test/test_inner/test_inner_inner/test.py
sys.path: ['/home/xiyuanyang/python/test/test_inner/test_inner_inner', '/home/xiyuanyang/anaconda3/lib/python312.zip', '/home/xiyuanyang/anaconda3/lib/python3.12', '/home/xiyuanyang/anaconda3/lib/python3.12/lib-dynload', '/home/xiyuanyang/anaconda3/lib/python3.12/site-packages']
```

- run `-m` as modules: **prepend CWD**

```bash
# run in ~/python/test 
python -m test_inner.test_inner_inner.test
```

```text
Current Working Directory: /home/xiyuanyang/python/test
Absolute path of this file: /home/xiyuanyang/python/test/test_inner/test_inner_inner/test.py
sys.path: ['/home/xiyuanyang/python/test', '/home/xiyuanyang/anaconda3/lib/python312.zip', '/home/xiyuanyang/anaconda3/lib/python3.12', '/home/xiyuanyang/anaconda3/lib/python3.12/lib-dynload', '/home/xiyuanyang/anaconda3/lib/python3.12/site-packages']
```

观察易得，两种调用方式会得到**不同的sys.path**，即解释器在导入模块的时候查找的目录也不一样。对于正常的**按脚本导入**，会查找**脚本所在的文件位置**，然而按模块导入会**查找当前工作目录**。

当然，`sys.path`返回是是一个列表，因此，你可以手动**添加自定义的文件路径**。这也可以作为一种快速修复导入问题的途径。

```python
import os
import sys

if __name__ == "__main__":
    print(f"Current Working Directory: {os.getcwd()}")
    print(f"Absolute path of this file: {os.path.abspath(__file__)}")
    print(f"sys.path: {sys.path}")

    sys.path.append(os.getcwd())
    # append at the bottom
    print("After appending")
    print(f"sys.path: {sys.path}")
```

```text
Current Working Directory: /home/xiyuanyang/python/test
Absolute path of this file: /home/xiyuanyang/python/test/test_inner/test_inner_inner/test.py
sys.path: ['/home/xiyuanyang/python/test/test_inner/test_inner_inner', '/home/xiyuanyang/anaconda3/lib/python312.zip', '/home/xiyuanyang/anaconda3/lib/python3.12', '/home/xiyuanyang/anaconda3/lib/python3.12/lib-dynload', '/home/xiyuanyang/anaconda3/lib/python3.12/site-packages']
After appending
sys.path: ['/home/xiyuanyang/python/test/test_inner/test_inner_inner', '/home/xiyuanyang/anaconda3/lib/python312.zip', '/home/xiyuanyang/anaconda3/lib/python3.12', '/home/xiyuanyang/anaconda3/lib/python3.12/lib-dynload', '/home/xiyuanyang/anaconda3/lib/python3.12/site-packages', '/home/xiyuanyang/python/test']
```

#### Demo

在掌握上文知识后，修复这样的bug也是信手拈来了。

```text
.
├── greetings.py
└── test_inner
    └── hello_world.py

2 directories, 2 files
```

```python
# ./greetings.py
def greetings():
    print("Hello world")

# ./test_inner/hello_world.py
import os
print(os.getcwd())

import greetings

if __name__ == "__main__":
    greetings.greetings()
```

```bash
python test_inner/hello_world.py
```

{% note error %}

Traceback (most recent call last):
  File "/home/xiyuanyang/test/test_inner/hello_world.py", line 10, in <module>
    import greetings
ModuleNotFoundError: No module named 'greetings'

{% endnote %}

FIX:

- Fix Method 1: run `python -m test_inner.hello_world` and run as scripts.

- Fix Method 2: add `sys.path.append(os.getcwd())` into the code before importing.


## 绝对路径和相对路径

在搞懂import内部的操作原理后，我们来看最后一个比较复杂的问题：**绝对路径和相对路径导入**。

-----

## Python 中的绝对路径导入与相对路径导入

在 Python 中，当我们组织代码成模块和包时，如何正确地导入这些模块就变得非常重要。Python 提供了两种主要的导入方式：**绝对路径导入 (Absolute Imports)** 和 **相对路径导入 (Relative Imports)**。理解它们的区别和适用场景是编写清晰、可维护代码的关键。

### 绝对路径导入 (Absolute Imports)

绝对路径导入是从项目的**根目录**或**顶级包**开始的导入方式。它明确指定了模块在整个项目结构中的完整位置。无论你在哪个文件或哪个目录执行导入语句，只要项目的根目录在 `sys.path` 中，绝对路径导入都会指向同一个确切的模块。因此，**绝对路径导入也会受到上述两种Python运行方式的差异**。


假设你的项目结构如下：

```bash
my_project/             # 项目根目录
├── main.py
├── package_a/
│   ├── __init__.py     # 使 package_a 成为一个包
│   ├── module_x.py
│   └── subpackage_b/
│       ├── __init__.py # 使 subpackage_b 成为一个包
│       └── module_y.py
└── package_c/
    ├── __init__.py     # 使 package_c 成为一个包
    └── module_z.py
```

  * 在 `main.py` 中导入 `module_x` 和 `module_y`：

    ```python
    # my_project/main.py
    import package_a.module_x              # 导入整个模块
    from package_a.subpackage_b import module_y # 从子包中导入特定模块

    # 接下来你就可以使用它们了
    package_a.module_x.some_function()
    module_y.another_function()
    ```

    这里，`package_a` 和 `package_a.subpackage_b` 都是从 `my_project` 这个顶级目录（假设它在 `sys.path` 中）开始的绝对路径。

  * 在 `package_a/module_x.py` 中导入 `module_z`：

    ```python
    # my_project/package_a/module_x.py
    from package_c import module_z # 即使 module_x 和 module_z 在不同的顶级包下，也可以通过绝对路径导入

    module_z.do_something()
    ```

    同样，`package_c` 是从项目根目录开始的绝对路径。

### 相对路径导入 (Relative Imports)

相对路径导入是相对于**当前模块所在的包**的位置进行导入。它使用 `.` 和 `..` 来表示相对位置，类似于文件系统中的相对路径。

{% note primary %}

**无论哪种调用方式，相对路径导入都是从CWD出发的**！

{% endnote %}

  * `.` 表示**当前包**。
  * `..` 表示**当前包的父级包**。可以有多个 `..` (`...` 表示祖父级包，以此类推)。

  > **建议尽量少用或者不用相对路径**，尤其是`..`，这会带来很多难以名状的导入错误。对于跨目录的包尽可能使用绝对路径导入。

**重要：相对导入只能在包内部的模块中使用，不能在顶层脚本（即不属于任何包的 `.py` 文件）中使用。**

**示例 (基于上面的项目结构):**

  * **在 `package_a/subpackage_b/module_y.py` 中导入 `module_x`：**

    ```python
    # my_project/package_a/subpackage_b/module_y.py
    from .. import module_x # 从父级包 (package_a) 导入 module_x

    module_x.some_function()
    ```

    `module_y.py` 位于 `subpackage_b` 包中。`..` 指的是 `subpackage_b` 的父包，即 `package_a`。

  * **在 `package_a/module_x.py` 中导入 `subpackage_b` 下的 `module_y`：**

    ```python
    # my_project/package_a/module_x.py
    from .subpackage_b import module_y # 从当前包 (package_a) 的子包导入 module_y

    module_y.another_function()
    ```

    `module_x.py` 位于 `package_a` 包中。`.` 指的是 `package_a` 自身。


  * **首选绝对路径导入：** 在绝大多数情况下，尤其是在大型、多层次的项目中，**推荐使用绝对路径导入**。它使代码更清晰、更易于理解和维护，并且在重构时更不容易出错。当你需要导入不同顶级包中的模块时，绝对路径导入是唯一选择。
  * **相对路径导入的适用场景：** 相对路径导入通常只在**包内部**用于模块间的相互引用。它能让导入语句更简洁，并且在将包作为一个整体移动时保持其内部逻辑的完整性。

  * **避免在顶层脚本中使用相对导入：** 如果你直接运行一个不属于任何包的 Python 脚本，并尝试在其中使用相对导入，你会遇到 `ImportError: attempted relative import with no known parent package` 错误。这是因为 Python 解释器在直接运行脚本时，无法确定它的“父包”。


## Demo

```bash
.
├── __init__.py
├── main.py
├── module_a
│   ├── __init__.py
│   ├── a.py
│   └── a_need_b.py
├── module_b
│   ├── __init__.py
│   ├── b.py
│   └── b_need_c.py
└── module_c
    ├── __init__.py
    └── c.py
```

```python
# main
from module_a import a, a_need_b
from module_b import b, b_need_c
from module_c import c

# module_a/a_need_b
from module_b import b_need_c
print("It is file a_need_b !")

# module_a/a
from .a_need_b import *
print("It is file a!")

# module_b/b_need_c
from module_c import c
print("C, i need you!")

# module_b/b: empty

# module_c/c:
print("It is the module C!")
```

{% note info %}
为什么 `from .a_need_b import *` 在 `python -m` 下能正常工作，但是正常运行就不可以？


当你运行像 `python -m your_package.your_module` 这样的命令时，Python 解释器会做几件关键的事情：

1.  **它将你的顶级包视为一个真正的包。** Python 会查找 `your_package` 在 `sys.path` 中的位置（通常是当前工作目录），并将其标记为**顶级包 (top-level package)**。
2.  **它设置了 `__package__` 和 `__name__` 属性。** 当一个模块通过 `import` 机制（包括 `python -m`）被加载时，Python 会为它设置 `__package__` 属性，这个属性指明了模块所属的包。这是进行相对导入的先决条件。
3.  **它确保了相对导入的上下文。** 有了正确的 `__package__` 上下文，当遇到 `from .a_need_b import *` 这样的相对导入时，Python 就能准确地知道 `.` 指的是当前模块所在的包，`..` 指的是当前包的父包，以此类推。


如果你在 `my_project` 目录下执行：

```bash
python -m module_a.a
```

这将发生以下情况：

1.  **设置 `sys.path`：** `my_project` (你的当前工作目录) 被添加到 `sys.path` 的最前面。
2.  **识别顶级包：** Python 知道它正在尝试运行 `module_a.a`。由于 `my_project` 在 `sys.path` 中，并且 `my_project/module_a/__init__.py` 存在，Python 就能够将 `my_project` 识别为一个**顶级包的上下文**，并且 `module_a` 是 `my_project` 下的一个子包。
3.  **执行 `module_a.a`：** 当 `a.py` 文件中的代码开始执行时：
      * Python 知道 `a.py` 的**完整模块名**是 `module_a.a`。
      * 因此，`a.py` 的 `__package__` 属性被设置为 `'module_a'`。
4.  **处理相对导入 `from .a_need_b import *`：**
      * `a.py` 内部的这个导入语句看到 `.`。
      * 因为 `a.py` 的 `__package__` 是 `module_a`，所以 `.` 被解析为 `'module_a'`。
      * 因此，`from .a_need_b import *` 实际上被解释为 `from module_a.a_need_b import *`。
      * 由于 `module_a.a_need_b` 在 `module_a` 包内是可用的，并且 `module_a` 已经被正确识别，所以导入成功。


如果你直接运行：

```bash
python my_project/module_a/a.py
```

会发生不同的事情：

1.  **设置 `sys.path`：** 此时，Python 会将 **`a.py` 所在的目录** (`my_project/module_a/`) 添加到 `sys.path` 的最前面。
2.  **不识别包上下文：** 当 `a.py` 被直接作为脚本执行时，Python 不会将其视为 `module_a` 包的一部分，更不会将其父目录 `my_project` 视为一个顶级包。
3.  **`__package__` 为 `None`：** 此时 `a.py` 的 `__package__` 属性会被设置为 `None` (或者根本不存在，取决于 Python 版本和加载方式)。
4.  **相对导入失败：** 当 Python 尝试解析 `from .a_need_b import *` 时，由于 `__package__` 是 `None`，它无法确定 `.` 到底指代什么包的上下文，因此会抛出 `ImportError: attempted relative import with no known parent package` (或者在你之前遇到的 `ImportError: attempted relative import beyond top-level package`，这取决于更复杂的包结构和导入链)。


`python -m` 的核心价值在于它明确地将你的程序作为一个**包或模块**来运行，从而为相对导入提供了必要的上下文 (`__package__` 属性)。这使得 Python 能够正确地解析诸如 `.` 和 `..` 这样的相对引用，从而在复杂的项目结构中实现模块间的顺畅通信。而直接运行一个文件，Python 倾向于将其视为一个独立的脚本，剥夺了它作为包内模块的身份，从而导致相对导入失败。

所以，当你的代码需要利用包的内部结构进行相对导入时，**`python -m` 通常是运行它的正确方式**。

{% endnote %}


## Conclusion

千言万语汇成一句话：**多用绝对路径**！！！