---
title: RAG-tutorial
date: 2025-03-29 13:29:15
index_img: /img/cover/RAG.jpg
math: true
excerpt: Theoretical explanation and implementation of RAG for LLM uasge.
categories:
  - Artificial Intelligence
tags:
  - RAG
  - LLMs
  - Artificial Intelligence
  - Deep Learning
---


<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# Learn RAG from scratch!

Lecture Notes for This Youtube Class.[^1]

## Basic Knowledge

Retrieval-Augmented Generation (RAG) is a hybrid framework that combines the strengths of retrieval-based and generative models for natural language processing tasks. The basic principle of RAG involves two key components: **a retriever and a generator**. 

The retriever is responsible for fetching relevant documents or information from a large corpus or knowledge base, typically using dense vector representations to find the most pertinent data for a given query. This ensures that the model has access to accurate, up-to-date external information beyond its training data.

The generator, usually a pre-trained language model, then uses this retrieved information to produce coherent and contextually appropriate responses. By grounding its outputs in retrieved facts, RAG reduces hallucinations and improves factual accuracy compared to purely generative models.

Basic Steps:

- **Load documents using `bs4` using Crawler**.

- Split text using function of `RecursiveCharacterTextSplitter()` from `langchain.text_splitter`.

- **Embedding splits into vectors**, using `Chroma` from `langchain_community.vectorstores` and `OpenAIEmbeddings` and return the vector store retriever.

- Define RAG-chain (including **prompt** and **LLM**)

	```python
	# Chain
	rag_chain = (
	    {"context": retriever | format_docs, "question": RunnablePassthrough()}
	    | prompt
	    | llm
	    | StrOutputParser()
	)
	```

- Using the `invoke` function to get the generation text!

## Query-Translation

### Multi Query

**Intuition**: For a large problem, it is likely to encounter situations where "words fail to convey meaning," and if the answer to the problem is relatively complex, a simple RAG system may not achieve good results through direct vector comparison. Therefore, we can **decompose the problem and use parallel retrieval**.

How to make the splitting works? You can use **Prompt Engineering** and add templates:

```python
from langchain.prompts import ChatPromptTemplate

# Multi Query: Different Perspectives
template = """You are an AI language model assistant. Your task is to generate five 
different versions of the given user question to retrieve relevant documents from a vector 
database. By generating multiple perspectives on the user question, your goal is to help
the user overcome some of the limitations of the distance-based similarity search. 
Provide these alternative questions separated by newlines. Original question: {question}"""
prompt_perspectives = ChatPromptTemplate.from_template(template)

from langchain_core.output_parsers import StrOutputParser
from langchain_openai import ChatOpenAI

generate_queries = (
    prompt_perspectives 
    | ChatOpenAI(temperature=0,
                 api_key= openai.api_key,
                 base_url= BaseUrl) 
    | StrOutputParser() 
    | (lambda x: x.split("\n"))
)
```

In this code, we define the workchain of **`generate_queries`**, where we let OpenAI model to split question using prompts.

This is a demo of how AI generate:

```txt
1. How do LLM agents utilize task decomposition in their operations?
2. Can you explain the concept of task decomposition as applied to LLM agents?
3. What role does task decomposition play in the functioning of LLM agents?
4. How is task decomposition integrated into the workflow of LLM agents?
5. In what way does task decomposition enhance the capabilities of LLM agents?
```

```python
from langchain.load import dumps, loads

def get_unique_union(documents: list[list]):
    """ Unique union of retrieved docs """
    # Flatten list of lists, and convert each Document to string
    flattened_docs = [dumps(doc) for sublist in documents for doc in sublist]
    # Get unique documents
    unique_docs = list(set(flattened_docs))
    # Return
    return [loads(doc) for doc in unique_docs]

# Retrieve
question = "What is task decomposition for LLM agents?"
retrieval_chain = generate_queries | retriever.map() | get_unique_union
docs = retrieval_chain.invoke({"question":question})
len(docs)

print(docs)
```

The core chain of retrieval is **`retrieval_chain = generate_queries | retriever.map() | get_unique_union`**, where the input questions is first splitted into subquestions using `genegrate_queries` and through a **mapping** from the queries to the retriever (for every subquestion, search for the ans_vectors). Finally, using `get_unique_union` to get the unique answer.

```python
from operator import itemgetter
from langchain_openai import ChatOpenAI
from langchain_core.runnables import RunnablePassthrough

# RAG
template = """Answer the following question based on this context:

{context}

Question: {question}
"""

prompt = ChatPromptTemplate.from_template(template)

llm = ChatOpenAI(temperature=0,
                 api_key= openai.api_key,
                 base_url= BaseUrl)

final_rag_chain = (
    {"context": retrieval_chain, "question": itemgetter("question")} 
    | prompt
    | llm
    | StrOutputParser()
)

final_rag_chain.invoke({"question":question})
```

