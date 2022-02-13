---
layout: page
title: Distributed Machine Learning with Google Cloud ML
permalink: /clouds/google/qwiklabs/data-science-on-google-cloud-platform-machine-learning/distributed-machine-learning-with-google-cloud-ml/
---

# [GSP274] Distributed Machine Learning with Google Cloud ML

https://www.qwiklabs.com/focuses/3389?parent=catalog

<br/>

### Overview

In this lab you will create and configure deep neural network models with Google Cloud ML, then use the Google Cloud ML Engine to make predictions using your trained models.

You will extend the basic Google Cloud ML machine learning framework developed in the previous lab in this quest, Machine Learning with TensorFlow, to explore a number of approaches to optimizing machine learning models.

The base data set that is used for these labs provides historic information about internal flights in the United States and has been retrieved from the US Bureau of Transport Statistics website. This data set can be used to demonstrate a wide range of data science concepts and techniques and is used in all of the other labs in the Data Science on the Google Cloud Platform and Data Science on Google Cloud Platform: Machine Learning quests. The specific data files used in this lab provide separate training and evaluation data sets. The details about how these files can be produced is covered in a previous lab in this quest, Processing Time Windowed Data with Apache Beam and Cloud Dataflow (Java).

Cloud Datalab is a powerful interactive tool created to explore, analyze, transform, and visualize data and build machine learning models on Google Cloud Platform. It runs on Google Compute Engine and connects to multiple cloud services such as Google BigQuery, Cloud SQL, or simple text data stored on Google Cloud Storage,so you can focus on your data science tasks.

Google BigQuery is a RESTful web service that enables interactive analysis of massively large datasets working in conjunction with Google Cloud Storage.

Objectives

* Extend a Python TensorFlow machine learning framework to use a deep neural network classifier
* Modify the deep neural network classifier to implement a wide and deep model
* Deploy a trained model to the Cloud ML Engine and make predictions using Python to execute API calls to the Cloud ML Engine


<br>

### Lab setup


GCP console > Compute Engine > VM instances > SSH

    $ sudo apt -y update && sudo apt -y upgrade

    $ sudo apt -y install python-pip

    $ sudo pip install --upgrade pip

    $ export PROJECT_ID=$(gcloud info --format='value(config.project)')

    $ export BUCKET=${PROJECT_ID}

<br/>

### Create a deep neural network machine learning model


The previous lab in this quest, Machine Learning with TensorFlow, showed you how to create a Python based machine learning model that could be used by Google Cloud ML Engine to develop a machine learning model using a linear classifier and how to modify that model to use a variety of feature sets.

In this lab you will continue to work with the same Python framework to implement a basic deep neural network model, then extend that to a wide and deep neural network model that combines features to evaluate how they might improve your model's performance.

Copy the pre-configured Python model code from your storage bucket into local storage in your lab VM:


    $ gsutil cp gs://${BUCKET}/flights/chapter9/linear-model.tar.gz ~
    $ cd ~
    $ tar -zxvf linear-model.tar.gz
    $ cd ~/tensorflow

<br/>

    $ vi ~/tensorflow/flights/trainer/model.py

Add a function that applies a dimensionality reduction function to the categorical fields. This reduces the number of one-hot encoded variables from the 1000 discretized values that was used when experimenting with the linear categorizer. Embedding converts a sparse data field that might be represented with 1000 separate columns when one-hot encoded and instead maps those to a much smaller number, significantly reducing the number of variable dimensions that have to be dealt with.

Insert the following code below the linear_model function, above the definition for the serving_input_fn:

