---
title: Math Integral Cheatsheet
date: 2025-05-23 14:43:19
index_img: /img/cover/int.jpg
excerpt: Integral cheatsheet for poor freshman.
mermaid: true
categories:
  - Project
tags:
  - Integral Cheatsheet
  - latex
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

{% note primary %}

Github Repo: [repo](https://github.com/xiyuanyang-code/Integral-CheatSheet)

{% endnote %}

# Integral CheatSheet

This project provides a web-based cheat sheet for common integral formulas. All formulas are maintained in LaTeX, automatically converted to JSON, and rendered beautifully on a web page using MathJax.

## Project Structure

```plaintext
.
├── README.md				 # Project documentation
├── index.html				# Main web page displaying the formulas
├── main.tex				# LaTeX source file with all integral formulas
└── src
    ├── get_json.py			# Python script to extract formulas into JSON
    ├── integrals.json		 # Generated JSON file with formulas and notes
    └── main.pdf
```

## Usage

### Quickly Setup

- Using the webpage: [Integral Cheatsheet](https://xiyuanyang-code.github.io/Integral-CheatSheet/)
- `git clone` all the source code to get the `main.tex` or the `src/main.pdf`
- Download [main.pdf](https://github.com/xiyuanyang-code/Integral-CheatSheet/releases/download/v1.1.0/main.pdf) directly.

### Using Overleaf

Download [files.zip](https://github.com/xiyuanyang-code/Integral-CheatSheet/releases/download/v1.1.0/files.zip) from the release page and drag the zip file to overleaf.

### Want to build your own?

Fork this repo and make some modifications on your own!

1. Edit Integral Formulas

Edit or add formulas in `src/main.tex` using LaTeX, preferably within `enumerate` environments.

2. Generate JSON Data

Run the Python script to extract formulas from the LaTeX file and generate the JSON file:

```bash
cd src
python get_json.py
```

This will read `main.tex` and output `integrals.json`.

3. View in Browser

Open `index.html` in your browser to view all integral formulas with LaTeX rendering and notes.

> **Note:**  
> Make sure the path to `integrals.json` in `index.html` matches its actual location (default: `src/integrals.json`).

## Technology Stack

- **LaTeX:** For editing and maintaining formulas
- **Python:** For extracting and converting formulas to JSON
- **HTML + JavaScript:** For web display
- **[MathJax](https://www.mathjax.org/):** For rendering LaTeX formulas

## Contribution

Contributions are welcome! Feel free to add or correct formulas, improve scripts, or submit issues and pull requests.

In one word, **PR Welcome!😍**
