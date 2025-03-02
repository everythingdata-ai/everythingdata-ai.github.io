---
layout: post
title: Taking Claude Code for a test drive
categories: [AI]
---

Today Anthropic announced their newest model, Claude Sonnet 3.7, and along with, Claude Code.
Anthropic has identified that Coding is their biggest strength, and have now released an agentic coding system that you can use right now.
This is huge, not only is Sonnet 3.7 significantly better at coding, but Claude Code addresses most of the major pain points related to using LLMs while coding (understanding codebase context, quickly making changes, focusing on key snippets rather than writing entire files.. etc.).
Basically, the entire coding process just got a whole lot easier, a whole lot faster, and a lot more accessible.

Let's take it for a test run.

#### Setting up

You can install Claude Code and launch it using these commands : 

```bash
npm install -g @anthropic-ai/claude-code
cd your-project-directory
claude
```

<img width="624" alt="image" src="https://github.com/user-attachments/assets/3a7aec5c-c47b-4fd4-8acb-bdf3d41c03c3" />

You can then press Enter to login in.

Claude Code uses Anthopic's API, so you need to buy credits to use it.
I will buy 5$ to see how long it lasts.

![image](https://github.com/user-attachments/assets/f43b3b03-2785-48ca-9eea-70e5d0dba492)

Claude Code is still in research preview.
Here is how to use it effectively : 

<img width="410" alt="image" src="https://github.com/user-attachments/assets/40fef799-c9ae-4e13-922c-d225224c7471" />

#### First steps

First thing we're going to do it is get Claude to write a CLAUDE.md file that documents the current project, including details about technologies used, setup instructions and other relevant information for Claude to better understand the codebase when interacting with it.

For that you can run the command `/init`
I will be using it with this [dbt/Airflow/Snowflake project](https://everythingdata-ai.github.io/dbt-airflow-snowflake/).

After a minute or two, and 0.22$ worth of tokens, here is what Claude came up with :

<img width="809" alt="image" src="https://github.com/user-attachments/assets/043927b7-9d4d-4cb5-886f-3f8b97837338" />

Not bad but nothing crazy either.
Now that Claude understands the scope of the project, let's ask it to explain it to us :

<img width="604" alt="image" src="https://github.com/user-attachments/assets/60a40c8c-81f3-438e-991d-e58ee89a99ea" />

This project is rather simple, if the results are as good for more complex projects then I'm gonna need to find a new career.
The cost of explaining the project was 0.07$, not too bad !



