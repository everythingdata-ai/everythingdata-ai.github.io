---
layout: post
title: Taking Claude Code for a test drive
categories: [AI]
---

Today's post will be about Anthropic's Claude Clode, the newest AI programming agent.
Is it worth the hype ? Let's find out !

#### Introduction 

Today Anthropic announced their newest model, Claude Sonnet 3.7, and along with, Claude Code.
Anthropic has identified that Coding is their biggest strength, and have now released an agentic coding system that you can use right now.
This is huge, not only is Sonnet 3.7 significantly better at coding, but Claude Code addresses most of the major pain points related to using LLMs while coding (understanding codebase context, quickly making changes, focusing on key snippets rather than writing entire files.. etc.).
Basically, the entire coding process just got a whole lot easier, a whole lot faster, and a lot more accessible.

Here are a few key capabilities of Claude Code:

- Editing and refactoring: Modify, optimize, and enhance your codebase with AI-powered suggestions.
- Bug fixing: Identify and resolve errors, missing dependencies, and performance bottlenecks.
- Code understanding: Ask questions about your code’s architecture, logic, and dependencies.
- Automated testing & linting: Execute and fix failing tests, run linting commands, and improve code quality.
- Git integration: Search through git history, resolve merge conflicts, create commits, and generate pull requests effortlessly.

Let's take it for a test run !

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

#### Going further

Now that we saw hoes Claud Caude does for a simple project, let's push it to the limit.
I will clone the [dbt-core](https://github.com/dbt-labs/dbt-core) project and see if Claude can uderstand it.

Despite the project being much larger, the `/init` command cost 0.22$ again.
However, explaining the project took almost a minute, and cost a staggering 0.21$ compared to 0.07$ for the previous project.
His exmplanation was on point :

<img width="960" alt="image" src="https://github.com/user-attachments/assets/f6c36ebf-b490-44d2-bc1d-441c85798e76" />

Now let's see if Claude can fix bugs. Going to the issues tab in the project's Github, the first bug that I found was this one : 

"It seems that starting the selection string with a * is mandatory in order for further wildcards to work. We should be able to use wildcards without having to start the selector string with "*""

For 0.18$, Claude finds the exact location related to the bug : 

```bash
 The specific issue is in the is_selected_node function (lines 69-108) which:
  1. First checks for exact matches (line 79)
  2. Then checks each component before finding a wildcard (lines 88-95)
  3. Only switches to wildcard matching mode when it encounters a wildcard (lines 97-105)

  This is why patterns that don't start with wildcards but contain them later don't work as expected - the selector checks for exact
  matches in the beginning parts and fails before it reaches the wildcard.

  The workaround is to always start your wildcard patterns with * when you need to use wildcards in selection patterns.
```

Now let's ask it to fix the bug :

<img width="974" alt="image" src="https://github.com/user-attachments/assets/be8b7da2-4243-4534-88d1-9b74d845784b" />

This is mind blowing, for 17 cents only Claude could fix a bug in an open source project ! 
I can imagine that in the near-future, you can include a Claude Code step in your CI/CD workflow, to automatically fix bugs.

<img width="952" alt="image" src="https://github.com/user-attachments/assets/76e1300c-5780-47f6-99bf-1c749ed3e425" />

I now officially have a contribution to the dbt project : 

![image](https://github.com/user-attachments/assets/6bb3bb0c-233d-434a-bf37-193e1c597a77)

And here is my total cost for these 2 use cases :

![image](https://github.com/user-attachments/assets/e8f5a60a-33fd-4580-b797-f4b1f05a9bb0)

#### Conclusion

After ChatGPT and Midjourney, and think this is the most hyped up I've felt about an AI tool !
