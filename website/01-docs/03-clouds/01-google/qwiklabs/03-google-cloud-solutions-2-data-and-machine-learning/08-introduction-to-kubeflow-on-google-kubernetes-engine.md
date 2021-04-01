---
layout: page
title: Introduction to Kubeflow on Google Kubernetes Engine
permalink: /clouds/google/qwiklabs/data-science-on-google-cloud-platform-machine-learning/introduction-to-kubeflow-on-google-kubernetes-engine/
---

# [GSP180] Introduction to Kubeflow on Google Kubernetes Engine

https://www.qwiklabs.com/focuses/960?parent=catalog

<br/>

As datasets continue to expand and models become more complex, distributing machine learning (ML) workloads across multiple nodes has become more attractive. Unfortunately, breaking up and distributing a workload can add both computational overhead and a great deal more complexity to the system. Data scientists should be able to focus on ML problems, not DevOps.

Fortunately, distributed workloads are becoming easier to manage, thanks to Kubernetes. Kubernetes is a mature, production-ready platform that gives developers a simple API to deploy programs to a cluster of machines as if they were a single piece of hardware. Using Kubernetes, computational resources and be added or removed as desired, and the same cluster can be used to both train and serve ML models.

This lab is an introduction to Kubeflow, an open-source project which aims to make running ML workloads on Kubernetes simple, portable and scalable. Kubeflow adds some resources to your cluster to assist with a variety of tasks, including training and serving models and running Jupyter Notebooks. It also extends the Kubernetes API by adding new Custom Resource Definitions (CRDs) to your cluster, so machine learning workloads can be treated as first-class citizens by Kubernetes.

What You'll Build

![Introduction to Kubeflow on Google Kubernetes Engine](/img/clouds/google/qwiklabs/google-cloud-solutions-2-data-and-machine-learning/introduction-to-kubeflow-on-google-kubernetes-engine/pic1.png "Introduction to Kubeflow on Google Kubernetes Engine"){: .center-image }

In this lab you will train and serve a TensorFlow model, and then deploy a web interface to allow users to interact with the model over the public internet. You will build a classic handwritten digit recognizer using the MNIST dataset.

The purpose of this lab is to get a brief overview of how to interact with Kubeflow. To keep things simple, use CPU-only training, and only make use of a single node for training. Kubeflow's user guide has more information when you are ready to explore further.


![Introduction to Kubeflow on Google Kubernetes Engine](/img/clouds/google/qwiklabs/google-cloud-solutions-2-data-and-machine-learning/introduction-to-kubeflow-on-google-kubernetes-engine/pic2.png "Introduction to Kubeflow on Google Kubernetes Engine"){: .center-image }

<br/>

### What You'll Learn

* How to set up Kubeflow on a Kubernetes Engine cluster
* How to package a TensorFlow program in a container and upload it to Google Container Registry
* How to submit a tf-train job and save the resulting model to Google Cloud Storage
* How to serve and interact with a trained model

<br/>

### Lab Setup

In the Cloud Shell window, click on the Settings dropdown at the far right. Select Enable Boost Mode, then “Restart Cloud Shell in Boost Mode”. This will provision a larger instance for your Cloud Shell session, resulting in speedier Docker builds.


    $ git clone https://github.com/googlecodelabs/kubeflow-introduction

    $ cd kubeflow-introduction

    $ export PROJECT_ID=$(gcloud config get-value project)

    $ gcloud container clusters create kubeflow-qwiklab \
    --zone us-central1-a \
    --machine-type n1-standard-2 \
    --verbosity=error


    // Connect your local environment to the cluster so you can interact with it using kubectl:
    $ gcloud container clusters get-credentials kubeflow-qwiklab \
    --zone us-central1-a

    // Change the permissions on the cluster to allow Kubeflow to run properly:
    $ kubectl create clusterrolebinding default-admin \
    --clusterrole=cluster-admin \
    --user=$(gcloud config get-value account)


<br/>

### Creating a ksonnet project

Kubeflow makes use of ksonnet to help manage deployments. ksonnet acts as another layer on top of kubectl. While Kubernetes is typically managed with static YAML files, ksonnet adds a further abstraction that is closer to standard OOP objects. Resources are managed as prototypes with empty parameters, which can be instantiated into components by defining values for the parameters. This system makes it easier to deploy slightly different resources to different clusters at the same time, making it easy to maintain different environments for staging and production. Components in ksonnet can either be exported as standard Kubernetes YAML files with ks show, or they can be directly deployed to the cluster with ks apply.

