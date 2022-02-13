---
layout: page
title: Bayes Classification with Cloud Datalab, Spark, and Pig on Google Cloud Dataproc
permalink: /clouds/google/qwiklabs/data-science-on-google-cloud-platform-machine-learning/bayes-classification-with-cloud-datalab-spark-and-pig-on-google-cloud-dataproc/
---

# [GSP270] Bayes Classification with Cloud Datalab, Spark, and Pig on Google Cloud Dataproc

https://www.qwiklabs.com/focuses/3388?parent=catalog

<br/>

### Overview

In this lab you will learn how to deploy a Google Cloud Dataproc cluster with Google Cloud Datalab pre-installed. You will then use Spark to perform quantization of a dataset to improve the accuracy of the data modelling over the single variable approaches used in the earlier labs.

The data is stored in Google BigQuery and the analysis will be performed using Google Cloud Datalab running in Google Cloud Dataproc.

The base dataset that is used provides historical information about internal flights in the United States retrieved from the US Bureau of Transport Statistics website. This data set can be used to demonstrate a wide range of data science concepts and techniques and is used in all of the other labs in the Data Science on the Google Cloud Platform and Data Science on Google Cloud Platform: Machine Learning quests.

Cloud Dataproc is a fast, easy-to-use, fully-managed cloud service for running Apache Spark and Apache Hadoop clusters in a simple, cost-efficient way.

Cloud Datalab is a powerful interactive tool created to explore, analyze, transform and visualize data and build machine learning models on Google Cloud Platform. It runs on Google Compute Engine and connects to multiple cloud services such as Google BigQuery, Cloud SQL or simple text data stored on Google Cloud Storage so you can focus on your data science tasks.

Google BigQuery is a RESTful web service that enables interactive analysis of massively large datasets working in conjunction with Google Storage.

Objectives

* Create a Cloud DataProc cluster running Cloud Datalab.
* Create a training data model using Spark on Cloud Datalab
* Evaluate a data model using Cloud Datalab.
* Perform bulk data analysis using Apache Pig


<br>

### Lab setup

GCP console > Compute Engine > VM instances > SSH

    $ sudo apt -y update && sudo apt -y upgrade

    $ sudo apt -y install git
    
    $ git clone  https://github.com/GoogleCloudPlatform/data-science-on-gcp/

    $ cd data-science-on-gcp/06_dataproc

    $ export PROJECT_ID=$(gcloud info --format='value(config.project)')

    $ export BUCKET=${PROJECT_ID}

For the purposes of this lab you need to install a copy of the Data Science on GCP git repository into each node on the cluster. When deploying a Cloud Dataproc cluster you can provide additional initialization scripts to deploy additional software and perform additional configuration tasks on the cluster nodes. You will use this capability to install the sample code on each node in the cluster. The file install_on_cluster.sh contains the script that will do this but you need to update it first with the correct username before using this in a cluster deployment of your own.

Update the install_on_cluster.sh script with your lab username and make a temporary copy of it:

    $ sed "s/\CHANGE_TO_USER_NAME/$USER/g" install_on_cluster.sh > /tmp/install_on_cluster.sh

Create additional environment variables to store the preferred lab zone and storage bucket locations that will contain the installation shell scripts:

    $ export ZONE=us-central1-a

    $ export DS_ON_GCP=gs://$BUCKET/flights/dataproc/install_on_cluster.sh

    $ export DL_INSTALL=gs://$BUCKET/flights/dataproc/datalab.sh

Copy the locally prepared script that installs the Data Science on GCP git repository samples to your storage bucket.

    $ gsutil cp /tmp/install_on_cluster.sh $DS_ON_GCP

In addition to the initialization script that you have just modified you also need to specify the actions required to install Cloud Datalab. A script to do just that is stored in a public bucket at: gs://dataproc-initialization-actions/datalab/datalab.sh. When deploying the cluster you can specify this script along with the script you copied to your own storage bucket in the last step but it it is best practice to stage this script yourself.

Copy the standard Cloud Dataproc script that installs Cloud Datalab to a Cloud Dataproc cluster to your storage bucket.

    $ gsutil cp gs://dataproc-initialization-actions/datalab/datalab.sh $DL_INSTALL

<br/>

### Deploy a new Cloud Dataproc cluster

For the purposes of the lab you are limiting this cluster to one master node and two relatively small worker nodes.

