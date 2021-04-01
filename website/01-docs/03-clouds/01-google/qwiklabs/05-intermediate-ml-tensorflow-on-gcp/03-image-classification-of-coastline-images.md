---
layout: page
title: Image Classification of Coastline Images Using TensorFlow on Cloud ML Engine
permalink: /clouds/google/qwiklabs/intermediate-ml-tensorflow-on-gcp/image-classification-of-coastline-images/
---

# [GSP014] Image Classification of Coastline Images Using TensorFlow on Cloud ML Engine

https://google.qwiklabs.com/focuses/1048?parent=catalog

<br/>

Делаю:  
02.08.2019

<br/>

Собственно можно сразу посмотреть:

https://github.com/GoogleCloudPlatform/training-data-analyst/blob/master/quests/scientific/coastline.ipynb


<br/>

### Overview

In this lab you will carry out a transfer learning example based on Inception-v3 image recognition neural network. The objective is to classify coastline images captured using drones based on their potential for flood damage.

<br/>

### What you'll learn

* How transfer learning for image classification works
* How to use Cloud Dataflow for batch processing of image data
* How to use Cloud ML and Datalab ML Toolbox to train a classification model
* How to use Cloud ML to provide a prediction API service

<br/>

### Introduction

Transfer learning is a machine learning method which utilizes a pre-trained neural network. For example, the image recognition model called Inception-v3 consists of two parts:

* The Feature extraction part, with a convolutional neural network.
* The Classification part, with fully-connected and softmax layers.

The pre-trained Inception-v3 model achieves state-of-the-art accuracy for recognizing general objects with 1000 classes, like "Zebra", "Dalmatian", and "Dishwasher". The model extracts general features from input images in the first part and classifies them based on those features in the second part.


![Image Classification of Coastline Images Using TensorFlow on Cloud ML Engine](/img/clouds/google/qwiklabs/intermediate-ml-tensorflow-on-gcp/image-classification-of-coastline-images/pic1.png "Image Classification of Coastline Images Using TensorFlow on Cloud ML Engine"){: .center-image }

In transfer learning, when you build a new model to classify your original dataset, you reuse the feature extraction part and re-train the classification part with your dataset. Since you don't have to train the feature extraction part (which is the most complex part of the model), you can train the model with less computational resources and training time.


<br/>

### Launch Cloud Datalab


To launch Datalab, you need to first create a VM instance and SSH into it. In the interest of time, we'll give you the code to do this.

    $ datalab create coastline --zone us-central1-c


[Y]

[Enter] [Enter]


<br/>

### Understand the Objective

![Image Classification of Coastline Images Using TensorFlow on Cloud ML Engine](/img/clouds/google/qwiklabs/intermediate-ml-tensorflow-on-gcp/image-classification-of-coastline-images/pic2.jpeg "Image Classification of Coastline Images Using TensorFlow on Cloud ML Engine"){: .center-image }


For this lab you'll be working with about 8,000 images that were captured from drones, and they have been classified into categories based on their potential to suffer flood damage. For example, the aerial image above depicts "scarps and steep slopes in clay".

This diagram, from "The Landslide Handbook" by LM Highland, shows the underlying structure:

![Image Classification of Coastline Images Using TensorFlow on Cloud ML Engine](/img/clouds/google/qwiklabs/intermediate-ml-tensorflow-on-gcp/image-classification-of-coastline-images/pic3.png "Image Classification of Coastline Images Using TensorFlow on Cloud ML Engine"){: .center-image }

Can a machine learning model that is shown examples of such coastlines learn to classify new images? When you look at the image, the "scarp" in question is barely visible - instead, it has to be inferred.

<br/>

### Load Datalab notebook from Github repository


<br/>

Cloud Shell Web preview -> Change port -> 8081 -> Change and Preview


    !git clone https://github.com/GoogleCloudPlatform/training-data-analyst


training-data-analyst/quests/scientific/coastline.ipynb


<br/>

### Execute preprocessing, training, and prediction jobs

You are now in a notebook in the Datalab environment. From here you'll read the narrative in the notebook and run each cell.