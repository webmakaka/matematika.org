---
layout: page
title: Running Distributed TensorFlow on Compute Engine
permalink: /clouds/google/qwiklabs/data-science-on-google-cloud-platform-machine-learning/running-distributed-tensorflow-on-compute-engine/
---

# [GSP137] Running Distributed TensorFlow on Compute Engine

https://www.qwiklabs.com/focuses/1826?parent=catalog

This lab shows you how to use a distributed configuration of TensorFlow on multiple Compute Engine instances to train a convolutional neural network model using the MNIST dataset. The MNIST dataset enables handwritten digit recognition, and is widely used in machine learning as a training set for image recognition.

TensorFlow is Google's open source library for machine learning, developed by researchers and engineers in Google's Machine Intelligence organization, which is part of Research at Google. TensorFlow is designed to run on multiple computers to distribute training workloads. For this lab you will run TensorFlow on multiple Compute Engine virtual machine instances to train the model. You can use Cloud Machine Learning Engine instead, which manages resource allocation tasks for you and can host your trained models. We recommend that you use Cloud ML Engine unless you have a specific reason not to. You can learn more in the this lab that uses Cloud ML Engine and Cloud Datalab.

The following diagram describes the architecture for running a distributed configuration of TensorFlow on Compute Engine, and using Cloud ML Engine with Cloud Datalab to execute predictions with your trained model.

![Running Distributed TensorFlow on Compute Engine](/img/clouds/google/qwiklabs/google-cloud-solutions-2-data-and-machine-learning/running-distributed-tensorflow-on-compute-engine/pic1.png "Running Distributed TensorFlow on Compute Engine"){: .center-image }

This Qwiklab shows you how to set up and use this architecture, and explains some of the concepts along the way.

**Objectives**

* Set up Compute Engine to create a cluster of virtual machines (VMs) to run TensorFlow.
* Learn how to run the distributed TensorFlow sample code on your Compute Engine cluster to train a model. The example code uses the latest TensorFlow libraries and patterns, so you can use it as a reference when designing your own training code.
* Deploy the trained model to Cloud ML Engine to create a custom API for predictions and then execute predictions using a Cloud Datalab notebook.

<br/>

### Creating the template instance

    $ gcloud config set compute/zone us-central1-c

    $ git clone https://github.com/GoogleCloudPlatform/cloudml-dist-mnist-example

    $ cd cloudml-dist-mnist-example

    $ sed -i "s/steps=10000/steps=5000/g" ./scripts/start-dist-mnist.sh

Create the initial VM instance from an Ubuntu Wily image:

    $ gcloud compute instances create template-instance \
    --image-project ubuntu-os-cloud \
    --image-family ubuntu-1604-lts \
    --boot-disk-size 10GB \
    --machine-type n1-standard-1

<br/>

    $ gcloud compute ssh template-instance

    $ sudo apt-get update && sudo apt-get -y upgrade
    
    $ sudo apt-get install -y python-pip python-dev

    $ sudo pip install --upgrade pip

    $ sudo pip install tensorflow==1.4.1

    // Check the version of TensorFlow running in your Cloud Shell instance
    $ sudo python -c 'import tensorflow as tf; print(tf.__version__)'

    $ exit

<br/>

### Creating a Cloud Storage bucket

Next, create a Cloud Storage bucket to store your MNIST files.

Create a regional Cloud Storage bucket to hold the MNIST data files that are to be shared among the worker instances:

    $ MNIST_BUCKET="mnist-$RANDOM"
    $ gsutil mb -c regional -l us-east1 gs://${MNIST_BUCKET}

Use the following script to download the MNIST data files and copy them to the bucket:

    $ sudo ./scripts/create_records.py
    $ gsutil cp /tmp/data/train.tfrecords gs://${MNIST_BUCKET}/data/
    $ gsutil cp /tmp/data/test.tfrecords gs://${MNIST_BUCKET}/data/

