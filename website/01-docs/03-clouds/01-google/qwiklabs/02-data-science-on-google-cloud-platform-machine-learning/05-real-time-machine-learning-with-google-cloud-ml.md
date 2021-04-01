---
layout: page
title: Real Time Machine Learning with Google Cloud ML
permalink: /clouds/google/qwiklabs/data-science-on-google-cloud-platform-machine-learning/real-time-machine-learning-with-google-cloud-ml/
---

# [GSP275] Real Time Machine Learning with Google Cloud ML

https://www.qwiklabs.com/focuses/3393?parent=catalog

<br/>

### Overview

In this lab you will combine a number of the components developed in earlier labs in the Data Science on Google Cloud Platform and Data Science on Google Cloud Platform: Machine Learning quests to create a real-time flight delay prediction service using Google Cloud Platform services.

The base data set that is used for the prediction service in this lab provides historic information about internal flights in the United States and has been retrieved from the US Bureau of Transport Statistics website. This data set can be used to demonstrate a wide range of data science concepts and techniques, and is used in all of the other labs in the Data Science on Google Cloud Platform and Data Science on Google Cloud Platform: Machine Learning quests.

The real time flight delay prediction service will use a Google Cloud Machine Learning model to predict whether real time flights will arrive on time or not based on the data available at the time of their departure. The prediction service will use a streaming Google Cloud Dataflow job to process simulated real-time flight event data that is fed into Google Cloud PubSub. The code for the real time flight event simulation data is written in Python and the real time machine learning prediction code is written in Java.

Objectives

* Configure and execute a real time flight event simulation in Python.
* Configure and deploy a streaming Google Cloud Dataflow job to provide real-time flight delay predictions.


<br>

### Lab setup


GCP console > Compute Engine > VM instances > SSH

    $ sudo apt -y update && sudo apt -y upgrade

    $ sudo apt -y install git virtualenv
    
    $ export PROJECT_ID=$(gcloud info --format='value(config.project)')
    $ export BUCKET=${PROJECT_ID}
    $ export REGION=us-central1

    $ git clone  https://github.com/GoogleCloudPlatform/data-science-on-gcp/

A TensorFlow machine learning model that was trained using this data set has been copied into the Google Cloud storage bucket to use in this lab. This TensorFlow model was trained using the techniques covered in the previous labs in the Data Science on Google Cloud Platform: Machine Learning quest. Once a TensorFlow model has been trained and exported it can be reused by any TensorFlow engine. In this lab you will load the model directly from Google Cloud Storage into Google Cloud ML.

Set some additional environment variables to point to Cloud Storage buckets for the output locations for your data and source location for your model:

    $ export OUTPUT_DIR=gs://${BUCKET}/flights/chapter9/output
    $ export MODEL_LOCATION=$(gsutil ls $OUTPUT_DIR/export/exporter | tail -1)

The source data for this exercise was copied in to this location for you when the lab was launched.

Now create a model and an initial version of the model:

    $ gcloud ai-platform models create flights --regions $REGION

    $ gcloud ai-platform versions create v1 --model flights \
                                        --origin ${MODEL_LOCATION} \
                                        --runtime-version 1.7

This will take about five minutes to complete. While you are waiting, switch back to the GCP Console.

<br/>

### Configure Java components.

In the Console window click the SSH link to open a second SSH session to your lab VM.

Install Java 8:

    $ sudo apt-get install -yq openjdk-8-jdk
    $ sudo update-alternatives --set java /usr/lib/jvm/java-8-openjdk-amd64/jre/bin/java

    $ sudo apt-get install -yq maven

<br/>

    $ cd ~/data-science-on-gcp/10_realtime/chapter10

    $ vi src/main/java/com/google/cloud/training/flights/FlightsMLService.java

Нужно указать PROJECT_ID

    private static final String PROJECT = "cloud-training-demos";

<br/>

    $ mvn clean compile

<br/>

### Start the real-time simulation script

Switch back to the original SSH shell.

    $ cd ~/data-science-on-gcp/04_streaming/simulate

    // Configure virtualenv so that you can safely install Google Cloud Python modules:
    $ virtualenv --python=python env

    // Activate virtualenv
    $ source ./env/bin/activate

    $ pip install google-cloud-pubsub google-cloud-bigquery  google_compute_engine google-cloud-storage

    // Create default application credentials:
    $ gcloud auth application-default login

<br/>

    $ cd ~/data-science-on-gcp/04_streaming/simulate

    $ export PROJECT_ID=$(gcloud info --format='value(config.project)')

    // This script generates flight data events using the real flight data from 2015.
    $ python ./simulate.py --project $PROJECT_ID --startTime '2015-01-01 06:00:00 UTC' --endTime '2015-01-03 00:00:00 UTC' --speedFactor=60




<br/>

### Start the real-time prediction service

In the second SSH session where you installed Java, make sure you are still in the correct directory:

    $ cd ~/data-science-on-gcp/10_realtime/chapter10

Create the temporary Cloud Pub/Sub topic required by the prediction code:

    $ gcloud pubsub topics create dataflow_temp

Replace the pom.xml with a working version:

    $ export PROJECT_ID=$(gcloud info --format='value(config.project)')

    $ export BUCKET=${PROJECT_ID}

    $ gsutil cp gs://$BUCKET/flights/chapter10/java/pom.xml pom.xml

<br/>

Now compile and deploy the prediction code to Cloud Dataflow:

    $ mvn compile exec:java \
    -Dexec.mainClass=com.google.cloud.training.flights.AddRealtimePrediction \
    -Dexec.args="--realtime --speedupFactor=60 --maxNumWorkers=10 --autoscalingAlgorithm=THROUGHPUT_BASED --bucket=$BUCKET --project=$PROJECT_ID"    

<br/>

Google Cloud Console > Dataflow > job.

Once you see elements being written into Bigquery, continue to the next step. This will take a couple of minutes.

Cloud Console > BigQuery.

In the Query Editor, add the following query and click RUN QUERY:

    SELECT * from flights.predictions ORDER by notify_time DESC LIMIT 5

If the query has an error (red check mark) wait a couple more minutes.

Once you start to see data being written into this BigQuery table, the real-time prediction service running on Cloud Dataflow is generating predictions in real-time using the flight data generated by the simulate.py script.    