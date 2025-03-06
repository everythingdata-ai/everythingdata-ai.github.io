---
layout: post
title: LangGraph Multi-Agent Swarm
categories: [AI]
---

LangGraph has been the go-to framework for building AI agents.
A core principle of LangGraph is to be as low level as possible, that's why a higher level of abstractions has been introduced this week through a new set of prebuilt agents built on top of LangGraph.
One of those agents is [LangGraph Multi-Agent Swarm](https://github.com/langchain-ai/langgraph-swarm-py), a Python library for creating swarm-style multi-agent systems.
Let's take a look at it.

#### What are agents ?

Agents are advanced systems powered by large language models (LLMs) that can independently interact with their environment and make decisions in real time. 
Unlike traditional LLM applications, which are structured as rigid, predefined pipelines (e.g., A → B → C), agentic workflows introduce a dynamic and adaptive approach. 
Agents leverage tools—functions or APIs that enable interaction with their environment—to decide their next steps based on context and goals. 
This flexibility allows agents to deviate from fixed sequences, enabling more autonomous and efficient workflows tailored to complex and evolving tasks.

#### What is a swarm ?

A swarm is a type of multi-agent architecture where agents dynamically hand off control to one another based on their specializations. 
The system remembers which agent was last active, ensuring that on subsequent interactions, the conversation resumes with that agent.

![image](https://github.com/user-attachments/assets/d6e1f08f-d9bb-4c30-96dd-b7e9f6905372)

When building an agentic AI application, one of the first choices we need to take is the architecture. There are several architectures and each of them has their pros and cons

![image](https://github.com/user-attachments/assets/b8d073cf-4222-409e-a292-3b9cd707b332)

