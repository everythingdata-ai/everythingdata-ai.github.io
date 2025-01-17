---
layout: post
title: Personalised GenAI image generator
categories: [AI]
---

When Grok came out and I played around a bit with it, I was amazed with how well it got the faces of celebrities. 
But what if you could train an AI to generate professional-quality images of yourself? 
Thanks to a technique called LoRA (Low-Rank Adaptation), this is now within reach for anyone with basic technical skills.

### What is LoRA?
Although it might sound complex, Low-Rank Adaptation is just a fancy word for this: instead of building an GenAI model from scratch, you take an existing one that generates amazing pictures, and teach it to recognize your face. 
If you're at a restaurant and your meal doesn't have enough salt, you don't go to cooking school to make the dish from scratch, you simply add a bit of salt, so why should you train a GenAI model yourself ?
Doing this saves tremendous time as well as computing resources (and let's be honest, technical expertise. Anyone with basic IT skills can re-train a model, but only a select few can build a great model.)

![LORA 1](/images/posts/2025/01/lora-01.png)

### Why Would You Want This?
The applications of your own GenAI model are diverse and exciting:
- Creating a professional LinkedIn headshot
- Generating unlimited ocial media posts
- Designing YouTube thumbnails 
- Producing promotional materials for your personal brand and creative projects

### Prerequisites

There is nothing complicated about this process, all you need is :

1. The training Dataset :
A collection of 20-40 high-quality photos of yourself 
Include variety of lighting conditions, facial expressions, angles, backgrounds and outfits.
The better your input photos, the better your results will be.
Put all your photos in a folder a compress it into a ZIP file.

3. The training model :

Obviously we need an existing model, I will be using FLUX, an open-source diffusion model made by Black Forest Labs, which is extremely good at making realistic faces.
The model is available on Replicate, a platform to host and deploy AI models, if you don't have an account already, go [here](https://replicate.com/) to make one.


3. A host for the model :

HuggingFace is a platform that allows you to host your AI models.
If you don't have an account already, go [here](https://huggingface.co/) to make one.

Once logged in, create a new model by clicking on your profil icon on the top right > New Model

![LORA 2](/images/posts/2025/01/lora-02.png)

We will also need a token, click on your profil icon on the top right > Generate Access Token:
Give your token all user permissions :

![LORA 3](/images/posts/2025/01/lora-03.png)

**IMPORTANT** : Once you click Create token, make sure to save it somewhere safe, as it won't be shown to you again !

### Training Process

Now that we got the basics out of the way, let's get to actual training !

Go to the FLUX model training page on Replicate [here](https://replicate.com/ostris/flux-dev-lora-trainer/train) :

Scroll down to fill out the training Form :

![LORA 4](/images/posts/2025/01/lora-04.png)


- Destination: select "Create a new model"
- Input Images: your ZIP file containing your photos
- Trigger Word: a unique word that will activate your custom model
- hf_repo_id: your HuggingFace repository ID (e.g. mouradgh/radmou)
- hf_token: the token you previously created on HuggingFace (and saved somewhere safe :D)

Optional Advanced Settings:

The default steps and lora_rank values are optimized for most use cases.
Advanced users can adjust these parameters for fine-tuning

That's about it, click on Create training, give yourself a high-five and go make a cup of coffee/tea while the model trains. 
The training might take up to an hour.

![LORA 5](/images/posts/2025/01/lora-05.png)

**Important** Note that this process is not free, training the model costs about 3$ and generating each image costs a couple cents, which to be honest is much cheaper than a professional photo shoot !

### Using the model

Once the model is done training, it's time for the last part, which is caleled inferencing (a.k.a using our newly trained model.)
For that, go to the FLUX inferencing page [here](https://replicate.com/lucataco/flux-dev-lora), scroll all the way down to hf_lora and paste your HuggingFace model ID (e.g. mouradgh/radmou) 
You can leave the rest of the fields as they are, and you're good to go.
nter your prompt, with a mention of your trigger word, and let the magic happen !

Here are some tiips for better results :

- Be specific in your prompts about lighting, background, and style
- Experiment with different descriptive words to fine-tune the results
- Generate multiple variations to find the perfect shot (you can generate up to 4 photos in each iteration by editing the num_outputs field)

![LORA 6](/images/posts/2025/01/lora-06.png)

Congratulations, you now have a new professional LinkedIn photo for the price of a cup coffee.

![LORA 7](/images/posts/2025/01/lora-07.png)

Or if you're more into Youtube, then you will have endless new thumbnails, wih exaggerated facial expressions, you know those get the most views ;)

![LORA 8](/images/posts/2025/01/lora-08.png)

Use your new superpower responsibly and creatively. The possibilities are endless !