```
def create_embed(sparse_col):
    dim = 10 # default
    if hasattr(sparse_col, 'bucket_size'):
       nbins = sparse_col.bucket_size
       if nbins is not None:
          dim = 1 + int(round(np.log2(nbins)))
    return tflayers.embedding_column(sparse_col, dimension=dim)

# function definition to create the deep neural network model
def dnn_model(output_dir):
    real, sparse = get_features()
    all = {}
    all.update(real)

    # create embeddings of the sparse columns
    embed = {
       colname : create_embed(col) \
          for colname, col in sparse.items()
    }
    all.update(embed)

    estimator = tflearn.DNNClassifier(
         model_dir=output_dir,
         feature_columns=all.values(),
         hidden_units=[64, 16, 4])
    estimator = tf.contrib.estimator.add_metrics(estimator, my_rmse)
    return estimator
```

Page down to the run_experiment function at the end of the file. You need to reconfigure the experiment to call the deep neural network estimator function instead of the linear classifier function.

Replace the line estimator = linear_model(output_dir)with:

```
  #estimator = linear_model(output_dir)
  estimator = dnn_model(output_dir)
```

Now set some environment variables to point to Cloud Storage buckets for the source and output locations for your data and model:

    $ export REGION=us-central1
    $ export OUTPUT_DIR=gs://${BUCKET}/flights/chapter9/output
    $ export DATA_DIR=gs://${BUCKET}/flights/chapter8/output

The source data for this exercise was copied in to this location for you when the lab was launched.

You are now ready to submit a job to the Cloud ML Engine using your Python model to process the larger dataset using the distributed cloud resources for TensorFlow available using Google Cloud ML.

Create a jobname to allow you to identify the job and change to the working directory:    

    $ export JOBNAME=dnn_flights_$(date -u +%y%m%d_%H%M%S)
    $ cd ~/tensorflow

Submit the Cloud-ML task providing region, cloud storage buckets for staging and working data, the training package directory, the training module name and other parameters. The custom parameters for your training job, such as the location of the training and evaluation data, are provided as custom parameters after all of the gcloud parameters.

    $ gcloud ml-engine jobs submit training $JOBNAME \
      --module-name=trainer.task \
      --package-path=$(pwd)/flights/trainer \
      --job-dir=$OUTPUT_DIR \
      --staging-bucket=gs://$BUCKET \
      --region=$REGION \
      --scale-tier=STANDARD_1 \
      --runtime-version=1.10 \
      -- \
      --output_dir=$OUTPUT_DIR \
      --traindata $DATA_DIR/train* --evaldata $DATA_DIR/test*

<br/>

AI Platform > jobs > dnn_flights-YYMMDD-HHMMSS

Ждем минут 5-10. По завершению > View Logs link.

You will see a large number of events but there will be an event towards the end of the job run with a description that starts with "Saving dict for global step ...".

Click the event to open the details.

You will see the full list of analysis metrics listed as shown below.

<br/>

### Add a wide and deep neural network model

You will now extend the model to include additional features by creating features that allow you to associate airports with broad geographic zones and from those derive simplified air traffic corridors. You start by creating location buckets for an n*n grid covering the USA and then assign each departure and arrival airport to their specific grid locations. You can also create additional features using a technique called feature crossing that creates combinations of features that may provide useful insights when combined. In this case grouping departure and arrival grid location combinations together to create an approximation of air traffic corridors and also grouping origin and destination airport ID combinations to use each such pair as a feature in the model.

Switch back to the SSH console and enter the following command to edit the model.py function again:

    $ vi ~/tensorflow/flights/trainer/model.py

Add the following two functions above the linear_model function to implement a wide and deep neural network model:

