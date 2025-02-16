---
layout: post
title: Shopify product image alt text generator with AI
categories: [AI]
---

After a few days of Terraform and Airflow, today it's time for a breath of fresh air.
A few days ago, a friend of mine reached out to me, saying he wants to improve his Shopify SEO by putting alt-text on his products.
Having hundreds of products, it will be painful to do this manually, so today I will be trying to automate this task.


#### Gadget

Having never used Shopify, everything was new to me. 
First thing I needed was creating a Shopify account obviously, and asking my friend to give me Collaborator access.
After searching the internet for a bit, I found out that the best way to automate alt-text genenration is through an app, and for that [Gadget](www.gadget.dev) is a great solution.
Gadget helps you build and launch Shopify apps fast, with built-in ecommerce integrations, a fully managed database, instant APIs and hosted React frontends.
The free-tier of Gadget is enough to to produce one app so there will be no need for a credit card.

Let's go ahead and create an account, and select Shopify app :

![Screenshot 2025-02-16 at 23 17 32](https://github.com/user-attachments/assets/dfbb042e-7d26-450c-9a49-91705c2f2e13)

Choose a name for your app and confirm : 

![image](https://github.com/user-attachments/assets/6ba68bf5-8b51-4b20-b782-0c9b7954429b)

Gadget will then set-up everything for you, so you can focus on writing code : 

![image](https://github.com/user-attachments/assets/2333b2db-0565-4a00-ae74-6a48dedacfb4)

Once everything is ready, click on Connect to Shopify :

![image](https://github.com/user-attachments/assets/f317eca0-5f63-4d10-9778-075a2503e6d4)

It is recommended to use a Shopify Partners account so let's go ahead and do that : 

![image](https://github.com/user-attachments/assets/514902bc-4190-4d14-9360-2d4ce5c11ccf)

#### Shopify Partners

Create a Shopify Partners account [here](https://partners.shopify.com/current).

Select "Create and set up a new partner organization" and choose "Build apps" as your main focus.
You will have to fill§out a form with your details as well to finish creating your Partner organization.

Once logged§in, on the left Click on Store > Create a development store.

![image](https://github.com/user-attachments/assets/bfbd1909-db90-44ca-a1dc-248b2d3f3a7f)

This store will be used to test our app, it will be created with sample products : 

![image](https://github.com/user-attachments/assets/3b5f1758-1bfc-411f-aa21-5be79887f09c)

