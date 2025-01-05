---
layout: post
title: Machine Learning with Spark
categories: [Machine learning, Data Engineering, Spark]
---

After yesterday's introduction to Spark, I decided to dive a bit deeper and get my hands dirty with the help of [IBM's course](https://www.coursera.org/learn/machine-learning-with-apache-spark)

### What is machine learning ?

Machine learning is the subfield of computer science that gives "computers the ability to learn without being explicitly programmed.”

Machine learning algorithms, inspired by the human learning process, iteratively learn from data and allow computers to find hidden insights. 
These models are helpful in a variety of tasks, such as object recognition, summarisation, recommendation, and so on.

Here are some real-life examples. Netflix and Amazon recommend videos, movies, and TV shows to their users, based on their knowledge of the types of shows they like to watch, banks decide when to approve a loan application by using machine learning to predict the probability of default for each applicant and then approve or deny the loan application based on that probability. Telecommunication companies use their customers’ demographic data to segment them or predict if they will unsubscribe from their company in the next month.


### What is the difference between AI, machine learning and deep learning?

- AI tries to make computers intelligent enough to mimic humans’ cognitive functions. So, AI is a general field with a broad scope, including computer vision, language processing, creativity, and summarisation. 

- Machine learning (or ML) is the branch of AI that covers the statistical part of artificial intelligence. It teaches the computer to solve problems by looking at hundreds or thousands of examples, learning from them, and then using that experience to solve the same problem in new situations. 

- Deep learning is an exceptional field of machine learning where computers can learn and make intelligent decisions independently. Deep learning involves a deeper level of automation in comparison to most machine learning algorithms.

### The two categories for machine learning

- Supervised learning uses labeled data to train your model. It can be split into two subcategories. Regression techniques are used for predicting a continuous value. For example, predicting the price of a house based on its characteristics or estimating the CO2 emission from a car’s engine. And classification techniques are used for predicting the class or category of a case. For example, if a cell is benign or malignant, or whether a customer will churn or not. 

- Unsupervised learning uses unlabelled data and algorithms to detect patterns in the data. An example of unsupervised learning is clustering. Clustering algorithms are used to group similar cases, for example, they can be used to find similar patients or to segment bank customers. Unsupervised learning techniques such as dimension reduction, density estimation, market basket analysis, and clustering are the most widely used unsupervised machine learning techniques.

### Branches of the machine learning field 

- Deep learning deals with algorithms inspired by the human brain and how humans learn. 
- Natural language processing encompasses how a machine understands written or spoken human language. 
- Computer vision deals with how computers see and understand digital images. 
- Reinforcement learning includes teaching a machine to make decisions by rewarding desired actions and punishing undesired actions.

### Machine Learning Model Lifecycle

1) Define the problem
2) Data collection
3) Data preparation
4) Model development and evaluation
5) Model deployment

This lifecycle is iterative, which means that we tend to go back and forth between these processes. For example, if you find some problems with your models that are deployed in production, you may need to go back to data collection, or even the problem definition step, and then re-iterate the subsequent processes.

### Regression

Basically, you can fit a line through the data and make an educated guess for values within or outside the range of the data set. This line is called the line of best fit. Simply put, regression is the relationship between a dependent and independent variable. In regression, the dependent variable is a continuous variable. Examples of regression include predicting the prices of houses in Manitoba, predicting scores on a final exam, predicting the weather, and so on.

The simplest form is lineal regression, which assume a linear relationship between the dependent and independent variable.

Sometimes, you may have a non-linear relationship and the linear regression model won't do well with a straight line. An example of that is polynomial regression. This model uses a polynomial of degree two to account for the nonlinear relationship.

And when you want to avoid too much reliance on the training data, you can use regularised regression, such as Ridge, Lasso, and ElasticNet. The main idea here is to constrain the slope of the independent variables to zero by adding a penalty. Ridge regression shrinks the coefficients by the same factor but doesn’t eliminate any of the coefficients. Lasso regression shrinks the data values toward the mean, which normally leads to a sparser model and fewer coefficients. ElasticNet regression is the optimal combination of Ridge and Lasso that adds a quadratic penalty.

The following are popular examples of advanced regression techniques :
- Random forest : a group of decision trees combined into a single model. 
- Support vector regression (SVR) : creates a line or a hyperplane that separates the data into classes. 
- Gradient boosting : makes predictions by using a group of weak models like decision trees.
- Neural networks : the idea behind neural networks is inspired by the neurons in the brain used to make predictions. Here are some differences between classification and regression.

