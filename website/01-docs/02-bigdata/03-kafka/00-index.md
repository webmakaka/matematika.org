---
layout: page
title: BigData - Kafka
description: BigData - Kafka
keywords: BigData, Kafka
permalink: /ds/bigdata/kafka/
---

# BigData - Kafka

Broker - Отдельный Kafka сервер. Получает сообщения от producers. Хранит у себя на диске. Отдатет сообщения consumers.

Producers - генерируют новые сообщения.

Consumers - читают сообщения.

Сообщения в Kafka распределяются по topics. Topics разбиваются на partitions.

Consumer подписывается на одну тему или более и читает сообщения в порядке их создания. Он отслеживает, какие сообщения он уже прочитал, запоминая смещение (offset) сообщений.

Apache Kafka использует ZooKeeper для хранения метаданных о кластере Kafka, а также подробностей о клиентах-потребителях. Полная версия ZooKeeper отдельный дистрибутив. Какая-то (по всей видимости) ограниченная версия ZooKeeper имеется прям в дистрибутиве Kafka.

<br/>

### [Запуск kafka в docker контейнере](//javadev.org/devtools/bigdata/kafka/docker/)

### [Инсталляция kafka в ubuntu с использованием vagrant и ansible](//javadev.org/devtools/bigdata/kafka/install/linux/)

### [Скрипты развертывания kafka с помощью ansible](https://github.com/matematika-org/kafka_ansible)

### [Примеры работы с kafka](/ds/bigdata/kafka/samples/)

### [Отправка логов в kafka](/ds/bigdata/kafka/send-logs/)