Finally, it is the **RAG** time! The `final_rag-chain` looks as follows:

```python
final_rag_chain = (
    {"context": retrieval_chain, "question": itemgetter("question")} 
    | prompt
    | llm
    | StrOutputParser()
)
```

We first use `retrieval_chain` defined above to get the related texts in the documents, add use the `itemgetter` to get the original problem. Then we feed these into a LLM with the predefined prompt. Finally, we use `StrOutputParser()` to get the final answer of LLM.

### RAG Fusions

**Intuition**: In Multi Query, RAG performs a crude deduplication and merging operation on the documents obtained for each subproblem, which is unreasonable because the weights corresponding to each problem are different, and the weights (similarities) of the answers obtained for each subproblem are also different. Therefore, the core idea of RAG fusion is to **use a multi-retrieval fusion method based on Reciprocal Rank Fusion (RRF)**, calculating the corresponding weights for each document before outputting them to obtain more reasonable results.

```python
from langchain.load import dumps, loads

def reciprocal_rank_fusion(results: list[list], k=60):
    """ Reciprocal_rank_fusion that takes multiple lists of ranked documents 
        and an optional parameter k used in the RRF formula """
    
    # Initialize a dictionary to hold fused scores for each unique document
    fused_scores = {}

    # Iterate through each list of ranked documents
    for docs in results:
        # Iterate through each document in the list, with its rank (position in the list)
        for rank, doc in enumerate(docs):
            # Convert the document to a string format to use as a key (assumes documents can be serialized to JSON)
            doc_str = dumps(doc)
            # If the document is not yet in the fused_scores dictionary, add it with an initial score of 0
            if doc_str not in fused_scores:
                fused_scores[doc_str] = 0
            # Retrieve the current score of the document, if any
            previous_score = fused_scores[doc_str]
            # Update the score of the document using the RRF formula: 1 / (rank + k)
            fused_scores[doc_str] += 1 / (rank + k)

    # Sort the documents based on their fused scores in descending order to get the final reranked results
    reranked_results = [
        (loads(doc), score)
        for doc, score in sorted(fused_scores.items(), key=lambda x: x[1], reverse=True)
    ]

    # Return the reranked results as a list of tuples, each containing the document and its fused score
    return reranked_results

retrieval_chain_rag_fusion = generate_queries | retriever.map() | reciprocal_rank_fusion
docs = retrieval_chain_rag_fusion.invoke({"question": question})
len(docs)
```

### Decomposition

Decomposition essentially uses a simpler concatenation method to hierarchically link problems together, similar to the step-by-step reasoning in Chain of Thought (CoT). It guides the model to decompose problems through prompts and, based on the solutions to previous problems, adds new retrievals on top of the previous problem's retrievals.

```python
from langchain.prompts import ChatPromptTemplate

# Decomposition
template = """You are a helpful assistant that generates multiple sub-questions related to an input question. \n
The goal is to break down the input into a set of sub-problems / sub-questions that can be answers in isolation. \n
Generate multiple search queries related to: {question} \n
Output (3 queries):"""
prompt_decomposition = ChatPromptTemplate.from_template(template)
```

Answer recursively

```python
# Prompt
template = """Here is the question you need to answer:

\n --- \n {question} \n --- \n

Here is any available background question + answer pairs:

\n --- \n {q_a_pairs} \n --- \n

Here is additional context relevant to the question: 

\n --- \n {context} \n --- \n

Use the above context and any background question + answer pairs to answer the question: \n {question}
"""

decomposition_prompt = ChatPromptTemplate.from_template(template)
```

> **Parallel structures are suitable for parallel problems, while hierarchical structures are appropriate for reasoning problems**. It is necessary to choose the appropriate RAG architecture based on different inquiries.

### Step Back

Step Back Prompting[^2] is a fancy way proposed by Google DeepMind, which uses Prompt Engineering to make the Queries more abstract in order to decrease noise.

