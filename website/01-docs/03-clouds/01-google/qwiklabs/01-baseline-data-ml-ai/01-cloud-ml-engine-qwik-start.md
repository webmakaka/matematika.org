---
layout: page
title: Cloud ML Engine - Qwik Start
permalink: /clouds/google/qwiklabs/baseline-data-ml-ai/cloud-ml-engine-qwik-start/
---

# [GSP076] Cloud ML Engine: Qwik Start

https://google.qwiklabs.com/focuses/581?parent=catalog

<br/>

### Overview

This lab will give you hands-on practice with TensorFlow model training, both locally and on Cloud ML Engine. After training, you will learn how to deploy your model to Cloud ML Engine for serving (prediction). You'll train your model to predict income category of a person using the United States Census Income Dataset.

This lab gives you an introductory, end-to-end experience of training and prediction on Cloud Machine Learning Engine. The lab will use a census dataset to:

Create a TensorFlow training application and validate it locally.
* Run your training job on a single worker instance in the cloud.
* Run your training job as a distributed training job in the cloud.
* Optimize your hyperparameters by using hyperparameter tuning.
* Deploy a model to support prediction.
* Request an online prediction and see the response.
* Request a batch prediction.

<br/>

### What you will build

The sample builds a wide and deep model for predicting income category based on United States Census Income Dataset. The two income categories (also known as labels) are:

* >50K — Greater than 50,000 dollars
* <=50K — Less than or equal to 50,000 dollars

Wide and deep models use deep neural nets (DNNs) to learn high-level abstractions about complex features or interactions between such features. These models then combine the outputs from the DNN with a linear regression performed on simpler features. This provides a balance between power and speed that is effective on many structured data problems.

The sample defines the model using TensorFlow's prebuilt DNNCombinedLinearClassifier class. The sample defines the data transformations particular to the census dataset, then assigns these (potentially) transformed features to either the DNN or the linear portion of the model.

<br>

### Lab setup

**Install TensorFlow**

    $ pip install --user --upgrade tensorflow

    $ python -c "import tensorflow as tf; print('TensorFlow version {} is installed.'.format(tf.VERSION))"

<br/>

**Clone the example repo**

    $ git clone https://github.com/GoogleCloudPlatform/cloudml-samples.git
    $ cd cloudml-samples/census/estimator

<br/>

**Get your training data**

    $ mkdir data
    $ gsutil -m cp gs://cloud-samples-data/ml-engine/census/data/* data/

<br/>

    $ export TRAIN_DATA=$(pwd)/data/adult.data.csv
    $ export EVAL_DATA=$(pwd)/data/adult.test.csv

<br/>

**Install dependencies**

    $ pip install --user -r ../requirements.txt


### Run a local training job

    $ export MODEL_DIR=output

    $ gcloud ai-platform local train \
        --module-name trainer.task \
        --package-path trainer/ \
        --job-dir $MODEL_DIR \
        -- \
        --train-files $TRAIN_DATA \
        --eval-files $EVAL_DATA \
        --train-steps 1000 \
        --eval-steps 100

<br/>

    $ tensorboard --logdir=$MODEL_DIR --port=8080


Click on the Web Preview icon, then Preview on port 8080.

<br/>

![Cloud ML Engine - Qwik Start](/img/clouds/google/qwiklabs/baseline-data-ml-ai/cloud-ml-engine-qwik-start/pic1.png "Cloud ML Engine - Qwik Start"){: .center-image }

<br/><br/>

![Cloud ML Engine - Qwik Start](/img/clouds/google/qwiklabs/baseline-data-ml-ai/cloud-ml-engine-qwik-start/pic2.png "Cloud ML Engine - Qwik Start"){: .center-image }

<br/><br/>

![Cloud ML Engine - Qwik Start](/img/clouds/google/qwiklabs/baseline-data-ml-ai/cloud-ml-engine-qwik-start/pic3.png "Cloud ML Engine - Qwik Start"){: .center-image }

<br/><br/>

![Cloud ML Engine - Qwik Start](/img/clouds/google/qwiklabs/baseline-data-ml-ai/cloud-ml-engine-qwik-start/pic4.png "Cloud ML Engine - Qwik Start"){: .center-image }

<br/><br/>

![Cloud ML Engine - Qwik Start](/img/clouds/google/qwiklabs/baseline-data-ml-ai/cloud-ml-engine-qwik-start/pic5.png "Cloud ML Engine - Qwik Start"){: .center-image }

<br/>

Type CTRL+C in Cloud Shell to shut down TensorBoard.

<br/>