Deploy the cluster:

    $ gcloud dataproc clusters create \
      --num-workers=2 \
      --scopes=cloud-platform \
      --worker-machine-type=n1-standard-2 \
      --master-machine-type=n1-standard-4 \
      --master-boot-disk-type=pd-ssd\
      --master-boot-disk-size=100GB\
      --worker-boot-disk-size=100GB\
      --worker-boot-disk-type=pd-ssd\
      --zone=$ZONE \
      --initialization-actions=$DL_INSTALL,$DS_ON_GCP \
      ch6cluster

This deployment will take about 10 minutes to complete. You can start the next set of tasks, but you will have to wait for the master node to be deployed in order to continue with the data analysis parts of the lab.

<br/>

### Bayes Classification

You are going to use this cluster to expand your prediction model to make it more selective. In earlier labs you built prediction models using a single variable, departure delay. Here you are going to improve on this by adding a second variable to the model, distance. Since longer flights have more time available to them to recover from initial delays it is reasonable to propose that flight distance will have an effect on arrival delay.

In order to see whether this has any merit you need to carry out some analysis on data, dividing the results into buckets so you can compare the various combinations.

For each of these buckets you can now carry out a separate decision calculation. The aim is to allow you to predict the point at which the probability of a flight arriving on time meets your criteria. In this case your criteria are that 70% of flights arrive with a delay of less than 15 minutes. You can carry out this analysis for each of these combinations and use that data as the basis for your predictive model. This technique is called Bayes classification.

This technique will work provided you have enough samples for each bin. For a model with two variables and almost 6 million samples you will have enough data, but if you extend this concept to more variables you will soon find that the number of samples for each combination of variables will become too small to use for reliable predictions.

A scalable approach for this, assuming that the variables you want to use for predictions are independent, is to calculate binned probabilities for each variable independently and then multiply the probabilities to get your actual final prediction. Since you are looking at just two variables, and you have a large dataset, you can bin the data and estimate all of the conditional probabilities directly.

Once the cluster is running and you can connect to Cloud Datalab running on it you will carry out the analysis.

<br/>

### Configure Firewall rules for Datalab access to the cluster

For security best practices reasons internet access to the cluster is not allowed by default. You need to create and apply a specific firewall rule to allow you to connect to the Cloud Datalab service running on the master node.

Create a firewall rule that allows TCP port 8080 access from the internet to any instances tagged with the master tag. This tag could be anything so long as it is consistent in this step and the next one. Since no specific VPC network has been specified, this firewall rule is configured for the default VPC network, which is where the cluster has been deployed.

Create the firewall by running the following:

    $ export MYRANGE=0.0.0.0/0

    $ gcloud compute firewall-rules create datalab-access \
      --allow tcp:8080 \
      --source-ranges $MYRANGE \
      --target-tags master

Now find the name of the compute instance that has been deployed as the master node for your cluster.

    $ export MASTERNODE=$(gcloud dataproc clusters describe ch6cluster --format='value(config.masterConfig.instanceNames)')

    $ echo $MASTERNODE

Add the appropriate network tag to the master node compute instance.

    $ gcloud compute instances add-tags $MASTERNODE --tags master --zone $ZONE

Echo the url of the Cloud Datalab session on the console. Click the link to open a Cloud Datalab session.

    $ export MASTERIP=$(gcloud compute instances describe $MASTERNODE --zone $ZONE --format='value(networkInterfaces[0].accessConfigs[0].natIP)')
    
    $ echo http://$MASTERIP:8080

http://35.222.165.127:8080/tree/datalab

<br/>

### Analyze Flight Data using Spark SQL

Now that you have a cluster running Cloud Datalab you can interactively analyze the data set using Jupyter notebooks as before. You can explore the dataset easily by opening a pre-configured notebook that is part of the github code repository for Data Science on Google Cloud Platform from O'Reilly Media, Inc. This notebook demonstrates a visualization technique that allows you to see how quantization of the data can help you improve your data modelling.

In the Datalab home page click the notebooks link to open the notebooks folder.

Now click +Notebook to create a new Jupyter notebook.

Enter the following into the first cell then click Run.

    !git clone http://github.com/GoogleCloudPlatform/data-science-on-gcp

When the command has completed click the Google Cloud Datalab link in the upper left of the page.

If you see a Leave site? warning dialog click Leave.

Navigate to the datalab/notebooks/data-science-on-gcp/06_dataproc folder.

Open quantization.ipynb

In the first cell replace the values for PROJECT and BUCKET with the project ID for your lab session. The region for the lab is set to us-central1 so you should leave this as is.

