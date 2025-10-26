---
title: Python Scraping Tutorial
date: 2025-07-24 21:01:23
index_img: /img/cover/scraping.jpg
excerpt: This blog introduces the basic tutorial for scraping, including the basic usage of bs4 and requests module, and a small demo for scraping contents for other's technical blog.
categories:
  - Code
  - Python
tags:
  - Tutorial
  - Finished
  - scraping
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Tutorial for Scraping

## Beautiful Soup for parsing HTML file

Beautiful Soup is a Python library for pulling data out of HTML and XML files. It works with your favorite parser to provide idiomatic ways of navigating, searching, and modifying the parse tree. It commonly saves programmers hours or days of work.

- XML (Extensible Markup Language)

```xml
<?xml version="1.0" encoding="UTF-8"?>
<bookstore>
  <book category="cooking">
    <title lang="en">Everyday Italian</title>
    <author>Giada De Laurentiis</author>
    <year>2005</year>
    <price>30.00</price>
  </book>
  <book category="children">
    <title lang="en">Harry Potter</title>
    <author>J.K. Rowling</author>
    <year>2005</year>
    <price>29.99</price>
  </book>
</bookstore>
```

- HTML (HyperText Markup Language)

```HTML
<!DOCTYPE html>
<html>
<head>
  <title>我的书店</title>
  <style>
    body { font-family: Arial, sans-serif; margin: 20px; }
    h1 { color: #333; }
    .book { border: 1px solid #ccc; padding: 15px; margin-bottom: 10px; }
    .book strong { color: #007bff; }
  </style>
</head>
<body>

  <h1>我的书店</h1>

  <div class="book">
    <h2>书名：Everyday Italian (英文)</h2>
    <p><strong>作者：</strong> Giada De Laurentiis</p>
    <p><strong>出版年份：</strong> 2005</p>
    <p><strong>价格：</strong> $30.00</p>
  </div>

  <div class="book">
    <h2>书名：Harry Potter (英文)</h2>
    <p><strong>作者：</strong> J.K. Rowling</p>
    <p><strong>出版年份：</strong> 2005</p>
    <p><strong>价格：</strong> $29.99</p>
  </div>

</body>
</html>
```

### Quick Setup

Before using `bs4` to scrap, let us start up quickly to parse an HTML file locally!

```python
file_path = "./demo.html"

with open(file_path, "r") as file:
    html_content: str = file.read()
    file.close()
from bs4 import BeautifulSoup

# accept an html string and parse it
soup = BeautifulSoup(html_content, 'html.parser')

print(soup.prettify())
# Prints the values to a stream, or to sys.stdout by default.
```

Then, you can almost do whatever you can do in `JavaScripts`!

```python
print(f"The tile is {soup.title}")
# The tile is <title>The Dormouse's story</title>

# to make it prettier
print(f"The tile is {soup.title.get_text()}")

# equivalent to this:
print(soup.find("title"))
print(soup.find("title").get_text())

# find all content
result = list(soup.find_all("p"))
print(len(result))
print(result[1].get_text())

# get all the text
print(soup.get_text())
```

### Parser

| Parser               | Typical usage                                                | Advantages                                                   | Disadvantages                                      |
| :------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- | :------------------------------------------------- |
| Python's html.parser | `BeautifulSoup(markup, "html.parser")`                       | - Batteries included - Decent speed                          | - Not as fast as lxml, less lenient than html5lib. |
| lxml's HTML parser   | `BeautifulSoup(markup, "lxml")`                              | - Very fast                                                  | - External C dependency                            |
| lxml's XML parser    | `BeautifulSoup(markup, "lxml-xml")` `BeautifulSoup(markup, "xml")` | - Very fast - The only currently supported XML parser        | - External C dependency                            |
| html5lib             | `BeautifulSoup(markup, "html5lib")`                          | - Extremely lenient - Parses pages the same way a web browser does - Creates valid HTML5 | - Very slow - External Python dependency           |

### Kind of Objects

#### `tag`

- `tag`: The same in JavaScripts. A Tag object corresponds to an XML or HTML tag in the original document.