![Introduction to Kubeflow on Google Kubernetes Engine](/img/clouds/google/qwiklabs/google-cloud-solutions-2-data-and-machine-learning/introduction-to-kubeflow-on-google-kubernetes-engine/pic3.png "Introduction to Kubeflow on Google Kubernetes Engine"){: .center-image }

<br/>

**Install ksonnet**

    $ export KS_VER=ks_0.9.2_linux_amd64

    $ wget -O /tmp/$KS_VER.tar.gz https://github.com/ksonnet/ksonnet/releases/download/v0.9.2/$KS_VER.tar.gz

    $ mkdir -p ${HOME}/bin

    $ tar -xvf /tmp/$KS_VER.tar.gz -C ${HOME}/bin

    $ export PATH=$PATH:${HOME}/bin/$KS_VER

<br/>

**Create a ksonnet project directory**

Ksonnet resources are managed in a single project directory, just like git.

To create your ksonnet project directory, use ks init:

    $ ks init ksonnet-kubeflow

    $ cd ${HOME}/kubeflow-introduction/ksonnet-kubeflow

In the Cloud Shell Code Editor open the kubeflow-introduction folder to look inside the new ksonnet-kubeflow project directory. You should see an app.yaml file and four directories.

The most important of these is components, which holds the resources that are deployed to the cluster, and environments, which holds information about the clusters associated with the project.

    // add your cluster as an available ksonnet environment
    $ ks env add gke

This command will look at your current Kubernetes configuration, and use that to find your cluster.

If you look in the environments directory, it should have a new "gke" environment that points to your GKE cluster.


<br/>

**ksonnet definitions:**

Environment: a unique location to deploy to. It's made up of: 1) a unique name, 2) the Kubernetes cluster address, 3) the cluster's namespace, and 4) the Kubernetes API version. Environments can maintain different settings, so you can deploy slightly different components to different clusters

Prototype: an object that describes a set of Kubernetes resources (and associated parameters) in an abstract way. Kubeflow has prototypes we will use for tf-job (a tensorflow training job), tf-serving (to serve a trained model), and kubeflow-core (for required helper resources)

Component: a specific implementation of a prototype. A component is created by "filling in" the empty parameters of a prototype. A component can directly generate standard Kubernetes YAML files, and can be deployed directly to a cluster. It can also hold different parameters for different environments

<br/>

### Adding Kubeflow packages

![Introduction to Kubeflow on Google Kubernetes Engine](/img/clouds/google/qwiklabs/google-cloud-solutions-2-data-and-machine-learning/introduction-to-kubeflow-on-google-kubernetes-engine/pic4.png "Introduction to Kubeflow on Google Kubernetes Engine"){: .center-image }

ksonnet is a generic tool used to deploy software to Kubernetes. You need to add Kubeflow to the ksonnet project. This is managed by installing a ksonnet package.

Run the following to add the Kubeflow repository to your project, and then you can pull the Kubeflow packages down into your local project:

    $ export KUBEFLOW_VERSION=v0.1.0-rc.0

    $ ks registry add kubeflow github.com/kubeflow/kubeflow/tree/${KUBEFLOW_VERSION}/kubeflow
    
    $ ks pkg install kubeflow/core@${KUBEFLOW_VERSION}

    $ ks pkg install kubeflow/tf-serving@${KUBEFLOW_VERSION}

    $ ks pkg install kubeflow/tf-job@${KUBEFLOW_VERSION}

<br/>

After running these commands, Kubeflow packages will be installed into your local ksonnet project. You should see the installed packages listed in your app.yaml file, and in the vendor directory.

Apply the standard Kubeflow components to your cluster - generate the kubeflow-core component from its prototype:

    $ ks generate core kubeflow-core --name=kubeflow-core --cloud=gke

Apply the component to your cluster:

    $ ks apply gke -c kubeflow-core


