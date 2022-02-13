---
layout: page
title: Processing Time Windowed Data with Apache Beam and Cloud Dataflow (Java)
permalink: /clouds/google/qwiklabs/data-science-on-google-cloud-platform-machine-learning/processin-time-windowed-data-with-apache-beam-and-cloud-dataflow/
---

# [GSP272] Processing Time Windowed Data with Apache Beam and Cloud Dataflow (Java)

https://www.qwiklabs.com/focuses/3392?parent=catalog

<br/>

### Overview

In this lab you will learn how to deploy a Java application using Maven to process data with Cloud Dataflow. This lab uses a Java application that implements time-windowed aggregation to augment the raw data in order to produce a consistent training and test datasets that can be used to refine you rmachine learning models in later labs.

The base data set that is used provides historical information about internal flights in the United States retrieved from the US Bureau of Transport Statistics website. This data set can be used to demonstrate a wide range of data science concepts and techniques and is used in all of the other labs in the Data Science on the Google Cloud Platform and Data Science on Google Cloud Platform: Machine Learning quests.

Time-windowed aggregate data sets are useful because they allow you to improve the accuracy of data models where behavior changes in a regular or semi-regular pattern over a period of time. For example with flights you know that, in general, taxi-out times increase during peak hour periods. You also know from earlier labs the arrival delay time that you are interested in modelling throughout these labs varies in response to taxi-out time. Airlines are aware of this too and adjust their scheduled arrival times to account for the average taxi-out time expected at the scheduled departure time at each airport. By computing time-windowed aggregate data you can more accurately model whether a given flight will be delayed by identify parameters, such as taxi-out time, that genuinely exceed the average for that time window.

Using Apache Beam to create these aggregate data sets is useful because it can be used in batch mode to create the training and test data sets using historical data but the same code can then be used in streaming mode to compute averages on real-time streaming data. This ability to use the same code helps mitigate any training-serving skew that could arise if a different language or platform was used to process the historical data and the streaming data.

Cloud Dataflow is a fully-managed service for transforming and enriching data in stream (real time) and batch (historical) modes via Java and Python APIs with the Apache Beam SDK. Cloud dataflow provides a serverless architecture that can be used to shard and process very large batch data sets, or high volume live streams of data, in parallel.

Google BigQuery is a RESTful web service that enables interactive analysis of massively large datasets working in conjunction with Google Storage.

Objectives

* Configure the Maven Apache project object model file
* Deploy the Java application to Apache Beam to create the aggregate training and test data files

<br/>

### Preparing your Environment


This lab uses a set of code samples and scripts developed for the Data Science on Google Cloud Platform book from O'Reilly Media, Inc. You will clone the sample repository from Github to the Cloud Shell and carry out all of the lab tasks from there. This lab demonstrates how to successfully configure and compile the code samples for Chapter 8, Time-Windows Aggregate Features.

    $ git clone https://github.com/GoogleCloudPlatform/data-science-on-gcp/

    $ cd ~/data-science-on-gcp/08_dataflow/

    $ export PROJECT_ID=$(gcloud info --format='value(config.project)')

    $ export BUCKET=${PROJECT_ID}

<br/>

Use Maven to create a new Cloud Dataflow project using the starter project archetype for Cloud Dataflow projects:


    // Create a new Cloud Dataflow project using the starter project archetype for Cloud Dataflow projects
    $ mvn archetype:generate \
      -DarchetypeArtifactId=google-cloud-dataflow-java-archetypes-starter \
      -DarchetypeGroupId=com.google.cloud.dataflow \
      -DgroupId=com.google.cloud.datascienceongcp.flights \
      -DartifactId=chapter8_temp \
      -Dversion="[1.0.0,2.0.0]" \
      -DinteractiveMode=false

<br/>

This will download a number of components and generate a basic dataflow Java application and the associated project object model in the chapter8_temp directory.


<br/>

### Deploy the Java application to Cloud Dataflow

    $ gsutil cp gs://$BUCKET/flights/chapter8/pom.xml ./chapter8/pom.xml

<br/>

    $ cd ~/data-science-on-gcp/08_dataflow/chapter8

<br/>

    $ mvn compile exec:java  -Dexec.mainClass=com.google.cloud.training.flights.CreateTrainingDataset -Dexec.args="--project=$PROJECT_ID --bucket=$BUCKET --fullDataset=true --maxNumWorkers=4 --autoscalingAlgorithm=THROUGHPUT_BASED"

<br/>

Ждем минут 10

<br/>

Navigation menu > Dataflow > createtrainingdataset

<br/>

Какие-то непонятные графики.

<br/>

Navigation menu > Storage > Browser > <bucket> > flights/chapter8/output

Click the file delays.csv to inspect it. This is the time aggregated average departure delay for every airport-hour combination in the training dataset. So, for example, the average departure delay at JFK between 9:00 AM and 10:00 AM is 43.8 minutes.

Close the tab and click one of the trainFlights-*.csv or testFlights-*.csv files to inspect it. It's not easy to see what the aggregated data provides here.

It will be easier to examine the data if you import these into Bigquery.

<br/>

Switch back to Cloud Shell.

The file mldataset.json defines the database schema that is needed to import the training and test datasets into BigQuery. This file is included in the source code for the lab, and is located in the directory ~/data-science-on-gcp/08_dataflow.

Enter the following commands to change to the directory where mldataset.json is located, then use the bq load commands to load the training and test datasets into BigQuery tables:

<br/>

    $ cd ..

    $ bq load -F , flights.trainFlights gs://$BUCKET/flights/chapter8/output/trainFlights* mldataset.json

    $ bq load -F , flights.testFlights gs://$BUCKET/flights/chapter8/output/testFlights* mldataset.json


<br/>

Navigation menu > BigQuery

Project ID > flights > trainFlights > Preview

<br/>

The aggregated data in these tables contains the important derived data for each of the training events, such as the actual departure delay experienced, whether the flight arrived on time or not, the time-window aggregated average departure delay for the departure airport and the time-window aggregated average arrival delay for the destination airport and arrival time slot.

You can inspect the flights > testFlights table to see that the evaluation data set contains similar data, but this is for an entirely separate subset of the data.