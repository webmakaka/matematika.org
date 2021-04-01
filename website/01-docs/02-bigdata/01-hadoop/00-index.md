---
layout: page
title: BigData - Hadoop
description: BigData - Hadoop
keywords: BigData, Hadoop
permalink: /ds/bigdata/hadoop/
---

# BigData - Hadoop

Offtopic: Если нет необходимости научиться инсталлировать opensource hadoop кластер, имеет смысл скачать готовую виртуалку с сайта cloudera и позапускать ее. Потом, попробовать поднять кластер с использованием инструментов cloudera или ambari на нескольких виртуальных машинах. И уже только потом, разобравшись что и как, уже пробовать поднимать свой opensource вариант.

<br/>

![BigData - Hadoop](/img/docs/bigdata/hadoop/pic1.png 'BigData - Hadoop'){: .center-image }

<br/>

Некоторые коммерческие фирмы предлагают готовые дистрибутивы Hadoop с наборами совместимых компонентов.

![BigData - Hadoop](/img/docs/bigdata/hadoop/pic2.png 'BigData - Hadoop'){: .center-image }

- Cloudera
- Hortonworks (Поглощена Cloudera)
- MapR Technologies

<br/>

Инструменты для управления окружением. Упрощают работу, добавление узлов, управления конфигами.

- [Cloudera](/ds/bigdata/hadoop/cloudera/)
- [Ambari](/ds/bigdata/hadoop/ambari/)
- [Без использования данных инструментов, ручная настройка](https://javadev.org/devtools/bigdata/hadoop/install/linux/)

<br/>

### Экосистема Hadoop

![BigData - Hadoop](/img/docs/bigdata/hadoop/pic3.png 'BigData - Hadoop'){: .center-image }

![BigData - Hadoop](/img/docs/bigdata/hadoop/pic4.png 'BigData - Hadoop'){: .center-image }

- HDFS — распределенная файловая система, работающая на больших кластерах стандартных машин.
- Yarn
- ZooKeeper — распределенный координационный сервис высокой доступности. ZooKeeper предоставляет примитивы, которые могут использоваться для постро­ения распределенных приложений (например, распределенные блокировки).
- MapReduce — модель распределенной обработки данных и исполнительная среда, работающая на больших кластерах типовых машин.
- Common — набор компонентов и интерфейсов для распределенных файловых сис­тем и общего ввода/вывода (сериализация, Java RPC, структуры данных).
- Avro — система сериализации для выполнениях эффективных межъязыковых вы­зовов RPC и долгосрочного хранения данных.
- Pig — язык управления потоком данных и исполнительная среда для анализа очень больших наборов данных. Pig работает в HDFS и кластерах MapReduce.
- Hive — распределенное хранилище данных. Hive управляет данными, хранимыми в HDFS, и предоставляет язык запросов на базе SQL (которые преобразуются ядром времени выполнения в задания MapReduce) для работы с этим данными.
- HBase — распределенная столбцово-ориентированная база данных. HBase ис­пользует HDFS для организации хранения данных и поддерживает как пакетные вычисления с использованием MapReduce, так и точечные запросы (произвольное чтение данных).
- Sqoop — инструмент эффективной массовой пересылки данных между структури­рованными хранилищами (такими, как реляционные базы данных) и HDFS.
- Oozie — сервис запуска и планирования заданий Hadoop (включая задания MapRe­duce, Pig, Hive и Sqoop jobs).

<br/>

### Решения в облаках

![BigData - Hadoop](/img/docs/bigdata/hadoop/pic5.png 'BigData - Hadoop'){: .center-image }

<br/>

### [HDFS](/ds/bigdata/hadoop/hdfs/)

### [MapReduce](/ds/bigdata/hadoop/mapreduce/)

<!--

<br/>

RDD - Resilient Distributed Dataset

-->
