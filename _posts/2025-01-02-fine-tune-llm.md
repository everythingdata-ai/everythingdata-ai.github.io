---
layout: post
title: Improve LLM models through prompt engineering and fine-tuning
categories: [Machine learning, AI]
---

To continue with yesterday's video about building LLMs from scratch, I decided to watch the other two related videos on Youtube about [prompt engineering](https://youtu.be/0cf7vzM_dZ0) and [fine-tuning](https://youtu.be/eC6Hd1hFvos).

## Prompt engineering - How to trick AI into solving your problems

### What is prompt engineering

Prompt engineering is any use of LLM out of the box.
It's new way to program computers, making programming and computation as easy as asking the computer what you want in a natural language.

Prompt engineering is an empirical art of composing and formatting the prompt to maximise a model's performance on a desired task.

### Two levels of prompt engineering 

- The easy way : ChatGPT (or something similar)
It's easy to use but restricted.

- The less easy way : programmatically
Fully customise how an LLM fits into a larger piece of software  
### How to build AI apps with prompt engineering

Use case : automatic grader for high school history class
Question : Who was the 35th president of the USA ?
Potential correct answers : John F. Kennedy, JFK, Jack Kennedy, John Fitzgerald Kennedy

Traditional paradigm : the developer has to figure out logic to handle all variations, might require a list of all possible answers

New paradigm : Use LLM to handle logic via prompt engineering
```
You are a high school history teacher grading homework assignments.
Based on the homework question indicated by "Q:" and the correct answer indicated by "A:", your task is to determine whethr the student's answer is correct.
Grading is binary; therefore, student answers can be correct or wrong.
Simple misspellings are okay.

Q: {question}
A: {correct_answer}

Student Answer: {student_answer}
```
### 7 tricks for prompt engineering

1) Be descriptive, more is better 

Write a birthday message for my dad 
_________

Write a birthday message for my dad no longer than 200 characters. This is a big birthday because he is turning 50. To celebrate, I booked us a boy's trip to Cancun. Be sure to include some cheeky humour, he loves that.

2) Give examples

Given the title of Towards Data Science blog article, write a subtitle for it.
Title : Prompt engineering - How to trick AI into solving your problems
Subtitle : 
___________

Given the title of Towards Data Science blog article, write a subtitle for it.
Title : Cracking open the OpenAI (Python) API
Subtitle : a complete beginner-friendly introduction with example code

Title : A practical introduction to LLMs
Subtitle : 3 levels of using LLMs in practice

Title : Prompt engineering - How to trick AI into solving your problems
Subtitle : 

3) Use structured text

Write me a recipe for chocolate chip cookies
_______
Create a well-organised recipe for chocolate chip cookies. Use the following formatting elements:

Title : Classic chocolate chip cookie
Ingredients : List the ingredients with precise measurements and formatting
Instructions : Provide step-by-step instructions in numbered format, detailing the baking process.
Tips : include a separate section with helpful baking tips and possible variations.

- Chain of thought, give the LLM time to think

Write me a LinkedIN post based on the following Medium blog
(paste blog post)
_____
Write me a LinkedIn post based on the step-by-step process and Medium blog given below :

Step 1 : Come up with a one line hook relevant to the blog
Step 2 : Extract 3 key points from the article
Step 3 : Compress each point to less than 50 characters
Step 4 : Combine the hook, compressed key points from step 3, and a call to action to generate the final output 

(paste blog post)

- Chatbot personas, assign a role

Make me a travel itinerary for a weekend in New York
_____
Act as an NYC native and cabbie who knows everything about the city.
Please make me a travel itinerary for a weekend in New York City based on your experience. Don't forget to include your charming NY accent in your response.

- Flipped approach, prompt the LLM to ask you questions

What is an idea for an LLM-based application ?

________

I wanted you to ask me questions to help me come up with an LLM-based application idea. Ask me one question at a time to keep things conversational.

- Reflect, review and refine  : prompt the chatbot for review and improvement

Review your previous response, pinpoint areas for enhancement, and offer an improved version. The explain your reasoning for how you improved the response.

### Example code : Automatic grader with LangChain

