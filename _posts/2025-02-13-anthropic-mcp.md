---
layout: post
title: A deep dive into Anthropic's Model Control Protocol
categories: [AI]
---

Anthropic just announced thei new model, Claude 3.7 Sonnet, as well as Claud Code, their first agentic coding tool.
While these anouncements made the rounds, one anouncement went unnoticed : open-sourcing the Model Context Protocol (MCP)
But what is MCP, you ask ? The Model Context Protocol was created by Anthropic to standardize how applications provide context to LLMs.
Think of MCP like a USB-C port for AI applications. Just as USB-C provides a standardized way to connect your devices to various peripherals and accessories, MCP provides a standardized way to connect AI models to different data sources and tools.
Following this protocol will make building AI agents so much easier.
Same as npm/pip libraries, there are now [MCP libraries](www.opentools.com) : someone wrote the code for a tool, you can integrate it with just one command.

MCP significantly enhances AI agents’ capabilities by enabling direct, bidirectional communication with external systems/data sources, which makes AI applications more interactive and aware of their surroundings. 
![image](https://github.com/user-attachments/assets/122668ab-5e5c-4a92-82d1-0b2c8f8e389f)


