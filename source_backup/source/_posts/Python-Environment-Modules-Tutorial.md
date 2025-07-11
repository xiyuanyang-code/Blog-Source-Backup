---
title: Python Environment Modules Tutorial
date: 2025-05-03 14:06:14
excerpt: An advanced tutorial focusing on several modules of environment management in Python.
index_img: /img/cover/os.png
categories:
  - Code
  - Python
tags:
  - Tutorial
  - Environment
  - Python
---

# Several Modules in Python

# `os` tutorial for python

In [ ]:

```python
import os
```

`os` is a python module for using the **operating system** dependent functionality.

## Use `os` module to query relevant file information.

In [ ]:

```python
print(os.name)
# will return posix (Portable Operating System Interface) for unix-like system like macos and linux 
```

In [ ]:

```python
print(type(os.environ))

# print all the environment settings
print(os.environ)
```

In [ ]:

```python
os.chdir("/GPFS/rhome/xiyuanyang/python_basic/Python-environment-modules-tutorial")

# get the file location
print(os.getcwd())

# get the environment
print(os.getenv("BASE_URL"))

# change the location
os.chdir(path="../")
print(os.getcwd())
```

## File operating for `os`

You can use the `os` library to perform basic file operations, including deleting and creating files and folders.

- `os.chdir()`: change the current directory.
- `os.makedirs()`: create a new directory.
- `os.remove()` & `os.removedirs()` : remove the file or the directory.
- `os.rename()`: rename the directory.
- `open(file_name, 'a').close()` can be used to create a new file.

In [ ]:

```python
os.chdir("/GPFS/rhome/xiyuanyang/python_basic/Python-environment-modules-tutorial")

# create a directory
if os.path.exists("test"):
    print("test folder has been created!")
else:
    os.makedirs("test")

file_name = "example.txt"
if os.path.exists(file_name):
    print(f"{file_name} exists")

with open(file_name, 'w') as file:
    file.write("This is an example file.")

# remove files and folders
os.remove("example.txt")
os.removedirs("test")

# rename
if not os.path.exists("2.txt"):
    open("2.txt", 'a').close() 

if os.path.exists("1.txt"):
    os.remove("1.txt")

os.rename(src="2.txt", dst="1.txt")
```

Well, actually if you are familiar with bash commands, there is no need for you to learn these again!

In [ ]:

```python
os.system("echo 1")
os.system("pwd")
os.system("mkdir test")
os.system("ls")
os.system("rm -rf test")
```

## `os.walk` and `os.path`

Very important! Most frequently used!

### `os.walk()`

If you want to walk through all the files and directories in a certain directory, you can use `os.walk()` to traverse.

In [ ]:

```python
help(os.walk)
```

In [ ]:

```python
# Traverse current directory and subdirectories
for root, dirs, files in os.walk('.'):
    print(f"Current directory: {root}")
    print(f"Subdirectories: {dirs}")
    print(f"Files: {files}")
    print("-" * 40)
```

### `os.path`

In [ ]:

```python
# path joining: it will automatically combine the two adddress together.
path_1 = "/GPFS/rhome/xiyuanyang/python_basic/"
path_2 = "Python-environment-modules-tutorial/helloworld.txt"
new_path = os.path.join(path_1, path_2)
print(new_path)

# path split
dir, file = os.path.split(new_path)
print(dir, file)

assert os.path.isdir(dir) and os.path.isfile(file)

# path exists
open(new_path, "a").close()
assert os.path.exists(new_path)
print(f"size: {os.path.getsize(new_path)}")

# print all kinds of names
print(new_path)
print(os.path.basename(new_path))
print(os.path.dirname(new_path))
```

Now, you can combine `os.walk()` and `os.path()` together to implement a small `grep` command!

In [ ]:

```python
# Find files with specific extension
def find_files(extension, search_path):
    for root, dirs, files in os.walk(search_path):
        # files is a list!
        for file in files:
            print(f"root: {root}")
            print(f"dirs: {dirs}")
            print(f"file: {file}")
            print(f"files: {files}")
            if file.endswith(extension):
                full_path = os.path.join(root, file)
                print(f"Found file: {full_path}")
                print(f"Size: {os.path.getsize(full_path)} bytes")

find_files('.ipynb', '.')
```

## `sys`

## File Management