```python
from langchain.chat_models import ChatOpenAI
from langchain.prompts import PromptTemplate
from langchain.chains import LLMChain
from langchain.schema import BaseOutputParser

# define chatbot
chat_model = ChatOpenAI(openai_api_key={your secret key}, temperature=0)

# define prompt template
prompt_template_text = """You are a high school history teacher grading homework assignments. \
Based on the homework question indicated by “**Q:**” and the correct answer indicated by “**A:**”, your task is to determine whether the student's answer is correct. \
Grading is binary; therefore, student answers can be correct or wrong. \
Simple misspellings are okay.

**Q:** {question}
**A:** {correct_answer}

**Student's Answer:** {student_answer}
"""

prompt = PromptTemplate(input_variables=["question", "correct_answer", "student_answer"], template = prompt_template_text)

# define chain
chain = LLMChain(
    llm=chat_model,
    prompt=prompt,
)

# define inputs
question = "Who was the 35th president of the United States of America?"
correct_answer = "John F. Kennedy"
student_answer =  "JFK"

# run chain
chain.run({'question':question, 'correct_answer':correct_answer, 'student_answer':student_answer})


# run chain in for loop
student_answer_list = ["John F. Kennedy", "JFK", "FDR", "John F. Kenedy", "John Kennedy", "Jack Kennedy", "Jacqueline Kennedy", "Robert F. Kenedy"]

for student_answer in student_answer_list:
    print(student_answer + " - " + str(chain.run({'question':question, 'correct_answer':correct_answer, 'student_answer':student_answer})))
    print('\n')

# define output parser
class GradeOutputParser(BaseOutputParser):
    """Determine whether grade was correct or wrong"""

    def parse(self, text: str):
        """Parse the output of an LLM call."""
        return "wrong" not in text.lower()

# update chain
chain = LLMChain(
    llm=chat_model,
    prompt=prompt,
    output_parser=GradeOutputParser()
)

# grade student answers
for student_answer in student_answer_list:
    print(student_answer + " - " + str(chain.run({'question':question, 'correct_answer':correct_answer, 'student_answer':student_answer})))
```

### Limitations 

- Optimal prompt strategies are model-dependent : the best prompt for Chatgpt might not be the best for Claude

- Not all pertinent information can fit in a context window

- General-purpose model may be cost inefficient and even overkill

- A smaller specialised model con out-perform a larger general-purpose model

## Fine tuning LLMs

Fine-tuning is taking a pre-trained model and training at least one model parameter (the internal weights/biases inside the neural network)

For example taking GPT 3 and fine-tuning for a specific use case : ChatGPT

The reason for that is sometimes a smaller (fine-tuned) model can often outperform a larger base model.

### 3 ways to fine-tune

1) Self-supervised learning : get a curated training corpus of text and you train the model i.e. take a sequence of text, feed it into the model and have it predict a completion. 

2) Supervised : having a training dataset consisting of inputs and associated outputs (ex. question answer pairs) and feeding it into the model

3) Reinforcement learning : consists of 3 steps 
First Supervised fine-tuning, then train reward model (if it generates a good completion, it gets a high score) and finally do reinforcement learning

### Supervised fine-tuning in 5 steps

1) Choose your fine-tuning task : this could text summarisation, text generation...
2) Preparing the training dataset : if you're doing text summarisation , you should have input output pairs of text with their summary
3) Choose a base model 
4) Fine-tune the model via supervised learning
5) Evaluate model performance

### 3 options for parameter training

1) Retrain all parameters : given the neural network, we tweak all the parameters.
The computational cost is expensive.

2) Transfer learning : instead of retraining all the parameters, we freeze most of them, and only retrain the head (the last few layers)

3) Parameter efficient fine-tuning : instead of freezing a subset of the weights, we freeze all of them, and augment the model with additional parameters that are trainable. One of the ways to do that is Low-Rank Adaptation (LoRA)

### Example code : Fine-tuning an LLM w/ LoRA