Here, you are setting values for the --name and --cloud parameters in the kubeflow-core prototype.

    $ kubectl get all
    NAME                                  READY   STATUS    RESTARTS   AGE
    pod/ambassador-7869c75f74-565wq       1/2     Running   0          45s
    pod/ambassador-7869c75f74-b82bv       1/2     Running   0          45s
    pod/ambassador-7869c75f74-d2g42       1/2     Running   0          45s
    pod/tf-hub-0                          1/1     Running   0          45s
    pod/tf-job-operator-698874c4b-jzvng   1/1     Running   0          45s

    NAME                       TYPE        CLUSTER-IP      EXTERNAL-IP   PORT(S)    AGE
    service/ambassador         ClusterIP   10.43.248.15    <none>        80/TCP     46s
    service/ambassador-admin   ClusterIP   10.43.249.232   <none>        8877/TCP   46s
    service/k8s-dashboard      ClusterIP   10.43.254.90    <none>        443/TCP    46s
    service/kubernetes         ClusterIP   10.43.240.1     <none>        443/TCP    4m40s
    service/tf-hub-0           ClusterIP   None            <none>        8000/TCP   49s
    service/tf-hub-lb          ClusterIP   10.43.255.121   <none>        80/TCP     49s

    NAME                              DESIRED   CURRENT   UP-TO-DATE   AVAILABLE   AGE
    deployment.apps/ambassador        3         3         3            0           45s
    deployment.apps/tf-job-operator   1         1         1            1           45s

    NAME                                        DESIRED   CURRENT   READY   AGE
    replicaset.apps/ambassador-7869c75f74       3         3         0       45s
    replicaset.apps/tf-job-operator-698874c4b   1         1         1       45s

    NAME                      DESIRED   CURRENT   AGE
    statefulset.apps/tf-hub   1         1         46s

<br/>

### Training

The code for your TensorFlow project can be found in the kubeflow-introduction/tensorflow-model folder. It contains a python file called MNIST.py that contains TensorFlow code, and a Dockerfile to build it into a container image.

MNIST.py defines a fairly straight-forward program:

* First it defines a simple feed-forward neural network with two hidden layers.
* Next, it defines tensor ops to train and evaluate the model's weights.
* Finally, after a number of training cycles, it saves the trained model up to a Cloud Storage bucket.

Of course, before you can use the storage bucket, you need to create it.

<br/>

**Setting up a Storage Bucket**

![Introduction to Kubeflow on Google Kubernetes Engine](/img/clouds/google/qwiklabs/google-cloud-solutions-2-data-and-machine-learning/introduction-to-kubeflow-on-google-kubernetes-engine/pic5.png "Introduction to Kubeflow on Google Kubernetes Engine"){: .center-image }

You will now create two things: the storage bucket that will hold your trained model, and a key file that can give your container access to it.

The following command will create the bucket. Your bucket name must be unique across all of Cloud Storage:

    $ export BUCKET_NAME=kubeflow-${PROJECT_ID}

    // Create the Cloud Storage bucket
    $ gsutil mb -c regional -l us-central1 gs://${BUCKET_NAME}

    // Now create your key. First, move back to the kubeflow-introduction project directory:
    $ cd ${HOME}/kubeflow-introduction

    // Next, create a service account to act as a virtual user in your GCP account
    $ gcloud iam service-accounts create kubeflow-qwiklab \
    --display-name kubeflow-qwiklab

The service account will act as a virtual user with restricted access to write data to the bucket on your behalf.

Now, grant the service account permission to read/write to the bucket.

Get the email associated with the new service account:

    $ export SERVICE_ACCOUNT_EMAIL=kubeflow-qwiklab@${PROJECT_ID}.iam.gserviceaccount.com

    // Allow the service account to upload data to the bucket
    $ gsutil acl ch -u ${SERVICE_ACCOUNT_EMAIL}:O gs://${BUCKET_NAME}

    // Finally, download the key file that lets authenticates as the service account
    $ gcloud iam service-accounts keys create ./tensorflow-model/key.json \
      --iam-account=${SERVICE_ACCOUNT_EMAIL}

After running the previous command, you should see a new file called key.json in the tensorflow-model directory. When you build the container, key.json will be added in along with the python code, allowing the TensorFlow program to authenticate to Cloud Storage and save model data to your bucket.


Important: Keep in mind that anyone with access to the key.json or a container that includes it will have access to read and write to your Cloud Storage bucket. Make sure you are aware of the security implications in your own projects.

<br/>

**Building the Container**

