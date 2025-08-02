---
title: 'Quick-GPT: A fast and simple CLI LLM calling function'
date: 2025-07-13 01:07:15
index_img: /img/cover/quick-gpt.jpg
excerpt: "My Github project: Quick-GPT, a fast and user-friendly CLI LLM calling command using just one command!"
categories:
  - Project
tags:
  - CLI
  - AI API Calling
  - Python
---

# Quick-GPT: A Fast and Easy-to-use CLI command

{% note primary %}

All the code is open-sourced in [GitHub](https://github.com/xiyuanyang-code/Quick-GPT).

{% endnote %}

# README

A fast, user-friendly CLI tool for multi-turn conversations with OpenAI and Google Gemini models.  
Supports conversation history, system prompts, and easy model switching.

**Run powerful AI conversations from your terminal in a single command!**


## Installation

```bash
git clone https://github.com/xiyuanyang-code/Quick-GPT.git
cd Quick-GPT
pip install .
```


## API Key Configuration

Quick-GPT requires API keys for OpenAI and/or Google Gemini.  
You can provide them via a `.env` file in your project root, or as environment variables. You can also export these variables in your shell profile (`~/.bashrc`, `~/.zshrc`, etc).

**Example `.env` file:**
```env
# OpenAI
OPENAI_API_KEY=sk-xxxxxx
BASE_URL="your api key here"

# Google Gemini
GEMINI_API_KEY=your-gemini-api-key
GOOGLE_GEMINI_BASE_URL=""your api key here"
```


## Quick Start

After installation and API configuration, use the CLI as follows:

```bash
quick-gpt "Your question here"
```

**Example:**
```bash
quick-gpt "Tell me something about Hexo Blog"
```

- The tool auto-selects the model based on your configuration and input.
- Conversation history is saved in the `history/` directory with a timestamped filename.
- View the full conversation history after each session.
- Use `@exit` or `@quit` to quit, and `@history` to display previous outputs.

---

## Advanced Usage

**Switch models:**
```bash
quick-gpt "Hello world" --model_name "gpt-4o-mini"
```

**Add a system prompt:**
```bash
quick-gpt "Hello world" --sys "You should only output in English"
```

- You can also customize system prompts and initial messages in your Python code if using Quick-GPT as a module.

# Code

这是笔者第一次尝试**中小型项目的多文件编程** (对于Python来说)。

项目结构：

```bash
git ls-files --cached --others --exclude-standard
.gitignore          
README.md
pyproject.toml
quick_gpt/__init__.py   
quick_gpt/history.py       # 历史记录模块，包括创建json历史记录，写入和读取历史数据
quick_gpt/main.py          # main函数，解析命令行参数，为主入口 
quick_gpt/quick_gpt.py     # 设计核心类 Quick-GPT，负责处理init和单轮对话等核心逻辑
quick_gpt/response.py      # 对不同的SDK设置对话封装函数，目前支持OpenAI和Gemini
quick_gpt/utils.py         # 相关小工具
```

{% note info %}

Code is available on GitHub!
https://github.com/xiyuanyang-code/Quick-GPT

{% endnote %}

## Todo List

这个项目本身的设计是因为笔者每次在输入命令行命令的时候（**现在真的是离不开命令行了**），总是需要一些References的AI辅助，尝试过cursor的命令行帮手，很好用，以及Gemini CLI，但是**都觉得太花里胡哨**，并且**响应时间太长**，又是我只是不想切屏去查资料，一个开箱即用的AI CLI助手是我迫切需要的。

后续项目可能不会再添加新功能，因为这个项目就是我正好需要的功能，再加反而难以维护。但是笔者在八月份会将开发的重心转移到**设计模式的学习**和**BLOGing-AI**的开发上。