Page down until you reach the cell that creates the table definition for SparkSQL by importing the CSV files from Cloud Storage.

Modify the first line so that it imports the file all_flights-00004-* rather than the file all_flights-00000-*.

    inputs = 'gs://{}/flights/tzcorr/all_flights-00004-*'.format(BUCKET)

Expand Run > in the task bar and click Run all Cells. 

Page back up to the section titled Exploration using BigQuery.

Our first step is to figure out the most effective way to quantize the departure delays and distance variables. What you need to do is to get a quick view of the approximate distribution of the data to see how the probabilities are likely to be affected as the values of the prediction variables change.

The first section of the Jupyter notebook here uses BigQuery to get a random sample of 0.1% of the records from the flights.tzcorr table, provided with some reasonable maxima and minima for the two variables. This is stored in a Pandas dataframe and then plotted as a hexbin chart using Seaborn.


You will see a font warning indicating that the sans-serif font is unavailable that you can safely ignore.

The hexbin plot shows the number of samples at each combination of delay and distance. Clearly there are a wide range of delays possible at every distance and vice versa and there is no overwhelming trend here that indicates that these variables cannot be treated as being independent.

The distribution plots show that the data is heavily clustered towards flights that depart on time or slightly early while most flights are shorter than 500 miles. When deciding how to bin this data you need to ensure that you have enough samples, especially in the more sparsely populated sections around the edges. This means that you can't use simple equispaced bins and need to figure out a more effective approach.

In this lab you are going to use Spark SQL first to analyze a sample of the data to see what the distributions look like. You import a subset of the data directly into Spark SQL and will then carry out the analysis within the cluster rather than using BigQuery. You could do this with BigQuery but using Hadoop clusters in this way gives you additional flexibility.

In addition you now also want to build this model using your training data only. The first BigQuery hexbin plot in the notebooks is based on the entire data set.

The next set of 10 Jupyter notebook cells, in the Set up views in Spark SQL and Restrict to train days sections of the notebook, create a subset of the timezone corrected data using the same initialization CSV files that were used to create the BigQuery table. In this case you are importing only a single CSV file containing a few hundred thousand records. This allows you to carry out queries and plots relatively quickly while still ensuring you have enough data to be able to make reasonable decisions. You then create a Spark SQL table for the training days using an export of the flights.trainday table from Bigquery. Finally the spark SQL flights and trainday tables are then joined to create a Spark SQL table containing just the training data.

In the Hexbin plot section of the notebook, the training Spark SQL table is used to create a Pandas dataframe. You can then compare that with the earlier example based on the BigQuery dataset. The description of this dataframe shows there are now just over a hundred and twenty thousand records in the joined dataset.

Once again a subset of this data is plotted as a Hexbin chart and you can see that the overall pattern remains the same. The notebook takes a 2% sample of the data to keep the memory footprint relatively small. This is not really required in this lab as you are working with the data from only one of the CSV files but if you selected the entire dataset this would be necessary.

The Quantization section uses Spark SQL to create quantiles using the built in approxQuantile function to analyze the training flights table in order to identify the variable ranges that you will need to use in order to create bins that have approximately equal numbers of samples. The code used here requests 10 quantiles for each parameter. The lowest value returned in each case is the minimum value found in the data and you will ignore it when looking at the bin limits. The remaining 9 values define the boundaries between each of the 10 quantiles for each variable.

The sample data provided should produce results similar to the following quantiles:

|    |            |
|----------|:-------------:|
| Distance | [31.0, 226.0, 332.0, 429.0, 547.0, 651.0, 821.0, 985.0, 1235.0, 1744.0] |
| Dep_Delay |[-39.0, -7.0, -5.0, -4.0, -2.0, -1.0, 1.0, 6.0, 15.0, 39.0]|

The values are not precise matches as this is an approximation, but if you change the relative error parameter, which is the final parameter in the approxQuantile function call, to zero, the values should match.

In order to confirm that the binning is effective, you'll next run a command in an empty notebook cell to adjust this query to count the number of flight records in one of the combined bins, corresponding to the third quantile from each variable:

    results = spark.sql('SELECT COUNT(*) FROM flights WHERE dep_delay >= -5 AND dep_delay < -4 AND distance >= 332 AND distance < 429')
    results.show()

This should return approximately 1500 records which is close to the expected 1% of the overall number of flights in the sample dataset you imported.

Recalculating this for each of the 100 different combinations of departure delay and distance quantile ranges is a tasks more suited to a data analysis tool like Apache Pig, which you'll use next.

