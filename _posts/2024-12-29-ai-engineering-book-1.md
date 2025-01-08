---
layout: post
title: AI Engineering book - Chapters 1 & 2
categories: [AI]
---

Today I read the first two chapters of the new O'reilly book AI Engineering : Building applications with foundation models
Foundation models have enabled many new AI use cases while lowering the barriers to entry for building AI products. 
This has transformed AI from an esoteric discipline into a powerful development tool that anyone can use—including those with no prior AI experience.
This book promises to be a comprehensive, well-structured guide to the essential aspects of building generative AI systems.

Here are my notes from the chapters 1 & 2

## 1 - Introduction to Building AI Applications with Foundation Models

Training LLMs requires data, compute resources, and specialized talent that only a few organizations can afford. Models developed by these few organizations are
made available for others to use as a service. Anyone who wishes to leverage AI to
build applications can now use these models to do so without having to invest up
front in building a model.

The demand for AI applications has increased while the barrier to entry for
building AI applications has decreased. This has turned AI engineering—the process
of building applications on top of readily available models—into one of the fastest-
growing engineering disciplines.

## The rise of AI Engineering

### Language models
A language model encodes statistical information about one or more languages. Intuitively, this information tells us how likely a word is to appear in a given context. For example, given the context “My favorite color is __ ”, a language model that encodes English should predict “blue” more often than “car”.

You can think of a language model as a completion machine: given a text (prompt), it
tries to complete that text. Completions are predictions, based on probabilities, and not guaranteed to be correct.

The basic unit of a language model is token. A token can be a character, a word, or a part of a word (like -tion), depending on the model.
GPT-4 breaks this phrase into nine tokens :
<span style="color:red">I</span> <span style="color:blue">can</span><span style="color:orange">'t</span> <span style="color:purple">wait</span> <span style="color:green">to</span> <span style="color:red">build</span> <span style="color:blue">awesome</span> <span style="color:orange">AI</span> <span style="color:purple">applications</span>

The process of breaking the original text into tokens is called tokenization. For
GPT-4, an average token is approximately ¾ the length of a word. So, 100 tokens are
approximately 75 words.

There are two main types of language models : 
- Masked language model : trained to predict missing tokens anywhere in a sequence, using the context from both before and after the missing token (fill in the blank)
- Autoregressive language model : trained to predict the next token using only the preceding tokens (predict what comes next)These models are the most popular

### Self-supervision

What’s special about language models that made them the center of the scaling approach that caused the ChatGPT moment?
Language models can be trained using self-supervision, while many other models require supervision. Supervision refers to the process of training ML algorithms using labeled data, which can be expensive and slow to obtain (to train a fraud detection model, you use examples of transactions, each labeled with “fraud” or “not fraud”.)

Self-supervised learning means that language models can learn from text sequences without requiring any labeling. Because text sequences are everywhere, it’s possible to construct a massive amount of training data, allowing language models to scale up to become LLMs.

### From LLMs to Foundation models

While language models are capable of incredible tasks, they are limited to text.
Being able to process data beyond text is essential for AI to operate in the real world.

For this reason, language models are being extended to incorporate more data modalities. GPT-4V and Claude 3 can understand images and texts. Some models even understand videos, 3D assets, protein structures, and so on, which makes them foundation models. 
The word foundation signifies both the importance of these models in AI applications and the fact that they can be built upon for different needs. They mark the transition from task-specific models to general-purpose models. Previously, models were often developed for specific tasks, such as sentiment analysis or translation. A model trained for sentiment analysis wouldn’t be able to do translation, and vice versa.

A model that can work with more than one data modality is also called a multimodal model. A generative multimodal model is also called a large multimodal model (LMM).

General-purpose models can work relatively well for many tasks. An LLM can do both sentiment analysis and translation. However, you can often tweak a general-purpose model to maximize its performance on a specific task.
![image](/images/posts/benchmark-tasks.png)
There are multiple techniques you can use to get the model to generate what you want. The most 3 common techniques to adapt a model to your needs are :  
- prompt engineering : craft detailed instructions with examples of the desirable product descriptions
- Retrieval-augmented generation (RAG) : connect the model to a database of customer reviews that the model can leverage to generate better descriptions
- fine-tuning : further train—the model on a dataset of high-quality product descriptions

Adapting an existing powerful model to your task is generally a lot easier than building a model for your task from scratch. Foundation models make it cheaper to develop AI applications and reduce time to market. 

There are still many benefits to task-specific models, for example, they might be a lot smaller, making them faster and cheaper to use.

### From Foundation models to AI Engineering

AI engineering refers to the process of building applications on top of foundation models. People have been building AI applications for over a decade—a process often known as ML engineering or MLOps (short for ML operations). 

Why do we talk about AI engineering now? If traditional ML engineering involves developing ML models, AI engineering leverages existing ones.

The availability and accessibility of powerful foundation models lead to three factors that, together, create ideal conditions for the rapid growth of AI engineering as a discipline:
- General-purpose AI capabilities : applications previously thought impossible are now possible, vastly increasing both the user base and the demand for AI applications.
- Increased AI investments : the success of ChatGPT prompted a sharp increase in investments in AI. As AI applications become cheaper to build and faster to go to market, returns on investment for AI become more attractive. Companies rush to incorporate AI into their products and processes. It is estimated that AI investment could approach $100 billion in the US and $200 billion globally by 2025. 1 in 3 S&P 500 companies mentioned AI in their earning calls for the second quarter of 2023, these companies saw their stock price increase more than those that didn't.
- Low entrance barrier to building AI applications : The model as a service approach popularized by OpenAI and other model providers makes it easier to leverage AI to build applications. In this approach, models are exposed via APIs that receive user queries and return model outputs. Not only that, AI also makes it possible to build applications with minimal coding. First, AI can write code for you, allowing people without a software engineering background to quickly turn their ideas into code and put them in front of their users. Anyone, and I mean anyone, can now develop AI applications.

### The AI Engineering stack

The number of new tools, techniques, models, and applications introduced every day can be overwhelming. Instead of trying to keep up with the constantly shifting sand, let’s look into the fundamental building blocks of AI engineering.

Some companies treat AI engineering the same as ML engineering, while others have separate job descriptions for AI engineering. Regardless of where organizations position AI engineers and ML engineers, their roles have significant overlap. Existing ML engineers can add AI engineering to their lists of skills to expand their job prospects. However, there are also AI engineers with no previous ML experience. With the availability of foundation models, ML knowledge is no longer a must-have for building AI applications.

At a high level, building applications using foundation models today differs from traditional ML engineering in three major ways:
1) AI engineers use models trained by someone else, focusing less on modeling and training, and more on model adaptation.
2) AI engineers work with bigger models that consume more compute resources and incur higher latency than traditional ML engineering, which means there's more need for engineers who know how to work with GPUs and big clusters.
3) AI engineers work with models than can produce open-ended outputs (flexible models than can be used for multiple tasks) which are harder to evaluate. This makes evaluation a bigger problem in AI engineering compared to ML.

There are three layers to any AI application stack :
- Application development : with models readily available, anyone can use them to develop apps. App development involves providing a model with good prompts and necessary context.
- Model development : this layer provides tooling for developing models, including frameworks for modeling, training, finetuning, and inference optimization. Because data is central to model development, this layer also contains dataset engineering.
- Infrastructure : At the bottom is the stack is infrastructure, which includes tooling for model serving, managing data and compute, and monitoring.
