---
layout: page
title: Using OpenTSDB to Monitor Time-Series Data on Cloud Platform
permalink: /clouds/google/qwiklabs/data-science-on-google-cloud-platform-machine-learning/using-opentsdb-to-monitor-time-series-data-on-cloud-platform/
---

# [GSP142] Using OpenTSDB to Monitor Time-Series Data on Cloud Platform

https://www.qwiklabs.com/focuses/629?parent=catalog

<br/>

In this lab you will learn how to collect, record, and monitor time-series data on Google Cloud Platform (GCP) using OpenTSDB running on Google Kubernetes Engine and Google Cloud Bigtable.

Time-series data is a highly valuable asset that you can use for several applications, including trending, monitoring, and machine learning. You can generate time-series data from server infrastructure, application code, and other sources. OpenTSDB can collect and retain large amounts of time-series data with a high degree of granularity.

In this hands-on lab you will create a scalable data collection layer using Kubernetes Engine and work with the collected data using Bigtable. The following diagram illustrates the high-level architecture of the solution: 

![Using OpenTSDB to Monitor Time-Series Data on Cloud Platform](/img/clouds/google/qwiklabs/google-cloud-solutions-2-data-and-machine-learning/using-opentsdb-to-monitor-time-series-data-on-cloud-platform/pic1.png "Using OpenTSDB to Monitor Time-Series Data on Cloud Platform"){: .center-image }


<br/>

### Objectives

* Create a new Bigtable instance.
* Create a new Kubernetes Engine cluster.
* Deploy OpenTSDB to your Kubernetes Engine cluster.
* Send time-series metrics to OpenTSDB.
* Visualize metrics using OpenTSDB and Grafana.

<br/>

### Preparing your environment

Enter the following commands in the Cloud Shell to prepare your environment.

    $ gcloud config set compute/zone us-central1-f

    $ git clone https://github.com/GoogleCloudPlatform/opentsdb-bigtable.git

    $ cd opentsdb-bigtable

<br/>

### Creating a Bigtable instance

You will be using Google Cloud Bigtable to store the time-series data that you collect. You must create a Bigtable instance to do that work.

Bigtable is a key/wide-column store that works especially well for time-series data, explained in Bigtable Schema Design for Time Series Data. Bigtable supports the HBase API, which makes it easy for you to use software designed to work with Apache HBase, such as OpenTSDB. You can learn about the HBase schema used by OpenTSDB in the OpenTSDB documentation.

A key component of OpenTSDB is the AsyncHBase client, which enables it to bulk-write to HBase in a fully asynchronous, non-blocking, thread-safe manner. When you use OpenTSDB with Bigtable, AsyncHBase is implemented as the AsyncBigtable client.

The ability to easily scale to meet your needs is a key feature of Bigtable. This lab uses a single-node development cluster because it is sufficient for the task and is economical. You should start your projects in a development cluster, moving to a larger production cluster when you are ready to work with production data. The Bigtable documentation includes detailed discussion about performance and scaling to help you pick a cluster size for your own work.

Now you will create your Bigtable instance.

Navigation menu > Bigtable > Create Instance.

    Instance name: OpenTSDB
    The page automatically sets Instance ID and Cluster ID after you enter your instance name.

    Instance type: Development
    Region: select us-central1
    Zone: select us-central1-f

Done > Create

Make note of the values of Instance ID and Zone. You will use them in a later step.

<br/>

### Creating a Kubernetes Engine cluster

Kubernetes Engine provides a managed Kubernetes environment. After you create a Kubernetes Engine cluster, you can deploy Kubernetes pods to it. This Qwiklab uses Kubernetes Engine and Kubernetes pods to run OpenTSDB.

OpenTSDB separates its storage from its application layer, which enables it to be deployed across multiple instances simultaneously. By running in parallel, it can handle a large amount of time-series data. Packaging OpenTSDB into a Docker container enables easy deployment at scale using Kubernetes Engine.

In Cloud Shell create a Kubernetes cluster by running the following command:

    $ gcloud container clusters create opentsdb-cluster \
    --no-enable-autoupgrade \
    --no-enable-ip-alias --no-enable-basic-auth \
    --no-issue-client-certificate \
    --metadata disable-legacy-endpoints=true \
    --scopes "https://www.googleapis.com/auth/bigtable.admin","https://www.googleapis.com/auth/bigtable.data"

<br/>

Adding the two extra scopes to your Kubernetes cluster allows your OpenTSDB container to interact with Bigtable. You can pull images from Google Container Registry without adding a scope for Cloud Storage, because the cluster can read from Cloud Storage by default. You might need additional scopes in other deployments.