```
def parse_hidden_units(s):
    return [int(item) for item in s.split(',')]

def wide_and_deep_model(output_dir,nbuckets=5,
                        hidden_units='64,32', learning_rate=0.01):
    real, sparse = get_features()
    # lat/lon cols can be discretized to "air traffic corridors"
    latbuckets = np.linspace(20.0, 50.0, nbuckets).tolist()
    lonbuckets = np.linspace(-120.0, -70.0, nbuckets).tolist()
    disc = {}
    disc.update({
       'd_{}'.format(key) : \
           tflayers.bucketized_column(real[key], latbuckets) \
           for key in ['dep_lat', 'arr_lat']
    })
    disc.update({
       'd_{}'.format(key) : \
           tflayers.bucketized_column(real[key], lonbuckets) \
           for key in ['dep_lon', 'arr_lon']
    })
    # cross columns that make sense in combination
    sparse['dep_loc'] = tflayers.crossed_column( \
           [disc['d_dep_lat'], disc['d_dep_lon']],\
           nbuckets*nbuckets)
    sparse['arr_loc'] = tflayers.crossed_column( \
           [disc['d_arr_lat'], disc['d_arr_lon']],\
           nbuckets*nbuckets)
    sparse['dep_arr'] = tflayers.crossed_column( \
           [sparse['dep_loc'], sparse['arr_loc']],\
           nbuckets ** 4)
    sparse['ori_dest'] = tflayers.crossed_column( \
           [sparse['origin'], sparse['dest']], \
           hash_bucket_size=1000)

    # create embeddings of all the sparse columns
    embed = {
       colname : create_embed(col) \
          for colname, col in sparse.items()
    }
    real.update(embed)

    #lin_opt=tf.train.FtrlOptimizer(learning_rate=learning_rate)
    #l_rate=learning_rate*0.25
    #dnn_opt=tf.train.AdagradOptimizer(learning_rate=l_rate)
    estimator = tflearn.DNNLinearCombinedClassifier(
         model_dir=output_dir,
         linear_feature_columns=sparse.values(),
         dnn_feature_columns=real.values(),
         dnn_hidden_units=parse_hidden_units(hidden_units))
         #linear_optimizer=lin_opt,
         #dnn_optimizer=dnn_opt)
    estimator = tf.contrib.estimator.add_metrics(estimator, my_rmse)
    return estimator
```

Page down to the run_experiment function at the end of the file. You need to reconfigure the experiment to call the deep neural network estimator function instead of the linear classifier function.

Replace the line estimator = dnn_model(output_dir) with:

```
  # estimator = linear_model(output_dir)
  # estimator = dnn_model(output_dir)
  estimator =  wide_and_deep_model(output_dir, 5, '64,32', 0.01)
```

<br/>

    $ export OUTPUT_DIR=gs://${BUCKET}/flights/chapter9/output2
    $ export JOBNAME=wide_and_deep_flights_$(date -u +%y%m%d_%H%M%S)

<br/>


    // Submit the Cloud-ML task for the new model:
    $ gcloud ml-engine jobs submit training $JOBNAME \
      --module-name=trainer.task \
      --package-path=$(pwd)/flights/trainer \
      --job-dir=$OUTPUT_DIR \
      --staging-bucket=gs://$BUCKET \
      --region=$REGION \
      --scale-tier=STANDARD_1 \
      --runtime-version=1.10 \
      -- \
      --output_dir=$OUTPUT_DIR \
      --traindata $DATA_DIR/train* --evaldata $DATA_DIR/test*


AI Platform > jobs >  REFRESH

named wide_and_deep_flights-YYMMDD-HHMMSS > View Logs link.

You will see a large number of events but there will be an event towards the end of the job run with a description that starts with "Saving dict for global step ...".

Click the event to open the details.

You will see the full list of analysis metrics listed as shown below.


<br/>

### Changing the learning rate

Switch back to the SSH session and enter the following command to edit the model.py function again:

    $ vi ~/tensorflow/flights/trainer/model.py

Page down until you find the def **wide_and_deep_model** definition. You will now remove the comments on the lines that configure the linear optimizer learning rate and the dnn optimizer learning rate. There are three lines just before the estimator definition where you need to remove comments, and then two lines inside the estimator definition where the comments must also be removed.

Нужно разкомментировать в методе **wide_and_deep_model** закомментированные строки перед определением **estimator**. 

И из строки dnn_hidden_units=parse_didden_units(hidden_units)) убалить в конце закрывающуюся скобку и поставить запятую.

