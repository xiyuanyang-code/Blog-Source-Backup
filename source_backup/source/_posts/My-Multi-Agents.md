---
title: My Multi-Agents
date: 2025-04-18 23:50:06
index_img: /img/cover/multiagents.jpg
excerpt: My preliminary exploration of the multi-agent framework, including camel-ai and autogen.
mermaid: true
categories:
  - Project
tags:
  - Multi-agents
  - Python
---

<style>
  html, body, .markdown-body {
    font-family: Georgia, sans, serif;
  }
</style>

# My Multi-Agents

## Introduction

Agents everywhere! Just feel free to explore and create your own agents.

See this [Repo](https://github.com/xiyuanyang-code/My-MultiAgents) for all the codes and more details.

## Agents

### Horrible Story Agent (`2025/04/18`)

A small system for generating horror stories through AI dialogue, composed of two agents, where users can customize the rules, urban legends, and horror elements to be used.

### English Essay Revision

I designed an automated tool for polishing and revising English essays, which can enhance grammar and strengthen logic, helping the article achieve a higher score.

#### Framework

{% mermaid %}

graph TD
    A[Task细化] --> B{Parallel}
    B --> C1[Conservative Modifier]
    B --> C2[Creative Modifier]
    C1 --> D[Integrator]
    C2 --> D
    D --> E[Final Report of Modifications]

{% endmermaid %}

You can see the code in the repo: 

