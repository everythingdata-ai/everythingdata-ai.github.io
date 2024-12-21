---
layout: post
title: How to build an LLM from scratch
categories: [Machine learning, AI]
---

Today I was curious to know how Large Language Models are built, so I found [this](https://www.youtube.com/watch?v=ZLbVdvOoTKM) Youtube video, and here are some notes I took while watching it.

### Introduction 

For the vast majority of LLM use cases, building a model from scratch is not necessary, it's better to use [prompt engineering](https://www.youtube.com/watch?v=0cf7vzM_dZ0) or [fine tune](https://www.youtube.com/watch?v=eC6Hd1hFvos) an existing model.

That's mainly related to cost of building an LLM :

Llama 2 (7b) took about 180 000 GPU hours to train
Llama 2 (70b) took about 1 700 000 GPU hours to train

Here's an estimation of how much that would cost :

- Option 1 : rent the GPUs on the cloud
Nvidia A100 : $1-2 per GPU per hour
7b model > $200 000
70b model > $2 000 000

- Option 2 : on premise
Nvidia A100 : $10 000
GPU cluster of 1000 : $10 000 000
Electricity : 1 000 megawatt hour x $100 per MWH = $100 000

But it's good to know how it works, and know when it's worth making one from scratch.
So here are the 4 steps it takes to build an LLM from scrach :

### 1 - Data curation

The most important and time consuming step.

"Garbage in, garbage out"
Data in = data out : the quality of your model is driven by the quality of your data.

GPT-3 was trained on  0.5T tokens, Llama 2 70b was trained on 2T tokens and Falcon 180b was trained on 3.5 trillion tokens (1 token = 1 word of text)

Where do we get all this data ?
- The internet (web pages, wikipedia, books, scientific articles, databases...)
- Common crawl (Colossal Clean Crawled Corpus, Falcon RefinedWeb)
- The Pile
- Hugging face datasets
- Private data sources (FinPile)
- Use an LLM (Alpaca)

A good training dataset seems to be a diverse dataset (webpages, code, conversational, books, scientific articles...) which will perform well in a wide variety of tasks. 

![image](https://github.com/everythingdata-ai/everythingdata-ai.github.io/blob/master/images/posts/dataset-diversity.png)

How to prepare the data ?

- Quality filtering : remove low-quality text from the dataset which is not helpful for the model (random gibberish, toxic language, objectively false statements...)
You can do that using a classifier : take a small high quality dataset, and use it to train a text classification model that automatically scores if a text is high or low-quality.
You can also do it heuristically, by removing specific words for example.

- Deduplication : removing several instances of the same (or very similar) text

- Privacy redaction : remove sensitive and confidential information

- Tokenization : translate text into numbers. Neural networks don't understand text, only numerics.
One of the most popular ways you can do that is using Bytepair encoding algorithm.
There are Python libraries that do that : SentencePiece and Tokenizers

### 2 - Model architecture

Transformers have emerged as the state-of-the-art model architecture for LLMs.
A transformer is a neural network that uses attention mechanisms : learns dependencies between different elements of a sequence based on position and content. (i.e. the context matters)

"I hit the baseball with a bat" / "I hit the bat with a baseball"

An attention mechanism uses both the sequence and position of each element to infer what the next element should be.

There are 3 types of transformers :
- Encoder-only : translates tokens into a semantically meaningful representation (good for text classification)
- Decoder-only : similar to encoder but tries to predict the next work - future tokens, it does not allow self-attention with future elements (good for text generation)
- Encoder-decoder : combines both and allows cross-attention (good for translation)

The most popular choice is the decoder-only architecture.

The next thing to consider is how big should the model be ?
- Too big or trained too long : it can overfit
- Too small or not trained long enough : it can underperform
As a rule of thumb, you should have 20 tokens per model parameter, and 100x increase in FLOPs for each 10x increase in model parameters

### 3 - Training at scale

The central challenge is their scale, tens of billions of parameters have big computational costs, and some tricks and techniques to speed up the process, like :

- Mixed precision training : use both 32bit and 16bit floating point data types
- 3D parallelism : combination of pipeline, model and data parallelism.
		Pipeline parallelism : distributes transformer layers across multiple GPUs
		Model parallelism : decomposes parameter matrix operation into multiple matrix multiplies distributed across multiple GPUs 
		Data parallelism : distributes training data across multiple GPUs 
- Zero redundancy optimizer (ZeRo) : reduces data redundancy regarding the optimizer state, gradient or parameter partitioning

All these models are available in the DeepSpeed Python library

Another thing we can do to ensure the process goes smoothly :
- Checkpointing : takes a snapshot of model artifacts so training can resume from that point.
- Weight decay : regularization strategy that penalizes large parameter values by adding a term (e.g. L2 norm of weight) to the loss function or changing the parameter update rule
- Gradient clipping : rescales the gradient of the objective function if its norm exceeds a pre-specified value

Common Hyperparameters :
- Batch size : typically static, around 16M tokens. It can also by dynamic, GPT-3 increased from 32K to 3.2M
- Learning rate : usually dynamic, increases linearly until reaching a maximum value and then reduces via a cosine decay until reaching 10% if its max value
- Optimizer : Adam-based optimizers
- Dropout : typically between 0.2 and 0.5

### 4 - Evaluation

Training the model is not enough, you need to see how it actually works.
For data you can use a benchmark dataset, like the Open LLM Leaderboard from Hugging Face.

There are various tasks you can use :

- Multiple-choice tasks : ARC, Hellaswing, MMLU
Usually on subjects like math, history, common knowledge...

- Open-ended tasks : TruthfulQA
There is no specific right answer 

### What's next ?

Base models are typically a starting point, not the final solution.
You can build something more practical on top of, using : 

- Promot engineering : feeding things into the LLM and harvesting their completion for a particular use case

- Model fine tuning : adapt the base model for a particular use case
