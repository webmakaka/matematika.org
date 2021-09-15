---
layout: page
title: Примеры работы с kafka
description: Примеры работы с kafka
keywords: BigData, Kafka, примеры работы
permalink: /ds/bigdata/kafka/samples/
---

<br/>

# Примеры работы с kafka

### Publishing Messages to a Topic in Kafka

 <br/>

    $ kafka-topics.sh \
    --create \
    --zookeeper zookeeper1:2181,zookeeper2:2181,zookeeper3:2181/kafka \
    --replication-factor 1 \
    --partitions 3 \
    --topic test

 <br/>
    
    $ kafka-topics.sh --zookeeper zookeeper1:2181,zookeeper2:2181,zookeeper3:2181/kafka --topic test --describe
    Topic:test	PartitionCount:3	ReplicationFactor:1	Configs:
	Topic: test	Partition: 0	Leader: 1	Replicas: 1	Isr: 1
	Topic: test	Partition: 1	Leader: 2	Replicas: 2	Isr: 2
	Topic: test	Partition: 2	Leader: 1	Replicas: 1	Isr: 1

<br/>

    $ kafka-console-producer.sh \
    --broker-list kafka1:9092,kafka2:9092,kafka3:9092 \
    --topic test
    >message1
    >message2
    >message3
    >^C

<br/>

<!--

    $ kafka-console-producer.sh --broker-list kafka1:9092 opic test --producer-property acks=all
    >message4
    >message5
    >^C

-->

<br/>

    $ kafka-topics.sh --zookeeper zookeeper1:2181,zookeeper2:2181,zookeeper3:2181/kafka --topic test --describe


    // Получить все сообщения
    $ kafka-console-consumer.sh --bootstrap-server kafka1:9092,kafka2:9092,kafka3:9092 --topic test --from-beginning

    // only new messages
    $ kafka-console-consumer.sh --bootstrap-server kafka1:9092 --topic test

<br/>

### Brokers

    $ zookeeper-shell.sh kafka1:2181

    ls /kafka
    [cluster, controller_epoch, controller, brokers, admin, isr_change_notification, consumers, log_dir_event_notification, latest_producer_id_block, config


    get /controller
    {"version":1,"brokerid":3,"timestamp":"1583181928919"}
    cZxid = 0x100000044
    ctime = Mon Mar 02 23:45:28 MSK 2020
    mZxid = 0x100000044
    mtime = Mon Mar 02 23:45:28 MSK 2020
    pZxid = 0x100000044
    cversion = 0
    dataVersion = 0
    aclVersion = 0
    ephemeralOwner = 0x1709cfef7c10001
    dataLength = 54
    numChildren = 0
