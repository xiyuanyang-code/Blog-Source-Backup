---
title: Docker Tutorial
date: 2025-04-26 23:01:51
index_img: /img/cover/docker.jpg
excerpt: This article will introduce the basic principles of Docker and provide a quick tutorial for getting started.
categories:
  - Efficient Tools
tags:
  - Finished
  - Docker
  - tutorial
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Docker

![docker](https://s1.imagehub.cc/images/2025/04/26/32e5576b53a0feedff1a56966fd1ec91.webp)

## Preliminaries

使用Docker之前必须要先安装docker！参考链接 [Docker安装](https://yeasy.gitbook.io/docker_practice/install/ubuntu)[^1]。笔者实测在如下本地环境中可以正常安装：

```
Distributor ID: Ubuntu
Description:    Ubuntu 24.04.2 LTS
Release:        24.04
Codename:       noble
```

{% note warning %}

Docker安装直接`sudo apt install`会报错，需要先更新软件源之后再`sudo apt update`刷新缓存之后，就可以正常的安装。

{% endnote %}

## Introduction

### Why We Need Docker?

**依赖地狱问题**[^2],是指在计算机软件中，是指在操作系统中由于软件之间的[依赖性](https://zh.wikipedia.org/wiki/耦合性_(計算機科學))不能被满足而引发的问题。一个软件包依赖于其它必要的软件包（且版本要符合要求），使得软件包系统形成了复杂的依赖关系网络，并可能引发一系列问题。一些软件包可能因为依赖性无法满足，需要安装大量软件包；另一方面，一个软件包的卸载可能引发数量众多的软件包无法工作。

这就会导致同一个软件在更换环境之后出现诸多异常的情况（对底层软件和工具链的依赖性过强），进而导致一系列的环境兼容问题。

如何解决，一种很常见的方法是只要**高级的包管理工具**，例如`Debian`的`apt`系列（`apt install`就是在安装软件包）。而**虚拟机**（例如VMware）实现了另一种解决方案，即**没有一致的环境就创造一致的环境**，通过分系统的配置可以实现环境的统一。

但是虚拟机的一大痛点就是**过于笨重**，因为需要模拟完整的操作系统，Windows OS动辄几十GB，同时安装VM虚拟机需要直接使用主机的内存和CPU。而**Docker**实现了这一问题的轻量级实现，在低资源占用，快启动，高性能的基础之上完美的实现了版本和环境的隔离！

### CI/CD

**CI/CD**（Continuous Integration / Continuous Delivery or Deployment）是现代软件开发的核心实践，旨在通过**自动化流程**快速、可靠地构建、测试和发布代码。它是 DevOps 文化的关键技术支撑。

在CI/CD中，从开发环境到测试环境中的`build`就需要使用Docker镜像！这样有助于开发和测试人员统一环境配置，更好的对代码进行审查。

> CI/CD是一个很有意思的东西~希望以后可以开个新坑讲一讲。

## Quick Startup

我们当然可以直接上代码！但是笔者希望先讲一点点的原理和基本概念~

![The Good and the Bad of Docker](https://www.altexsoft.com/static/blog-post/2023/11/50d965c7-b468-4de6-ad45-d8c8cb385a02.jpg)

[图片来源](https://www.altexsoft.com/static/blog-post/2023/11)

虚拟机笨重的很重要的一个原因就是他需要和**硬件进行交互**，并且模拟操作系统的运行。而Docker很聪明的绕过了这一点，将`Docker Engine`建立在了操作系统之上，作为链接各种软件和底层操作系统的**拦路虎**，并在此之上建立相互隔离的`Docker Container`，实现不同软件包的隔离。

### Docker Image

在虚拟机中，存在**快照**（Snapshot）的概念，即在特定时间点**保存虚拟机状态的技术**。快照可以捕获虚拟机的整个运行状态，包括其内存、CPU 状态、硬盘内容和网络状态。这样，用户可以在将来恢复到这个状态，方便进行测试、备份或恢复操作。对于Docker来说，**Docker Image**就是一种更轻量级的快照（因为在操作系统的上层），一个**只读的模板**，包含运行应用所需的代码、运行时环境、库和配置。

### Docker Container

在准备好镜像之后，将**镜像实例化**就是创建**Docker Container**的过程，并且不同的Container之间实现了隔离的功能，（可以把Docker Image看做抽象出来的类，而Docker Container就是实例化之后的对象），在Docker Container中，我们就有了修改的权限。

### Docker File

如何构建镜像？我们不可能手动安装每一个依赖包（这还不如虚拟机），因此我们就需要编写**自动化脚本来实现镜像的构建**，有点类似于`cmakelists.txt`。

## Initial Project

在学习完Docker的这三个基本概念之后，就可以开始上手第一个Docker项目了！我们来看下面的示例：我们希望使用Docker“隔离”出一个环境来运行Helloworld，同时，为了增加复杂性，我们将会引入**pip包管理系统**，来看看Docker如何在创建image的时候自动的就把环境给配好了。

首先，我们需要创建一个working directory：

```bash
.
├── Dockerfile
├── main.py
└── requirements.txt

1 directory, 3 files
```

其中Dockerfile是脚本，而剩下两个文件就是实际Python开发中需要的文件。

```python
import numpy as np

print("Hello world")
print("This is my first docker project")

random_number = np.random.randint(1,30)
print(random_number)
```

```
# in requirements.txt
numpy
```

接下来，我们需要创建一个Image，可以来支持运行我们基础的Python文件，我们可以使用如下的Dockerfile来实现这个功能：

```dockerfile
# Using Docker image officially
FROM python:3.9-slim

# Setting the directory
WORKDIR /app

# 将当前目录的文件复制到容器的/app目录
COPY . .

# Several commands when installing the environments
RUN pip install -i https://pypi.tuna.tsinghua.edu.cn/simple -r ./requirements.txt

# 定义容器启动时执行的命令
CMD ["python", "main.py"]
```

- `FROM python:3.9-slim`代表下载Docker官方的Python镜像，可以在Docker Hub上找到你想要的镜像。
- `WORKDIR /app`是设置**容器**的工作目录。（这一点在后面会讲到）
- `COPY . .`代表**将当前目录的文件复制到容器的/app目录**，当然你也可以只复制你想要的。
- `RUN`部分的指令是Docker的精髓：
	- `RUN` 指令用于在**构建镜像时**执行命令（例如安装软件、配置环境等）
	- 他支持命令行脚本语句，具体就是使用`/bin/sh -c`
	- 也只是exec格式，相当于更灵活的命令行parser：`RUN ["/bin/bash", "-c", "echo Hello World"]`
- `CMD`部分和`RUN`非常想，但是实在**容器启动（已经被实例化）**之后需要执行的命令。

> 简单来说，`RUN`就是安装环境，而`CMD`就是执行命令。

### 故意报点错

我们不妨先将RUN那一行注释掉，看会发生什么：

```bash
docker build -t python-helloworld .
```

使用`docker build`来实现**构建docker镜像**（Dockerfile -> Docker Image的过程），`-t`后接后缀镜像的名字，注意最后的 `.` 表示当前目录是构建上下文。

```
failed to fetch metadata: fork/exec /usr/local/lib/docker/cli-plugins/docker-buildx: no such file or directory

DEPRECATED: The legacy builder is deprecated and will be removed in a future release.
            Install the buildx component to build images with BuildKit:
            https://docs.docker.com/go/buildx/

Sending build context to Docker daemon  4.096kB
Step 1/4 : FROM python:3.9-slim
 ---> 501f96d59d70
Step 2/4 : WORKDIR /app
 ---> Using cache
 ---> a4e76098ab7c
Step 3/4 : COPY . .
 ---> 51a1c6924060
Step 4/4 : CMD ["python", "main.py"]
 ---> Running in a3dbbb5ffd68
 ---> Removed intermediate container a3dbbb5ffd68
 ---> fb4b52ccc604
Successfully built fb4b52ccc604
Successfully tagged python-helloworld:latest
```

Docker build成功了！接下来我们使用`docker run`来**实现镜像的实例化**。

```bash
docker run --rm python-helloworld 
```

其中`python-helloworld`就是镜像的名字，而`--rm`代表创建临时镜像，适合作为临时的调试。

{% note primary %}

非常推荐安装Vscode的Docker插件，可以实时监控Docker的镜像和示例container：

![Docker](https://s1.imagehub.cc/images/2025/04/27/e948a8f618de3f30e90a157dfba8d5cc.png)

{% endnote %}

貌似**出现报错**了...看看报错信息

```
Traceback (most recent call last):
  File "/app/main.py", line 1, in <module>
    import numpy as np
ModuleNotFoundError: No module named 'numpy'
```

也就是说，Docker已经成功设置好了一个Python的解释器的镜像（在Python-helloworld中），但是未安装相关包`numpy`的依赖，导致脚本文件运行失败。

### 完整运行

现在我们把注释掉的命令恢复，`docker build` again！

```
Looking in indexes: https://pypi.tuna.tsinghua.edu.cn/simple
Collecting numpy
  Downloading https://pypi.tuna.tsinghua.edu.cn/packages/b9/14/78635daab4b07c0930c919d451b8bf8c164774e6a3413aed04a6d95758ce/numpy-2.0.2-cp39-cp39-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (19.5 MB)
     ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 19.5/19.5 MB 15.3 MB/s eta 0:00:00
Installing collected packages: numpy
WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager. It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv
Successfully installed numpy-2.0.2

[notice] A new release of pip is available: 23.0.1 -> 25.1
[notice] To update, run: pip install --upgrade pip
```

我们发现他成功执行了我们的脚本！这下再Docker run就可以正常输出信息了！

```
Hello world
This is my first docker project
15
```

### Some tips

我们也可以直接使用`docker pull`来安装镜像！例如执行`docker pull amd64/python`，就拉取了一个针对 AMD64 架构的官方 Python 镜像，相当于安装了一个Python解释器，同样，我们也可以针对这个docker image进行`docker run`。

![image](https://s1.imagehub.cc/images/2025/04/27/2a00dd0c15768842d52f88f8f63329b6.png)

## More Advanced Usage

到这里，Docker的基本命令就差不多了！个人感觉Docker的CLI做的还是比较清爽的，不像某些bash脚本一样要加上各种各样奇奇怪怪的后缀😅。

For more advanced usage, to be continued in the future.

## References

[^1]: https://yeasy.gitbook.io/docker_practice/install/ubuntu
[^2]: https://zh.wikipedia.org/zh-cn/%E7%9B%B8%E4%BE%9D%E6%80%A7%E5%9C%B0%E7%8B%B1
