---
title: Profiling and Debugging
date: 2025-04-23 21:34:29
excerpt: Profiling and Debugging for Lecture Notes of Missing Semester, mainly focused on Python debugging.
index_img: /img/cover/profiling.jpg
categories:
  - Efficient Tools
  - Missing Semesters
tags:
  - Tutorial
  - Finished
  - Profiling
  - Debugging
  - Python
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

## Lec7: Debugging and Profiling

**Debugging** is of great importance! Mastering debugging in the coding process will significantly enhance your coding speed and time you will cost when your codes get an error.

> “The most effective debugging tool is still careful thought, coupled with judiciously placed print statements” — Brian Kernighan, *Unix for Beginners*.

This is the simplest way, somehow, an "effective" way to find your bugs in your code by using **print** functions to print all the significant variables and statements where you encountered your bug.

**It is simple, but sometimes naive**. We need to learn some advance techniques for better performance and efficiency. **So that is why we need to learn debugging!**

## Logging

Logging的作用就是在程序运行的时候输出一些提示信息，可以看做是**高级版的卫生检查大法**。

{% note primary %}

不过Logging最关键的作用是“**日志等级的划分**”，具体常见的等级划分可以分为 **DEBUG, INFO, WARNING, ERROR, FATAL**，一共五个等级。

- DEBUG（调试）
	- **用途**：开发/测试阶段使用，记录详细的运行流程或变量值。
	- **特点**：
	  - 最细粒度，通常在生产环境中关闭。
	  - 只有开发者需要关心。

```python
logger.debug(f"User input: {user_input}, Processing step 3/5")
```

- INFO（信息）

	- **用途**：记录程序正常运行的关键节点信息。

	- **特点**：
	  - 生产环境常用，用于跟踪系统状态。
	  - 运维或技术支持人员会关注。


```python
logger.info("User 'admin' logged in from IP 192.168.1.1")
```

- WARNING（警告）

	- **用途**：记录潜在问题，但当前未影响流程。

	- **特点**：
	  - 需要人工检查，但无需立即处理。
	  - 可能预示未来错误（如磁盘空间不足）。


```python
logger.warning("High memory usage: 90%, consider optimization")
```

- ERROR（错误）

	- **用途**：记录程序无法处理的异常或故障。

	- **特点**：
	  - 需要人工干预，但程序可能继续运行（如部分功能失效）。
	  - 影响单个请求或模块。


```python
logger.error("Database connection failed, retrying...")
```

- CRITICAL/FATAL（严重）

	- **用途**：系统级致命错误，可能导致服务终止。

	- **特点**：
	  - 必须立即处理（如崩溃、数据丢失）。
	  - 影响整个应用。


```python
logger.critical("Server out of disk space, shutting down")
```

{% endnote %}

### demo

我们来实现一个简单的日志模拟系统：

我们假设单次`simulation`中系统会生成一个随机数，接着判断这个随机数的性质来judge日志的等级。

```python
"""
Author: Xiyuan Yang   xiyuan_yang@outlook.com
Date: 2025-02-23 20:59:54
LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
LastEditTime: 2025-04-26 10:20:22
FilePath: /Lec6_Debugging/log_test.py
Description: The test for the logging in python
Do you code and make progress today?
Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved.
"""

# import logging package for python:
import logging
import random
import time
from datetime import datetime

# Getting time stamp
timestamp = datetime.now().strftime("%Y%m%d_%H-%M-%S")

# Configure logging
logging.basicConfig(
    level=logging.DEBUG,
    format="%(asctime)s - %(levelname)s - %(message)s",
    handlers=[
        logging.FileHandler(f"{timestamp}.log"),
    ],
)

# Create a StreamHandler for console output
console_handler = logging.StreamHandler()
console_handler.setLevel(logging.ERROR)
formatter = logging.Formatter("%(asctime)s - %(levelname)s - %(message)s")
console_handler.setFormatter(formatter)

# Add the console handler to the root logger
logging.getLogger().addHandler(console_handler)
logger = logging.getLogger(__name__)


def simulation(epoch=100, maxsize=1000000):
    """generate a random integer k from 1 to 10000,
    for every time:
        if k % 500 == 0, the log level is "ERROR"
        if k % 100 == 0, the log level is "WARNING"
        for any other stuf, the log level is "DEBUG" and print the value of k
        for several certain epochs, we will print log.INFO
    """
    random.seed(2025)

    # for info messages
    for i in range(epoch):
        time.sleep(0.01)
        if i == 0:
            logger.info("The simulation begins")
        elif i % 100 == 0:
            logger.info(f"Now is the {i} rounds of simulation")

        # generate random number
        num = random.randint(1, maxsize)

        # logging
        if num % 500 == 0:
            logger.error(
                f"Error!, the {i}th simulation's value is {num}, is a multiple of 500"
            )
        elif num % 100 == 0:
            logger.warning(
                f"Warning!, the {i}th simulation's value is {num}, is a multiple of 100"
            )
        else:
            logger.debug(f"The {i}th simulation's value is {num}")

        if i == epoch - 1:
            logger.info("The simulation ends")

if __name__ == "__main__":
    simulation()
```