```python
from datasets import load_dataset, DatasetDict, Dataset

from transformers import (
    AutoTokenizer,
    AutoConfig, 
    AutoModelForSequenceClassification,
    DataCollatorWithPadding,
    TrainingArguments,
    Trainer)

from peft import PeftModel, PeftConfig, get_peft_model, LoraConfig
import evaluate
import torch
import numpy as np

# load dataset from HuggingFace
dataset = load_dataset('shawhin/imdb-truncated')

# display % of training data with label=1
print(np.array(dataset['train']['label']).sum()/len(dataset['train']['label']))

# Model
model_checkpoint = 'distilbert-base-uncased'
# model_checkpoint = 'roberta-base' # you can alternatively use roberta-base but this model is bigger thus training will take longer

# define label maps
id2label = {0: "Negative", 1: "Positive"}
label2id = {"Negative":0, "Positive":1}

# generate classification model from model_checkpoint
model = AutoModelForSequenceClassification.from_pretrained(
    model_checkpoint, num_labels=2, id2label=id2label, label2id=label2id)

# display architecture
model

# create tokenizer
tokenizer = AutoTokenizer.from_pretrained(model_checkpoint, add_prefix_space=True)

# add pad token if none exists
if tokenizer.pad_token is None:
    tokenizer.add_special_tokens({'pad_token': '[PAD]'})
    model.resize_token_embeddings(len(tokenizer))

# create tokenize function
def tokenize_function(examples):
    # extract text
    text = examples["text"]

    #tokenize and truncate text
    tokenizer.truncation_side = "left"
    tokenized_inputs = tokenizer(
        text,
        return_tensors="np",
        truncation=True,
        max_length=512
    )

    return tokenized_inputs

# tokenize training and validation datasets
tokenized_dataset = dataset.map(tokenize_function, batched=True)
tokenized_dataset

# create data collator
data_collator = DataCollatorWithPadding(tokenizer=tokenizer)

# import accuracy evaluation metric
accuracy = evaluate.load("accuracy")

# define an evaluation function to pass into trainer later
def compute_metrics(p):
    predictions, labels = p
    predictions = np.argmax(predictions, axis=1)

    return {"accuracy": accuracy.compute(predictions=predictions, references=labels)}

# define list of examples
text_list = ["It was good.", "Not a fan, don't recommed.", "Better than the first one.", "This is not worth watching even once.", "This one is a pass."]

print("Untrained model predictions:")
print("----------------------------")
for text in text_list:
    # tokenize text
    inputs = tokenizer.encode(text, return_tensors="pt")
    # compute logits
    logits = model(inputs).logits
    # convert logits to label
    predictions = torch.argmax(logits)

    print(text + " - " + id2label[predictions.tolist()])

# Here are the results of this code
# Not a fan, don't recommed. - Negative
# Better than the first one. - Negative
# This is not worth watching even once. - Negative
# This one is a pass. - Negative

# Now we train model 
peft_config = LoraConfig(task_type="SEQ_CLS",
                        r=4,
                        lora_alpha=32,
                        lora_dropout=0.01,
                        target_modules = ['q_lin'])

peft_config

model = get_peft_model(model, peft_config)
model.print_trainable_parameters()

# hyperparameters
lr = 1e-3
batch_size = 4
num_epochs = 10

# define training arguments
training_args = TrainingArguments(
    output_dir= model_checkpoint + "-lora-text-classification",
    learning_rate=lr,
    per_device_train_batch_size=batch_size,
    per_device_eval_batch_size=batch_size,
    num_train_epochs=num_epochs,
    weight_decay=0.01,
    evaluation_strategy="epoch",
    save_strategy="epoch",
    load_best_model_at_end=True,
)

# creater trainer object
trainer = Trainer(
    model=model,
    args=training_args,
    train_dataset=tokenized_dataset["train"],
    eval_dataset=tokenized_dataset["validation"],
    tokenizer=tokenizer,
    data_collator=data_collator, # this will dynamically pad examples in each batch to be equal length
    compute_metrics=compute_metrics,
)

# train model
trainer.train()

model.to('mps') # moving to mps for Mac (can alternatively do 'cpu')

print("Trained model predictions:")
print("--------------------------")
for text in text_list:
    inputs = tokenizer.encode(text, return_tensors="pt").to("mps") # moving to mps for Mac (can alternatively do 'cpu')

    logits = model(inputs).logits
    predictions = torch.max(logits,1).indices

    print(text + " - " + id2label[predictions.tolist()[0]])

# Trained model predictions :
# It was good. - Positive
# Not a fan, don't recommed. - Negative
# Better than the first one. - Positive
# This is not worth watching even once. - Negative
# This one is a pass. - Negative
```

[Predibase](https://predibase.com/) offers state-of-the-art fine-tuning techniques on the cloud.

[Ludwig](https://github.com/ludwig-ai/ludwig) is a declarative framework to develop, train, fine-tune, and deploy state-of-the-art deep learning and large language models using low-level code.

[Unsloth](https://unsloth.ai/) allows to easily finetune & train LLMs