<br/>

### Creating the template image and training instances

To create the worker, master, and parameter server instances, convert the template instance into an image, and then use the image to create each new instance.

Turn off auto-delete for the template-instance VM, which preserves the disk when the VM is deleted:

    $ gcloud compute instances set-disk-auto-delete template-instance \
    --disk template-instance --no-auto-delete

Delete template-instance:

    $ gcloud compute instances delete template-instance

When you see the Do you want to continue(Y/n)? prompt enter Y to confirm that you want to delete the instance. It will take a couple of minutes to complete.

Create the image template-image from the template-instance disk:

    $ gcloud compute images create template-image \
    --source-disk template-instance

Now create additional instances. For this lab, create four instances named master-0, worker-0, worker-1, and ps-0. The storage-rw scope allows the instances to access your Cloud Storage bucket.

Create two single vCPU instances for the parameter and master servers:

    $ gcloud compute instances create master-0 ps-0 \
    --image template-image \
    --machine-type n1-standard-1 \
    --scopes=default,storage-rw

Create two quad vCPU instances for the two worker nodes:

    $ gcloud compute instances create worker-0 worker-1 \
    --image template-image \
    --machine-type n1-standard-4 \
    --scopes=default,storage-rw

The cluster is now ready to run distributed TensorFlow.

<br/>

### Running the distributed TensorFlow code

In this section, run a script to instruct all of your VM instances to run TensorFlow code to train the model.

In Cloud Shell, run the following command from the cloudml-dist-mnist-example directory:

    $ ./scripts/start-training.sh gs://${MNIST_BUCKET}

The script named start-training.sh pushes the code to each VM and sends the necessary parameters to start the TensorFlow process on each machine to create the distributed cluster. The output stream in Cloud Shell shows the loss and the accuracy values for the test dataset.

The training process will take approximately fifteen minutes to complete on this cluster. Feel free to read the next section while the training is running.

When the training script completes, the script prints out the location of the newly generated model files in the format gs://${MNIST_BUCKET}/job_[TIMESTAMP]/export/Servo/[JOB_ID]/:


```
Trained model is stored in gs://mnist-4801/job_190616_141937/export/Servo/1560695829/

```
Copy the location of your bucket path for use in later steps.

<br/>

### Publishing your model for predictions

You've successfully generated a new model that you can use to make predictions. Training more-sophisticated models requires more-complex TensorFlow code, but the configuration of the compute and storage resources are similar.

Training the model is only half of the story. You need to plug your model into your application, or wrap an API service around it with authentication, and eventually make it all scale. There is a relatively significant amount of engineering work left to make your model useful.

Cloud ML Engine can help with some of this work. Cloud ML Engine is a fully managed version of TensorFlow running on Cloud Platform. Cloud ML Engine gives you all of powerful features of TensorFlow without needing to set up any additional infrastructure or install any software. You can automatically scale your distributed training to use thousands of CPUs or GPUs, and you pay for only what you use.

Because Cloud ML Engine runs TensorFlow behind the scenes, all of your work is portable, and you aren't locked into a proprietary tool.

The Using Distributed TensorFlow with Cloud Datalab lab will show you how to use the same example code to train your model by using Cloud ML Engine.

You can also configure Cloud ML Engine to host your model for predictions, which you'll be doing next. Hosting your model helps give you the ability to quickly test and apply your model at scale, with all the security and reliability features you would expect from a Google-managed service.

<br/>

**Publish to Cloud ML Engine**

In this section will define the MODEL_BUCKET variable using the bucket path saved earlier.

This is what the bucket variable is composed of:

MODEL_BUCKET: gs://${MNIST_BUCKET}/job_[TIMESTAMP]/export/Servo/[JOB_ID]/

Next you'll define a new v1 version of your model by using the gcloud command-line tool, and point it to the model files in your bucket.

