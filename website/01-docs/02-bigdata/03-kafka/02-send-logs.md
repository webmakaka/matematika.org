---
layout: page
title: Отправка логов в kafka
description: Отправка логов в kafka
keywords: BigData, Kafka, Отправка логов в kafka
permalink: /ds/bigdata/kafka/send-logs/
---

<br/>

# Отправка логов в kafka

**Works with python2 only**

https://github.com/dgadiraju/gen_logs

    $ cd ~/tmp
    $ git clone https://github.com/dgadiraju/gen_logs

    $ sudo mv gen_logs /opt/gen_logs

<br/>

    $ sudo ln -s /opt/gen_logs/start_logs.sh /usr/bin/.
    $ sudo ln -s /opt/gen_logs/stop_logs.sh /usr/bin/.
    $ sudo ln -s /opt/gen_logs/tail_logs.sh /usr/bin/.

<br/>

    $ start_logs.sh
    $ stop_logs.sh
    $ tail_logs.sh

<br/>

    // Create topic
    $ kafka-topics.sh \
    --zookeeper zookeeper1:2181,zookeeper2:2181,zookeeper3:2181/kafka \
    --create \
    --topic retail \
    --partitions 3 \
    --replication-factor 1

<br/>

    // List topic
    $ kafka-topics.sh \
    --list \
    --zookeeper zookeeper1:2181,zookeeper2:2181,zookeeper3:2181/kafka

<br/>

### Run

    $ start_logs.sh

<br/>

    // Publish Messages
    $ tail_logs.sh | kafka-console-producer.sh \
    --broker-list kafka1:9092,kafka2:9092,kafka3:9092 \
    --topic retail

<br/>

<!--

    // Publish Messages
    $ tail_logs.sh | kafka-console-producer.sh \
    --broker-list kafka1:9092,kafka2:9092,kafka3:9092 \
    --topic retail

-->

<br/>

    // Receive Messages
    $ kafka-console-consumer.sh \
    --bootstrap-server kafka1:9092,kafka2:9092,kafka3:9092 \
    --topic retail

<br/>

    $ stop_logs.sh