### Classification

Classification answers the question “What category does this fall into?” For example, when asking yourself, “Will I pass or fail my biology exam?“ there are two categories: you will pass your biology exam, or you will fail your biology exam. Note that this is an either/or situation because you will either pass or fail, you can’t do both. On the other hand, regression answers “What will my biology exam score be?” For example, I can determine how my hours of sleep and studying impact my biology exam scores.

You can divide classification algorithms into two : 

- lazy learner - doesn’t have a training phase per se as it waits to have a test data set before making predictions. This means that it doesn’t generalize the model, therefore taking longer to predict. A very popular example is the k-nearest neighbour algorithm, also known as KNN. KNN classifies the unknown data points by finding the most common classes in the k-nearest examples. Then, it finds the closest match to the test data.


- eager learner - spends a lot of time training and generalizing the model, so it spends less time predicting the test data set. You can also use logistic regression. This model is used to predict the probability of a class. For example, given the number of classes I attended, what is the probability I will pass or fail my biology exam? Finally, you have decision trees, which are tree-like algorithms that use an ”If-then” rule. In this example, it classifies if you will pass or fail based on some rules. Other advanced algorithms are support vector machines, naïve Bayes, discriminant analysis, and neural networks, just to name a few.

### Evaluating Machine Learning Models

To train a ML model, you shouldn't feed it all the data in the data set, instead you should split it into :
- a training set that will teach the model
- a test set that will evaluate how well the model performs

#### Evaluating Classification
##### Accuracy
You can calculate accuracy by taking the number of observations that the model predicted accurately and dividing it by the total number of observations.
##### Confusion Matrix
Another way of looking at accuracy of classifiers is to look at a confusion matrix. A confusion matrix is a table with combinations of predicted values compared to actual values. It measures the performance of the classification problem. On the y-axis, you have the True label and on the x-axis, you have the Predicted label. True positive means you predicted pass and it was pass. True negative means you predicted fail and it was fail. False positive means you predicted pass, but it is actually fail. False negative means you predicted fail and it is actually pass.

##### Precision
Precision is the fraction of true positives among all the examples that were predicted to be positives. Precision is “Total correct predicted pass” divided by "Total observations as pass”. Mathematically speaking, it is the true positives divided by the sum of true and false positives. An example where precision may be more important than accuracy is a movie recommendation engine. Precision involves the cost of failure, so the more false positives you have, the more cost you incur. If the movie was a false positive, meaning that the user isn’t interested in the movie that was recommended, then that would be an additional cost with no benefit.

##### Recall
Recall is the fraction of true positives among all the examples that were actually positive. Looking at all the observations that are actually “Pass” consider the number of “pass” observations the model got right out of the total true “pass“ observations.

Mathematically speaking, it is the number of true positives divided by the sum of true positives and false negatives. When you have a situation where opportunity cost is more important—that is, when you give up an opportunity when dealing with a false negative—recall may be a more important metric. An example of this is in the medical field. It’s important to account for false negatives, especially when it comes to patient health. 
##### The F1-score
Imagine that you are in the medical field and have incorrectly classified patients as having an illness. That is very important because you could be treating the wrong diagnosis. In cases like this where precision and recall are equally important, you can’t try to optimize one or the other. 

The harmonic or balanced mean of precision and recall, can be used in this situation. It is calculated as : (2 * precision * recall) / (precision + recall). 

Rather than manually trying to strike the balance between precision and recall, the F1-score does that for you.

#### Evaluating Regression

##### MSE : Mean Squared Error
The average of squared differences between the prediction and the true output. The aim is to minimize the error, in this case MSE. The lower the MSE, the closer the predicted values are to the actual values and the stronger and more confident you can be in your model’s prediction
$$
MSE=\frac{1}{n} \sum_{i=1}^n​(yi​−\hat{yi}​)^2
$$
##### RMSE : Root Mean Square Error
It is the square root of the MSE and has the same unit as your target variable, making it easier to interpret than the MSE
$$
RMSE = \sqrt{MSR}
$$

##### MAE : Mean Absolute Error
The average of the absolute values of the errors
$$
MAE=\frac{1}{n} \sum_{i=1}^n=​∣yi​−\hat{yi}​^​∣
$$
##### MAP : Mean absolute percentage error
Quantifies accuracy as a percentage of the error
$$
\text{MAPE} = \frac{1}{n} \sum_{i=1}^n \left| \frac{y_i - \hat{y}_i}{y_i} \right| \times 100
$$