程序最关键的部分在于Debugging的设置，可以作为模版，设置如下：

```python
# import logging package for python:
import logging
import random
import time
from datetime import datetime

# Getting time stamp
timestamp = datetime.now().strftime("%Y%m%d_%H-%M-%S")

# Configure logging
logging.basicConfig(
    level=logging.DEBUG,
    format="%(asctime)s - %(levelname)s - %(message)s",
    handlers=[
        logging.FileHandler(f"{timestamp}.log"),
    ],
)

# Create a StreamHandler for console output
console_handler = logging.StreamHandler()
console_handler.setLevel(logging.ERROR)
formatter = logging.Formatter("%(asctime)s - %(levelname)s - %(message)s")
console_handler.setFormatter(formatter)

# Add the console handler to the root logger
logging.getLogger().addHandler(console_handler)
logger = logging.getLogger(__name__)
```

首先设置Logging的`BasicConfig`，即从哪一个级别的日志开始记录，在这里是从最低等级的`DEBUG`开始记录。并设置时间格式，以及日志存放的位置。(**通常使用时间戳**)

这些日志会被写入日志文件中，如果我们希望在终端能够实时地检测到一些异常情况或者报错，可以设置`logging.StreamHandler()`来实现日志在terminal的实时打印。在这里我们只会打印`ERROR`级别以上的日志等级。

Then, you can output several log messages during your coding process. Like this:

```python
logging.info(f"Starting task: {self.name}")
logging.debug(f"Task added: {task.name} (Duration: {task.duration}s)")
logging.error(f"Task failed: {futures[future].name} with error: {e}")
```

{% note primary %}

**Logging 模块的内置级别**

Python 的 `logging` 模块内置了 **5 个标准日志级别**，按严重性从低到高排列如下：

| 级别名       | 整数值 | 用途说明                                                     |
| :----------- | :----- | :----------------------------------------------------------- |
| **DEBUG**    | 10     | 调试信息，记录详细的程序运行细节（如变量值、流程跟踪），通常只在开发时启用。 |
| **INFO**     | 20     | 一般信息，记录程序正常运行的关键事件（如用户登录、服务启动）。 |
| **WARNING**  | 30     | 警告信息，记录潜在问题（如磁盘空间不足），但程序仍能继续运行。 |
| **ERROR**    | 40     | 错误信息，记录程序某个功能失败（如数据库连接异常），需要人工干预。 |
| **CRITICAL** | 50     | 严重错误，记录导致系统崩溃或无法继续运行的致命问题（如内存耗尽）。 |

因此，在`logging`模块中，调用`print(logging.INFO)`会输出20。这也给了我们**自主设计日志等级的自由**，不过要注意不要引起冲突。

```python
# 添加 NOTICE 级别（介于 INFO 和 WARNING 之间）
NOTICE_LEVEL = 25
logging.addLevelName(NOTICE_LEVEL, "NOTICE")
```

{% endnote %}

## Debugger

Just simply output several logging messages isn't enough! For example, you can not **detect memory leak** with printing all variables and messages. Thus, we need more **specific tools to debugging**！