![Introduction to Kubeflow on Google Kubernetes Engine](/img/clouds/google/qwiklabs/google-cloud-solutions-2-data-and-machine-learning/introduction-to-kubeflow-on-google-kubernetes-engine/pic6.png "Introduction to Kubeflow on Google Kubernetes Engine"){: .center-image }


To deploy your code to Kubernetes, you have to first build your local project into a container.

Run the following to tag the version number associated with this model each time it runs with the current unix timestamp:

    $ export VERSION_TAG=$(date +%s)

    $ export TRAIN_IMG_PATH=us.gcr.io/${PROJECT_ID}/kubeflow-train:${VERSION_TAG}

    // Build the TensorFlow-model directory
    $ docker build -t ${TRAIN_IMG_PATH} ./tensorflow-model \
        --build-arg version=${VERSION_TAG} \
        --build-arg bucket=${BUCKET_NAME}

The container is tagged with its eventual path in Container Registry, but it stays local for now.

Note: The --build-arg in the docker build command are used to pass arguments into the dockerfile, which then passes them on to MNIST.py

If everything went well, your program should be encapsulated in a new container. Test it locally to make sure everything is working:

    $ docker run -it ${TRAIN_IMG_PATH}

If you're seeing logs, that means training is working and you can terminate the container with Ctrl+c in Cloud Shell.

Now you can safely upload it to Container Registry so you can run it on your cluster.

    // Allow docker to access your Container Registry by running:
    $ gcloud auth configure-docker

    // Push the container to Container Registry:
    $ docker push ${TRAIN_IMG_PATH}

<br/>

### Training on the Cluster

![Introduction to Kubeflow on Google Kubernetes Engine](/img/clouds/google/qwiklabs/google-cloud-solutions-2-data-and-machine-learning/introduction-to-kubeflow-on-google-kubernetes-engine/pic7.png "Introduction to Kubeflow on Google Kubernetes Engine"){: .center-image }

Finally, run the training job on the cluster.

The first step is to generate a ksonnet component out of the corresponding tf-job prototype.

    $ cd ${HOME}/kubeflow-introduction/ksonnet-kubeflow

    // Generate the component from the prototype
    $ ks generate tf-job train

You should now see a new file "train" in the kubeflow-ksonnet/components directory.

Now you can customize the component's parameters to point to your container in Container Registry as the training image by running:

    $ ks param set train image ${TRAIN_IMG_PATH}

    $ ks param set train name "train-"${VERSION_TAG}

    // Apply the container to the cluster:
    $ ks apply gke -c train

After applying the component, there should be a new tf-job on the cluster called "train". You can use kubectl to query some information about the job, including its current state:

    $ kubectl describe tfjob