### Running model prediction locally (in Cloud Shell)

    $ ls output/export/census/
    1560123948

    // $ gcloud ai-platform local predict \
    // --model-dir output/export/census/<timestamp> \
    // --json-instances ../test.json

    $ gcloud ai-platform local predict \
    --model-dir output/export/census/1560123948 \
    --json-instances ../test.json

<br/>

    CLASS_IDS  CLASSES  LOGISTIC              LOGITS                 PROBABILITIES
    [0]        [u'0']   [0.0504511296749115]  [-2.9349818229675293]  [0.9495488405227661, 0.0504511296749115]

<br/>

Where class 0 means income <= 50k and class 1 means income >50k.


<br/>

### Use your trained model for prediction

Once you've trained your TensorFlow model, you can use it for prediction on new data. In this case, you've trained a census model to predict income category given some information about a person.

For this section we'll use a predefined prediction request file, test.json, included in the GitHub repository in the census directory. You can inspect the JSON file in the Cloud Shell editor if you're interested in learning more.

<br/>

### Run your training job in the cloud

    $ PROJECT_ID=$(gcloud config list project --format "value(core.project)")
    $ BUCKET_NAME=${PROJECT_ID}-mlengine
    $ echo $BUCKET_NAME
    $ REGION=us-central1

<br/>

    // Create the new bucket:
    $ gsutil mb -l $REGION gs://$BUCKET_NAME

    // Copy the two files to your Cloud Storage bucket:
    $ gsutil cp -r data gs://$BUCKET_NAME/data

    $ TRAIN_DATA=gs://$BUCKET_NAME/data/adult.data.csv
    $ EVAL_DATA=gs://$BUCKET_NAME/data/adult.test.csv

    // Copy the JSON test file test.json to your Cloud Storage bucket:
    $ gsutil cp ../test.json gs://$BUCKET_NAME/data/test.json

    $ TEST_JSON=gs://$BUCKET_NAME/data/test.json

<br/>

### Run a single-instance trainer in the cloud


    $ JOB_NAME=census_single_1
    $ OUTPUT_PATH=gs://$BUCKET_NAME/$JOB_NAME

    // Submit a training job in the cloud
    $ gcloud ai-platform jobs submit training $JOB_NAME \
        --job-dir $OUTPUT_PATH \
        --runtime-version 1.10 \
        --module-name trainer.task \
        --package-path trainer/ \
        --region $REGION \
        -- \
        --train-files $TRAIN_DATA \
        --eval-files $EVAL_DATA \
        --train-steps 1000 \
        --eval-steps 100 \
        --verbosity DEBUG

    // monitor the progress of your training job
    $ gcloud ai-platform jobs stream-logs $JOB_NAME

Or monitor in the Console: ML Engine > Jobs.
(Вроде на самом деле AI Platform --> Jobs)

Как все закончится:
Task completed successfully.

    $ gsutil ls -r $OUTPUT_PATH

The outputs should be similar to the outputs from training locally (above).


<br/>

    // Если есть желание посмотреть на визуализацию.
    // Preview on port 8080
    $ tensorboard --logdir=$OUTPUT_PATH --port=8080

<br/>

### Deploy your model to support prediction

    $ MODEL_NAME=census

    // Create a Cloud ML Engine model:
    $ gcloud ai-platform models create $MODEL_NAME --regions=$REGION


    $ gsutil ls -r $OUTPUT_PATH/export
    ***
    gs://qwiklabs-gcp-e56ffdf830559bdd-mlengine/census_single_1/export/census/1560125020/
    ***

<br/>

    // $ MODEL_BINARIES=$OUTPUT_PATH/export/census/<timestamp>/
    $ MODEL_BINARIES=$OUTPUT_PATH/export/census/1560125020/

<br/>

    // Run the following command to create a version v1 of your model
    $ gcloud ai-platform versions create v1 \
    --model $MODEL_NAME \
    --origin $MODEL_BINARIES \
    --runtime-version 1.10

<br/>

    // list of your models 
    $ gcloud ai-platform models list
    NAME    DEFAULT_VERSION_NAME
    census  v1

<br/>

### Send an online prediction request to your deployed model


You can now send prediction requests to your deployed model. The following command sends a prediction request using the test.json file included in the GitHub repository.

    $ gcloud ai-platform predict \
      --model $MODEL_NAME \
      --version v1 \
      --json-instances ../test.json 


<br/>

    CLASS_IDS  CLASSES  LOGISTIC               LOGITS                PROBABILITIES
    [0]        [u'0']   [0.07608077675104141]  [-2.496829032897949]  [0.9239192008972168, 0.07608077675104141]

<br/>

The response includes the probabilities of each label (>50K and <=50K) based on the data entry in test.json, thus indicating whether the predicted income is greater than or less than 50,000 dollars

Where class 0 means income <= 50k and class 1 means income >50k.

