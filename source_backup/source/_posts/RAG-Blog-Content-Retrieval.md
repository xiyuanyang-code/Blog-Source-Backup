---
title: RAG-Blog-Content-Retrieval
date: 2025-03-29 20:20:20
index_img: /img/cover/retrieval.jpg
excerpt: My Github project RAG Blog content retrieval
categories:
  - Project
tags:
  - RAG
  - Python
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# RAG Blog content retrieval

This is My github Project: [RAG Blog Content Retrieval](https://github.com/xiyuanyang-code/RAG_blog_content_retrieval).

The contents following is copied from the `README.md` file:

## 😊Introduction

This is a simple application for blog content retrieval implemented using RAG as the core technology.

## 🚀Installation

### Requirements

- Make sure you have **OpenAI API Key** and **LangSmith** API key!

#### Install Python requirements

After cloning the project, run:

```bash
pip install -r requirements.txt
```

#### Set up `.env` file

Set up a file named `.env` in your current directory:

```bash
touch .env
```

Fill in your keys and other information in the `.env` file, a demo is shown below:

```
API_KEY=123456
BASE_URL=123456
Langchain_api=123456
```

**Remember to replace your api key value into the key value**!

## 💓Usage

**Make sure you have passed through the `installation` section successfully.** 

### Run `Streamlit` code Locally

run following commands:

```bash
streamlit run ./main.py
```

Then you can see the webpages like this:

![demo](https://s1.imagehub.cc/images/2025/03/29/e78604608a8deae5a20b687fd9f65689.png)

Then you can run this locally in your computer, enjoy RAG now!

A demo:

![demo2](https://s1.imagehub.cc/images/2025/03/29/b736eb5e2afe5029a2c2f1b4011605ca.png)

You can freely search send queries regarding the blog passage and get answers.

### Run `Streamlit` demo online

I will finish it later...

But actually I don't recommend this for it is unsafe to input your secret key to the internet!

## 🤖Discussion

Just for fun, don't be serious.

If you have any issues, don't hesitate to contact the author.

## 👍Advertisement

My personal Blog: [Xiyuan Yang's Blog](https://xiyuanyang-code.github.io/)

# Code

I will demonstrate the codes here, which can be used like a demonstration for freshman to learn `bs4`, `streamlit`, `langchain` and **RAG**!

```python
'''
Author: Xiyuan Yang   xiyuan_yang@outlook.com
Date: 2025-03-29 15:17:02
LastEditors: Xiyuan Yang   xiyuan_yang@outlook.com
LastEditTime: 2025-03-29 16:26:27
FilePath: /RAG_try/RAG/main.py
Description: 
Do you code and make progress today?
Copyright (c) 2025 by Xiyuan Yang, All Rights Reserved. 
'''

# Several Requirements
import bs4
import dotenv
import openai
import os
import streamlit as st
from langchain import hub
from langchain.text_splitter import RecursiveCharacterTextSplitter
from langchain_community.document_loaders import WebBaseLoader
from langchain_community.vectorstores import Chroma
from langchain_core.output_parsers import StrOutputParser
from langchain_core.runnables import RunnablePassthrough
from langchain_openai import ChatOpenAI, OpenAIEmbeddings



# getting environments done
dotenv.load_dotenv()
openai.api_key = os.getenv("API_KEY")
openai.base_url = os.getenv("BASE_URL")

# langchain api
os.environ['LANGCHAIN_TRACING_V2'] = 'true'
os.environ['LANGCHAIN_ENDPOINT'] = 'https://api.smith.langchain.com'
os.environ['LANGCHAIN_API_KEY'] = os.getenv("Langchain_api")

# Define LLMs and prompts
LLM = ChatOpenAI(model_name="gpt-3.5-turbo", temperature=0,
                    api_key = openai.api_key,
                    base_url = openai.base_url)

prompt = hub.pull("rlm/rag-prompt")

# load documents:
def loader_documents(url):
    loader = WebBaseLoader(
    web_paths=(url,),

    )
    docs = loader.load()
    return docs



def text_splitter(docs):
    text_splitter = RecursiveCharacterTextSplitter(chunk_size=1000, chunk_overlap=200)
    splits = text_splitter.split_documents(docs)

    return splits

def embedding(splits):
    '''Embeding the vector'''
    vectorstore = Chroma.from_documents(documents=splits, 
                                        embedding=OpenAIEmbeddings(
                                            base_url="https://api.zhizengzeng.com/v1",
                                            api_key=os.environ["OPENAI_API_KEY"]
                                        ))

    retriever = vectorstore.as_retriever()

    return vectorstore, retriever

# Post-processing
def format_docs(docs):
    return "\n\n".join(doc.page_content for doc in docs)

def get_answer(retriever, prompt, llm, question):
    rag_chain = (
        {"context": retriever | format_docs, "question": RunnablePassthrough()}
        | prompt
        | llm
        | StrOutputParser()
    )

    answer = rag_chain.invoke(question)

    return answer 

def getstreamlit_UI(prompt, LLM):
    
    st.title("RAG-Based Blog System")
    st.subheader("Author: Xiyuan Yang (xiyuanyang-code)")
    st.session_state["Prompt"] = prompt
    st.session_state["LLM"] = LLM

    # Introduction Part
    st.write("## About this website")
    st.write("This website is my **RAG implementation** for searching and retrieving my own blog posts, you can see my own blog posts\
                and choose the blog url down here!")
    st.write("My Blog posts: [xiyuanyang-code](https://xiyuanyang-code.github.io)")

    # Choose website
    st.write("default url: https://xiyuanyang-code.github.io/posts/Algorithm-BinaryTree/")
    st.write("**Make sure your url is valid!**")
    url = st.text_input("Choose your website: ")




    default_url = "https://xiyuanyang-code.github.io/posts/Algorithm-BinaryTree/"
    if st.button("Scrape Blog Content"):
        with st.spinner("Scraping blog content..."):
            blog_content = loader_documents(url)
            if blog_content:
                st.success("Blog content scraped successfully!")
                st.session_state["blog_content"] = blog_content
            else:
                st.error("Failed to scrape blog content.",icon="🚨")
                st.write("Using the default url")
                blog_content = loader_documents(default_url)


    if "blog_content" in st.session_state:
        blog_content = st.session_state["blog_content"]
        if st.button("Process and Store Content"):
            with st.spinner("Processing and storing content..."):
                splits = text_splitter(blog_content)
                vectorstore, retriever = embedding(splits)
                st.session_state["vectorstore"] = vectorstore
                st.session_state["retriever"] = retriever
                st.success("Content processed and stored in Chroma!")


    if "vectorstore" in st.session_state:
        vectorstore = st.session_state["vectorstore"]
        retriever = st.session_state["retriever"]
        prompt = st.session_state["Prompt"]
        LLM = st.session_state["LLM"]

        query = st.text_input("Ask a question about the blog:")
        if query:
            with st.spinner("Generating answer..."):
                answer = get_answer(retriever=retriever, prompt=prompt, llm=LLM, question=query)
                st.write("**Answer:**")
                st.write(answer)
    
if __name__ == "__main__":
    getstreamlit_UI(prompt=prompt, LLM=LLM)
```

{% note primary %}

That is Python! You can use code of less than 150 lines top build a complex **RAG** system, generating a neat Web UI interface and implement all the needs you want! What about C++? Maybe a guessing number game...

But C++ is very important too. Just for en 

{% endnote %}