<br/>

### Close Cloud Datalab Sessions

Сохранить копию notebook.

Справа вверху кнопка "Running Sessions"

Остановить все запущенные, кликнув по "Shutdown".



<br/>

### Bulk Data Analysis using Apache Pig

Apache Pig is a platform for analyzing large data sets that combines a high-level programming language for expressing data analysis tasks combined with infrastructure for evaluating Apache Pig programs. Google Cloud Dataproc clusters provide native support for Apache Pig.

Return to the SSH console window and update the sample Pig script, bayes.pig, provided in the code samples for chapter 6 to point to your Cloud Storage Bucket by running the following:

    $ sed "s/cloud-training-demos-ml/$BUCKET/g" bayes.pig > mybayes.tmp
    
    $ sed "s/all_flights-00000-/all_flights-00004-/g" mybayes.tmp > mybayes.pig

The Pig script uses the Cloud Dataproc cluster to calculate the distribution of flights using the 10 quantile ranges established in the previous section for distance and the 10 quantile ranges for departure delay. This is a total of 100 separate distribution buckets.

    $ gcloud dataproc jobs submit pig --cluster ch6cluster --file  mybayes.pig

This will start the job and distribute the individual worker components across the Google Cloud Dataproc cluster. It will take two to three minutes to complete. If it does not progress, make sure you have closed all Google Cloud Datalab Jupyter Notebooks to free up resources.

Once it completes the output data can be inspected in the files saved to a file called /flights/pigoutput/part-r-00000 in the Google Cloud Storage bucket.

Enter the following command to list the contents of this file:

    $ gsutil cat gs://$BUCKET/flights/pigoutput/part-r-00000

This output file contains the ontime arrival probabilities for each distance and delay distribution bin. One section of the file, for the fifth distance quintile, which refers to flights in the 650 to 800 mile range, showing the ontime probabilities for each of the ten delay quintiles, looks like this:

```
5,0,0.9794553272814143
5,1,0.98125
5,2,0.9742673338098642
5,3,0.9722785665990534
5,4,0.9630444346678398
5,5,0.9487750556792873
5,6,0.9252873563218391
5,7,0.8756578947368421
5,8,0.5220170454545454
5,9,0.009246417013407305
```

This data indicates that about 70% of the flights arrive on time, since the percentage of flights arriving on time for the first 7 delay bins is high and the number falls rapidly for bins 8 and 9. The cutoff time for bin 8 is 15 minutes this looks right.

This is a basic first level approach that can be refined in a number of ways.

* Restrict the scope to just the training subset of the data.
* Run this against the entire dataset not just one shard.
* Provide more meaningful labels - e.g. replace the distance bin numbers with names that correspond to the distance etc.

Additional analysis not covered here shows that the distribution ranges for the distance bins can be made quite large and the really important number that you want to see for your decision model is the specific departure delay for each distance range where the probability of ontime arrival drops below 70%.

The bayes_final.pig script incorporates all of these changes. It is configured to run against the entire data set by default rather than the single data shard that you have used for previous analysis tasks in this lab so this will produce much more robust results.

First clean up the existing Pig output storage files and update the Pig script to point to your storage bucket for its source files and output folder.

    $ gsutil rm -r gs://$BUCKET/flights/pigoutput

    $ sed "s/cloud-training-demos-ml/$BUCKET/g" bayes_final.pig > mybayes_final.pig

Submit the updated Pig job to the cluster for processing:

    $ gcloud dataproc jobs submit pig --cluster ch6cluster --file  mybayes_final.pig

This will take about 5 minutes to complete the processing tasks across the entire dataset.

If you now examine the Pig output file /flights/pigoutput/part-r-00000 in your Google Cloud Storage bucket after it completes you will see a much more condensed report.

Enter the following command to list the contents of the file:

    $ gsutil cat gs://$BUCKET/flights/pigoutput/part-r-00000

This now shows the departure delay threshold value where the predicted arrival time drops below 70% for the reduced distance range buckets.


```
368,15
575,17
838,18
1218,18
9999,19
```

This means that for flights up to 368 miles, the arrival time is expected to be delayed if the flight leaves 15 minutes (or more) late. For flights between 368 and 575 miles the threshold cutoff is 17 minutes etc.

Further analysis, including a deeper exploration of some of the assumptions not covered in detail in this lab are covered fully in Chapter 6 in Data Science on Google Cloud Platform from O'Reilly Media, Inc., which explores the concepts covered here in much more detail.