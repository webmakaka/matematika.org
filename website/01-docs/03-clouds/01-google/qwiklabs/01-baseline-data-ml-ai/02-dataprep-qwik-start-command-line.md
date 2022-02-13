---
layout: page
title: Dataprep - Qwik Start
permalink: /clouds/google/qwiklabs/baseline-data-ml-ai/dataprep-qwik-start-command-line/
---

# [GSP104] Dataproc: Qwik Start - Command Line

https://google.qwiklabs.com/focuses/585?parent=catalog

<br/>

### Overview


    $ gcloud dataproc clusters create example-cluster


    // Submit a sample Spark job that calculates a rough value for pi
    $ gcloud dataproc jobs submit spark --cluster example-cluster \
      --class org.apache.spark.examples.SparkPi \
      --jars file:///usr/lib/spark/examples/jars/spark-examples.jar -- 1000

    // To change the number of workers in the cluster to four,
    $ gcloud dataproc clusters update example-cluster --num-workers 4