The rest of this Qwiklab uses a prebuilt container, gcr.io/cloud-solutions-images/opentsdb-bigtable:v1 located in Container Registry. The Dockerfile and ENTRYPOINT script used to build the container are located in the build folder of the Qwiklab repository.


<br/>

### Creating a ConfigMap with configuration details

Kubernetes provides a mechanism called the ConfigMap to separate configuration details from the container image to make applications more portable. The configuration for OpenTSDB is specified in opentsdb.conf. A ConfigMap containing opentsdb.conf is included with the sample code. You must edit it to reflect your instance details.

**Create the ConfigMap**

Next you will edit the OpenTSDB configuration to use the project name, instance identifier, and zone that you used when creating your instance.

    $ vi configmaps/opentsdb-config.yaml

* Replace the placeholder text REPLACE_WITH_PROJECT with the GCP Project ID for this Qwiklab.
* Replace the placeholder text REPLACE_WITH_INSTANCE with the BigTable instance identifier. If you used the recommended Bigtable name when you created the the instance then the instance identifier will be "opentsdb".
* Replace the placeholder text REPLACE_WITH_ZONE with the BigTable instance zone identifier. If you selected the recommended zone when you created the instance then the zone identifier will be "us-central1-f".

<br/>

    $ kubectl create -f configmaps/opentsdb-config.yaml

<br/>

### Creating OpenTSDB tables in Bigtable

Before you can read or write data using OpenTSDB, you need to create the necessary tables in Bigtable to store that data. Follow these steps to create a Kubernetes job that creates the tables.

Launch the job:

    $ kubectl create -f jobs/opentsdb-init.yaml

The job can take up to a minute or more to complete. Verify the job has completed successfully by periodically running this command:

    $ kubectl describe jobs

The output should indicate 1 SUCCEEDED under the heading, Pods Statuses. Do not proceed until you see this status.

Get the table creation job logs by running the following commands:

    $ pods=$(kubectl get pods --selector=job-name=opentsdb-init \
    --output=jsonpath={.items..metadata.name})

Wait a couple of minutes to allow the container to start, then run:

    $ kubectl logs $pods

If you are returned to the command line with an error, the container is waiting to start. Wait a couple of minutes and run the command again.

When you get the logs, examine the bottom of the output, which should indicate each table that was created. This job runs several table creation commands, each taking the form of create 'TABLE_NAME'. Look for a line of the form 0 row(s) in 0.0000 seconds, where the actual command duration is listed instead of 0.0000.

Your output should include a section that looks something like the following:


```
create 'tsdb-uid',
  {NAME => 'id', COMPRESSION => 'NONE', BLOOMFILTER => 'ROW'},
  {NAME => 'name', COMPRESSION => 'NONE', BLOOMFILTER => 'ROW'}
0 row(s) in 1.3680 seconds

Hbase::Table - tsdb-uid

create 'tsdb',
  {NAME => 't', VERSIONS => 1, COMPRESSION => 'NONE', BLOOMFILTER => 'ROW'}
0 row(s) in 0.6570 seconds

Hbase::Table - tsdb

create 'tsdb-tree',
  {NAME => 't', VERSIONS => 1, COMPRESSION => 'NONE', BLOOMFILTER => 'ROW'}
0 row(s) in 0.2670 seconds

Hbase::Table - tsdb-tree

create 'tsdb-meta',
  {NAME => 'name', COMPRESSION => 'NONE', BLOOMFILTER => 'ROW'}
0 row(s) in 0.5850 seconds

Hbase::Table - tsdb-meta

```

You only need to run this job once.

An error message will be returned if the tables already exist. You can continue using existing tables, if present.

<br/>

### Data Model

The tables you just created will store data points from OpenTSDB. In a later step, you will configure a test service to write time-series data into these tables. Time-series data points are organized and stored as follows:

![Using OpenTSDB to Monitor Time-Series Data on Cloud Platform](/img/clouds/google/qwiklabs/google-cloud-solutions-2-data-and-machine-learning/using-opentsdb-to-monitor-time-series-data-on-cloud-platform/pic3.png "Using OpenTSDB to Monitor Time-Series Data on Cloud Platform"){: .center-image }

<br/>

### Deploying OpenTSDB

The rest of this Qwiklab provides instructions for making the sample scenario work. The following diagram shows the architecture you will use:

![Using OpenTSDB to Monitor Time-Series Data on Cloud Platform](/img/clouds/google/qwiklabs/google-cloud-solutions-2-data-and-machine-learning/using-opentsdb-to-monitor-time-series-data-on-cloud-platform/pic2.png "Using OpenTSDB to Monitor Time-Series Data on Cloud Platform"){: .center-image }

