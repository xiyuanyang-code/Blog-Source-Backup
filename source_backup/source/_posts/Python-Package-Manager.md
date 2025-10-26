---
title: Python Package Manager
date: 2025-07-14 22:39:48
excerpt: A basic tutorial for several advanced package manager, including pip, venv, uv and conda.
index_img: /img/cover/dependencies.jpg
categories:
  - Code
  - Python
tags:
  - Tutorial
  - Package Manager
  - Python
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Python 包管理工具

相比于Rust等语言规整的语法规则和项目结构，Python自由的语法规则和开发的第三方社区导致**Python项目管理问题**一直是令人们头疼的事情之一。

## Preliminaries

### What are Modules and Packages

在知道Python包 (Package) 之前，我们有必要先了解一下**模块** (Module) 的概念。

在官方文档: [Official Docs for Python 3.13.5](https://docs.python.org/3/tutorial/modules.html) 中，Module的定义是这样的：

{% note info %}
To support this, Python has a way to ==put definitions in a file== and use them in a script or in an interactive instance of the interpreter. **Such a file is called a module**; definitions from a module can be imported into other modules or into the main module (the collection of variables that you have access to in a script executed at the top level and in calculator mode).
{% endnote %}

简单来说，一份`.py`代码就是模块，或者说模块就是`.py`文件。使用者可以通过`import`命令导入相对应的模块，使用其中的函数，变量和类等，实现功能封装和代码复用。


有关更多`import`命令的使用技巧，推荐 [this blog](https://xiyuanyang-code.github.io/posts/Python-Architecture-Patterns-Multi-file-Progarmming/)


在更复杂的项目管理时，单纯的使用一个文件不可能囊括所有要import的代码，当然，我们可以对每一个文件分别import，但是这样做缺乏逻辑性，并且操作繁琐后续难以维护。

因此，Python提出了**包** (Packages[^1]) 的概念。一个包含多个模块的目录，且必须有一个 `__init__.py` 文件（即使是空的）。

```
mypackage/
├── __init__.py     # 必须存在（标识这是一个包）
├── module1.py      # 模块1
└── module2.py      # 模块2
```

### Versions of Packages and Dependency Hell

**站在前人的肩膀上看世界**。在开发过程中，我们经常会依赖许多第三方包 (例如numpy)来加速开发。然而，这些包本身也在不断迭代更新，发布新版本。这就引入了一个复杂的问题：**版本管理**。


#### Semantic Versioning

{% note primary %}

目前最广泛采用的版本号规范是语义化版本控制 (Semantic Versioning)，简称 SemVer。它通过一套简单、明确的规则来定义版本号，让人们能够从版本号本身推断出版本之间的兼容性和变更类型。**SemVer 的版本号格式通常是 MAJOR.MINOR.PATCH**，例如 1.2.3：

- MAJOR (主版本号)：当你做了不兼容的 API 变更时，升级主版本号。这意味着依赖这个库的代码在**升级主版本号后可能需要修改**。例如，从 1.x.x 升级到 2.0.0。

- MINOR (次版本号)：当你做了向下兼容的功能性新增时，升级次版本号。这意味着新增了功能，但不会破坏现有代码的兼容性。例如，从 1.2.x 升级到 1.3.0。

- PATCH (修订版本号)：当你做了向下兼容的问题修复时，升级修订版本号。这意味着修复了 bug，但没有增加新功能或改变 API。例如，从 1.2.3 升级到 1.2.4。

除了这三个主要部分，SemVer 还允许使用预发布版本（例如 1.0.0-alpha.1、1.0.0-beta.2）和构建元数据（例如 1.0.0+20130313144700）来表示更细致的版本状态。

{% endnote %}

#### Version Conflict

举个简单的例子，你的本地项目A依赖于第三方包B(Version: 1.2.3)，但是某一天B迎来了一次重大升级或重构(Version: 2.1.0)，废弃了在原先主版本中的一些接口。此时，如果用户使用你的项目A，但是其安装的库B的版本是最新版本，此时就会产生**版本冲突问题**，因为A**依赖的是B库的老版本的接口**。

在实际应用中，软件包的依赖项更是错综复杂，一个程序往往依赖动辄几十个甚至上百个**版本号各不相同**包，导致项目难以运行和维护。这种情况被叫做**依赖地狱**。(Dependency Hell)。当依赖性过多，且具有多级结构，形成错综复杂的网络，依赖性的解析就会变得异常困难，甚至出现无法解析的致命错误。


```
A depends on B(>=2.2.0), and A depends on C(>=2.4.0)
B depends on D(>=2.3.4), and D depends on C(>=1.2.0)
C depends on B(<=1.18.0)

Can A be successfully installed?
In real cases, things get worse!
```

### Advanced Package Manager

依赖地狱是一个NP难问题 (SAT)，依赖开发者的手动构建和调试是不可能的。此外，一些基本编程语言的非兼容性变更是毁灭性的，例如Python主版本号从2到3的转变[^2]，许多上层应用都要跟着修改接口和代码。因此，**高级软件管理包**的概念被提出，他就像一个管理者，专门负责管理不同依赖项之间的冲突和版本控制问题。

{% note primary %}

包管理工具不仅局限于Python开发，现代软件工程中地狱依赖的现象随处可见，常见的包管理工具有：

- `apt` for Debian & Ubuntu.

- `npm` for Nodejs.

- `Cargo` for Rust.

- `bundle` for Ruby.

- Python package manager, which will be discussed today.

{% endnote %}

## Python Package Manager

### `pip`

`pip`[^3] 是 Python 官方推荐的包管理工具，用于安装和管理 Python 包。它可以从 [PyPI (Python Package Index)](https://pypi.org/) 下载并安装第三方库。

> pip is the package installer for Python. You can use pip to install packages from the Python Package Index and other indexes.

> 具体的安装过程详见：[https://pypi.org/project/pip/](https://pypi.org/project/pip/)


```bash
# 安装包
pip install package_name

# 指定版本安装
pip install package_name==1.2.3

# 升级包
pip install --upgrade package_name

# 卸载包
pip uninstall package_name

# 查看已安装包
pip list

# 导出当前环境的依赖列表
pip freeze > requirements.txt

# 根据依赖文件安装
pip install -r requirements.txt
```

> 你甚至可以使用pip安装自己本地的库并且使用！需要使用`pip install .`并且配置好`pyproject.toml`的配置文件。

有了pip，你就可以自由安装除标准库之外第三方开发者写好的库并且在你的代码中使用。

### 基于项目的虚拟环境构建工具

在跑别人的项目时，可以使用`pip install -r requirements.txt`来安装别人的项目依赖，而不需要自己手动调整并且安装。但是这会带来新的隐患。

{% note primary %}

例如下面的例子（Python 3.9），我在本地的numpy环境是`2.0.2`，而matplotlib的版本是`3.9.4`，相安无事。

但是如果我在安装他人代码项目依赖的时候，降级安装了`numpy==1.22`的版本（这在许多比较老的项目版本中比较常见），就会出现如下报错：

```text
ERROR: pip's dependency resolver does not currently take into account all the packages that are installed. This behaviour is the source of the following dependency conflicts.
matplotlib 3.9.4 requires numpy>=1.23, but you have numpy 1.22.0 which is incompatible.
```
后续又会产生一系列兼容性的问题。

{% endnote %}

对于上述版本冲突问题，往往采用**虚拟环境**解决，即将冲突的相关依赖分别放置在两个虚拟环境中，井水不犯河水。

#### `venv`

`venv` 是 Python 3.3+ 内置的虚拟环境管理工具。它允许你为每个项目创建独立的包和解释器环境，避免依赖冲突。


```bash
# 创建虚拟环境
python3 -m venv venv_name

# 激活虚拟环境
# Linux/macOS
source venv_name/bin/activate
# Windows
venv_name\Scripts\activate

# 退出虚拟环境
deactivate
```

`venv` 是**基于项目的**，即一个项目对应一个venv的环境，这**几乎完美的解决了地狱依赖的问题**，使用者需要激活对应的虚拟环境，就可以配置一个可运行的Python环境，并且这个环境不会影响全局包版本。


#### `uv`

**URL**：

- [uv official docs](https://docs.astral.sh/uv/)

- [A lecture about uv](https://youtube.com/watch?v=gSKTfG1GXYQ)

`uv` 是一个新兴的、极快的 Python 包管理和虚拟环境工具，兼容 pip 和 venv 的大部分功能。它采用 Rust 编写，速度极快，支持依赖解析、虚拟环境管理和包安装。

- 安装和解析依赖速度极快。
- 兼容 pip 的 requirements.txt。
- 支持多平台。

**强烈推荐各位使用uv**！尤其在编写自己的项目中。

虚拟环境在解决版本依赖问题的同时会非常消耗**存储空间**，这是不可避免的。例如，你在10个开发项目中都需要用到**相同版本的torch库**，但是这10个venv创建的虚拟环境会安装10个torch，造成了严重的空间浪费。uv在venv的基础之上利用**符号链接优化**，可以自动识别并且优化虚拟环境，这也是uv如此快速的原因。

{% note primary %}

Pypi的网站服务器在国外，在国内的下载速度会非常慢，因此，**国内设置了很多开源镜像站**，例如阿里源，清华源等，因此，可以配置全局文件实现pip和uv的换源。同理，这样一套思路也适用于其他开源软件的安装，例如apt换源， HuggingFace换源等。

```bash
# edit in ~/.bashrc
export UV_DEFAULT_INDEX="https://mirrors.aliyun.com/pypi/simple"
```

```bash
# edit in ~/.pip/pip.conf
[global]
index-url = https://pypi.tuna.tsinghua.edu.cn/simple
```

{% endnote %}

### 多项目复用虚拟环境：`conda`

`conda` 是 Anaconda 发行版自带的包和环境管理工具，支持 Python 及其他语言。它不仅可以管理 Python 包，还能管理 **C/C++ 等底层依赖**。conda的虚拟环境管理是全局意义上的，因此，在更广泛的场景上，使用conda作为项目管理会更加方便。

我们推荐安装更轻量级的miniconda：[Miniconda](https://www.anaconda.com/docs/getting-started/miniconda/main)

```bash
# 创建新环境
conda create -n env_name python=3.10

# 激活环境
conda activate env_name

# 安装包
conda install package_name

# 导出环境
conda env export > environment.yml

# 根据环境文件创建环境
conda env create -f environment.yml

# 删除环境
conda remove -n env_name --all
```

## References

[^1]: [Packages in Python](https://docs.python.org/3/tutorial/modules.html#packages)

[^2]: [From Python2 to Python3](https://www.ibm.com/docs/en/cloud-pak-sec-aas?topic=scripts-python-2-python-3-differences)

[^3]: [Pip official docs](https://pypi.org/project/pip/)