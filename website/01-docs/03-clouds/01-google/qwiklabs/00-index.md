---
layout: page
title: qwiklabs
permalink: /clouds/google/qwiklabs/
---

# qwiklabs

<br/>

    // Скопировать ID проекта
    $ export GCP_PROJECT=$(gcloud config get-value core/project)

    $ echo ${GCP_PROJECT}

<br/>

**Создать bucket**


    $ export MYBUCKET="${GCP_PROJECT}-bucket"

    $ echo ${MYBUCKET}

    // create the bucket
    $ gsutil mb gs://${MYBUCKET}

    // Run this to see the contents of your bucket
    $ gsutil ls -R gs://${MYBUCKET}

<br/>

**Create a Cloud Dataproc cluster**

    $ MYCLUSTER="${USER/_/-}-qwiklab"

    $ echo MYCLUSTER=${MYCLUSTER}

    $ gcloud config set compute/zone us-central1-a

    $ gcloud dataproc clusters create ${MYCLUSTER} --worker-machine-type=n1-standard-2 --master-machine-type=n1-standard-2

<br/>

### Cloud Datalab (Что-то вроде jupyter notebook)

Dataflow API должен быть enabled

    $ datalab create babyweight --zone us-central1-c --no-create-repository




<br/>

## Quest's

### [Baseline: Data, ML, AI](/clouds/google/qwiklabs/baseline-data-ml-ai/)

### [Data Science on Google Cloud Platform: Machine Learning](/clouds/google/qwiklabs/data-science-on-google-cloud-platform-machine-learning/)

### [Google Cloud Solutions II: Data and Machine Learning](/clouds/google/qwiklabs/google-cloud-solutions-2-data-and-machine-learning/)

### [Advanced ML: ML Infrastructure](/clouds/google/qwiklabs/advanced-ml-infrastructure/)

### [Intermediate ML: TensorFlow on GCP](/clouds/google/qwiklabs/intermediate-ml-tensorflow-on-gcp/)