##### R-squared :
The amount of variance in the dependent variable that can be explained by the independent variable. It is also called the coefficient of determination and measures the goodness of fit of the model. The values range from zero to one, with zero being a badly fit model and one being a perfect model.
$$
R^2 = 1 - \frac{\sum_{i=1}^n (y_i - \hat{y}_i)^2}{\sum_{i=1}^n (y_i - \bar{y})^2}​
$$

### Clustering 

Clustering is the process of grouping unlabeled examples. Clustering helps in identifying patterns or connections in the data. It uses unsupervised machine learning to identify groups or clusters of data points with comparable properties based on their similarity or distance.

Clustering has numerous applications in a wide range of industries :
- Customer segmentation : customize customers under customer segmentation using information about their purchase history and demographics. Businesses use this to learn more about their customers and personalize their service offerings
- Image segmentation : allows you to divide images into categories based on color and content. This is useful for image analysis and computer vision applications
- Anomaly detection : identifies data points that are unusual or abnormal and do not fit into any of the established clusters. It's useful in the detection of fraud and in cybersecurity
- Document clustering : groups together documents that are similar in content and keywords. It's useful for retrieving information and grouping news articles such as sports, politics, and so on
- Recommendation systems : group comparable items or products based on customer behavior. They are useful for creating recommendation systems that recommend products or services to e-commerce customers

### Clustering types and algorithms

- Partitioning clustering : this is a type of clustering in which the dataset is partitioned or clustered into k partitions or clusters, where k is a user-specified parameter. Partitioning clustering algorithms include k-means and k-medoids. 
- Hierarchical clustering : this is a type of clustering in which the dataset is divided into clusters in a hierarchy, with each level of the hierarchy representing a different level of granularity. Hierarchical clustering algorithms include agglomerative clustering and divisive clustering. 
- Density-based clustering : this type of clustering defines clusters as dense regions of data points separated by lower density regions. DBSCAN and OPTICS are two density-based clustering algorithms.

###  Generative AI Overview and Use Cases

Artificial Intelligence (AI) is defined as Augmented Intelligence that enables experts to scale their capabilities while machines handle time-consuming tasks like recognizing speech, playing games, and making decisions. On the other hand, Generative Artificial Intelligence, or GenAI, is an AI technique capable of creating new and unique data, ranging from images and music to text and entire virtual worlds.

Unlike conventional AI models that rely on pre-defined rules and patterns, Generative AI models use deep learning techniques and rely on vast datasets to generate entirely new data with various applications. A Generative AI model can also use LLM, Large Language Model, a type of artificial intelligence based on deep learning techniques designed to process and generate natural language.

In the field of healthcare and precision medicine, Generative AI can support physicians in identifying genetic mutations responsible for patients' illnesses and providing tailored treatments. It can also produce medical images, simulate surgeries, and predict new drug properties to aid doctors in practicing procedures and developing treatments. 

In agriculture, Generative AI can optimize crop yields and create more robust plant varieties that can withstand environmental stressors, pests, and diseases. 

In biotechnology, Generative AI can aid in the development of new therapies and drugs by identifying potential drug targets, simulating drug interactions, and forecasting drug efficacy. 

In forensics, Generative AI can help solve crimes by analyzing DNA evidence and identifying suspects. In environmental conservation, Generative AI can support the protection of endangered species by analyzing their genetic data and suggesting breeding and conservation strategies. In creative fields, Generative AI can produce unique digital art, music, and video content for advertising and marketing campaigns, and generate soundtracks for films or video games. 

In gaming, Generative AI can create interactive game worlds by generating new levels, characters, and objects that adapt to player behavior. In fashion, Generative AI can design and produce virtual try-on experiences for customers and recommend personalized fashion choices based on customer behavior and preferences.

### Spark for Data Engineers

Spark is a distributed computing system that processes large-scale datasets. It overcomes the drawbacks of the Hadoop MapReduce framework, which was slow and ineffective for specific data processing jobs. Spark provides a unified computing engine that supports a variety of data processing tasks, such as batch processing, stream processing, machine learning, and graph processing.

The first key feature of Spark is its in-memory processing capability. This feature of Spark enables caching of data in the memory and eliminates the high I/O costs associated with traditional disk-based processing systems. The second crucial feature of Spark is its support for several programming languages, including Java, Scala, Python, and R. Spark offers an easy to use API that allows developers to construct data processing applications efficiently. The third feature of Spark is its ability to build scalable and efficient data processing systems.