```python
with open(file_path, "r") as file:
    soup_2 = BeautifulSoup(file, "html.parser")

tag = soup_2.find("p")

# several info messages for this tag
print(tag)
print(type(tag))
print(tag.name)

# attrs
# it will return a dict
print(tag.attrs)
print(tag.attrs.keys())

# when not found, this will throw an error
print(tag["id"])
print(tag["class"])

# you can change the settings of the tag!
tag.name = "changed_tag_name"
print(tag)

# changing the attrs
tag['id'] = 'verybold'
tag['another-attribute'] = 1
print(tag.attrs)
print(soup_2.prettify())

# print(soup_2)
# # write back to the original file
# with open(file_path, "w", encoding="utf-8") as file:
#     # write the new str
#     file.write(soup_2.prettify())
```

#### class `NavigableString`

A tag can contain strings as pieces of text. Beautiful Soup uses the `NavigableString` class to contain these pieces of text:

```python
print(tag.string)
soup_3 = BeautifulSoup("<p>Welcome<b>Hello</b>text</p>", "html.parser")

tag_3 = soup_3.find("b")
print(tag_3.string)

tag_3.string.replace_with("test")

print(tag_3)
```

#### Navigating the DOM Tree

- `.children`: return a generator for all the child of this element.
- `.descendants`: iterate over all of a tag's children, recursively: its direct children, the children of its direct children.
- `.string` and `.content`:
	- If a tag has only one child, and that child is a `NavigableString`, the child is made available as `.string`.

```python
from bs4 import BeautifulSoup, NavigableString

# open the html file
with open("./demo2.html", "r") as file:
    soup_new = BeautifulSoup(file, "lxml")

# find all the children

tag_installation = soup_new.find(id="installation-section")
print(tag_installation)

# for child in tag_installation.children:
#     print(child)

# it is a generator!
# print(type(tag_installation.children))

# you can transform that into a list
child_list = list(tag_installation.children)
print(len(child_list))
print(r"Numbers of \n: ", child_list.count("\n")) 

# if you want to ignore '\n':
filtered_string = [child for child in tag_installation.children if not (isinstance(child, NavigableString) and child.strip() == "")]
print(len(filtered_string))
print(tag_installation.string)

# split into array, the same as list(tag_installation.children)
print(tag_installation.contents)

filtered = list(filter(lambda x: x != "\n", tag_installation.contents))
print(filtered)

for content in filtered:
    print(content.string)


# or you can use the descendants methods
all_des = list(filter(lambda x: x != "\n", tag_installation.descendants))
print(all_des)
for content in all_des:
    print(content)
    print(content.string)
```

## Using `requests` to see a website's HTML

The key to scrapping files is **understanding files' structure**.

```python
import requests
from bs4 import BeautifulSoup

response = requests.get("https://baidu.com")
response.encoding = "utf-8"

get_text = response.text
soup_baidu = BeautifulSoup(get_text)

print(soup_baidu.prettify())
```

### A Small Demo

We will trying to get some information about my own github channel!

```python
import requests
from bs4 import BeautifulSoup

URL = "https://github.com/xiyuanyang-code"
response = requests.get(URL)
response.encoding = "utf-8"

# print(BeautifulSoup(response.text).prettify())

soup_github = BeautifulSoup(response.text)
print(soup_github.title)
print(soup_github.get_text())
filtered = list(filter(lambda x: x != "\n", soup_github))
print(filtered)
```

Let's see another demo? How will you paste content on a web? Maybe you can use your mouse to scroll down or press `Ctrl + A` command and copy it. Now you can use scrapping to finish this!

We will use Lilian Weng's Blog [How we think](https://lilianweng.github.io/posts/2025-05-01-thinking/) as a demo.

```python
URL_2 = "https://lilianweng.github.io/posts/2025-05-01-thinking/"
response = requests.get("https://lilianweng.github.io/posts/2025-05-01-thinking/")
response.encoding = "utf-8"

# print(BeautifulSoup(response.text).prettify())

soup_lilian = BeautifulSoup(response.text)
print(soup_lilian.find("title").get_text())
paras = soup_lilian.find_all("p")

for para in paras:
    print(para.get_text())
```

After that, you can split the string and get the article!

```python
content_string = [para.get_text().strip() for para in paras]
final_string = "\n".join(content_string)

print(final_string)

# write into file

with open("demo.md", "w") as file:
    file.write(final_string)
    file.close()
refs = soup_lilian.find_all("a")
for ref in refs:
    print(ref.attrs["href"])
    # attrs is a dict
```