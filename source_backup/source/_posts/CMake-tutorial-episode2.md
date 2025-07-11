---
title: CMake-tutorial-episode2
date: 2024-12-15 19:58:01
index_img: /img/cover/cmake.jpg
excerpt: In this article, You will learn how to use CMake to compile a more complex multi-file C++ program in Clion, together with some advice regarding building a formal cpp project with the help of CMake Tools.
categories:
  - Efficient Tools
tags:
  - tutorial
  - Finished
  - CMake
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# CMake 编译多文件 C++ 程序：项目指南

本指南将帮助你使用 CMake 构建一个包含多个 `.cpp` 文件的 C++ 项目。我们将逐步介绍如何配置 `CMakeLists.txt` 文件，以及如何管理项目中的源文件和头文件。

------

### 1. 项目结构

假设我们有以下项目结构：

```
/project
  ├── CMakeLists.txt      # 项目根目录的 CMake 配置文件
  ├── src/                # 源代码文件夹
  │   ├── main.cpp        # 主程序文件
  │   ├── foo.cpp         # 其他源文件
  │   └── bar.cpp         # 其他源文件
  ├── include/            # 头文件文件夹
  │   ├── foo.h           # foo 的头文件
  │   └── bar.h           # bar 的头文件
  └── CMakeLists.txt      # src 子目录中的 CMake 配置文件
```

### 2. 创建 CMake 配置文件

#### 2.1 根目录的 `CMakeLists.txt`

在项目的根目录下创建一个 `CMakeLists.txt` 文件，配置全局设置和添加子目录。

```cmake
# 设置 CMake 最低版本要求
cmake_minimum_required(VERSION 3.10)

# 设置项目名称
project(MyMultiFileProject)

# 设置 C++ 标准
set(CMAKE_CXX_STANDARD 11)

# 添加 src 目录作为子目录
add_subdirectory(src)
```

#### 2.2 `src/CMakeLists.txt`

在 `src/` 目录下创建另一个 `CMakeLists.txt` 文件，用于配置源文件的编译规则。

```cmake
# 查找 src 目录及其子目录中的所有 .cpp 文件
file(GLOB_RECURSE SOURCES "*.cpp")

# 创建可执行文件
add_executable(MyExecutable ${SOURCES})

# 设置头文件搜索路径
target_include_directories(MyExecutable PRIVATE ../include)
```

- `file(GLOB_RECURSE ...)`：递归查找 `src/` 目录及其子目录下的所有 `.cpp` 文件。
- `add_executable(MyExecutable ${SOURCES})`：将所有 `.cpp` 文件编译成一个可执行文件 `MyExecutable`。
- `target_include_directories(MyExecutable PRIVATE ../include)`：设置 `MyExecutable` 可执行文件的头文件搜索路径，指向项目的 `include/` 目录。

### 3. 自动查找源文件

如果源文件数量较多，手动列出每个源文件可能比较麻烦。可以使用 `file(GLOB ...)` 或 `file(GLOB_RECURSE ...)` 自动查找 `.cpp` 文件。

#### 3.1 查找当前目录下的 `.cpp` 文件

```cmake
file(GLOB SOURCES "*.cpp")
```

`file(GLOB ...)` 会查找当前目录下所有 `.cpp` 文件，并将它们添加到 `SOURCES` 变量中。

#### 3.2 查找子目录中的 `.cpp` 文件

```cmake
file(GLOB_RECURSE SOURCES "*.cpp")
```

`file(GLOB_RECURSE ...)` 会递归地查找指定目录及其所有子目录中的 `.cpp` 文件。

### 4. 构建和编译

#### 4.1 生成构建目录

在项目根目录下创建一个 `build` 目录，并进入该目录：

```bash
mkdir build
cd build
```

#### 4.2 运行 CMake 配置

在 `build` 目录中运行 CMake，指定项目的根目录作为源目录：

```bash
cmake ..
```

这将配置项目并生成适合你平台的构建系统。

#### 4.3 编译项目

使用 CMake 生成的构建系统来编译项目。如果你使用 Makefiles，可以运行：

```bash
make
```

如果使用其他构建系统（如 Visual Studio），可以直接在 IDE 中进行编译。

#### 4.4 运行程序

编译完成后，你可以在 `build` 目录中找到生成的可执行文件 `MyExecutable`，然后运行它：

```bash
./MyExecutable
```

### 5. 处理大型项目结构

对于大型项目，你可能会将源文件和头文件拆分成多个子目录。在这种情况下，可以使用 `add_subdirectory()` 将各个子目录包含进来，每个子目录都可以有自己的 `CMakeLists.txt` 文件。

#### 示例：子目录结构

```
/project
  ├── CMakeLists.txt
  ├── src/
  │   ├── CMakeLists.txt
  │   ├── main.cpp
  │   ├── foo.cpp
  │   └── bar.cpp
  └── include/
      ├── foo.h
      └── bar.h
```

根目录的 `CMakeLists.txt` 文件：

```cmake
cmake_minimum_required(VERSION 3.10)

project(MyMultiFileProject)

set(CMAKE_CXX_STANDARD 11)

# 添加 src 子目录
add_subdirectory(src)
```

`src/CMakeLists.txt` 文件：

```cmake
# 查找 src 目录及其子目录中的所有 .cpp 文件
file(GLOB_RECURSE SOURCES "*.cpp")

# 创建可执行文件
add_executable(MyExecutable ${SOURCES})

# 设置头文件搜索路径
target_include_directories(MyExecutable PRIVATE ../include)
```

### 6. 总结

通过以上步骤，你可以使用 CMake 管理多源文件的 C++ 项目：

1. 在项目根目录创建一个 `CMakeLists.txt` 文件，配置全局项目设置。
2. 在 `src/` 目录创建子目录的 `CMakeLists.txt` 文件，自动查找 `.cpp` 文件。
3. 使用 `file(GLOB ...)` 或 `file(GLOB_RECURSE ...)` 自动查找源文件。
4. 配置头文件搜索路径，确保程序能找到必要的头文件。
5. 使用 `cmake` 和 `make` 命令构建项目，并生成可执行文件。

这样，你就能高效地管理和构建多个源文件的 C++ 项目，同时也能确保项目结构清晰，易于扩展和维护。