This changes the optimizers from their default learning rate for the FtrlOptimizer which is either 0.2 or 1/sqrt(N) where N is the number of linear columns. In this case that means the learning rate for that optimizer is 0.2. For the AdagradOptimizer used by the DNN section the classifier uses a default learning rate of 0.05. Your code has now changed these learning rates to 0.01 for the FtrlOptimizer and 0.0025 for the AdagradOptimizer.

<br/>

    $ export OUTPUT_DIR=gs://${BUCKET}/flights/chapter9/output3
    $ export JOBNAME=learn_rate_flights_$(date -u +%y%m%d_%H%M%S)

<br/>

    $ gcloud ml-engine jobs submit training $JOBNAME \
      --module-name=trainer.task \
      --package-path=$(pwd)/flights/trainer \
      --job-dir=$OUTPUT_DIR \
      --staging-bucket=gs://$BUCKET \
      --region=$REGION \
      --scale-tier=STANDARD_1 \
      --runtime-version=1.10 \
      -- \
      --output_dir=$OUTPUT_DIR \
      --traindata $DATA_DIR/train* --evaldata $DATA_DIR/test*

<br/>

AI Platform > jobs >  REFRESH 

learn_rate_flights-YYMMDD-HHMMSS > View Logs link.

You will see a large number of events but there will be an event towards the end of the job run with a description that starts with "Saving dict for global step ...".

Click the event to open the details.

You will see the full list of analysis metrics listed as shown below.

<br/>

### Deploying and using the Model

Now that you have a trained model you can use it to make predictions. The evaluation specification for the experimental model in the code defined a serving input function that will handle REST API calls to the model once it has been loaded into ML Engine:

    $ MODEL_LOCATION=$(gsutil ls $OUTPUT_DIR/export/exporter | tail -1)

Now you create a model and an initial version of the model:


    $ gcloud ml-engine models create flights --regions us-central1

    $ gcloud ml-engine versions create v1 --model flights \
                                        --origin ${MODEL_LOCATION} \
                                        --runtime-version 1.10



This will take a few minutes to complete.

You will now use Python interactively to perform the query against the model.

First install the Google API client Python modules:

    $ sudo pip install --upgrade google-api-python-client
    $ sudo pip install --upgrade oauth2client

<br/>

    $ python

Now import the modules that you will need:

<br/>

```
from googleapiclient import discovery
from oauth2client.client import GoogleCredentials
import os
import json
```

Create the credential object to allow you to access the API:

```
credentials = GoogleCredentials.get_application_default()
```

<br/>

Create the API call:

```
api = discovery.build('ml', 'v1', credentials=credentials,
      discoveryServiceUrl=
     'https://storage.googleapis.com/cloud-ml/discovery/ml_v1_discovery.json')
PROJECT = os.environ['PROJECT_ID']
parent = 'projects/%s/models/%s/versions/%s' % (PROJECT, 'flights', 'v1')
```

Create a request object with some sample flight details:


```
request_data = {'instances':
  [
      {
        'dep_delay': 16.0,
        'taxiout': 13.0,
        'distance': 160.0,
        'avg_dep_delay': 13.34,
        'avg_arr_delay': 67.0,
        'carrier': 'AS',
        'dep_lat': 61.17,
        'dep_lon': -150.00,
        'arr_lat': 60.49,
        'arr_lon': -145.48,
        'origin': 'ANC',
        'dest': 'CDV'
      }
  ]
}

```

Query the API:


```
response = api.projects().predict(body=request_data, name=parent).execute()
print "response={0}".format(response)
```

This yields something similar to the following response:

```
response={
u'predictions': [{
u'probabilities': [0.45998525619506836, 0.5400148034095764], u'class_ids': [1],
u'classes': [u'1'],
u'logits': [0.1604020595550537],
u'logistic': [0.5400148034095764]
}]}
```

This shows you that with this model, the probability that the flight is late is 0.46. Note that since the model has been trained on a very limited demo data set this prediction is not reliable, but you can see just how easy it is to make a trained model into an operational function within an application using Google Cloud ML Engine.