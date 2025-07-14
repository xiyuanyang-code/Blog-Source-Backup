---
title: Python Threadings
date: 2025-05-01 22:58:27
index_img: /img/cover/gil.jpg
excerpt: This blog explains the basic concepts related to Python threads, including GIL, multithreading, multiprocessing, and asynchronous programming.
categories:
  - Code
  - Python
tags:
  - Tutorial
  - Finished
  - Python
  - GIL
  - Threadings
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Threads and Processes in Python

{% note primary %}

All codes for this tutorial are open-sourced here:

{% btn https://github.com/xiyuanyang-code/Threadings_tutorial, Codes, "Show you my code😘" %}

{% endnote %}

## Introduction

### 线程和进程

- **线程**（Threading）：操作系统进行计算和调度的最小单位，每一个线程都有对应的上下文。
- **进程**（Processing）：由若干个线程组成，这些线程可以享有这个进程的内存（**变量共享**）
	- **进程是程序的执行实例**，是操作系统分配资源（CPU、内存、文件句柄等）的基本单位。
	- 每个进程有 **独立的内存空间**，进程间数据隔离（默认不共享变量）。
	- 进程间通信（IPC）需要通过特殊机制（如管道、队列、共享内存等）。



多线程和多进程在现在计算任务中具有很重要的意义，可以很大的提升程序的运行效率。

- **多进程**：利用多核 CPU 并行计算（如机器学习训练）。
- **多线程**：在 I/O 等待时切换任务（如同时下载多个文件）。

- **GUI 应用**：主线程负责界面渲染，子线程处理耗时任务（避免界面卡顿）。
- **Web 服务器**：同时处理多个客户端请求。

### Race Condition

但是，线程在提升效率的同时牺牲了**安全性**，我们来看下面的程序示例：

```python
import threading
import time

# Shared variables for threadings
balance = 100


def withdraw(amount):
    global balance
    if balance >= amount:
        time.sleep(1)
        print(f"Getting money..., the amount is {amount}")
        balance -= amount
        print(f"Get {amount}, {balance} remaining...")
    else:
        print("Error! You don't have enough money!")


def test_thread_safe():
    """test for race condition, safe mode for entering in queue"""
    print("If they take turns to withdraw money at the counter in order.")
    t1 = threading.Thread(target=withdraw, args=(80,))
    t2 = threading.Thread(target=withdraw, args=(70,))
    t3 = threading.Thread(target=withdraw, args=(50,))
    t4 = threading.Thread(target=withdraw, args=(20,))

    # Starting multi-sessions, in queue
    t1.start()
    t1.join()
    t2.start()
    t2.join()
    t3.start()
    t3.join()
    t4.start()
    t4.join()


def test_thread_unsafe():
    """test for race condition, unsafe mode for multithreads"""
    print("If the four of them withdraw money at the counter simultaneously...")
    t1 = threading.Thread(target=withdraw, args=(80,))
    t2 = threading.Thread(target=withdraw, args=(70,))
    t3 = threading.Thread(target=withdraw, args=(50,))
    t4 = threading.Thread(target=withdraw, args=(20,))

    # Starting multi-sessions
    t1.start()
    t2.start()
    t3.start()
    t4.start()
    t1.join()
    t2.join()
    t3.join()
    t4.join()


if __name__ == "__main__":
    # test for safe single-thread
    test_thread_safe()

    # reset balance
    balance = 100

    # test for race condition: multithreads
    test_thread_unsafe()

```

程序实现了一个简单的柜台模拟器，模拟了四个用户**以不同的顺序**对相同账户取钱的结果。

```
If they take turns to withdraw money at the counter in order.
Getting money..., the amount is 80
Get 80, 20 remaining...
Error! You don't have enough money!
Error! You don't have enough money!
Getting money..., the amount is 20
Get 20, 0 remaining...
If the four of them withdraw money at the counter simultaneously...
Getting money..., the amount is 80Getting money..., the amount is 70
Get 80, 20 remaining...

Get 70, -50 remaining...
Getting money..., the amount is 50
Get 50, -100 remaining...
Getting money..., the amount is 20
Get 20, -120 remaining...
```

一看输出就发现了问题，在`unsafe_for_multithreads`中，钱出现了负值的情况，而并没有像第一种情况一样输出error。

#### Explanation

出错的核心逻辑在于**在同一进程的若干个线程中**，他们可以共享变量并且对这一个变量做修改，这会导致代码的逻辑错误。

例如上面的取钱逻辑其实就是一个`while`循环，如果四个用户一个个按顺序取钱（**第二位用户不会在第一位用户取钱操作未完成的时候开始自己的取钱操作**），很显然`balance`的值会被正确的更新，all is well。但是对于第二种情况，四个线程几乎同时启动（第四个线程启动的时间早于第一个线程结束的时间），这就会导致此时`balance -= amount`的核心逻辑并未执行，四个while循环全部判断为True，导致超出限额的扣款。

> 这会带来很多的问题，在更复杂的函数逻辑中对变量的修改更加千变万化，出错的严重程度更高。

### Lock

这个问题的解决方式是对程序**加锁（Lock）**，即强制**让只有一个线程可以运行这段程序**，而不允许同时运行的操作。

```python
import threading
import time

# Shared variables for threadings
balance = 100

# lock object
lock = threading.Lock()


def withdraw_with_locked(amount):
    global balance
    with lock:
        if balance >= amount:
            time.sleep(1)
            print(f"Getting money..., the amount is {amount}")
            balance -= amount
            print(f"Get {amount}, {balance} remaining...")
        else:
            print("Error! You don't have enough money!")


def test_thread_unsafe_with_locked():
    """test for race condition, unsafe mode for multithreads, but with lock"""
    print("If the four of them withdraw money at the counter simultaneously..., but with lock😋!")
    t1 = threading.Thread(target=withdraw_with_locked, args=(80,))
    t2 = threading.Thread(target=withdraw_with_locked, args=(70,))
    t3 = threading.Thread(target=withdraw_with_locked, args=(50,))
    t4 = threading.Thread(target=withdraw_with_locked, args=(20,))

    # Starting multi-sessions
    t1.start()
    t2.start()
    t3.start()
    t4.start()
    t1.join()
    t2.join()
    t3.join()
    t4.join()

```

程序的输出显示取钱的逻辑正常！

```
If the four of them withdraw money at the counter simultaneously..., but with lock😋!
Getting money..., the amount is 80
Get 80, 20 remaining...
Error! You don't have enough money!
Error! You don't have enough money!
Getting money..., the amount is 20
Get 20, 0 remaining...
```

## Lock Again...

锁最原始的设计是非常简单粗暴的，他通过线程之间的**对变量的读写权限**来保证程序逻辑的正常运行，但是锁本身的设计也会带来更多的问题，下面简单介绍**死锁**，这一个非常经典的锁问题。

### Dead Lock

死锁是指 **多个线程/进程因互相等待对方释放资源而无限阻塞** 的情况。例如两个线程各自持有锁A和锁B，同时试图获取对方的锁。

#### 死锁的四个必要条件（Coffman条件）

1. **互斥条件**：资源一次只能被一个线程占用。
2. **占有并等待**：线程持有至少一个资源，并等待获取其他资源。
3. **非抢占条件**：已分配的资源不能被强制剥夺。
4. **循环等待**：多个线程形成环形等待链（如T1等T2，T2等T1）。

```python
import threading

lock_a = threading.Lock()
lock_b = threading.Lock()


def thread_1():
    with lock_a:
        print("Thread 1 acquired lock A")
        with lock_b:
            print("Thread 1 acquired lock B")

    print("Finished!")


def thread_2():
    with lock_b:
        print("Thread 2 acquired lock B")
        with lock_a:
            print("Thread 2 acquired lock A")

    print("Finished!")


t1 = threading.Thread(target=thread_1)
t2 = threading.Thread(target=thread_2)
t1.start()
t2.start()
t1.join()
t2.join()

```

程序在输出`Thread 1 acquired lock AThread 2 acquired lock B`之后就不再输出任何的内容，出现了卡死的情况。

一种比较普遍的解决办法就是**设置timeout**，设置等待的最大时间。（这只是保证程序不会一直卡死，并没有解决实际问题）

```python
def thread_1_timeout():
    if lock_a.acquire(timeout=2):
        print("Thread 1 acquired lock A")
        try:
            if lock_b.acquire(timeout=2):
                print("Thread 1 acquired lock B")
                print("Thread 1 finished!")
            else:
                print("Thread 1 could not acquire lock B, releasing lock A")
        finally:
            lock_a.release()
    else:
        print("Thread 1 could not acquire lock A")


def thread_2_timeout():
    if lock_b.acquire(timeout=2):
        print("Thread 2 acquired lock B")
        try:
            if lock_a.acquire(timeout=2):
                print("Thread 2 acquired lock A")
                print("Thread 2 finished!")
            else:
                print("Thread 2 could not acquire lock A, releasing lock B")
        finally:
            lock_b.release()
    else:
        print("Thread 2 could not acquire lock B")


t1 = threading.Thread(target=thread_1_timeout)
t2 = threading.Thread(target=thread_2_timeout)
t1.start()
t2.start()
t1.join()
t2.join()
```

```
Thread 1 acquired lock AThread 2 acquired lock B

Thread 2 could not acquire lock A, releasing lock B
Thread 1 acquired lock B
Thread 1 finished!
```

我们发现，`Thread 2 could not acquire lock A, releasing lock B`这里超时后解锁，然后这个死锁问题就被解开了。

## GIL (Global Interpreter Lock)

在现代C++中，智能指针使用`Reference count`来实现引用计数，进而自动化回收内存，但是在多线程（无锁）的情况下，很容易出现Race Condition的问题。

因此，GIL（Global Interpreter Lock）是 **CPython 解释器**（Python 官方实现）中的一个核心机制，其本质是一个 **互斥锁（Mutex）**，用于保证同一时刻只有一个线程可以执行 Python 字节码。它的设计初衷是 **简化 CPython 的内存管理**，尤其是 **引用计数的线程安全**。

### Impact of GIL

没有 GIL，Python 需要为所有数据结构（如列表、字典）实现细粒度锁，极大增加代码复杂度。而GIL非常完美的unify了这一点。同时，GIL减少了锁开销，使其在单线程中获得更好的时间性能。

但是GIL也有很大的限制，他会导致**多核CPU的CPU密集型任务**时间下降，因为GIL需要保证只有一个进程在进行Python字节码！

```python
import threading
import time

def count_down(n):
    while n > 0:
        n -= 1

# Single-threaded execution
start_time = time.time()  # Start timing
count_down(500_000_000) 
end_time = time.time()
print(f"Single-threaded execution time: {end_time - start_time:.2f} seconds")

# Multi-threaded execution (theoretically faster, but actually slower due to GIL)
start_time = time.time()  # Start timing
t1 = threading.Thread(target=count_down, args=(250_000_000,))
t2 = threading.Thread(target=count_down, args=(250_000_000,))
t1.start()
t2.start()
t1.join()
t2.join()
end_time = time.time()
print(f"Multi-threaded execution time: {end_time - start_time:.2f} seconds (due to GIL overhead)")
```

例如上文的程序模拟CPU密集型任务，由于GIL强制保证**只有一个线程可以执行Python字节码**，这会导致**伪多线程**的出现：

```
Single-threaded execution time: 3.81 seconds
Multi-threaded execution time: 3.98 seconds (due to GIL overhead)
```

> Python 3.12 对 GIL 进行了重大改进（[PEP 703](https://peps.python.org/pep-0703/)），**允许更细粒度的线程切换**，尤其是在纯 CPU 循环中。
>
> - **旧版本（如 Python 3.11）**：GIL 导致多线程在 CPU 密集型任务中更慢。
> - **新版本（Python 3.12+）**：GIL 的切换策略优化，使得简单循环（如 `n -= 1`）可能表现出伪并行加速。
>
> 测试上文代码务必下调Python版本。

但是，GIL只对Python字节码做了约束，**在面对IO密集型**的任务时多线程可以实现很大程度的加速。

```python
import threading
import time


def cpu_task(n: int):
    """simulation of CPU intensive task

    Args:
        n (int): the total count steps
    """
    while n > 0:
        n -= 1
    print("CPU task ends")


def io_task():
    """simulation of IO-intensive task"""
    time.sleep(1)


def test_task_cpu(task_function):
    """test for GIL in CPU-intensive task"""
    print("Test for CPU-intensive task:")
    print("Test for single threaded:")
    start_time = time.time()
    task_function(500_000_000)
    end_time = time.time()
    print(f"Single-threaded execution time: {end_time - start_time:.2f} seconds")

    print("Test for multi threaded:")
    start_time = time.time()
    t1 = threading.Thread(target=task_function, args=(250_000_000,))
    t2 = threading.Thread(target=task_function, args=(250_000_000,))
    t1.start()
    t2.start()
    t1.join()
    t2.join()
    end_time = time.time()
    print(f"Multi-threaded execution time: {end_time - start_time:.2f} seconds")


def test_task_io(task_function):
    print("\nTest for Io-intensive task:")
    # single threads:
    start_time = time.time()
    for _ in range(10):
        io_task()
    end_time = time.time()
    print(f"Single-threaded execution time: {end_time - start_time:.2f} seconds")

    threads = [threading.Thread(target=io_task) for _ in range(10)]
    start_time = time.time()
    for t in threads:
        t.start()
    for t in threads:
        t.join()
    end_time = time.time()
    print(f"Multi-threaded execution time: {end_time - start_time:.2f} seconds")


if __name__ == "__main__":
    test_task_cpu(cpu_task)
    test_task_io(io_task)

```

```
Test for CPU-intensive task:
Test for single threaded:
CPU task ends
Single-threaded execution time: 20.48 seconds
Test for multi threaded:
CPU task ends
CPU task ends
Multi-threaded execution time: 20.52 seconds

Test for Io-intensive task:
Single-threaded execution time: 10.01 seconds
Multi-threaded execution time: 1.01 seconds
```

{% note primary %}

### GIL是万能的吗？

GIL作为全局锁，并不是所有操作都可以被保护！例如上面的银行取款操作就不会受到GIL的保护，而需要**显式同步锁**才可以实现线程安全。

GIL的设计初衷就是**保证引用计数的安全性**，因此可以保护以下内容：

1. **Python字节码执行的原子性**：
	- 单个Python字节码指令的执行是原子的（不会被其他线程打断）
	- 例如：`x = 1`这样的简单赋值操作是原子的
2. **内置类型的简单操作**：
	- 对简单数据类型（如整数、浮点数）的单个操作通常是原子的
	- 例如：`x += 1`（如果x是整数）在Python中是原子的
3. **解释器内部状态**：
	- 保护**CPython解释器内部数据结构**不被多线程破坏
	- 确保引用计数等机制在多线程环境下的安全

GIL**不能保护的内容**

1. **复合操作**：
	- 需要多个步骤/字节码的操作（如检查后修改）
	- 例如：`if x > 0: x -= 1`（检查+修改不是原子的）
2. **I/O操作期间**：
	- 执行I/O操作（如文件读写、网络请求、time.sleep）时会释放GIL
	- 其他线程可以在I/O操作期间执行
3. **扩展模块中的操作**：
	- C扩展模块可以显式释放GIL
	- NumPy等科学计算库经常这样做以提高性能
4. **应用程序级别的数据一致性**：
	- 不保证应用程序逻辑的正确性
	- 开发者仍需对共享数据的复合操作使用锁

{% endnote %}

## Multi-Processing

### Introduction

由于GIL的限制，Python的多线程一直被诟病成**伪多线程**，因为他并没有用到多核CPU的加速能力。多线程由于GIL被迫退化为阻塞的单线程，导致时间非常的慢。

一种常用的解决方案就是使用**多进程**！多进程可以绕过这个限制，因为每个进程有自己的Python解释器和内存空间，因此也有自己的GIL。

```Python
import multiprocessing
import os


def worker(num):
    print(f"Worker {num} running in process {os.getpid()}")
    return


if __name__ == "__main__":
    processes = []
    for i in range(5):
        p = multiprocessing.Process(target=worker, args=(i,))
        processes.append(p)
        p.start()

    for p in processes:
        p.join()
    print("All processes finished")

```

```
Worker 0 running in process 8140
Worker 1 running in process 8141
Worker 2 running in process 8142
Worker 3 running in process 8143
Worker 4 running in process 8144
All processes finished
```

我们可以使用多进程来改写上面的CPU密集型任务，充分发挥多核CPU的优势。

```Python
def multiprocess_cpu_task():
    print("Using multi-processing")
    start_time = time.time()
    processes = []
    for i in range(5):
        p = multiprocessing.Process(target=cpu_task, args=(100_000_000,))
        processes.append(p)
        p.start()

    for p in processes:
        p.join()
    end_time = time.time()
    print(f"All processes finished, time: {end_time - start_time:.2f}")

```

```
Test for CPU-intensive task:
Test for single threaded:
CPU task ends
Single-threaded execution time: 23.63 seconds
Test for multi threaded:
CPU task ends
CPU task ends
Multi-threaded execution time: 21.67 seconds
Using multi-processing
CPU task ends
CPU task ends
CPU task ends
CPU task ends
CPU task ends
All processes finished, time: 4.44
```

> 使用多进程就可以绕开锁，实现多核CPU的加速！

### Advanced

#### 进程池

在上文中，我们使用一个列表的`append`来实现多进程的管理，我们也可以使用**进程池**来实现这一部分更加高效的实现。

```Python
from multiprocessing import Pool

def worker(x):
    return x * x

if __name__ == '__main__':
    with Pool(processes=4) as pool: 
        # 同步执行
        result = pool.apply(worker, (10,))
        print(result)  # 输出: 100
        
        # 异步执行
        result = pool.apply_async(worker, (20,))
        print(result.get())  # 输出: 400
        
        # map方法
        results = pool.map(worker, range(10))
        print(results)  # 输出: [0, 1, 4, 9, 16, 25, 36, 49, 64, 81]
        
        # imap方法(惰性求值)
        for res in pool.imap(worker, range(5)):
            print(res)  # 依次输出: 0, 1, 4, 9, 16
```

- 同步执行 和 异步执行 见下文
- map方法表示对`range(10)`的每一个值调用函数，最后返回一个列表
- `imap`是**惰性求值**，最后会迭代输出每一个答案（时间更慢，但内存效率更高）

#### 进程间通信(IPC)

进程之间的GIL，内存等资源都是互相独立的，但是多进程之间也可以进行通信。

##### Queue

使用队列的push和pop可以实现进程间通信：

```Python
from multiprocessing import Process, Queue
import os


def worker(q):
    print(f"PID2: {os.getpid()}")
    q.put("Hello from child process")
    print(f"Message sent from subprocess: {os.getpid()}")


if __name__ == "__main__":
    q = Queue()
    p = Process(target=worker, args=(q,))
    print(f"PID1: {os.getpid()}")
    p.start()
    print(f"Message received to {os.getpid()}: {q.get()}")
    p.join()

```

##### Pipe

在CLI中非常好用，在本质上**也是一种多进程通信**的手段。

```python
from multiprocessing import Process, Queue, Pipe
import multiprocessing
import multiprocessing.connection
import os


def worker_queue(q: Queue):
    print(f"PID2: {os.getpid()}")
    q.put("Hello from child process")
    print(f"Message sent from subprocess: {os.getpid()}")


def worker_pipe(conn: multiprocessing.connection.Connection):
    print(f"PID2: {os.getpid()}")
    conn.send("Message from child")
    print(f"Message sent from subprocess: {os.getpid()}")
    conn.close()


def test_pipe():
    parent_conn, child_conn = Pipe()
    print(f"PID1: {os.getpid()}")
    p = Process(target=worker_pipe, args=(child_conn,))
    p.start()
    print(f"Message received to {os.getpid()}: {parent_conn.recv()}")
    p.join()


def test_queue():
    q = Queue()
    p = Process(target=worker_queue, args=(q,))
    print(f"PID1: {os.getpid()}")
    p.start()
    print(f"Message received to {os.getpid()}: {q.get()}")
    p.join()


if __name__ == "__main__":
    test_pipe()
    test_queue()

```

```
PID1: 14515
PID2: 14516
Message sent from subprocess: 14516
Message received to 14515: Message from child
PID1: 14515
PID2: 14517
Message sent from subprocess: 14517
Message received to 14515: Hello from child process
```

也可以使用：

- **Value/Array**: 用于共享基本数据类型
- **Manager**: 可以创建共享的复杂数据结构(如字典、列表)

来实现内存内容的共享（对于更加复杂的数据结构），这里不详细展开。

## `asyncio`

异步编程是一种 **非阻塞式并发模型**，核心思想是 **在等待I/O操作（如网络请求、文件读写）时释放CPU资源**，让程序可以同时处理其他任务，从而提升效率。它是现代高并发程序（如Web服务器、爬虫）的核心技术之一。

> 异步适合于**IO密集型任务**而非**CPU密集型任务**。

### 同步和异步编程

面对IO密集型任务，程序需要耗费很多时间进行IO操作的等待，例如等待用户的输入或者文件读写，获取请求等等，这成为了程序运行的时间瓶颈。在同步编程中，**每个操作必须完成后才能执行下一个操作**。**异步编程**是一种非阻塞的编程范式，它允许程序在等待I/O操作（如网络请求、文件读写等）完成时继续执行其他任务，而不是干等着。

### 核心概念

1. **事件循环 (Event Loop)**:
	- 异步编程的核心，负责调度和执行协程
	- 不断检查哪些协程可以继续执行
	- 处理I/O事件和回调
2. **协程 (Coroutine)**:
	- 可暂停和恢复的函数（就是**需要IO密集等待的函数**）
3. **Future/Task**:
	- Future表示异步操作的最终结果
	- Task是Future的子类，用于包装和管理协程的执行

### 工作原理

1. 程序启动**事件循环**
2. 将协程注册为任务加入事件循环
3. 遇到I/O操作时，**协程暂停并释放控制权**
4. 事件循环执行其他可运行的任务
5. I/O操作完成后，事件循环恢复暂停的协程
6. 重复上述过程直到所有任务完成

### Code

#### Example1: Fetching Urls.

> Talk is cheap! Just show you my code.

我们这里模拟客户端的行为，使用`requests`库来进行对三个网站的访问，统计**非并发（同步编程）**和**异步编程（并发）**的速度差异。同时，我们可以使用`aiohttp`来实现异步的http请求。

```python
import time
import requests
import asyncio
import aiohttp

urls = [
    "https://github.com",
    "https://sjtu.edu.cn",
    "https://acm.sjtu.edu.cn/OnlineJudge/",
    "https://chat.deepseek.com/",
    "https://www.bilibili.com/",
    "https://stackoverflow.com/questions"
]


def fetch_url_simple(url):
    try:
        response = requests.get(url, timeout=10)  # 设置超时时间为 10 秒
        return response.text
    except requests.exceptions.Timeout:
        print(f"Timeout occurred while fetching {url}")
        return ""
    except requests.exceptions.ConnectionError as e:
        print(f"Connection error occurred: {e}")
        return ""
    except requests.exceptions.RequestException as e:
        print(f"An error occurred: {e}")
        return ""


def test_for_simple():
    # Using the for-loop
    for url in urls:
        content = fetch_url_simple(url)
        print(f"{url} returns {len(content)} bytes")


async def fetch_url(url):
    timeout = aiohttp.ClientTimeout(total=10)  # 设置超时时间为 10 秒
    try:
        async with aiohttp.ClientSession(timeout=timeout) as session:
            async with session.get(url) as response:
                return await response.text()
    except asyncio.TimeoutError:
        print(f"Timeout occurred while fetching {url}")
        return ""


async def test_for_concurrency():
    """: The test for fetching urls: test for concurrency"""
    # Task list
    tasks = [fetch_url(url) for url in urls]

    # For all tasks: Concurrency
    results = await asyncio.gather(*tasks)

    for url, content in zip(urls, results):
        print(f"{url} returns {len(content)} bytes")


if __name__ == "__main__":
    start_time = time.time()
    asyncio.run(test_for_concurrency())
    end_time = time.time()
    print(f"Total time for concurrency: {end_time - start_time:.2f} seconds")

    start_time = time.time()
    test_for_simple()
    end_time = time.time()
    print(f"Total time for no concurrency: {end_time - start_time:.2f} seconds")

```

> 这里只是最简单的网站请求模拟，很多网站**会对高并发的网络请求行为**做出限制（比如github pages，甚至不可以ping😅），可以选择一些比较大的网站。

输出：

```
https://github.com returns 282454 bytes
https://sjtu.edu.cn returns 69499 bytes
https://acm.sjtu.edu.cn/OnlineJudge/ returns 6467 bytes
https://chat.deepseek.com/ returns 2024 bytes
https://www.bilibili.com/ returns 1824 bytes
https://stackoverflow.com/questions returns 6994 bytes
Total time for concurrency: 0.62 seconds
https://github.com returns 282453 bytes
https://sjtu.edu.cn returns 69499 bytes
https://acm.sjtu.edu.cn/OnlineJudge/ returns 6467 bytes
https://chat.deepseek.com/ returns 2023 bytes
https://www.bilibili.com/ returns 1886 bytes
https://stackoverflow.com/questions returns 6994 bytes
Total time for no concurrency: 2.71 seconds
```

#### Example2: Simulation of User I/O

```python
import time
import asyncio
import random


def user_io_simulation():
    """Use time.sleep to simulate the wait for user input"""
    time.sleep(2)
    return random.randint(1, 5)


def simulation():
    total = 100
    while total > 0:
        total -= 1
        if total % 20 == 0:
            input = user_io_simulation()
            print(f"User enters the input {input}")
            total -= input
        print(f"Now the total value is {total}")
    print("Done!")

async def user_io_simulation_2():
    """Simulation with async

    Returns:
        int: The user input
    """
    await asyncio.sleep(2)
    return random.randint(1, 5)


async def simulation_concurrency():
    total = 100
    tasks = []  # List to store tasks
    while total > 0:
        total -= 1
        if total % 20 == 0:
            # Create a task for user input simulation
            task = asyncio.create_task(user_io_simulation_2())
            tasks.append(task)
        print(f"Now the total value is: {total}")

    # Wait for all tasks to complete
    results = await asyncio.gather(*tasks)
    for user_input in results:
        print(f"User enters the input: {user_input}")
        total -= user_input

    print("Done!")
    return 0


async def main():
    await simulation_concurrency()


if __name__ == "__main__":
    print("Testing simulation for no concurrency")
    start_time = time.time()
    simulation()
    end_time = time.time()
    print(f"Time cost: {end_time - start_time:.2f} seconds")

    print("Testing simulation for concurrency")
    start_time = time.time()
    asyncio.run(main())
    end_time = time.time()
    print(f"Time cost: {end_time - start_time:.2f} seconds")
```

程序的输出：

```
Testing simulation for no concurrency
Now the total value is 99
Now the total value is 98
Now the total value is 97
Now the total value is 96
Now the total value is 95
Now the total value is 94
Now the total value is 93
Now the total value is 92
Now the total value is 91
Now the total value is 90
Now the total value is 89
Now the total value is 88
Now the total value is 87
Now the total value is 86
Now the total value is 85
Now the total value is 84
Now the total value is 83
Now the total value is 82
Now the total value is 81
User enters the input 1
Now the total value is 79
Now the total value is 78
Now the total value is 77
Now the total value is 76
Now the total value is 75
Now the total value is 74
Now the total value is 73
Now the total value is 72
Now the total value is 71
Now the total value is 70
Now the total value is 69
Now the total value is 68
Now the total value is 67
Now the total value is 66
Now the total value is 65
Now the total value is 64
Now the total value is 63
Now the total value is 62
Now the total value is 61
User enters the input 3
Now the total value is 57
Now the total value is 56
Now the total value is 55
Now the total value is 54
Now the total value is 53
Now the total value is 52
Now the total value is 51
Now the total value is 50
Now the total value is 49
Now the total value is 48
Now the total value is 47
Now the total value is 46
Now the total value is 45
Now the total value is 44
Now the total value is 43
Now the total value is 42
Now the total value is 41
User enters the input 4
Now the total value is 36
Now the total value is 35
Now the total value is 34
Now the total value is 33
Now the total value is 32
Now the total value is 31
Now the total value is 30
Now the total value is 29
Now the total value is 28
Now the total value is 27
Now the total value is 26
Now the total value is 25
Now the total value is 24
Now the total value is 23
Now the total value is 22
Now the total value is 21
User enters the input 4
Now the total value is 16
Now the total value is 15
Now the total value is 14
Now the total value is 13
Now the total value is 12
Now the total value is 11
Now the total value is 10
Now the total value is 9
Now the total value is 8
Now the total value is 7
Now the total value is 6
Now the total value is 5
Now the total value is 4
Now the total value is 3
Now the total value is 2
Now the total value is 1
User enters the input 2
Now the total value is -2
Done!
Time cost: 10.76 seconds
Testing simulation for concurrency
Now the total value is: 99
Now the total value is: 98
Now the total value is: 97
Now the total value is: 96
Now the total value is: 95
Now the total value is: 94
Now the total value is: 93
Now the total value is: 92
Now the total value is: 91
Now the total value is: 90
Now the total value is: 89
Now the total value is: 88
Now the total value is: 87
Now the total value is: 86
Now the total value is: 85
Now the total value is: 84
Now the total value is: 83
Now the total value is: 82
Now the total value is: 81
Now the total value is: 80
Now the total value is: 79
Now the total value is: 78
Now the total value is: 77
Now the total value is: 76
Now the total value is: 75
Now the total value is: 74
Now the total value is: 73
Now the total value is: 72
Now the total value is: 71
Now the total value is: 70
Now the total value is: 69
Now the total value is: 68
Now the total value is: 67
Now the total value is: 66
Now the total value is: 65
Now the total value is: 64
Now the total value is: 63
Now the total value is: 62
Now the total value is: 61
Now the total value is: 60
Now the total value is: 59
Now the total value is: 58
Now the total value is: 57
Now the total value is: 56
Now the total value is: 55
Now the total value is: 54
Now the total value is: 53
Now the total value is: 52
Now the total value is: 51
Now the total value is: 50
Now the total value is: 49
Now the total value is: 48
Now the total value is: 47
Now the total value is: 46
Now the total value is: 45
Now the total value is: 44
Now the total value is: 43
Now the total value is: 42
Now the total value is: 41
Now the total value is: 40
Now the total value is: 39
Now the total value is: 38
Now the total value is: 37
Now the total value is: 36
Now the total value is: 35
Now the total value is: 34
Now the total value is: 33
Now the total value is: 32
Now the total value is: 31
Now the total value is: 30
Now the total value is: 29
Now the total value is: 28
Now the total value is: 27
Now the total value is: 26
Now the total value is: 25
Now the total value is: 24
Now the total value is: 23
Now the total value is: 22
Now the total value is: 21
Now the total value is: 20
Now the total value is: 19
Now the total value is: 18
Now the total value is: 17
Now the total value is: 16
Now the total value is: 15
Now the total value is: 14
Now the total value is: 13
Now the total value is: 12
Now the total value is: 11
Now the total value is: 10
Now the total value is: 9
Now the total value is: 8
Now the total value is: 7
Now the total value is: 6
Now the total value is: 5
Now the total value is: 4
Now the total value is: 3
Now the total value is: 2
Now the total value is: 1
Now the total value is: 0
User enters the input: 2
User enters the input: 3
User enters the input: 4
User enters the input: 3
User enters the input: 1
Done!
Time cost: 2.01 seconds
```

在**异步**的版本中，用户的输出（阻塞的IO操作）被加入了一个task列表中，在输出中也可以看见**用户的输出后于其他程序逻辑执行**，因此盲目的使用**异步编程**也可能会导致代码逻辑的错乱，必须要谨慎使用。
