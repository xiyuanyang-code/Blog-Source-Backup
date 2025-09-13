---
title: My Memo
date: 2025-04-25 20:36:27
index_img: /img/cover/memo2.jpg
excerpt: All kinds of stuffs... Mostly for the lab and AI usage.
tags:
  - Memo
  - Updating
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# My Memo

All kinds of stuffs... Mostly for the lab and AI usage.

## Table of Contents

- [Hugging Face 下载数据集](#Hugging-Face-数据集下载)
- [Markdown 文本内设置超链接](#Markdown-文本内设置超链接)
- [日志书写](#日志书写)
- [Tmux 使用备忘录](#Tmux-使用备忘录)
- [Python Tutorial](https://xiyuanyang-code.github.io/posts/Python-cheatsheet/)
- [Conda Quick Setup](#Conda-Quick-Setup)

## Hugging Face 数据集下载

{% note primary %}

**相关网站的集合**：

- [原始网站](https://huggingface.co/)
- [镜像站](https://hf-mirror.com/)
- [超级详细的教程](https://zhuanlan.zhihu.com/p/663712983)

{% endnote %}

### 环境变量的配置

环境声明：**本地环境和服务器环境**均测试通过。

- 服务器环境

```
Distributor ID: Ubuntu
Description:    Ubuntu 20.04.6 LTS
Release:        20.04
Codename:       focal
```

- 本地WSL环境

```
Distributor ID: Ubuntu
Description:    Ubuntu 24.04.2 LTS
Release:        24.04
Codename:       noble
```

在`.bashrc`或者`.zshrc`中加入如下export：

```bash
export HUGGINGFACE_HUB_URL="https://mirrors.tuna.tsinghua.edu.cn/hugging-face-hub"
export HF_DATASETS_URL="https://mirrors.tuna.tsinghua.edu.cn/hugging-face-datasets"
export HF_METRICS_URL="https://mirrors.tuna.tsinghua.edu.cn/hugging-face-metrics"
export HF_HOME="/GPFS/rhome/xiyuanyang/HuggingFace/HF_HOME"
export TRANSFORMERS_CACHE="/GPFS/rhome/xiyuanyang/HuggingFace/TRANSFORMER_HOME"
export HF_DATASETS_CACHE="/GPFS/rhome/xiyuanyang/HuggingFace/HF_DATASETS_CACHE"
export HF_ENDPOINT="https://hf-mirror.com"
```

（不知道是否有用）

### CLI 下载数据集和模型

#### 下载aria2

```bash
sudo apt install aria2
```

检查如下命令是否通过：

```bash
aria2c --version
```

```
aria2 version 1.37.0
Copyright (C) 2006, 2019 Tatsuhiro Tsujikawa

This program is free software; you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation; either version 2 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

** Configuration **
Enabled Features: Async DNS, BitTorrent, Firefox3 Cookie, GZip, HTTPS, Message Digest, Metalink, XML-RPC, SFTP
Hash Algorithms: sha-1, sha-224, sha-256, sha-384, sha-512, md5, adler32
Libraries: zlib/1.3 libxml2/2.9.14 sqlite3/3.45.1 GnuTLS/3.8.3 nettle GMP/6.3.0 c-ares/1.27.0 libssh2/1.11.0
Compiler: gcc 13.2.0
  built by  x86_64-pc-linux-gnu
  on        Apr 16 2024 09:56:00
System: Linux 5.15.153.1-microsoft-standard-WSL2 #1 SMP Fri Mar 29 23:14:13 UTC 2024 x86_64

Report bugs to https://github.com/aria2/aria2/issues
Visit https://aria2.github.io/
```

> 如果是服务器没有sudo权限，可以使用网速较慢且没有多线程功能的`wget`工具，但是体验感稍差。

#### 下载hfd

```bash
wget https://hf-mirror.com/hfd/hfd.sh
chmod a+x hfd.sh
```

#### 下载对应的模型和数据集

如果已经下载过aria2，使用命令：

```bash
# 下载模型
./hfd.sh gpt2

# 下载数据集
./hfd.sh tatsu-lab/alpaca_eval --dataset
```

如果没有下载，需要额外加一行命令：

```bash
./hfd.sh tatsu-lab/alpaca_eval --dataset --tool wget
```

如果看到如下的输出，说明已经成功下载，在当前目录下查看对应名称的文件夹即可

```
Fetching repo metadata...
Generating file list...
[Warning] jq not installed, using grep/awk for metadata json parsing (slower). Consider installing jq for better parsing performance.
Starting download with wget to alpaca_eval...
--2025-04-25 20:35:24--  https://hf-mirror.com/datasets/tatsu-lab/alpaca_eval/resolve/main/.gitattributes
Resolving hf-mirror.com (hf-mirror.com)... 153.121.61.152, 153.121.57.40, 160.16.110.249, ...
Connecting to hf-mirror.com (hf-mirror.com)|153.121.61.152|:443... connected.
HTTP request sent, awaiting response... 200 OK
Length: 2693 (2.6K) [text/plain]
Saving to: ‘.gitattributes’

.gitattributes                 100%[==================================================>]   2.63K  --.-KB/s    in 0s

2025-04-25 20:35:26 (15.7 MB/s) - ‘.gitattributes’ saved [2693/2693]
```

### 数据集下载到本地之后的使用

由于上面手动下载数据集的方式导致`datasets.load_dataset()`的方法不可以被调用（还是搞不懂为什么不可以😅），但是我们可以手动提取数据集，以`alpaca_eval`为例：

```python
import datasets
eval_set = datasets.Dataset.from_json("/GPFS/rhome/xiyuanyang/alpaca_eval/alpaca_eval.json")
eval_set = eval_set.remove_columns(["output", "generator"])
print(eval_set)
```

会输出结果：

```
Dataset({
    features: ['dataset', 'instruction'],
    num_rows: 805
})
```

很明显，我们成功加载了数据集。

### 更新: 使用官方的 HF CLI DOWNLOAD

{% note primary %}

貌似把网站更新为国内的镜像源就可以正常使用 HF DOWNLOAD 的命令行功能了，并且使用 `load_dataset()` 的官方函数也支持从本地模型路径和数据集路径导入，非常好！

> 并且，个人感觉与其把所有的数据集和模型全部下载到一个固定的下载链接，分散到不同的项目中更方便项目的管理。

{% endnote %}

#### HF CLI

[Official Guide](https://huggingface.co/docs/huggingface_hub/en/guides/cli)

```bash
hf --help
```

```
usage: hf <command> [<args>]

positional arguments:
  {auth,cache,download,jobs,repo,repo-files,upload,upload-large-folder,env,version,lfs-enable-largefiles,lfs-multipart-upload}
                        hf command helpers
    auth                Manage authentication (login, logout, etc.).
    cache               Manage local cache directory.
    download            Download files from the Hub
    jobs                Run and manage Jobs on the Hub.
    repo                Manage repos on the Hub.
    repo-files          Manage files in a repo on the Hub.
    upload              Upload a file or a folder to the Hub. Recommended for single-commit uploads.
    upload-large-folder
                        Upload a large folder to the Hub. Recommended for resumable uploads.
    env                 Print information about the environment.
    version             Print information about the hf version.

options:
  -h, --help            show this help message and exit
```

最常见的使用就是 `hf download` 的功能。

- Download datasets

  ```bash
  hf download --repo-type dataset --resume-download mrfakename/identity  --local-dir ./data/mrfakename/identity
  ```

- Download models

  ```bash
  hf download --resume-download HuggingFaceTB/SmolLM2-135M-Instruct  --local-dir ./models/HuggingFaceTB/SmolLM2-135M-Instruct
  ```

- Download repository

  ```bash
  hf download HuggingFaceH4/zephyr-7b-beta --resume_download --local_dir ./models/HuggingFaceH4/zephyr-7b-beta
  ```

- More
  - **For more, RTFM**!
  - Download a single file
  - Download multiple files
  - Download spaces
  - hf login for getting tokens
  - Specify cache directory


## Markdown 文本内设置超链接

Upd: `2025/04/25`

对于文本内的链接跳转（例如**目录跳转**）：

{% note primary %}

例如：[Hugging Face 下载数据集](#Hugging-Face-数据集下载)

{% endnote %}

- 注意圆括号内部的字符串需要使用`-`来替代空格。
- 注意需要加几个`#`（可以看HTML的链接确定）

## 日志书写

详见博客：[Debugging and Profiling](https://xiyuanyang-code.github.io/posts/Profiling-and-Debugging/)

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

## Tmux 使用备忘录

- `tmux new -t name`
- `tmux attach -t name`
- `tmux kill-session -t name`
- `Ctrl-b + [`进入滚动模式，`q`退出。

```
TMUX MEMO
tmux new -s <session-name> for create a new tmux session.
tmux attach -t <session-name> for attach a new tmux session.
tmux kill-session -t <session-name> for kill a session.
tmux tmux switch -t <session-name> for switching different sessions.
tmux rename-session -t 0 <new-name> for renaming the sessions.
tmux split-window for spliting windows, -h for left and right.
mux select-pane for moving cursor, -UDLR
tmux swap-pane -U for swapping the current pane up.
tmux swap-pane -D for swapping the current pane down.
Ctrl+b % for splitting the window vertically.
Ctrl+b " for splitting the window horizontally.
Ctrl+b <arrow key> for switching to another pane.
Ctrl+b ; for switching to the last pane.
Ctrl+b o for switching to the next pane.
Ctrl+b { for swapping the current pane with the previous one.
Ctrl+b } for swapping the current pane with the next one.
Ctrl+b Ctrl+o for moving all panes forward.
Ctrl+b Alt+o for moving all panes backward.
Ctrl+b x for closing the current pane.
Ctrl+b ! for splitting the current pane into a new window.
Ctrl+b z for toggling the current pane to fullscreen.
Ctrl+b Ctrl+<arrow key> for resizing panes.
Ctrl+b q for displaying pane numbers.
tmux new-window for creating a new window.
tmux new-window -n <window-name> for creating a new window with a specific name.
tmux select-window -t <window-number> for switching to a specific window by number.
tmux select-window -t <window-name> for switching to a specific window by name.
tmux rename-window <new-name> for renaming the current window.
Ctrl+b c for creating a new window.
Ctrl+b p for switching to the previous window.
Ctrl+b n for switching to the next window.
Ctrl+b <number> for switching to a specific window by its number.
Ctrl+b w for selecting a window from a list.
Ctrl+b , for renaming the current window.
tmux list-keys for listing all key bindings.
tmux list-commands for listing all tmux commands and their parameters.
tmux info for listing information about current tmux sessions.
tmux source-file ~/.tmux.conf for reloading the current tmux configuration.
```

写成脚本函数再source一下可以随时查看：

```bash
# Tmux memo
# This funcion shows the basic usage of several tmux commands.
TMUXH(){
    print_colored red "TMUX MEMO"
    echo "\033[36mtmux new -s <session-name>\033[0m for create a new tmux session."
    echo "\033[36mtmux attach -t <session-name>\033[0m for attach a new tmux session."
    echo "\033[36mtmux kill-session -t <session-name>\033[0m for kill a session."
    echo "\033[36mtmux tmux switch -t <session-name>\033[0m for switching different sessions."
    echo "\033[36mtmux rename-session -t 0 <new-name>\033[0m for renaming the sessions."
    echo "\033[36mtmux split-window\033[0m for spliting windows, -h for left and right."
    echo "\033[36tmux select-pane\033[0m for moving cursor, -UDLR"
    echo "\033[36mtmux swap-pane -U\033[0m for swapping the current pane up."
    echo "\033[36mtmux swap-pane -D\033[0m for swapping the current pane down."
    echo "\033[36mCtrl+b %\033[0m for splitting the window vertically."
    echo "\033[36mCtrl+b \"\033[0m for splitting the window horizontally."
    echo "\033[36mCtrl+b <arrow key>\033[0m for switching to another pane."
    echo "\033[36mCtrl+b ;\033[0m for switching to the last pane."
    echo "\033[36mCtrl+b o\033[0m for switching to the next pane."
    echo "\033[36mCtrl+b {\033[0m for swapping the current pane with the previous one."
    echo "\033[36mCtrl+b }\033[0m for swapping the current pane with the next one."
    echo "\033[36mCtrl+b Ctrl+o\033[0m for moving all panes forward."
    echo "\033[36mCtrl+b Alt+o\033[0m for moving all panes backward."
    echo "\033[36mCtrl+b x\033[0m for closing the current pane."
    echo "\033[36mCtrl+b !\033[0m for splitting the current pane into a new window."
    echo "\033[36mCtrl+b z\033[0m for toggling the current pane to fullscreen."
    echo "\033[36mCtrl+b Ctrl+<arrow key>\033[0m for resizing panes."
    echo "\033[36mCtrl+b q\033[0m for displaying pane numbers."

    echo "\033[36mtmux new-window\033[0m for creating a new window."
    echo "\033[36mtmux new-window -n <window-name>\033[0m for creating a new window with a specific name."
    echo "\033[36mtmux select-window -t <window-number>\033[0m for switching to a specific window by number."
    echo "\033[36mtmux select-window -t <window-name>\033[0m for switching to a specific window by name."
    echo "\033[36mtmux rename-window <new-name>\033[0m for renaming the current window."
    echo "\033[36mCtrl+b c\033[0m for creating a new window."
    echo "\033[36mCtrl+b p\033[0m for switching to the previous window."
    echo "\033[36mCtrl+b n\033[0m for switching to the next window."
    echo "\033[36mCtrl+b <number>\033[0m for switching to a specific window by its number."
    echo "\033[36mCtrl+b w\033[0m for selecting a window from a list."
    echo "\033[36mCtrl+b ,\033[0m for renaming the current window."

    echo "\033[36mtmux list-keys\033[0m for listing all key bindings."
    echo "\033[36mtmux list-commands\033[0m for listing all tmux commands and their parameters."
    echo "\033[36mtmux info\033[0m for listing information about current tmux sessions."
    echo "\033[36mtmux source-file ~/.tmux.conf\033[0m for reloading the current tmux configuration."

}
```

## Python Memo

[See this Blog for more details!](https://xiyuanyang-code.github.io/posts/Python-cheatsheet/):

- 字符串
- 列表 & 元组 & 字典
- OOP

## Conda Quick Setup

Conda常见的命令：

- Conda创建虚拟环境

```bash
conda create --name aflow -y
```

其中`--name`后缀名代表创建的虚拟环境的名称（或者使用 `-n`）

- Conda创建指定版本号的虚拟环境

如果需要Python的版本回退，需要指定的Python版本号：

```bash
conda create --name aflow python=3.8 -y
```

{% note primary %}

**语义版本控制（Semantic Versioning）**

版本号由三部分组成：`主版本号.次版本号.修订号`（`MAJOR.MINOR.PATCH`），例如 `2.5.3`：

1. **主版本号（MAJOR）**
	- **不兼容的变更**：当代码发生破坏性（Backward Incompatible）修改时递增。
	- **示例**：移除 API、重大架构调整。
	- **升级风险**：用户需检查代码是否适配新版本。
2. **次版本号（MINOR）**
	- **向下兼容的功能新增**：新增功能或改进，但不影响现有接口。
	- **示例**：添加新 API、性能优化。
	- **升级建议**：通常可安全升级。
3. **修订号（PATCH）**
	- **向下兼容的问题修复**：修复漏洞或小改进，不影响功能。
	- **示例**：Bug 修复、文档更新。
	- **升级建议**：建议立即更新。

{% endnote %}

- Conda激活环境

```bash
# activate specific version
conda activate aflow

# deactivate to the base version
conda deactivate 
```

- Conda删除虚拟环境

```bash
conda remove --name your_env_name --all
```

- 查询信息

```bash
# 查看虚拟环境的列表
conda env list

# 查看当前虚拟环境的conda详细配置
conda info

# 或者
conda env export

# 如果你只是想要查看Python版本号
python --version

# 查看当前安装的第三方库
pip list

# 或者
conda list <package_name>

# 查看更细致的信息
pip show <package_name>
```

> 使用pip查看信息会比conda要快很多，强烈建议安装miniconda而非anaconda。

## SSH Usage

See [this blog](https://xiyuanyang-code.github.io/posts/Secure-Shell-and-Encryption/) for more details.

## `ipdb` Usage

IPDB 是 Python 的交互式调试器，是 `pdb` 的增强版，提供了更好的体验，例如自动补全、语法高亮等。

- **在代码中设置断点：**

	```python
	import ipdb
	ipdb.set_trace()
	```

- **出现异常时自动进入：**

	```python
	import ipdb
	ipdb.set_trace(context=2) # 可以在异常发生时自动进入，并显示上下文行数
	```

	```python
	# 在命令行运行脚本并发生异常后
	# 在交互式环境中输入：
	import ipdb
	ipdb.pm()
	```

	这会在最近的异常发生点启动 IPDB。

- **在命令行启动：**

	```bash
	python -m ipdb your_script.py
	```

| **命令**            | **简介**                                           | **示例**                                                     |
| ------------------- | -------------------------------------------------- | ------------------------------------------------------------ |
| `n` (next)          | 执行当前行，但不进入函数内部。                     | `n`                                                          |
| `s` (step)          | 执行当前行，如果当前行是函数调用，则进入函数内部。 | `s`                                                          |
| `c` (continue)      | 继续执行，直到遇到下一个断点或程序结束。           | `c`                                                          |
| `q` (quit)          | 退出调试器，终止程序。                             | `q`                                                          |
| `b` (breakpoint)    | 设置/查看断点。                                    | `b 10` (在第10行设置断点)&lt;br>`b func_name` (在函数入口设置断点)&lt;br>`b` (查看所有断点) |
| `cl` (clear)        | 清除断点。                                         | `cl 1` (清除断点编号1)&lt;br>`cl` (清除所有断点)             |
| `l` (list)          | 显示当前位置周围的代码。                           | `l`                                                          |
| `p` (print)         | 打印变量的值。                                     | `p my_variable`                                              |
| `pp` (pretty print) | 美观地打印变量的值（常用于字典、列表等复杂结构）。 | `pp my_dict`                                                 |
| `a` (args)          | 显示当前函数的参数。                               | `a`                                                          |
| `w` (where)         | 显示当前堆栈跟踪。                                 | `w`                                                          |
| `up`                | 向上移动堆栈帧。                                   | `up`                                                         |
| `down`              | 向下移动堆栈帧。                                   | `down`                                                       |
| `h` (help)          | 获取帮助。                                         | `h` (`h b` 查看 `b` 命令的帮助)                              |
| `!`                 | 执行任意 Python 表达式。                           | `!my_variable = 10`                                          |

