---
title: My Typst Note
date: 2025-10-05 17:04:51
index_img: /img/cover/cicd-typst.jpg
excerpt: Automated Note CI/CD Build with a Custom Rust Crate for Efficient Command-Line Note Management
categories:
  - Efficient Tools
tags: 
  - Announcement
  - Finished
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# My Typst Note

{% note primary %}

Github Source URL: [https://github.com/xiyuanyang-code/My-Typst-Note](https://github.com/xiyuanyang-code/My-Typst-Note)

{% endnote %}

{% note primary %}

Final Download URL for all my lecture notes: [https://github.com/xiyuanyang-code/My-Typst-Note/releases/latest](https://github.com/xiyuanyang-code/My-Typst-Note/releases/latest)

{% endnote %}


## Typst

[Typst Official Docs](https://typst.app/)

天下苦 LaTeX 久矣！

Typst 作为新型的一种文档编译语言，兼具编译能力和编译性能，于我而言，现在几乎全面转战 Typst 的原因在于：

- 作为 Rust 项目简单的安装和命令行接口，免去了 TeX 复杂和庞大的命令行安装流程
- 简单简洁的语法和语法糖
  - 数学公式和 LaTeX 保持基本一致，可移植性高
  - 编程语言更简洁，没有那么多的转移字符
    - 可读性更高
    - 在 Typst 中数学公式作为一种编程语言实现，不同的符号作为变量，一些特殊的数学函数和标记也作为函数出现
- 快速，简洁，闪电般的本地编译速度
- Vscode 给力的插件支持和 inline suggestions
  - 支持实时查看和快速 PDF 编译
  - 支持语法高亮和 inline suggestions
- 第三方社区支持：
  - （相对还不错的）社区支持
  - 该点并非个人做笔记使用的痛点。
- 和 Markdown 相比支持更多功能，例如插入丰富格式的表格、图片等等

在 Overleaf 一再缩减编译时间的情况下，LaTeX 已经不再是个人笔记的首选场景了！

## Typst Note CLI

A simple CLI tool for quickly generating formatted notes written in typst and deploying them in PDF.

```
.
├── Cargo.lock
├── Cargo.toml
├── LectureNote
│   ├── AlgorithmsNote
│   │   ├── algorithm.typ
│   │   ├── bowling.png
│   │   ├── dp.typ
│   │   ├── leetcode.typ
│   │   └── tex_note
│   │       └── code_demo.tex
│   ├── MachineLearning
│   │   ├── AI1811.typ
│   │   └── ML
│   │       ├── Dimensionality_reduction.md
│   │       ├── Model_selection.md
│   │       ├── Parameter_Estimation.md
│   │       ├── classification.md
│   │       ├── clustering.md
│   │       ├── intro.md
│   │       └── regression.md
│   ├── NumericalAnalysis
│   │   ├── AI1807.typ
│   │   └── src
│   │       └── demo.py
│   ├── Probability
│   │   └── MATH1207.typ
│   └── SoftwareEngineering
│       └── CS3604.typ
├── README.md
├── images
│   └── demo.png
├── scripts
│   ├── install.sh
│   └── refresh.py
├── src
│   └── main.rs
└── template
    ├── backup.typ
    ├── empty.typ
    ├── format.typ
    └── image.png
```

### Template

We use [dvdtyp](https://github.com/DVDTSB/dvdtyp) as the template for notes. Really awesome template!

> Several modifications based on personal needs are made.

### Installation

First, ensure you have [Rust](https://www.rust-lang.org/tools/install) and [Cargo](https://doc.rust-lang.org/cargo/) installed.

For Linux system or WSL, just run the quick scripts below to install all the dependencies.

```bash
bash scripts/install.sh
```


Firstly, install typst-cli using cargo:

```bash
cargo install typst-cli
```

From the project root, build the self-contained executable:

```bash
cargo build --release
```

Finally, install the executable locally using `cargo install --path .`:

```bash
# install locally
cargo install --path .
```

Then restart your terminal with `source ~/.zshrc`, etc.

### Usage

To create a new note, run the following command, replacing `"<note title>"` with your desired title:

```bash
note new "<note title>"
```

This will create a new `<note title>.typ` file in the current directory.

To compile a note into a PDF, run:

```bash
note pdf "<note title>"
```

For example:

```bash
note new algebra
# it will create a new algebra.typ file in current working directory

note pdf algebra
# compile algebra.typ into algebra.pdf
```

## My CI&CD Workflow

### MakeFile 

我们提供了编写好的 MakeFile，实现自动化的编译构建。

```MakeFile
TYPST_ROOT ?= "/home/xiyuanyang/Note/TypstNote/"
SOURCE_FILE ?= /home/xiyuanyang/Note/TypstNote/LectureNote/AlgorithmsNote/algorithm.typ
OUTPUT_FILE ?= ./result/algorithm.pdf

TYPST_CMD := typst compile --root $(TYPST_ROOT)

.PHONY: all
all: $(OUTPUT_FILE)

$(OUTPUT_FILE): $(SOURCE_FILE)
	$(TYPST_CMD) $< $@
```

### Python Compile Scripts

- 增加了自动检查冗余 PDF 文件的功能
- 递归检索 `LectureNote` 文件夹下的所有 `.typ` 文件

```python
import os
import subprocess
from tqdm import tqdm

TARGET_FILE_PATH = os.path.abspath("./LectureNote")
OUTPUT_DIR = "./result"  # Define the output directory once


def get_all_notes():
    """
    Recursively finds all Typst source files (.typ) in the TARGET_FILE_PATH.
    """
    final_results = []
    for root, dirs, files in os.walk(TARGET_FILE_PATH, topdown=True):
        for file_name in files:
            if file_name.lower().endswith(".typ"):
                full_path = os.path.join(root, file_name)
                final_results.append(full_path)
    return final_results


def generate_output_path(input_path):
    """
    Generates the expected PDF output path from a Typst source file path.
    """
    # Use the defined OUTPUT_DIR
    file_name = os.path.basename(input_path)
    base_name, _ = os.path.splitext(file_name)
    base_name = f"{base_name}.pdf"
    return os.path.join(OUTPUT_DIR, base_name)


def generate_source_path(output_path):
    """
    Generates the expected Typst source path from a PDF output file path.
    This assumes a flat structure for source files relative to TARGET_FILE_PATH
    if the original Typst file was found in the top level. Since get_all_notes
    scans recursively, we need to search the entire source tree for a match.
    """
    pdf_name = os.path.basename(output_path)
    typ_name = os.path.splitext(pdf_name)[0] + ".typ"

    # Search for the source file recursively
    for root, _, files in os.walk(TARGET_FILE_PATH):
        if typ_name in files:
            return os.path.join(root, typ_name)

    # Return None if the file isn't found
    return None


def cleanup_orphaned_pdfs():
    """
    Scans the output directory and deletes any PDF files for which
    the corresponding Typst source file no longer exists.
    """
    print(f"Scanning for orphaned PDFs in '{OUTPUT_DIR}'...")

    if not os.path.exists(OUTPUT_DIR):
        print(f"Output directory '{OUTPUT_DIR}' does not exist. Skipping cleanup.")
        return

    # List all files in the result directory
    output_files = [
        os.path.join(OUTPUT_DIR, f)
        for f in os.listdir(OUTPUT_DIR)
        if f.lower().endswith(".pdf")
    ]

    orphaned_count = 0

    for output_pdf_path in tqdm(output_files, desc="Checking PDFs"):
        # We need to check if the corresponding .typ file exists anywhere
        # under TARGET_FILE_PATH.
        source_typ_path = generate_source_path(output_pdf_path)

        # Check if the generated source path is None, meaning the file wasn't found
        if source_typ_path is None:
            # The source file is missing, so this PDF is orphaned.
            print(f"-> Deleting orphaned PDF: {output_pdf_path}")
            try:
                os.remove(output_pdf_path)
                orphaned_count += 1
            except OSError as e:
                print(f"Error deleting file {output_pdf_path}: {e}")

    print(f"Cleanup complete. Deleted {orphaned_count} orphaned PDF files.")


def compile(input_files):
    """
    Compiles the list of Typst input files into PDFs using the 'make' command.
    """
    # Ensure the output directory exists before compiling
    os.makedirs(OUTPUT_DIR, exist_ok=True)

    length = len(input_files)

    for input_file in tqdm(input_files, total=length, desc="Compiling"):
        output_path = generate_output_path(input_file)
        root_dir = "/home/xiyuanyang/Note/TypstNote/"
        command_list = [
            "make",
            f"TYPST_ROOT={root_dir}",
            f"SOURCE_FILE={input_file}",
            f"OUTPUT_FILE={output_path}",
        ]

        try:
            subprocess.run(
                command_list, capture_output=True, text=True, check=True, shell=False
            )
        except subprocess.CalledProcessError as e:
            print(f"\nError compiling {input_file}:")
            print(f"STDOUT: {e.stdout}")
            print(f"STDERR: {e.stderr}")
            # Continue to the next file even if one fails
            continue


if __name__ == "__main__":
    cleanup_orphaned_pdfs()
    compile(input_files=get_all_notes())
```

### CI/CD in one command

最终，复杂的 CI&CD 构建流程如下：

- 提交源文件的相关更新
- 检查冗余文件并更新编译
- 提交更新后的 PDF 文件
- 更新 tag 并构建 CI & CD 流程
- push tag 形成最新的 release

```bash
#!/bin/bash

# commit changes
LATEST_TIMESTAMP=$(git log -1 --format=%cd)
git add .
git commit -m "Auto Commit: $LATEST_TIMESTAMP"
git push

# compile and make
python scripts/update_notes/compile.py

# generate new tags
LATEST_TAG=$(git describe --tags --abbrev=0 2>/dev/null || echo "v0.0.0")
VERSION_NO_V=$(echo "$LATEST_TAG" | sed 's/^v//')
IFS='.' read -r -a VERSION_ARRAY <<< "$VERSION_NO_V"

MAJOR=${VERSION_ARRAY[0]}
MINOR=${VERSION_ARRAY[1]}
PATCH=${VERSION_ARRAY[2]}
NEXT_PATCH=$((PATCH + 1))
NEW_TAG="v$MAJOR.$MINOR.$NEXT_PATCH"

git add .
git commit -m "Auto Commit: $LATEST_TIMESTAMP"
git push

# pushing new tags
git tag "$NEW_TAG"
git push origin "$NEW_TAG"
```


## Todo List in the future

- 探索更多插件支持库
- 构建 Typst Math Memo
- 构建更复杂的 CI & CD 流程