For even more information, you can retrieve the python logs from the pod that's running the container itself (after the container has finished initializing):

    $ export POD_NAME=$(kubectl get pods --selector=tf_job_name=train-$VERSION_TAG \
          --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')

<br/>

    $ kubectl logs -f ${POD_NAME}

<br/>

The same python logs can be accessed through the Kubernetes Engine Cloud Console page:

    Go to Kubernetes Engine > Workloads.
    Click on your "train-xxxxxxx" component.
    Click on the Logs tab.


![Introduction to Kubeflow on Google Kubernetes Engine](/img/clouds/google/qwiklabs/google-cloud-solutions-2-data-and-machine-learning/introduction-to-kubeflow-on-google-kubernetes-engine/pic8.png "Introduction to Kubeflow on Google Kubernetes Engine"){: .center-image }

When training is complete, you should see the model data pushed into your Cloud Storage bucket, tagged with the same version number as the container that generated it. Click on your bucket name to explore.

In a production environment, it's likely that you will need to train more than once. Kubeflow gives you a simple deploy pipeline you can use to train new versions of your model repeatedly. When you have a new version to push, you simply build a new container (with a new version tag), modify your tf-job parameters, and re-apply it to the cluster. You don't need to regenerate the tf-job component every time, just set the parameters to point to the new version of your container. New model versions will appear in appropriately tagged directories inside the Cloud Storage bucket.


<br/>

### Serving

![Introduction to Kubeflow on Google Kubernetes Engine](/img/clouds/google/qwiklabs/google-cloud-solutions-2-data-and-machine-learning/introduction-to-kubeflow-on-google-kubernetes-engine/pic9.png "Introduction to Kubeflow on Google Kubernetes Engine"){: .center-image }

Now that you have a trained model, it's time to put it in a server so it can be used to handle requests. This task is handled by the tf-serving prototype, which is the Kubeflow implementation of TensorFlow Serving. Unlike the tf-job, no custom container is required for the server process. Instead, all the information the server needs is stored in the model file.

You need to point the server component to your Cloud Storage bucket where the model data is stored, and it will spin up to handle requests.

Create a ksonnet component from the prototype:

    $ ks generate tf-serving serve --name=mnist-serve

Set the parameters and apply to the cluster:

    $ ks param set serve modelPath gs://${BUCKET_NAME}/
    $ ks apply gke -c serve

One interesting detail to note is that you don't need to add a VERSION_TAG, even though you may have multiple versions of your model saved in your bucket. Instead, the serving component will pick up on the most recent tag, and serve it.

Like during training, you can check the logs of the running server pod to ensure everything is working as expected:


    $ export POD_NAME=$(kubectl get pods --selector=app=mnist-serve \
    --template '{{range .items}}{{.metadata.name}}{{"\n"}}{{end}}')

    $ kubectl logs ${POD_NAME}

<br/>

### Deploying the UI

![Introduction to Kubeflow on Google Kubernetes Engine](/img/clouds/google/qwiklabs/google-cloud-solutions-2-data-and-machine-learning/introduction-to-kubeflow-on-google-kubernetes-engine/pic10.png "Introduction to Kubeflow on Google Kubernetes Engine"){: .center-image }

Now you can deploy the final piece of your system: a web interface that can interact without a trained model server. This code is stored in the kubeflow-introduction/web-ui directory.

The web page for this task is fairly basic, consisting of a simple flask server hosting HTML/CSS/Javascript files. The flask server makes use of mnist_client.py, which contains a python function that directly interacts with the TensorFlow server.

Like in the training step, first build a container from your code.

    $ cd ${HOME}/kubeflow-introduction

    $ export UI_IMG_PATH=us.gcr.io/${PROJECT_ID}/kubeflow-web-ui

    $ docker build -t ${UI_IMG_PATH} ./web-ui

    $ docker push ${UI_IMG_PATH}


To deploy the web UI to the cluster, again create a ksonnet component. This time, use the ksonnet built-in deployed-service prototype. This component will create the deployment and Load Balancer so you can connect with your flask server from outside the cluster.

    $ cd ${HOME}/kubeflow-introduction/ksonnet-kubeflow

    // Generate the component from its prototype:
    $ ks generate deployed-service web-ui \
    --containerPort=5000 \
    --image=${UI_IMG_PATH} \
    --name=web-ui \
    --servicePort=80 \
    --type=LoadBalancer 

    // Apply the component to your cluster
    $ ks apply gke -c web-ui

Now there should be a new web UI running in the cluster. To access it through your web browser, run the following to find the external IP address of the service:

    $ kubectl get service web-ui
    NAME     TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)        AGE
    web-ui   LoadBalancer   10.43.252.186   34.68.179.102   80:30888/TCP   115s

Copy the external IP address from the output and paste it into your browser, and the web UI should load.

http://34.68.179.102/

To interact with the model within the web UI, you'll enter three details of the TensorFlow server running in the cluster - a name, an address, and a port:

* Server Name: The name will be the same name we gave to your serving component in step 4 ("mnist-serve").
* Server Address: The server address can be entered as a domain name or an IP address. Remember that this is an internal IP for the "mnist-serve" service within your cluster, not a public address. To simplify addressing issues, Kubernetes has an internal DNS service, so you can write the name of the service in the address field, (again, "mnist-serve") and Kubernetes will route all requests to the required IP automatically.
* Port: The server will be listening on port 9000 by default

Type these values into the web UI, then click Connect. It will find the server in you cluster and display the classification results.

<br/>

### The Final Product

If everything worked properly, you should see an interface for your machine learning model. Each time you refresh the page, it will load a random image from the MNIST testing set and perform a prediction. The table below the image displays the probability of each class label. Because the model was properly trained, confidence will be high and mistakes should be very rare. See if you can find any!


![Introduction to Kubeflow on Google Kubernetes Engine](/img/clouds/google/qwiklabs/google-cloud-solutions-2-data-and-machine-learning/introduction-to-kubeflow-on-google-kubernetes-engine/pic11.png "Introduction to Kubeflow on Google Kubernetes Engine"){: .center-image }