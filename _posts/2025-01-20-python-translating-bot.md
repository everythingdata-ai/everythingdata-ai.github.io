---
layout: post
title: Python tips
categories: [Python, AI]
---

![Job hunting 1](/images/posts/2025/02/job-hunt1.png)

Being a polyglot, I have always wanted to learn one or two new languages, so in 2024 I decided to finally go for it.
After visiting Brazil, I decided I wanted to learn portuguese, and I started my journey in march, and 6 months later I could understand written portuguese well.
However, I struggled to understand spoken portuguese. And if you're ever had brazilian friends, you know they love sending audios on Whatsapp !
So to help me understand spoken portuguese better, and as any geek would do, I decided to build a Whatsapp Bot that transcribes and translates Whatsapp audio messages !

Here is how that went and how you can build your own :

## Prequisites 

This is an application of a previous blog, if you haven't seen it, I suggest you take a look there first :
[Developing AI Applications with Python and Flask](https://everythingdata-ai.github.io/python-flask/)

### 1. Setup a Python Virtual Environment
A virtual environment is a tool that helps to keep dependencies required by different projects separate by creating isolated python virtual environments for them.

Run following command to create a new virtual environment inside your project folder:

```
python -m venv myvenv
```

Activate the virtual environment by running following command:
 
```
source myvenv/bin/activate
```

### 2. Create an account on ngrok
ngrok allows you to tunnel your localhost to the web. 
This will be useful to test the app.

[Ngork](https://ngrok.com)

Once you login to your account, go to Universal Gateway > Domains and copy your domain which will have this format : [your-domain].ngrok-free.app

### 3. Create an account on Twilio
The Twilio API for WhatsApp is the quickest way to be able to send and receive WhatApp messages through Python code.
[Twilio](https://www.twilio.com/)

Once you login to your account, go to Messaging > Try it out > Send a WhatsApp message
After going through the tutorial to set up your Sandbox, go to Sandbox settings and paste your ngork domain in the first field, followed by /whatsapp

<img width="844" alt="Screenshot 2024-12-06 at 23 32 52" src="https://github.com/user-attachments/assets/88f608bc-e830-4014-8a0c-42ad41c39d7e">


### 4. Install required Python Packages
For this project we will need the following packages : 

- [flask](https://github.com/pallets/flask)
    
    ```
    pip install flask
    ```
    
- [twilio](https://github.com/twilio/twilio-python)
    
    ```
    pip install twilio
    ```
- [ngrok](https://github.com/NGROK)
    
    ```
    brew install ngrok/ngrok/ngrok
    ```


## Coding the bot 

Here are the main building blocks of this bot :

- A function to convert the audio from the Whatsapp format (.ogg) to a regular format (.wav)

```python
def convert_audio(input_file, output_file):
    audio = AudioSegment.from_file(input_file, format="ogg")
    audio.export(output_file, format="wav")
```

- A function to transcribe audio :

```python
def transcribe_audio(audio_path):
    try:
        recognizer = sr.Recognizer()
        with sr.AudioFile(audio_path) as source:
            audio = recognizer.record(source)
            # Specify language as Portuguese (Brazil)
            transcription = recognizer.recognize_google(audio, language='pt-BR')
            return transcription
```

- For the translation we will use the GoogleTranslator library :

```python
translator = GoogleTranslator(source='pt', target='en')
translation = translator.translate(text)
```

- And the most complicated part is extracting the Whatsapp audio :

```python
audio_url = request.values.get('MediaUrl0')
response = requests.get(audio_url, auth=HTTPBasicAuth(self.twilio_account_sid, self.twilio_auth_token))
audio_content = response.content
with open('audio_received.ogg', 'wb') as f:
  f.write(audio_content)
```

## Final result

You can find the whole code on my Github [here](https://github.com/mouradgh/whatsapp-audio-bot/blob/main/whatsapp-bot-translator.py).

You can use it by editing these variables :

- twilio_account_sid and twilio_auth_token that you can find on your Twilio account in the Verifications tab

- Your source and target languages for the audio that will be transcribed and translated, in my case I'm translating Portuguese into English : source='pt', target='en'
Make sure to edit the emojis as well 🇧🇷

- On a separate Terminal, start ngork using this command
    ```
    ngrok http --url=[your-domain].ngrok-free.app 8080
    ```

And you're good to go, once your transfer an audio message to your Bot, you will get your results back !

![Whatsapp bot](/images/posts/2025/01/wa-bot.png)
