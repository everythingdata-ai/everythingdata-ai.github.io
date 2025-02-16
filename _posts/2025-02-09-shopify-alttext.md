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

To continue we need a Client Id and Client Secret. To get those we need some extra steps.

![image](https://github.com/user-attachments/assets/514902bc-4190-4d14-9360-2d4ce5c11ccf)

It is recommended to use a Shopify Partners account so let's go ahead and do that : 

#### Shopify Partners

Create a Shopify Partners account [here](https://partners.shopify.com/current).

Select "Create and set up a new partner organization" and choose "Build apps" as your main focus.
You will have to fill§out a form with your details as well to finish creating your Partner organization.

Once logged-in, on the left Click on Store > Create a development store.

![image](https://github.com/user-attachments/assets/bfbd1909-db90-44ca-a1dc-248b2d3f3a7f)

This store will be used to test our app, it will be created with sample products : 

![image](https://github.com/user-attachments/assets/3b5f1758-1bfc-411f-aa21-5be79887f09c)

Next thing we need to do is create an app. Again on the left of your Shopify Partners account choose Apps > Create app > Create app manually :

![image](https://github.com/user-attachments/assets/c2e7a595-d275-4040-a60b-904ee0c080bd)

Once created, you will get the Client ID and Client Secret that we needed in Gadget.

![image](https://github.com/user-attachments/assets/69e1c783-efe8-46c0-8b73-02cee9db348c)

Go ahead and Copy/paste them to Gadget.

Click Next and you will be prompted to select the API scopes and data models necessary for our app.
In our case, we want to be able to Read and Write product data (description, title, image).
So we can filter on Products and select Read/Write the Product sub-model.

![image](https://github.com/user-attachments/assets/e887bfd4-cb5d-4c96-80d5-2631a58a15e0)

Click Confirm. Gadget then gives two URLs :

![image](https://github.com/user-attachments/assets/376b6bc1-cbf7-4e8a-b21c-3a7cf08667a1)

Go back to your Shopify Partners account > Apps > select your app > Configuration

![image](https://github.com/user-attachments/assets/64ea194f-b212-47ae-91dc-c683a7bbd276)

Notice that the URLs that Gadget created can be fille-in here, so go ahead and Copy/paste them and click Save and release :

![image](https://github.com/user-attachments/assets/e9b6d938-21e2-431e-8a9f-e7a0fbf54078)

The last thing we need to do to finish setting-up is to test the app.
Go back to your app page on Shopify Partners, and click§on Select store in the "Test your app" section.

![image](https://github.com/user-attachments/assets/73f58a00-6c7a-4775-a708-747b79a97b8c)

Select your previously created store, and click Install : 

![image](https://github.com/user-attachments/assets/ed14bd55-7bc2-4859-831d-84d4c7704eb6)

Go back to Gadget to make sure everything went well :

![image](https://github.com/user-attachments/assets/dc904606-2fc4-45c5-9a49-39a615083a99)