![Step Back Prompting](https://s1.imagehub.cc/images/2025/04/04/b2538ca755acad7e0bae5e08cbd91fb0.png) 

It is quite similar to **Chain of Thought**, but it will focus more on the **abstraction** process, which will distill key questions from specific contexts to achieve better results in the RAG process.

Let's just see the prompt!

Moreover, it uses **few-shot learning** for learning template, which performs better than **zero-shot learning**.

```python
# Few Shot Examples
from langchain_core.prompts import ChatPromptTemplate, FewShotChatMessagePromptTemplate
examples = [
    {
        "input": "Could the members of The Police perform lawful arrests?",
        "output": "what can the members of The Police do?",
    },
    {
        "input": "Jan Sindel’s was born in what country?",
        "output": "what is Jan Sindel’s personal history?",
    },
]
# We now transform these to example messages
example_prompt = ChatPromptTemplate.from_messages(
    [
        ("human", "{input}"),
        ("ai", "{output}"),
    ]
)
few_shot_prompt = FewShotChatMessagePromptTemplate(
    example_prompt=example_prompt,
    examples=examples,
)
prompt = ChatPromptTemplate.from_messages(
    [
        (
            "system",
            """You are an expert at world knowledge. Your task is to step back and paraphrase a question to a more generic step-back question, which is easier to answer. Here are a few examples:""",
        ),
        # Few shot examples
        few_shot_prompt,
        # New question
        ("user", "{question}"),
    ]
)
```

And for the response prompt:

```python
# Response prompt 
response_prompt_template = """You are an expert of world knowledge. I am going to ask you a question. Your response should be comprehensive and not contradicted with the following context if they are relevant. Otherwise, ignore them if they are not relevant.

# {normal_context}
# {step_back_context}

# Original Question: {question}
# Answer:"""
response_prompt = ChatPromptTemplate.from_template(response_prompt_template)

chain = (
    {
        # Retrieve context using the normal question
        "normal_context": RunnableLambda(lambda x: x["question"]) | retriever,
        # Retrieve context using the step-back question
        "step_back_context": generate_queries_step_back | retriever,
        # Pass on the question
        "question": lambda x: x["question"],
    }
    | response_prompt
    | ChatOpenAI(temperature=0)
    | StrOutputParser()
)

chain.invoke({"question": question})
```

It feed AI with both the answers **with normal questions and with step_back contents**.

### HyDE

In practical situations, questions and texts exist in two completely different spaces. Directly embedding them can lead to errors and noise interference. Therefore, we need to **ensure consistency between the two spaces**.

{% note primary %}

The difficulty of zero-shot dense retrieval lies precisely: it requires learning of two embedding functions (for query and document respectively) **into the same embedding space** where inner product captures relevance. Without relevance judgments/scores to fit, learning becomes intractable.

{% endnote %}

Let's just see the abstract part for the passage:

{% note primary %}

Given a query, HyDE first **zero-shot instructs an instruction-following language model** (e.g. `InstructGPT`) to generate a hypothetical document. The document captures relevance patterns but is unreal and may contain false details. Then, **an unsupervised contrastively learned encoder**~(e.g. `Contriever`) encodes the document into an embedding vector. This vector identifies a neighborhood in the corpus embedding space, **where similar real documents are retrieved based on vector similarity**. This second step ground the generated document to the actual corpus, with the encoder's dense bottleneck filtering out the incorrect details. Our experiments show that HyDE significantly outperforms the state-of-the-art unsupervised dense retriever Contriever and shows strong performance comparable to fine-tuned retrievers, across various tasks (e.g. web search, QA, fact verification) and languages~(e.g. sw, ko, ja).

{% endnote %}

That is what **HyDE**[^3] for! The core idea of HyDE is to generate a hypothetical document that provides richer contextual information for the retrieval process. In HyDE, we need to generate a new hypothetical passage to simulate all possible answers.

Firstly, we need to generate a new hypothetical passage:

```python
from langchain.prompts import ChatPromptTemplate

# HyDE document genration
template = """Please write a scientific paper passage to answer the question
Question: {question}
Passage:"""
prompt_hyde = ChatPromptTemplate.from_template(template)

from langchain_core.output_parsers import StrOutputParser
from langchain_openai import ChatOpenAI

generate_docs_for_retrieval = (
    prompt_hyde | ChatOpenAI(temperature=0) | StrOutputParser() 
)

# Run
question = "What is task decomposition for LLM agents?"
generate_docs_for_retrieval.invoke({"question":question})
```

```python
# Retrieve
retrieval_chain = generate_docs_for_retrieval | retriever 
retireved_docs = retrieval_chain.invoke({"question":question})
retireved_docs
```

`retrieval_chain = generate_docs_for_retrieval | retriever` is the most fundamental part! After generating a hypothetical passage for retrieval, we then feed the answer into the retriever.

```python
# RAG
template = """Answer the following question based on this context:

{context}

Question: {question}
"""

prompt = ChatPromptTemplate.from_template(template)

final_rag_chain = (
    prompt
    | llm
    | StrOutputParser()
)

final_rag_chain.invoke({"context":retireved_docs,"question":question})
```

## Routing

**Basic Principle**: Routing the decomposed question into the right vector space.

It includes:

- Logical Routing
- Semantic Routing

## References

[^1]: https://www.youtube.com/watch?v=sVcwVQRHIc8
[^2]: https://arxiv.org/pdf/2310.06117
[^3]: https://arxiv.org/abs/2212.10496