> 例如当你不断的遇到segmentation fault的时候。😅
>
> 不过我们这里将会主要聚焦于Python的Debugging和Profiling~

Many programming languages come with some form of debugger. In Python this is the Python Debugger [`pdb`](https://docs.python.org/3/library/pdb.html).

Here is a brief description of some of the commands `pdb` supports:

- **l**(ist) - Displays 11 lines around the current line or continue the previous listing.
- **s**(tep) - Execute the current line, stop at the first possible occasion.
- **n**(ext) - Continue execution until the next line in the current function is reached or it returns.
- **b**(reak) - Set a breakpoint (depending on the argument provided).
- **p**(rint) - Evaluate the expression in the current context and print its value. There’s also **pp** to display using [`pprint`](https://docs.python.org/3/library/pprint.html) instead.
- **r**(eturn) - Continue execution until the current function returns.
- **q**(uit) - Quit the debugger.

### demo

- 在代码内硬编码断点：

```python
"""
Author: Xiyuan Yang   xiyuan_yang@outlook.com
Date: 2025-04-26 12:55:51
LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
LastEditTime: 2025-04-26 12:55:59
FilePath: /Lec6_Debugging/debug_test.py
Description
Do you code and make progress today?
Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved.
"""

import pdb


def calculate_average(nums):
    total = 0
    for num in nums:
        total += num
    average = total / len(nums)
    return average


def main():
    numbers = [10, 20, 30, "40", 50]
    print("Calculating average...")

    pdb.set_trace()

    avg = calculate_average(numbers)
    print(f"Average is: {avg}")


if __name__ == "__main__":
    main()

```

这样在运行脚本的时候，当Python解释器遇到断点`pdb.set_trace()`的时候，就会自动终止程序运行，进入pdb的界面，你可以在这里查看相关变量的值。

- 使用命令行界面直接交互

在终端运行程序时添加 `-m pdb` 参数，程序会在 **第一行代码前自动暂停**，进入交互式调试模式：

```bash
python -m pdb your_script.py
```

相关命令：

| 命令       | 缩写 | 作用                                 |
| :--------- | :--- | :----------------------------------- |
| `list`     | `l`  | 显示当前代码上下文（前后共11行）     |
| `next`     | `n`  | 执行下一行（不进入函数内部）         |
| `step`     | `s`  | 进入函数内部调试                     |
| `continue` | `c`  | 继续执行直到下一个断点或程序结束     |
| `print`    | `p`  | 打印变量值（如 `p total`）           |
| `break`    | `b`  | 设置断点（如 `b 10` 在第10行设断点） |
| `where`    | `w`  | 显示当前调用栈（查看程序执行路径）   |
| `quit`     | `q`  | 强制退出调试                         |

- 使用vscode辅助debug（推荐）

![image](https://s1.imagehub.cc/images/2025/04/26/70b4d46d52428ae93c76fe58fb268cdb.png)

## Profiling

又是在运行程序的时候（尤其是Python程序），运行一次代码可能动辄几分钟甚至几小时，这会导致在复杂程序中的debug难度非常的大。因此，Python 的 **Profiling（性能分析）** 用于识别代码中的性能瓶颈，帮助你优化程序运行速度或内存占用。

我们以矩阵乘法为例，我们实现了手动for循环的矩阵乘法和`numpy`优化过后的矩阵乘法，来分析时间性能：

```python
import numpy as np
import random


def generate_matrix(n):
    """Generate an n x n random matrix with values between 0 and 1."""
    return [[random.random() for _ in range(n)] for _ in range(n)]


def matrix_multiply(a, b):
    """Matrix multiplication (pure Python, intentionally unoptimized)."""
    n = len(a)
    result = [[0.0 for _ in range(n)] for _ in range(n)]
    for i in range(n):
        for j in range(n):
            for k in range(n):
                result[i][j] += a[i][k] * b[k][j]
    return result


def matrix_multiply_np(a, b):
    """Matrix multiplication (optimized with NumPy)."""
    return np.dot(a, b)


def test_performance(n=100):
    """Test performance of matrix multiplication."""
    # Generate two n x n matrices
    a = generate_matrix(n)
    b = generate_matrix(n)

    # Pure Python implementation
    print(f"Testing Native Python (n={n})...")
    result_py = matrix_multiply(a, b)

    # NumPy implementation
    print(f"Testing NumPy (n={n})...")
    a_np, b_np = np.array(a), np.array(b)
    result_np = matrix_multiply_np(a_np, b_np)

    # Verify results (allow floating-point errors)
    assert np.allclose(result_py, result_np, atol=1e-6)


if __name__ == "__main__":
    test_performance(n=500) 

```

整个函数主要由4个函数组成：

- `generate_matrix()`生成两个随机矩阵
- `matrix_multiply()`实现手动的矩阵乘法
- ` matrix_multiply_np`实现numpy库中的矩阵乘法
- `np.allclose()`来检查结果的正确性

### Time profiling

#### `cprofile`

统计每个函数的调用次数、运行时间等，用来分析整体的时间性能。

```bash
python -m cProfile -s cumulative your_script.py
```

对上面的代码进行时间性能分析，结果如下

```
         598638 function calls (596148 primitive calls) in 17.913 seconds

   Ordered by: cumulative time

   ncalls  tottime  percall  cumtime  percall filename:lineno(function)
    116/1    0.001    0.000   17.913   17.913 {built-in method builtins.exec}
        1    0.005    0.005   17.913   17.913 profile_test.py:1(<module>)
        1    0.000    0.000   17.658   17.658 profile_test.py:26(test_performance)
        1   17.503   17.503   17.503   17.503 profile_test.py:10(matrix_multiply)
       14    0.001    0.000    0.540    0.039 __init__.py:1(<module>)
    152/1    0.001    0.000    0.250    0.250 <frozen importlib._bootstrap>:1349(_find_and_load)
    152/1    0.001    0.000    0.250    0.250 <frozen importlib._bootstrap>:1304(_find_and_load_unlocked)
    140/1    0.001    0.000    0.248    0.248 <frozen importlib._bootstrap>:911(_load_unlocked)
    114/1    0.000    0.000    0.248    0.248 <frozen importlib._bootstrap_external>:989(exec_module)
    372/2    0.000    0.000    0.246    0.123 <frozen importlib._bootstrap>:480(_call_with_frames_removed)
    373/9    0.000    0.000    0.234    0.026 {built-in method builtins.__import__}
   250/40    0.000    0.000    0.232    0.006 <frozen importlib._bootstrap>:1390(_handle_fromlist)
        1    0.000    0.000    0.117    0.117 __config__.py:1(<module>)
        2    0.059    0.029    0.096    0.048 profile_test.py:5(generate_matrix)
  140/136    0.000    0.000    0.084    0.001 <frozen importlib._bootstrap>:806(module_from_spec)
    25/24    0.000    0.000    0.081    0.003 <frozen importlib._bootstrap_external>:1287(create_module)
...
```

配合栈帧可以更好的检测到那个函数花了更多的时间。

#### `snakeviz`

使用内置的`cprofile`的交互感相对较差，尤其在使用包调用的时候可能动辄几千几万条函数调用，无法找到真正有意义的信息。因此，我们可以使用`snakeviz`为第三方工具来进行可视化分析。（需要pip install）	

> 在Linux环境中推荐使用FireFox浏览器，或者手动下载渲染。

![SnakeViz Demo](https://s1.imagehub.cc/images/2025/04/26/a9b2f19f015d4dcf843a945b6ad064eb.png)

#### `line_profiler`

```bash
pip install line_profiler
```

添加**装饰器**：

```python
"""
Author: Xiyuan Yang   xiyuan_yang@outlook.com
Date: 2025-04-26 13:10:02
LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
LastEditTime: 2025-04-26 21:50:32
FilePath: /Lec6_Debugging/profile_test.py
Description:
Do you code and make progress today?
Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved.
"""

import numpy as np
import random
# from memory_profiler import profile
from line_profiler import profile

def generate_matrix(n):
    """Generate an n x n random matrix with values between 0 and 1."""
    return [[random.random() for _ in range(n)] for _ in range(n)]


def matrix_multiply(a, b):
    """Matrix multiplication (pure Python, intentionally unoptimized)."""
    n = len(a)
    result = [[0.0 for _ in range(n)] for _ in range(n)]
    for i in range(n):
        for j in range(n):
            for k in range(n):
                result[i][j] += a[i][k] * b[k][j]
    return result


def matrix_multiply_np(a, b):
    """Matrix multiplication (optimized with NumPy)."""
    return np.dot(a, b)

@profile
def test_performance(n=100):
    """Test performance of matrix multiplication."""
    # Generate two n x n matrices
    a = generate_matrix(n)
    b = generate_matrix(n)

    # Pure Python implementation
    print(f"Testing Native Python (n={n})...")
    result_py = matrix_multiply(a, b)

    # NumPy implementation
    print(f"Testing NumPy (n={n})...")
    a_np, b_np = np.array(a), np.array(b)
    result_np = matrix_multiply_np(a_np, b_np)

    # Verify results (allow floating-point errors)
    assert np.allclose(result_py, result_np, atol=1e-6)


if __name__ == "__main__":
    test_performance(n=500)

```

使用如下命令：

```bash
kernprof -l -v profile_test.py 
```

输出：

```
Testing Native Python (n=500)...
Testing NumPy (n=500)...
Wrote profile results to profile_test.py.lprof
Timer unit: 1e-06 s

Total time: 24.949 s
File: profile_test.py
Function: test_performance at line 37

Line #      Hits         Time  Per Hit   % Time  Line Contents
==============================================================
    37                                           @profile
    38                                           def test_performance(n=100):
    39                                               """Test performance of matrix multiplication."""
    40                                               # Generate two n x n matrices
    41         1      33593.1  33593.1      0.1      a = generate_matrix(n)
    42         1      33500.2  33500.2      0.1      b = generate_matrix(n)
    43                                           
    44                                               # Pure Python implementation
    45         1         54.6     54.6      0.0      print(f"Testing Native Python (n={n})...")
    46         1   24843149.5    2e+07     99.6      result_py = matrix_multiply(a, b)
    47                                           
    48                                               # NumPy implementation
    49         1         80.5     80.5      0.0      print(f"Testing NumPy (n={n})...")
    50         1      14597.5  14597.5      0.1      a_np, b_np = np.array(a), np.array(b)
    51         1       7472.5   7472.5      0.0      result_np = matrix_multiply_np(a_np, b_np)
    52                                           
    53                                               # Verify results (allow floating-point errors)
    54         1      16569.4  16569.4      0.1      assert np.allclose(result_py, result_np, atol=1e-6)
```

### Memory Profiling

#### `memory_profiler`

在代码的对应函数上加上装饰器：

```python
import numpy as np
import random
from memory_profiler import profile

def generate_matrix(n):
    """Generate an n x n random matrix with values between 0 and 1."""
    return [[random.random() for _ in range(n)] for _ in range(n)]


def matrix_multiply(a, b):
    """Matrix multiplication (pure Python, intentionally unoptimized)."""
    n = len(a)
    result = [[0.0 for _ in range(n)] for _ in range(n)]
    for i in range(n):
        for j in range(n):
            for k in range(n):
                result[i][j] += a[i][k] * b[k][j]
    return result


def matrix_multiply_np(a, b):
    """Matrix multiplication (optimized with NumPy)."""
    return np.dot(a, b)


@profile
def test_performance(n=100):
    """Test performance of matrix multiplication."""
    # Generate two n x n matrices
    a = generate_matrix(n)
    b = generate_matrix(n)

    # Pure Python implementation
    print(f"Testing Native Python (n={n})...")
    result_py = matrix_multiply(a, b)

    # NumPy implementation
    print(f"Testing NumPy (n={n})...")
    a_np, b_np = np.array(a), np.array(b)
    result_np = matrix_multiply_np(a_np, b_np)

    # Verify results (allow floating-point errors)
    assert np.allclose(result_py, result_np, atol=1e-6)


if __name__ == "__main__":
    test_performance(n=100)

```

使用命令：

```bash
python -m memory_profiler matrix_test.py
```

> 注意，加入装饰器之后会**严重增加**程序的运行时间，建议把矩阵size改成100。

程序的输出：

```
Filename: profile_test.py

Line #    Mem usage    Increment  Occurrences   Line Contents
=============================================================
    26     66.4 MiB     66.4 MiB           1   @profile
    27                                         def test_performance(n=100):
    28                                             """Test performance of matrix multiplication."""
    29                                             # Generate two n x n matrices
    30     66.7 MiB      0.3 MiB           1       a = generate_matrix(n)
    31     67.2 MiB      0.5 MiB           1       b = generate_matrix(n)
    32                                         
    33                                             # Pure Python implementation
    34     67.2 MiB      0.0 MiB           1       print(f"Testing Native Python (n={n})...")
    35     67.5 MiB      0.3 MiB           1       result_py = matrix_multiply(a, b)
    36                                         
    37                                             # NumPy implementation
    38     67.5 MiB      0.0 MiB           1       print(f"Testing NumPy (n={n})...")
    39     67.7 MiB      0.3 MiB           1       a_np, b_np = np.array(a), np.array(b)
    40     93.9 MiB     26.2 MiB           1       result_np = matrix_multiply_np(a_np, b_np)
    41                                         
    42                                             # Verify results (allow floating-point errors)
    43     94.2 MiB      0.3 MiB           1       assert np.allclose(result_py, result_np, atol=1e-6)
```

### `py-spy`

使用`py-spy`可以监听特定进程下的内存使用。

```bash
 ps aux | grep python  
```

使用上面的命令可以得到PID进程号。例如如下输出：

```
root         377  0.0  0.2 107000 22580 ?        Ssl  21:33   0:00 /usr/bin/python3 /usr/share/unattended-upgrades/unattended-upgrade-shutdown --wait-for-signal
xiyuany+    1247  0.1  0.6 275508 51396 pts/3    Sl+  21:38   0:01 /home/xiyuanyang/anaconda3/bin/python /home/xiyuanyang/.vscode-server/extensions/ms-python.black-formatter-2025.2.0/bundled/tool/lsp_server.py --stdio
xiyuany+    1843  4.3  9.9 14501136 811436 pts/3 Sl+  21:39   0:55 /home/xiyuanyang/.vscode-server/bin/17baf841131aa23349f217ca7c570c76ee87b957/node /home/xiyuanyang/.vscode-server/extensions/ms-python.vscode-pylance-2025.4.1/dist/server.bundle.js --cancellationReceive=file:bcfc39616cdc7d84375b70192eaf5317398c5de188 --node-ipc --clientProcessId=1064
xiyuany+   11198  107  1.0 424384 88148 pts/10   Rl+  21:59   0:45 python -u /home/xiyuanyang/Command_Line/missing_semester/Lec6_Debugging/profile_test.py
xiyuany+   11606  0.0  0.0   4100  1928 pts/9    S+   22:00   0:00 grep --color=auto --exclude-dir=.bzr --exclude-dir=CVS --exclude-dir=.git --exclude-dir=.hg --exclude-dir=.svn --exclude-dir=.idea --exclude-dir=.tox python
```

很明显，我们需要查看的进程号是`11198`。

接下来，使用`py-spy`：

```bash
py-spy top --pid 11198
```

![py-spy](https://s1.imagehub.cc/images/2025/04/26/16ab2740b64f3c014c5897955da8479b.png)

当然，也可以生成可视化的图形：

```bash
 py-spy record -o profile.svg -d 10 --pid 11198
```

其中`profile.svg`是生成的svg图像，`-d 10`是限制的秒数，`--pid`是对应需要监听的进程号。

![Visualization](https://s1.imagehub.cc/images/2025/04/26/6bab3c70f995810e8a8495e537ec3ecb.png)

当然，你的火焰图也可以长这样：

![FlameGraph](http://www.brendangregg.com/FlameGraphs/cpu-bash-flamegraph.svg)
