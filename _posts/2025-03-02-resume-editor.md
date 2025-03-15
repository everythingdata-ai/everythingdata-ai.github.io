---
layout: post
title: Create a resume editor app from scratch
categories: [AI]
---

Yesterday I received a message from a friend asking if I knew an AI that can adapt his resume to a job offer.
While I'm sure such a tool exists, I want to create one myself, harnessing the power of "vibe coding".
Let's see how it goes.

### First steps

First things first, I will be using Cursor with Anthropic's Claude Sonnet 3.7 model.
If you haven't already, you can download Cursor [here](https://www.cursor.com) and install it.

Once installed, open the Cursor settings to choose your model : 

<img width="945" alt="image" src="https://github.com/user-attachments/assets/8f21a42b-5f2b-47dc-984e-f886c1204871" />

And we're ready to start building.
Switch to Composer mode, select the thinking model, and give Cursor your best description of the project : 

<img width="366" alt="image" src="https://github.com/user-attachments/assets/bf4c5fe1-ac24-4386-92c1-45d20803f5a1" />

I forgot to mention which technical stack to use, I was thinking of doing it in Python and Flask since it's the language I'm most familiar with.
But since I didn't mention it, Cursor chose a different stack for me, so this will be a good example of having a POV of someone with no/minimal coding knwoledge.

<img width="363" alt="image" src="https://github.com/user-attachments/assets/168bff66-e6e7-4c25-8388-f2d4bc7102df" />

Cursor will handle putting everything in place, including installing the libraries and handling errorr.
It's like having a personal intern to do your work for you :

<img width="579" alt="image" src="https://github.com/user-attachments/assets/fcf97b3d-4622-4f04-8f6d-098cffe3e021" />

After a few minutes, the app is ready !

```markdown
Great! The application is now complete and running. Let's summarize what we've built:
1. Homepage: A clean landing page that explains the purpose of the app and how it works.
2. Resume Builder Page: Allows users to upload their resume (PDF, DOCX, or Markdown) and enter a job posting URL.
3. Resume Results Page: Displays the original and tailored resume, with options to download the PDF.
4. API Endpoints:
/api/resume/tailor: Processes uploaded resumes and job postings
/api/resume/[id]: Retrieves processed resumes
/api/test: Tests the application environment
5. Core Functionality:
Resume parsing for different file formats
Job posting analysis using AI
Resume tailoring with OpenAI
PDF generation for the tailored resume
6. Additional Features:
Status page to check application health
Environment variable verification
Comprehensive documentation
```

Now we can run our app using ```npm run dev```

And the result is, bluffing !
I was expecting a basic black and white UI but it's actually nice-looking : 

![image](https://github.com/user-attachments/assets/424fc99b-0670-4baa-99a1-efc83608fe3e)

Let's give it a try :

![image](https://github.com/user-attachments/assets/944c2e34-f70d-4b73-aca0-3552a9c9133c)

Obviously this was too good to be true, and we run into our first error : 

![image](https://github.com/user-attachments/assets/bd048b06-8fe6-482d-a9dd-fbd1dca867bc)

Instead of going the traditional route, searching Google and going through endless Stackoverflow issues, let's just get Cursor to fix it :

<img width="584" alt="image" src="https://github.com/user-attachments/assets/9bf551e9-e1be-417f-8ec2-2a95034891fe" />

After a few minutes, Cursor solved the issue of langchain, and now we have our second error :

![image](https://github.com/user-attachments/assets/f1c0e3f8-7052-455f-8035-708b2291fe4c)

You know the drill, back to the Composer chat : 

<img width="584" alt="image" src="https://github.com/user-attachments/assets/c43eb998-2eb4-44d5-8418-53c82c934ae8" />

What would have taken me at least an hour to solve, now only takes a couple of minutes :

<img width="584" alt="image" src="https://github.com/user-attachments/assets/ab503329-3884-4854-8584-ab1775c9304a" />

If goes so far as modifying a freaking module : 

<img width="582" alt="image" src="https://github.com/user-attachments/assets/f60521a0-e523-4780-b597-74a1ae8ed20d" />

But still another error, hopefully the last one :

![image](https://github.com/user-attachments/assets/15600496-3987-45a8-9de1-6e16982ff4f8)

And finally, after 5-10 minutes of back and forth, we have our first resume, which, has nothing to do with the original resume or the job posting.
So there's still some work to do : 

![image](https://github.com/user-attachments/assets/160cc1fc-913e-464e-80e3-3a41b1e43aa4)

### Next steps

After about an hour from opening Cursor, I got a fully functional web application 
Mind you the tailoring is far from perfect, I will need to manually edit the LLM prompt.
Also the formatting of the CV is terrible, I had to work on that aspect as well.

But overall, I'm very satisfied with the result, I got a working MVP in an hour, without AI it would have taken me at least a week of work, and for someone without coding experience, it would have been almost impossible.

In the next few days I will keep working on the project, asking Claude to add other functionalities like :
- a login / account system
- a solid security, maybe add Cloudflare
- different resume formatting options

### MCP

In a previous article I talked about MCP, Anthropic's
Let's give the server a try





