---
layout: post
title: GCP Storage options
categories: [Data Engineering, GCP]
---

Todat I am starting a new job, and my first task is to define the architecture of the project.
If you're lost between all the services that GCP offers, then you're inthe right place.
In this article, I will list the different storage options, and which one to choose based on your needs.

### File Storage
First, let's start with storing your unstructured or semi-structured files.
When working locally, your hard drive acts as your file storage, whether it's to store your code, your images/videos or any other type of file.
When working on the cloud things are pretty much the same, you need a place to store all those file.

There are three types of storage options offered by most cloud providers: block storage, file storage, and object storage

These are the services that GCP offers for storage needs :

#### Block Storage

Imagine you have a large bookshelf with many shelves, and each shelf can hold a specific number of pages from a book.
Now, let's say you have a collection of books, but they are all different sizes. To efficiently store these books, you decide to divide them into smaller uniform pieces, called blocks, that fit nicely on the shelves.
Each shelf can only store 100 pages from a book. The Great Gatsby has about 200 pages, so each shelf can only store half the book. 
Therefore, this single book will be stored in two separate shelves, with half of the book in one location and the other in another location.

Hard disk drives (HDD) and solid state drives (SSD) that are attached to a computer physically or via a network are examples of block storage devices.
The block storage service offered by GCP is Persistent Disk.
Block storage is ideal for applications that require high performance and low latency storage like databases, high performance computing applications and ETL (Extract Transform Load), among other applications.
You can also store an operating system on your Persistent Disk.


#### File Storage

Block storage is the lowest level abstraction of storage. It provides a low-level interface where you can read from or write to individual blocks of data. 
But it does not inherently understand the concept of files, directories, or the hierarchical structure typically associated with file systems.
File storage is an abstraction built on top of block storage. It introduces the concept of files, directories, and a hierarchical structure for organising and managing data.

Using our bookshelf analogy, with file storage, all you interact with are files and their hierarchies, just like how a book store can organise their books in a structured way to make it easier for customers to find and browse through the selections. 
Books can be arranged alphabetically by author name, by genre, or a combination of both.

This hierarchical file structure is just an abstraction. Behind the scenes, the operating system abstracts away the underlying block storage and instead gives the appearance of a file cabinet with a folder-like structure. 
This simplifies access to applications trying to read or write files on the disk.
Applications don’t need to know the underlying block address to retrieve the files, which makes it easier for the application to interact with the files. 
This ease of interaction comes with a performance cost, which is acceptable for some use cases.

The GCP service for file storage is filestore.

#### Cloud Storage

This is the newest form of storage on the cloud. Object storage stores all data as objects in a flat structure. 
There is no hierarchy, unlike in file storage. But an artificial folder-like hierarchy is imparted to block storage to give it the appearance of having a structure.

Objects can be any file. It could be video, audio, image, text file, Excel file, word document, HTML, CSS, XML, JSON, and so on.

Object storage is highly durable – that is, there is a very low probability that any object stored there will be lost.
Object storage offered by cloud providers usually provides 99.999999999% durability over a year. This is colloquially referred to as 11 nines of durability.
Durability is defined as the probability of not losing an object. A storage system that has a durability of 99.999999999% has a 0.000000001% chance of losing a single object in a year. 
This means that even if you have a million objects stored in object storage, you are likely to only lose a single object in 100,000 years.

The GCP service for object storage is Cloud Storage.

#### When to use each storage type 

![image](https://github.com/user-attachments/assets/7c205d3f-9aa8-4a95-9e19-831212949c46)

Let’s imagine a simple scenario where you would need to use block, file, and object storage together.
Imagine you have been tasked with designing the cloud architecture for a law firm. You need to store a large number of evidence files in different formats (audio, video, image, text, Excel, JSON, and so on).
You need to processes these files, extract the useful information, and make it available for further processing by different people. 
You also need to make it available for direct use to a team of lawyers in different parts of the world.

From a high level, how could you design such a solution?

First, the raw files could be stored using either object storage or file storage. 
Object storage is preferable because it is lower cost. It is also ideal for low cost archival storage if you need to keep files for several years.
To process the files, you can run multiple applications on an EC2 instance that uses block storage. The other alternative is to use file storage.
Block storage is preferable because these processing tasks may include things like transcribing audio files, extracting text from images, improving and stabilising video files, a database that extracts data in JSON format and stores it in a relational database, and so on. 
These are all tasks that require higher performance, which is an ideal use case for block storage.
The processed files are then stored in S3 again before they are loaded into a file system with several instances mounted on it.
The up-to-date processed files must be available for further processing or for direct use by a team of lawyers. Block storage is not ideal here because block storage cannot be shared across multiple instances. 
Object storage is also not ideal because its not mountable (can’t be attached to a compute instance).
In this case, file storage is ideal because it has non of these constraints – it can be shared across multiple instances and it is mountable.

### Database storage

#### BigQuery


#### Cloud SQL

#### AlloyDB

#### Cloud Compute
