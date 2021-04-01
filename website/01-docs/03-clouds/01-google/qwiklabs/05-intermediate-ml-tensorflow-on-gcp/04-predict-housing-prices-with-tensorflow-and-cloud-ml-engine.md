---
layout: page
title: Predict Housing Prices with Tensorflow and Cloud ML Engine
permalink: /clouds/google/qwiklabs/intermediate-ml-tensorflow-on-gcp/predict-housing-prices-with-tensorflow-and-cloud-ml-engine/
---

# [GSP418] Predict Housing Prices with Tensorflow and Cloud ML Engine

https://run.qwiklabs.com/focuses/3644?parent=catalog


<br/>

Делаю:  
02.08.2019

<br/>

Собственно можно сразу посмотреть:

https://github.com/vijaykyr/tensorflow_teaching_examples/blob/master/housing_prices/cloud-ml-housing-prices.ipynb

А выполнение в datalab завершилось ошибкой, хотя я в нем ничего не менял.


<br/>

### Overview

In this lab you will build an end to end machine learning solution using Tensorflow + Cloud ML Engine and leverage the cloud for distributed training and online prediction.

tf.estimator — a high-level TensorFlow API that greatly simplifies machine learning programming. Estimators encapsulate the following actions:

* training
* evaluation
* prediction
* export for serving

This lab is focused on interacting with Datalab and Cloud ML Engine. Non-relevant concepts and code blocks are glossed over and are provided for you to execute in your Datalab notebook.

<br/>

### Datalab setup

Datalab is set up on a GCE VM. For that we need to specify the project and the zone where the VM is created. Typically Datalab is set up from your client machine (desktop/laptop) with Cloud SDK installed. Here we are going to use Cloud Shell as the client to run the installation commands.


    $ export PROJECT_ID=$(gcloud config list project --format "value(core.project)")

    $ gcloud config set core/project ${PROJECT_ID}
    $ gcloud config set compute/zone us-central1-f


    $ export BUCKET_NAME=${PROJECT_ID}-bucket

    // Create a storage bucket for your code packages to deploy to Cloud ML engine,
    $ gsutil mb -c multi_regional -l us gs://${BUCKET_NAME}

    // Create a Datalab VM instance by running the following:
    $ datalab create my-datalab --machine-type n1-standard-4

<br/>

[Y]

[Enter] [Enter]

<br/>

Ждем, пока не появится:

    Click on the *Web Preview* (square button at top-right), select *Change port > Port 8081*, and start using Datalab.


<br/>

Cloud Shell Web preview -> Change port -> 8081 -> Change and Preview


<br/>

### Download lab notebook

Open a new notebook by clicking the + Notebook button (top left of screen)

    !git clone https://github.com/vijaykyr/tensorflow_teaching_examples.git housing_prices

Shift + Enter


<br/>

### Open and execute the housing prices notebook

From within the Google Cloud Datalab console, switch back to the datalab home page and select 

datalab -> housing_prices -> housing_prices -> cloud-ml-housing-prices.ipynb 

In the top ribbon, click Clear > Clear all Cells:

From here, read the instructions in the notebook to complete the lab.


    // наверное бакет лучше задать следующим образом
    bucket = 'gs://' + datalab_project_id() + '-bucket'


Execute the cells one by one and observe the results. A convenient way to progress through the cells is by clicking in a cell, then click Shift + Enter, waiting for each cell to complete before progressing. Code cell completion is indicated by a blue bar on the left of the cell. Any output is printed below.

Read the instructions and the comments in the code blocks carefully. You will be asked to edit some of the code blocks before running them.

For example, you will be setting environment variables in the notebook, so add your bucket name and project ID before running the cell.

