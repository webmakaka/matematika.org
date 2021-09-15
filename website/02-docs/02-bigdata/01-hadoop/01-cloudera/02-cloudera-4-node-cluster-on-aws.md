---
layout: page
title: BigData - Hadoop - Cloudera
description: BigData - Hadoop - Cloudera
keywords: BigData, Hadoop, Cloudera
permalink: /ds/bigdata/hadoop/hortonworks/cloudera-4-node-cluster-on-aws/
---

# BigData - Hadoop - Cloudera - Instructions are to set up a 4 node cluster on AWS

http://s3.amazonaws.com/learn-hadoop/4-node-cluster-setup-script.txt

```
/*
 this document is part of infinite skills Hadoop course
 you can download this document from:
 https://s3.amazonaws.com/learn-hadoop/4-node-cluster-setup-script.txt

 these instructions are to set up a 4 node cluster on AWS' infrastructure
 this requires setting up an AWS cloud account and we do not cover those steps here
 the instructions should work equally well on your own infrastructure (just sub out the IP addresses for local ones)

 log in to http://console.aws.amazon.com, select EC2 & press "launch instances"
 for our cluster, we use an m1.medium for the master, and m1.smalls for the slaves
 choose ubuntu 12.04 LTS for both OSes, make sure to give the master AT LEAST 15GB of EBS,
 slaves 20GB of EBS disk
 in the video, we cover security group and keypair creation
 more details on the setup can be found at:
 http://www.cloudera.com/content/cloudera-content/cloudera-docs/CM4Ent/latest/Cloudera-Manager-Installation-Guide/cmig_install_path_A.html?scroll=cmig_topic_6_5

 @author rICh <rich@quicloud.com>
*/

<--- VIDEO #1 & 2 (MASTER SETUP, COMMAND LINE) --->

# port review http://www.cloudera.com/content/cloudera-content/cloudera-docs/CM4Ent/latest/Cloudera-Manager-Installation-Guide/cmig_ports_CM.html

# download the private part of your keypair and change it's permissions to rw------- with:
chmod 600 /path/to/your/HadoopKey.pem

# first spin up the master node, then spin up the slaves. Once master node is up, log into it with:
ssh -i /path/to/your/HadoopKey.pem ubuntu@[MASTER-PUBLIC-DNS]

# very first thing to do is update the packages. Cloudera Manager sometimes whacks out if pkgs are not
# up to date
sudo aptitude update

# next, run the following commands to download the Cloudera Manager installer & start the install process:
cd /usr/local/src/
# get the source installer
sudo wget http://archive.cloudera.com/cm4/installer/latest/cloudera-manager-installer.bin
# make it executable
sudo chmod u+x cloudera-manager-installer.bin
# install
sudo ./cloudera-manager-installer.bin

<--- VIDEO #2 (MASTER SETUP, WEB GUI) --->

# the installer will take roughly 5-7 minutes to complete the command line install.
# once it finishes (tells you to go to localhost:7180), instead browse to:
http://[MASTER-PUBLIC-DNS]:7180
# log in with admin/admin

# restart cloudera manager with (restarts in a minute or so):
 for cloudera manager to restart (1-2 mins)
# once master is set up, go ahead & burn an AMI of it (lets us start/stop our cluster later)
# note down the IP addresses of the slaves & pub DNS of master here:
MASTER PUB DNS:
MASTER PRIVATE IP:
SLAVE PRIVATE IPS:

<--- VIDEO #3 (SLAVE SETUP AND VERIFY) --->

# verify cluster operation (on master node):
cd ~/
mkdir code-and-data
cd code-and-data
sudo wget https://s3.amazonaws.com/learn-hadoop/hadoop-infiniteskills-richmorrow-class.tgz
tar -xvzf hadoop-infiniteskills-richmorrow-class.tgz
# the "data" directory has all of our datasets, of which shakespeare is one
cd data
sudo -u hdfs hadoop fs -mkdir /user/ubuntu
sudo -u hdfs hadoop fs -chown ubuntu /user/ubuntu
# upload file to HDFS with
hadoop fs -put shakespeare shakespeare-hdfs
hadoop version
# take note of the version, hopefully doesn't influence the directories below
# if you get java errors on these directories, you can do a "sudo su -; find / -type f -name 'hadoop-examples.jar'" to find the new path
hadoop jar /usr/share/hue/apps/oozie/examples/lib/hadoop-examples.jar wordcount shakespeare-hdfs wordcount-output
hadoop jar /usr/share/hue/apps/oozie/examples/lib/hadoop-examples.jar sleep -m 10 -r 10 -mt 20000 -rt 20000

# browse web gui
#  cluster workload
#  health & performance (Services -> All)
#  reports (disk usage -- Services->HDFS, MR usage -- Services->MR)
#  reports on sys performance


```