#### SparkML
Spark MLlib is a scalable and distributed machine learning library that integrates seamlessly with the Spark ecosystem. Spark ML provides a number of regression algorithms including linear regression, decision tree regression, random forest regression, gradient-boosted tree regression, and others. These algorithms enable you to build models that can predict a continuous target variable based on a set of input features.

The general steps for performing regression analysis using Spark ML are as follows :
1, import the necessary libraries. 
2, create a SparkSession. 
3, read the data from a CSV file. 
4, select the relevant columns. 
5, create a vector assembler. 
6, transform the selected data using the vector assembler. 
7, split the transform data into training and test sets. 
8, create an instance of the linear regression model. 
9, fit the model to the training data. 
10, make predictions on the test data. 
11, evaluate the model. 
12, print the root mean squared error, RMSE. 
13, print the coefficients and intercept of the model and 
14, stop the SparkSession.

Here is an example :

```python
# Regression using SparkML : use SparkML to predict the mileage of a car  
  
import findspark  
findspark.init()  
  
from pyspark.sql import SparkSession  
  
# import functions/Classes for sparkml  
from pyspark.ml.feature import VectorAssembler  
from pyspark.ml.regression import LinearRegression  
  
# import functions/Classes for metrics  
from pyspark.ml.evaluation import RegressionEvaluator  
  
#Create SparkSession  
spark = SparkSession.builder.appName("Regressing using SparkML").getOrCreate()  
  
# Load mpg dataset  
mpg_data = spark.read.csv("../Data/mpg.csv", header=True, inferSchema=True)  
  
# Explore the dataset  
mpg_data.printSchema()  
mpg_data.show(5)  
  
# Group the input Cols as single column named "features" using VectorAssembler  
assembler = VectorAssembler(inputCols=["Cylinders", "Engine Disp", "Horsepower", "Weight", "Accelerate", "Year"], outputCol="features")  
mpg_transformed_data = assembler.transform(mpg_data)  
  
# Display the assembled "features" and the label column "MPG"  
mpg_transformed_data.select("features","MPG").show()  
  
# Split data into training and testing sets : 70% training data, 30% testing data  
# The random_state variable "seed" controls the shuffling applied to the data before applying the split.  
# Pass the same integer for reproducible output across multiple function calls  
(training_data, testing_data) = mpg_transformed_data.randomSplit([0.7, 0.3], seed=42)  
  
# Train a linear regression model  
lr = LinearRegression(featuresCol="features", labelCol="MPG")  
model = lr.fit(training_data)  
  
# Evaluate the model  
# Make predictions on testing data  
predictions = model.transform(testing_data)  
  
# R-squared (R2) - higher the value better the performance.  
evaluator = RegressionEvaluator(labelCol="MPG", predictionCol="prediction", metricName="r2")  
r2 = evaluator.evaluate(predictions)  
print("R Squared =", r2)  
  
# Root Mean Squared Error (RMSE) - lower values indicate better performance.  
evaluator = RegressionEvaluator(labelCol="MPG", predictionCol="prediction", metricName="rmse")  
rmse = evaluator.evaluate(predictions)  
print("RMSE =", rmse)  
  
# Mean Absolute Error (MAE)-lower values indicate better performance.  
evaluator = RegressionEvaluator(labelCol="MPG", predictionCol="prediction", metricName="mae")  
mae = evaluator.evaluate(predictions)  
print("MAE =", mae)  
  
# Stop the Spark session  
spark.stop()
```
##### Spark GraphFrames

GraphFrames is an extension to Apache Spark that enables Spark to perform graph processing, run computations, and analyze standard graphs. 
GraphFrames based on Spark DataFrames uses the same APIs and represents data as a DataFrame. 
The use of DataFrames is the main difference between GraphFrames and GraphX which is based on RDDs. 
GraphFrames contains standard graph-based algorithms capable of covering most use cases.

GraphFrames currently supports the following widely used algorithms :
- Breadth-first search (BFS) 
- Connected components
- Label Propagation Algorithm (LPA)
- PageRank
- shortest paths 
- Triangle count


mongoexport --uri="mongodb://root:vqlUbC6SbK6DCKE9ZjuzF4Ki@172.21.102.84:27017/catalog" --collection=electronics --type=csv --fields=_id, type, model --out=electronics.csv

mongoexport --uri="mongodb://root:vqlUbC6SbK6DCKE9ZjuzF4Ki@172.21.102.84:27017/catalog" --collection=electronics --type=csv --fields=_id,type,model --out=electronics.csv


mongodb://<credentials>@172.21.102.84:27017/?directConnection=true&appName=mongosh+2.3.4