Run the following, replacing [YOUR_BUCKET_PATH] with the output path from the previous step. .

    $ MODEL="MNIST"

    // $ MODEL_BUCKET=[YOUR_BUCKET_PATH]
    $ MODEL_BUCKET=gs://mnist-4801/job_190616_141937/export/Servo/1560695829/

    $ gcloud ml-engine models create ${MODEL} --regions us-central1

    $ gcloud ml-engine versions create \
    --origin=${MODEL_BUCKET} --model=${MODEL} --runtime-version=1.4 v1

The will take about five minutes to create.

The model is now running with Cloud ML and is able to process predictions using the default v1 model you just created. In the next section, you use Cloud Datalab to make and visualize predictions.

<br/>

### Executing predictions with Cloud Datalab

To test your predictions, create a Cloud Datalab instance that uses interactive Jupyter Notebooks to execute code.

In Cloud Shell, enter the following command to create a Cloud Datalab instance:

    $ datalab create mnist-datalab

This will take a few minutes to complete.

When you see The connection to Datalab is now open and will remain until this command is killed, return to the Console.



Compute Engine > VM Instances > mnist-datalab > SSH

Cloud Shell > Web Preview > Change port > 8081 > Change and Preview.

A new tab will open with the Cloud Datalab application.

In the Cloud Datalab application, create a new notebook by clicking on the +Notebook icon in the upper left.

Paste the following text into the first cell of the new notebook:

```
%%bash
wget https://raw.githubusercontent.com/GoogleCloudPlatform/cloudml-dist-mnist-example/master/notebooks/Online%20prediction%20example.ipynb
cat Online\ prediction\ example.ipynb > Untitled\ Notebook.ipynb
```

Click Run at the top of the page to download the Online prediction example.ipynb notebook. The script copies the remote notebook's contents into the current notebook.

Refresh the browser page to load the new notebook content. Then select the first cell containing the JavaScript code and click Run to execute it.

У меня никаих цифр не отобразилось. Поэтому дальше все просто копирую из лабы.

Scroll down the page until you see the blank number drawing panel and draw a number with your cursor:

![Running Distributed TensorFlow on Compute Engine](/img/clouds/google/qwiklabs/google-cloud-solutions-2-data-and-machine-learning/running-distributed-tensorflow-on-compute-engine/pic2.png "Running Distributed TensorFlow on Compute Engine"){: .center-image }

Click in the cell under the drawing panel to activate it, then click on the down arrow next to the Run button at the top and select Run from this Cell.

![Running Distributed TensorFlow on Compute Engine](/img/clouds/google/qwiklabs/google-cloud-solutions-2-data-and-machine-learning/running-distributed-tensorflow-on-compute-engine/pic3.png "Running Distributed TensorFlow on Compute Engine"){: .center-image }

The output of the prediction is a length-10 array in which each index, 0-9, contains a number corresponding to that digit. The closer the number is to 1, the more likely that index matches the digit you entered.

In this example, the fourth slot, which corresponds to the number 3, highlighted in the list below, is very close to 1, and therefore has a high probability of matching the digit.

```
CLASSES  PROBABILITIES
3        [5.780508960384623e-09, 3.193441691640153e-11, 0.001525901723653078, 0.9984740614891052, 1.1779428994718177e-11, 1.1389337251088705e-09, 2.6817359097264237e-11, 2.8552131769998823e-09, 7.421151887454513e-11, 2.443356905690308e-10]
```

The last cell in the notebook displays a bar chart that clearly shows that it recognized the number, which was 3 in this case.

![Running Distributed TensorFlow on Compute Engine](/img/clouds/google/qwiklabs/google-cloud-solutions-2-data-and-machine-learning/running-distributed-tensorflow-on-compute-engine/pic4.png "Running Distributed TensorFlow on Compute Engine"){: .center-image }

Feel free to click on the Clear button and try drawing another number and running the test again to see new results.