This Qwiklab uses two Kubernetes deployments. One deployment sends metrics into OpenTSDB and the other reads from it. Using two deployments prevents long-running reads and writes from blocking each other. The pods in each deployment use the same container. OpenTSDB provides a daemon called tsd that runs in each container.

A single tsd process can handle a high throughput of events every second. To distribute load, each deployment in this Qwiklab creates 3 replicas of the read and write pods.

<br/>

### Create a deployment for writing metrics

The configuration information for the writer deployment is in opentsdb-write.yaml in the deployments folder of the example repository. Use the following command to create it:

    $ kubectl create -f deployments/opentsdb-write.yaml

Check that the deployment for writing metrics is running:

    $ kubectl get pods


<br/>

### Create a deployment for reading metrics

The configuration information for the reader deployment is in opentsdb-read.yaml in the deployments folder of the example repository. Use the following command to create it:

    $ kubectl create -f deployments/opentsdb-read.yaml

    $ kubectl get pods


<br/>

### Creating OpenTSDB services


In order to provide consistent network connectivity to the deployments, you will create two Kubernetes services. One service writes metrics into OpenTSDB and the other reads.

**Create the service for writing metrics**


The configuration information for the metrics reading service is contained in opentsdb-write.yaml in the services folder of the example repository. Use the following command to create the service:

    $ kubectl create -f services/opentsdb-write.yaml

This service is created inside your Kubernetes cluster and is accessible to other services running in your cluster. In the next section of this Qwiklab you write metrics to this service.

    $ kubectl get services


<br/>

**Create the service for reading metrics**

The configuration information for the metrics reading service is contained in opentsdb-read.yaml in the services folder of the example repository. Use the following command to create the service:

    $ kubectl create -f services/opentsdb-read.yaml

<br/>

    $ kubectl get services

<br/>

### Writing time-series data to OpenTSDB

There are several mechanisms to write data into OpenTSDB. After you define service endpoints, you can direct processes to begin writing data to them. This Qwiklab uses Heapster to demonstrate writing data. Your Heapster deployment collects data about Kubernetes and publishes metrics from the Kubernetes Engine cluster on which you are running OpenTSDB.

**Create ClusterRoleBinding configuration for heapster**

    $ vi rbac.yaml

<br/>

```
kind: ClusterRoleBinding
apiVersion: rbac.authorization.k8s.io/v1beta1
metadata:
  name: heapster
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: system:heapster
subjects:
- kind: ServiceAccount
  name: heapster-opentsdb
  namespace: default
```

<br/>

    $ kubectl apply -f rbac.yaml

<br/>

    // Deploy Heapster to your cluster
    $ kubectl create -f deployments/heapster.yaml

    // Check that Heapster has been deployed
    $ kubectl get deployments

<br/>

### Examining time-series data with OpenTSDB

You can query time-series metrics by using the opentsdb-read service endpoint that you deployed earlier. You can use the data in a variety of ways. One common option is to visualize it. OpenTSDB includes a basic interface to visualize metrics that it collects. This Qwiklab uses Grafana, a popular alternative for visualizing metrics that provides additional functionality.

**Set up Grafana**

Running Grafana in your cluster requires a similar process that you used to set up OpenTSDB. In addition to creating a ConfigMap and a deployment, you need to configure port forwarding so that you can access Grafana while it is running in your Kubernetes cluster.

Use the following steps to set up Grafana.

Create the Grafana ConfigMap using the configuration information in grafana.yaml in the configmaps folder of the example repository:

    $ kubectl create -f configmaps/grafana.yaml

    $ kubectl get configmaps

    $ kubectl create -f deployments/grafana.yaml

    $ kubectl get deployments

<br/>

    $ grafana=$(kubectl get pods --selector=app=grafana \
    --output=jsonpath={.items..metadata.name})

    $ kubectl port-forward $grafana 8080:3000

The port forwarding kubectl command remains running. The output should match the following, indicating that port forwarding is active. Once you see this you can continue to the next section.


**Connect to the Grafana web interface**

Web Preview > Preview on port 8080.

This deployment of Grafana has been customized for this lab. The files configmaps/grafana.yaml and deployments/grafana.yaml configure Grafana to:

![Using OpenTSDB to Monitor Time-Series Data on Cloud Platform](/img/clouds/google/qwiklabs/google-cloud-solutions-2-data-and-machine-learning/using-opentsdb-to-monitor-time-series-data-on-cloud-platform/pic4.png "Using OpenTSDB to Monitor Time-Series Data on Cloud Platform"){: .center-image }


* connect to the opentsdb-read service
* allow anonymous authentication
* display some basic cluster metrics

A deployment of Grafana in a production environment would implement the proper authentication mechanisms and use richer time-series graphs.
