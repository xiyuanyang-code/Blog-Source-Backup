---
title: Vim-tutorial
date: 2025-01-23 12:59:07
index_img: /img/cover/vim.jpg
excerpt: This guide covers Vim's essential commands, modes, and shortcuts for efficient text editing, helping beginners and advanced users master its core features.
categories:
  - Efficient Tools
  - Missing Semesters
tags:
  - Vim
  - tutorial
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Vim Basic Learning Notes

The Learning curve for vim is too damn steep! I have tried several times trying to get used to using vim. :cry:

## Abstract

This document provides a comprehensive guide to the **basic functionalities of Vim**, a powerful text editor. It covers essential commands and modes, including Command Line, Normal Mode, Visual Mode, and Insert Mode. The guide also includes common shortcuts and tips for efficient text editing, such as moving within documents, searching, copying, cutting, and pasting text.

## Introduction

### What is Vim

**Vim** is a [free and open-source](https://en.wikipedia.org/wiki/Free_and_open-source), [screen-based text editor](https://en.wikipedia.org/wiki/Screen-based_text_editor) program.

Just like the powerful `Vscode`, which is the most popular code editor used in **GUI**, **vim** is the most powerful text editor used in **Command Line**.

We will not discuss how powerful Vim is in the text, because **Vim is a text editor**. You can also implement the same tasks without using vim, with the expense of sacrificing a huge amount of time.

Before diving deeply into Vim's world, we need to ensure the **core principle of Vim**: **make all the operations executed in the keyboard, not with the mouse.**

**Vim's interface is a programming language**!

> So you've better not type too slowly in English. You need to ensure typing letters is more efficient for you than moving mouse to everywhere!

## Modes in Vim

- normal mode
- insert mode (`i`)
- replace mode (`R`)
- visual mode (`v`)
  - visual line (`S-V`)
  - visual block (`C-V`)
- command line mode (`:`)

### Command Line Mode

Command mode can be entered by typing `:` in Normal mode. Your cursor will jump to the command line at the bottom of the screen upon pressing `:`. This mode has many functionalities, including opening, saving, and closing files, and [quitting Vim](https://twitter.com/iamdevloper/status/435555976687923200).

- `:q` quit (close window)
- `:w` save (“write”)
- `:wq`  save and quit
- `:e {name of file}` open file for editing
- `:ls` show open buffers
- `:help {topic}` open help
  - `:help :w` opens help for the `:w` command
  - `:help w` opens help for the `w` movement

### Normal Mode

#### Movement

In vim, you cannot simply click the mouse to certain positions to change where the cursor is. **In normal mode**, you can implement this using several keys to efficiently move your cursor to everywhere you want!

Based on **precision**, Vim's movement keys can be divided into different categories.

- **Movement in characters**
  
  - `hjkl` (left, down, up, right)

- **Movement in words**
  
  - `w` : to the next word
  - `b` : **Beginning of the word**
  - `e` : **End of the word**

- **Movement in lines**
  
  - `0` : (beginning of line) 
    
    - The combination `j+0` : move to the beginning of next line.
    - The combination `k+0` : move to the beginning of last line.
  
  - `Enter` : **The beginning of next line**
  
  - `^` : (first non-blank character)
  
  - `$` : (end of line)
  
  - `+` : The first non-blank character of next line
  
  - `{` and `}` : move to the next empty line
  
  - Line numbers: `:{number}<CR>` or `{number}G` (line {number})
    
    > `<CR>` means press the `Enter` key.
    
    - **`:{number}<CR>`**: Enter command mode, type the line number, and press `Enter` to jump to that line.
    - **`{number}G`**: Directly jump to the specified line number (e.g., `10G` goes to line 10).

- **Movement in screens**
  
  - `H` : (top of screen)
  - `M` : (middle of screen)
  - `L` : (bottom of screen)
  - **scrolling**
    - (I often use my touchpad)
    - `Ctrl-u` : scrolls the screen **up by half a page** while keeping the cursor in place.
    - `Ctrl-d` : scrolls the screen **down by half a page** while keeping the cursor in place.

- **Movement in files**
  
  - `gg` (beginning of file), `G` (end of file)
  - You can also use `]]` (top) and `[[` (end) commands.
  - **`%`**: Jump to the corresponding matching item (e.g., between `()`, `{}`, `[]`, etc.)
    - **This is quite useful while coding in Vim!**

#### Navigation

In Vim, the `f`, `t`, `F`, and `T` commands are used for **character-based navigation within the current line**. 

- **`f`**: Move to the next character.

- **`t`**: Move just before the next character.

- **`F`**: Move to the previous character.

- **`T`**: Move just after the previous character.

- **`,`**: Repeat the last find command in reverse.

- **`;`**: Repeat the last find command forward.
1. **Find Character on the Current Line**
   
   - **`f{character}`**:  
     
     - Moves the cursor **to** the next occurrence of `{character}` **on the current line**.  
     - Example: `fa` moves the cursor to the next `a` on the line.
   
   - **`t{character}`**:  
     
     - Moves the cursor **just before** (up to) the next occurrence of `{character}` on the current line.  
     - Example: `ta` moves the cursor to the character right before the next `a`.

2. **Find Character Backward on the Current Line**
   
   - **`F{character}`**:  
     
     - Moves the cursor **to** the previous occurrence of `{character}` on the current line (backward search).  
     - Example: `Fa` moves the cursor to the previous `a` on the line.
   
   - **`T{character}`**:  
     
     - Moves the cursor **just after** (up to) the previous occurrence of `{character}` on the current line (backward search).  
     - Example: `Ta` moves the cursor to the character right after the previous `a`.

3. **Navigating Between Matches**
   
   - **`,` (comma)**:  
     
     - Repeats the last `f`, `t`, `F`, or `T` command in the **reverse direction**.  
     - Example: If you used `fa` to move to the next `a`, pressing `,` will move to the previous `a`.
   
   - **`;` (semicolon)**:  
     
     - Repeats the last `f`, `t`, `F`, or `T` command in the **same direction**.  
     - Example: If you used `fa` to move to the next `a`, pressing `;` will move to the next `a` after that.

#### Searching

1. **Search Forward**
   
   - **`/{regex}<CR>`**:  
     - Searches forward in the file for the specified regular expression (`{regex}`).  
     - Example: `/hello<CR>` searches for the next occurrence of the word `hello`.  
     - After pressing `Enter`, the cursor jumps to the first match.

2. **Navigate Matches**
   
   - **`n`**:  
     
     - Moves the cursor to the **next match** of the search pattern.  
     - Example: After searching for `/hello`, pressing `n` jumps to the next occurrence of `hello`.
   
   - **`N`**:  
     
     - Moves the cursor to the **previous match** of the search pattern.  
     - Example: After searching for `/hello`, pressing `N` jumps to the previous occurrence of `hello`.

3. **Search Backward**
   
   - **`?{regex}<CR>`**:  
     - Searches backward in the file for the specified regular expression (`{regex}`).  
     - Example: `?hello<CR>` searches for the previous occurrence of the word `hello`.  
     - After pressing `Enter`, the cursor jumps to the first match.

4. **Additional Tips**
   
   - **Highlight Matches**:  
     - Use `:set hlsearch` to highlight all matches of the search pattern.  
       - This can be written into `~/.vimrc` file to become global settings.
     - Use `:nohlsearch` (or `:noh`) to temporarily disable highlighting.
   - **Case Sensitivity**:  
     - Use `:set ignorecase` (`:set ic`) to make searches case-insensitive.  
     - Use `:set smartcase` to make searches case-insensitive unless the search pattern contains uppercase letters.

#### Editing

- `d{motion}` : **DELETE**
  
  - `dw` is delete **word**
  - `d$` is delete to **end of line**
  - `d0` is delete to **beginning of line**.
  - `de` is delete to **end of word**
  - `db` is delete to **beginning of word**
  - `x` delete character (equal to `dl`)
  - `dd` delete the whole line.

- `r{motion}` : **REPLACE**
  
  - replace the letter with the given letter where the cursor lies.
  - e.g. `rf` will replace the character to f where the cursor lies.

- `u` : **UNDO**
  
  - `u` to undo, `<C-r>` to redo
    
    > `<C-r>` means press `Ctrl+r` and it will redo the operations.

- `~` flips the case of a character.

### Insert Mode

Here you are free to text in and modify the contents.

- `i` Enter Insert Mode
  - `a` is also useful for entering Inert Mode after the cursor. (For position)
- `o` / `O` insert line below / above (in Normal mode)
  - This command will **first insert a new line and then change the mode from normal mode to insert mode.**
- `c{motion}` : **CHANGE**
  - `cw` : equals to `dw` and then enter into **Insert Mode**.
  - **Others are the same!** 
    - `cw`, `c$`, `c0`, `ce`, `cb`, `cl`
  - `s` substitute character (equal to `cl`)
  - `cc` delete the whole line and then enter into insert mode.

### Visual Mode

**Visual mode** is used to select text, which are often combined with several text-editing command.

- Visual: `v`
  
  - Selects text at the character level.

- Visual Line: `V`
  
  - Selects entire lines of text.

- Visual Block: `Ctrl-v`
  
  - Selects a rectangular block of text, useful for editing tables or performing the same operation on multiple lines.

**Shrink Selection**: Press `o` to switch the cursor to the other end of the selection, then move the cursor to shrink the selection.

#### Copy and Paste

- `y` for copy
  - `yy` for copying for the whole line.
  - `yw` for copying the current word.
  - `y$` and `y0` is for the same syntax.
- `p` for paste
  - `p` : paste after the cursor.
  - `P` : paste before the cursor.

{% note primary %}

In Vim, the `y` command copies the selected content into Vim's **registers**, not the system clipboard. The `p` command pastes the content from Vim's registers at the cursor position.

**How to Achieve Compatibility Between Vim and the System Clipboard**

1. **Use the `+` or `*` Registers**:
   
   - Vim provides the `+` (system clipboard) and `*` (primary selection buffer) registers for interacting with the system clipboard.
   - When copying, use `"+y` or `"*y` to copy content to the system clipboard.
     - Example: `"+yy` copies the current line to the system clipboard.
   - When pasting, use `"+p` or `"*p` to paste content from the system clipboard.
     - Example: `"+p` pastes content from the system clipboard.

2. **Check if Vim Supports the System Clipboard**:
   
   - Run the following command to check if Vim supports the system clipboard:
     
     ```bash
     vim --version | grep clipboard
     ```
   
   - If the output includes `+clipboard`, Vim supports the system clipboard.
   
   - If not, you can install a version of Vim that supports the clipboard (e.g., `vim-gtk` or `vim-gnome`).
     
     ```bash
     sudo apt install vim-gtk
     ```

3. **Configure Vim to Use the System Clipboard by Default**:
   
   - Add the following line to your Vim configuration file (`~/.vimrc` or `~/.config/nvim/init.vim`):
     
     ```vim
     set clipboard=unnamedplus
     ```
     
     This makes `y` and `p` use the system clipboard (`+` register) by default.

{% endnote %}

## Advanced Usage

### Counts

You can combine nouns and verbs with a count, which will perform a given action a number of times.

- `3w` move 3 words forward
- `5j` move 5 lines down
- `7dw` delete 7 words

### Modifiers

You can use modifiers to change the meaning of a noun. Some modifiers are `i`, which means “inner” or “inside”, and `a`, which means “around”.

- **`i`** - "inner" or "inside", acts on the inside of brackets, quotes, etc.
  - `ci(` change inside parentheses.
  - `ci[` change inside square brackets.
  - `ci"` change inside double quotes.
  - `ci'` change inside single quotes.
- **`a`** - "around", includes the brackets, quotes, etc.
  - `ca(` change around parentheses.
  - `ca[` change around square brackets.
  - `ca"` change around double quotes.
  - `ca'` change around single quotes.

### Repeat

In Vim, the `.` command repeats the last change made, such as insertions, deletions, or replacements, making it a powerful tool for efficient editing. Additionally, `n .` repeats the last change `n` times, while `@:` repeats the last Ex command (e.g., `:s/foo/bar/g`), further enhancing productivity.

## Common Shortcuts

It's common for green hands to feelHere’s the table you requested, comparing common text editing shortcuts, their functions, and their corresponding Vim operations:

| **Shortcut** | **Function**              | **Vim Equivalent**          |
| ------------ | ------------------------- | --------------------------- |
| `Ctrl + C`   | Copy selected text        | `y` (yank, copy)            |
| `Ctrl + V`   | Paste text from clipboard | `p` (paste)                 |
| `Ctrl + A`   | Select all text           | `ggVG` (select entire file) |
| `Ctrl + X`   | Cut selected text         | `d` (delete, cut)           |
| `Ctrl + S`   | Save the current file     | `:w` (write, save file)     |

## References

[Missing Semester](https://missing.csail.mit.edu/2020/editors/) : It's enough for learning vim for the first time!

## Conclusion

**Vim is used for programming, yet Vim itself is a programming language!** As a developer, time is the most valuable stuff, just learn it and confront it!

> The text below is the Chinese translation.

# Vim 基础学习笔记

Vim 的学习曲线实在是太陡峭了！我已经尝试了好几次，试图习惯使用 Vim。:cry:

## 摘要

本文档提供了 **Vim 基本功能** 的全面指南，Vim 是一款功能强大的文本编辑器。它涵盖了基本命令和模式，包括命令行模式、普通模式、可视模式和插入模式。本指南还包括常见快捷键和高效文本编辑的技巧，例如在文档中移动、搜索、复制、剪切和粘贴文本。

## 介绍

### 什么是 Vim

**Vim** 是一个 [免费且开源的](https://en.wikipedia.org/wiki/Free_and_open-source) [基于屏幕的文本编辑器](https://en.wikipedia.org/wiki/Screen-based_text_editor) 程序。

就像强大的 `Vscode` 是 **GUI** 中最流行的代码编辑器一样，**Vim** 是 **命令行** 中最强大的文本编辑器。

我们不会在本文中讨论 Vim 有多强大，因为 **Vim 是一个文本编辑器**。你也可以在不使用 Vim 的情况下完成相同的任务，但会牺牲大量的时间。

在深入 Vim 的世界之前，我们需要确保 **Vim 的核心原则**：**所有操作都通过键盘执行，而不是鼠标**。

**Vim 的界面是一种编程语言**！

> 所以你最好在英文打字时不要太慢。你需要确保打字比用鼠标到处点击更高效！

## Vim 中的模式

- 普通模式
- 插入模式 (`i`)
- 替换模式 (`R`)
- 可视模式 (`v`)
  - 行可视模式 (`Shift + V`)
  - 块可视模式 (`Ctrl + V`)
- 命令行模式 (`:`)

### 命令行模式

在普通模式下按 `:` 可以进入命令行模式。按下 `:` 后，光标会跳转到屏幕底部的命令行。此模式具有许多功能，包括打开、保存和关闭文件，以及 [退出 Vim](https://twitter.com/iamdevloper/status/435555976687923200)。

- `:q` 退出（关闭窗口）
- `:w` 保存（“写入”）
- `:wq` 保存并退出
- `:e {文件名}` 打开文件进行编辑
- `:ls` 显示打开的缓冲区
- `:help {主题}` 打开帮助
  - `:help :w` 打开 `:w` 命令的帮助
  - `:help w` 打开 `w` 移动命令的帮助

### 普通模式

#### 移动

在 Vim 中，你不能简单地用鼠标点击某个位置来移动光标。**在普通模式下**，你可以使用几个键来高效地将光标移动到任何你想要的位置！

根据 **精确度**，Vim 的移动键可以分为不同的类别。

- **字符级移动**
  
  - `hjkl`（左、下、上、右）

- **单词级移动**
  
  - `w`：移动到下一个单词
  - `b`：移动到单词的开头
  - `e`：移动到单词的末尾

- **行级移动**
  
  - `0`：移动到行首
    
    - 组合键 `j+0`：移动到下一行的行首。
    - 组合键 `k+0`：移动到上一行的行首。
  
  - `Enter`：移动到下一行的行首
  
  - `^`：移动到第一个非空白字符
  
  - `$`：移动到行尾
  
  - `+`：移动到下一行的第一个非空白字符
  
  - `{` 和 `}`：移动到下一个空行
  
  - 行号：`:{number}<CR>` 或 `{number}G`（跳转到第 {number} 行）
    
    > `<CR>` 表示按下 `Enter` 键。
    
    - **`:{number}<CR>`**：进入命令行模式，输入行号，然后按 `Enter` 跳转到该行。
    - **`{number}G`**：直接跳转到指定行号（例如，`10G` 跳转到第 10 行）。

- **屏幕级移动**
  
  - `H`：移动到屏幕顶部
  - `M`：移动到屏幕中间
  - `L`：移动到屏幕底部
  - **滚动**
    - （我通常使用触摸板）
    - `Ctrl-u`：向上滚动半页，同时保持光标位置不变。
    - `Ctrl-d`：向下滚动半页，同时保持光标位置不变。

- **文件级移动**
  
  - `gg`（文件开头），`G`（文件末尾）
  - 你也可以使用 `]]`（顶部）和 `[[`（末尾）命令。
  - **`%`**：跳转到匹配的符号（例如 `()`、`{}`、`[]` 等）
    - **这在 Vim 中编程时非常有用！**

#### 导航

在 Vim 中，`f`、`t`、`F` 和 `T` 命令用于 **在当前行内进行基于字符的导航**。

- **`f`**：移动到下一个字符。

- **`t`**：移动到下一个字符的前一个位置。

- **`F`**：移动到上一个字符。

- **`T`**：移动到上一个字符的后一个位置。

- **`,`**：反向重复上一次查找命令。

- **`;`**：正向重复上一次查找命令。
1. **在当前行查找字符**
   
   - **`f{字符}`**：
     - 将光标移动到当前行中下一个 `{字符}` 的位置。
     - 示例：`fa` 将光标移动到下一个 `a`。
   - **`t{字符}`**：
     - 将光标移动到当前行中下一个 `{字符}` 的前一个位置。
     - 示例：`ta` 将光标移动到下一个 `a` 的前一个位置。

2. **在当前行反向查找字符**
   
   - **`F{字符}`**：
     - 将光标移动到当前行中上一个 `{字符}` 的位置（反向搜索）。
     - 示例：`Fa` 将光标移动到上一个 `a`。
   - **`T{字符}`**：
     - 将光标移动到当前行中上一个 `{字符}` 的后一个位置（反向搜索）。
     - 示例：`Ta` 将光标移动到上一个 `a` 的后一个位置。

3. **在匹配项之间导航**
   
   - **`,`（逗号）**：
     - 反向重复上一次 `f`、`t`、`F` 或 `T` 命令。
     - 示例：如果你使用 `fa` 移动到下一个 `a`，按 `,` 将移动到上一个 `a`。
   - **`;`（分号）**：
     - 正向重复上一次 `f`、`t`、`F` 或 `T` 命令。
     - 示例：如果你使用 `fa` 移动到下一个 `a`，按 `;` 将移动到下一个 `a`。

#### 搜索

1. **向前搜索**
   
   - **`/{正则表达式}<CR>`**：
     - 在文件中向前搜索指定的正则表达式（`{正则表达式}`）。
     - 示例：`/hello<CR>` 搜索下一个 `hello`。
     - 按下 `Enter` 后，光标跳转到第一个匹配项。

2. **导航匹配项**
   
   - **`n`**：
     - 将光标移动到搜索模式的下一个匹配项。
     - 示例：搜索 `/hello` 后，按 `n` 跳转到下一个 `hello`。
   - **`N`**：
     - 将光标移动到搜索模式的上一个匹配项。
     - 示例：搜索 `/hello` 后，按 `N` 跳转到上一个 `hello`。

3. **向后搜索**
   
   - **`?{正则表达式}<CR>`**：
     - 在文件中向后搜索指定的正则表达式（`{正则表达式}`）。
     - 示例：`?hello<CR>` 搜索上一个 `hello`。
     - 按下 `Enter` 后，光标跳转到第一个匹配项。

4. **额外提示**
   
   - **高亮匹配项**：
     - 使用 `:set hlsearch` 高亮所有匹配项。
       - 可以将其写入 `~/.vimrc` 文件以成为全局设置。
     - 使用 `:nohlsearch`（或 `:noh`）暂时禁用高亮。
   - **大小写敏感**：
     - 使用 `:set ignorecase`（`:set ic`）使搜索不区分大小写。
     - 使用 `:set smartcase` 使搜索不区分大小写，除非搜索模式包含大写字母。

#### 编辑

- `d{移动命令}`：**删除**
  
  - `dw` 删除一个单词
  - `d$` 删除到行尾
  - `d0` 删除到行首
  - `de` 删除到单词末尾
  - `db` 删除到单词开头
  - `x` 删除字符（等同于 `dl`）
  - `dd` 删除整行

- `r{移动命令}`：**替换**
  
  - 将光标处的字符替换为指定字符。
  - 例如：`rf` 将光标处的字符替换为 `f`。

- `u`：**撤销**
  
  - `u` 撤销，`Ctrl + r` 重做
    
    > `Ctrl + r` 表示按下 `Ctrl + r`，它将重做操作。

- `~` 切换字符的大小写

### 插入模式

在这里，你可以自由输入和修改内容。

- `i` 进入插入模式
  - `a` 也可以用于在光标后进入插入模式（用于定位）
- `o` / `O` 在下方 / 上方插入新行（在普通模式下）
  - 此命令会 **先插入新行，然后将模式从普通模式切换到插入模式**。
- `c{移动命令}`：**更改**
  - `cw`：等同于 `dw`，然后进入 **插入模式**。
  - **其他命令类似！**
    - `cw`、`c$`、`c0`、`ce`、`cb`、`cl`
  - `s` 替换字符（等同于 `cl`）
  - `cc` 删除整行并进入插入模式。

### 可视模式

**可视模式** 用于选择文本，通常与多个文本编辑命令结合使用。

- 可视模式：`v`
  
  - 以字符级别选择文本。

- 行可视模式：`V`
  
  - 选择整行文本。

- 块可视模式：`Ctrl + v`
  
  - 选择一个矩形文本块，适用于编辑表格或在多行上执行相同操作。

**缩小选择范围**：按 `o` 将光标切换到选择的另一端，然后移动光标以缩小选择范围。

#### 复制和粘贴

- `y` 复制
  - `yy` 复制整行
  - `yw` 复制当前单词
  - `y$` 和 `y0` 语法相同
- `p` 粘贴
  - `p`：粘贴到光标后
  - `P`：粘贴到光标前

{% note primary %}

在 Vim 中，`y` 命令将选中的内容复制到 Vim 的 **寄存器** 中，而不是系统剪贴板。`p` 命令将内容从 Vim 的寄存器粘贴到光标位置。

**如何实现 Vim 与系统剪贴板的兼容性**

1. **使用 `+` 或 `*` 寄存器**：
   
   - Vim 提供了 `+`（系统剪贴板）和 `*`（主选择缓冲区）寄存器，用于与系统剪贴板交互。
   - 复制时，使用 `"+y` 或 `"*y` 将内容复制到系统剪贴板。
     - 示例：`"+yy` 将当前行复制到系统剪贴板。
   - 粘贴时，使用 `"+p` 或 `"*p` 从系统剪贴板粘贴内容。
     - 示例：`"+p` 从系统剪贴板粘贴内容。

2. **检查 Vim 是否支持系统剪贴板**：
   
   - 运行以下命令检查 Vim 是否支持系统剪贴板：
     
     ```bash
     vim --version | grep clipboard
     ```
   
   - 如果输出包含 `+clipboard`，则 Vim 支持系统剪贴板。
   
   - 如果不支持，可以安装支持剪贴板的 Vim 版本（例如 `vim-gtk` 或 `vim-gnome`）。
     
     ```bash
     sudo apt install vim-gtk
     ```

3. **配置 Vim 默认使用系统剪贴板**：
   
   - 在 Vim 配置文件（`~/.vimrc` 或 `~/.config/nvim/init.vim`）中添加以下行：
     
     ```vim
     set clipboard=unnamedplus
     ```
     
     这将使 `y` 和 `p` 默认使用系统剪贴板（`+` 寄存器）。

{% endnote %}

## 高级用法

### 计数

你可以将名词和动词与计数结合使用，以执行给定次数的操作。

- `3w` 向前移动 3 个单词
- `5j` 向下移动 5 行
- `7dw` 删除 7 个单词

### 修饰符

你可以使用修饰符来改变名词的含义。一些修饰符包括 `i`，表示“内部”或“里面”，以及 `a`，表示“周围”。

- **`i`** - "内部" 或 "里面"，作用于括号、引号等的内部。
  - `ci(` 更改括号内的内容。
  - `ci[` 更改方括号内的内容。
  - `ci"` 更改双引号内的内容。
  - `ci'` 更改单引号内的内容。
- **`a`** - "周围"，包括括号、引号等。
  - `ca(` 更改括号及其内容。
  - `ca[` 更改方括号及其内容。
  - `ca"` 更改双引号及其内容。
  - `ca'` 更改单引号及其内容。

### 重复

在 Vim 中，`.` 命令重复上一次的更改操作，例如插入、删除或替换，使其成为高效编辑的强大工具。此外，`n .` 重复上一次更改 `n` 次，而 `@:` 重复上一次 Ex 命令（例如 `:s/foo/bar/g`），进一步提高生产力。

## 常见快捷键

新手常常感到困惑，以下表格比较了常见的文本编辑快捷键、它们的功能以及对应的 Vim 操作：

| **快捷键**    | **功能**    | **Vim 等效操作**   |
| ---------- | --------- | -------------- |
| `Ctrl + C` | 复制选中的文本   | `y`（复制）        |
| `Ctrl + V` | 粘贴剪贴板中的文本 | `p`（粘贴）        |
| `Ctrl + A` | 全选文本      | `ggVG`（选择整个文件） |
| `Ctrl + X` | 剪切选中的文本   | `d`（删除，剪切）     |
| `Ctrl + S` | 保存当前文件    | `:w`（写入，保存文件）  |
