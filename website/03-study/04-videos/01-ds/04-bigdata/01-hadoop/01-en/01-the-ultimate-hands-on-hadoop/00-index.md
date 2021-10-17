---
layout: page
title: The Ultimate Hands-On Hadoop – Tame Your Big Data!
description: The Ultimate Hands-On Hadoop – Tame Your Big Data!
keywords: hadoop, udemy, the ultimate hands on hadoop tame your big data
permalink: /videos/ds/bigdata/hadoop/en/the-ultimate-hands-on-hadoop/
---

# The Ultimate Hands-On Hadoop – Tame Your Big Data!

**Ссылка на курс:**  
https://udemy.com/the-ultimate-hands-on-hadoop-tame-your-big-data/

<br/>

**Я не рекомендую его покупать. Что ни шаг, так ошибка. Для тех кто никогда не работал (например я) с этим стеком, это может быть большой проблемой.** И вообще курс "голопом по Европам". Т.е. просто для ознакомления с экосистемой.

<br/>

**Рекомендую использовать ту же версию, что и автор курса**. Я решил использовать последнюю из текущей версии. Ошибка на ошибке.

<br/>

Надеюсь в будушем я исправлю ошибки. Если кто кочет помочь, приглашаю в чат.

<br/>

**Наиболее актуальные исходники для курса:**  
https://github.com/matematika-org/The-Ultimate-Hands-On-Hadoop

<br/>

**Данные для исследований:**  
http://files.grouplens.org/datasets/movielens/ml-100k.zip

<br/>

![The Ultimate Hands-On Hadoop](/img/videos/bigdata/hadoop/en/the-ultimate-hands-on-hadoop/the-ultimate-hands-on-hadoop-01.png 'The Ultimate Hands-On Hadoop'){: .center-image }

![The Ultimate Hands-On Hadoop](/img/videos/bigdata/hadoop/en/the-ultimate-hands-on-hadoop/the-ultimate-hands-on-hadoop-02.png 'The Ultimate Hands-On Hadoop'){: .center-image }

![The Ultimate Hands-On Hadoop](/img/videos/bigdata/hadoop/en/the-ultimate-hands-on-hadoop/the-ultimate-hands-on-hadoop-03.png 'The Ultimate Hands-On Hadoop'){: .center-image }

![The Ultimate Hands-On Hadoop](/img/videos/bigdata/hadoop/en/the-ultimate-hands-on-hadoop/the-ultimate-hands-on-hadoop-04.png 'The Ultimate Hands-On Hadoop'){: .center-image }

<br/>

### [Подготовка окружения](/ds/bigdata/hadoop/ambari/)

<br/>

## 01 - Learn all the buzzwords! And install Hadoop

    Hive --> view

<br/>

    Upload Table --> CSV

    Field Delimeter --> TAB

    u.data

    Table Name -- ratings

    user_id movie_id rating rating_time

    UPLOAD

<br/>

    Field Delimiter 124 |

    u.item

    Table name movie_names

    movie_id name

    Upload

<br/>

**Query**

<br/>

```
SELECT movie_id, count(movie_id) as ratingCount
FROM ratings
GROUP BY movie_id
ORDER BY ratingCount
DESC
```

<br/>

```
SELECT name
FROM movie_names
WHERE movie_id = 50;
```

<br/>

## 02 - Using Hadoop's Core - HDFs and MapReduce

Подключаюсь к виртуальной машине по SSH.

Логин/Пароль: maria_dev/maria_dev

    $ ssh maria_dev@127.0.0.1 -p 2222

    $ sudo su -

    # yum install -y python-pip nano

    # pip install mrjob

    # ctrl ^D

    $ wget http://media.sundog-soft.com/hadoop/ml-100k/u.data

    $ wget http://media.sundog-soft.com/hadoop/RatingsBreakdown.py

    $ nano RatingsBreakdown.py

<br/>

### Run locally

    $ python RatingsBreakdown.py u.data

<br/>

### Run with Hadoop

    $ python RatingsBreakdown.py -r hadoop --hadoop-streaming-jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar u.data

<br/>

### Sort by popularity

    $ wget http://media.sundog-soft.com/hadoop/TopMovies.py
    $ python TopMovies.py u.data

Хз почему, у меня результаты не сошлись с результатами автора курса. Хз почему, разбираться лень. Думаю, данные разные.

<br/>

## 03 - Programming Hadoop with Pig

    // Наверное, чтобы просто посмотреть
    # ambari-admin-password-reset

<br/>

http://localhost:8080

Залогинились как maria_dev/maria_dev

<br/>

Files View --> user --> maria_dev

У нас здесь 2 файла: u.data и u.item

Pig View --> New Script
Name: Oldies But Goodies

<br/>

**Find old 5-star movies with Pig**

<br/>

https://bitbucket.org/sysadm-ru/the-ultimate-hands-on-hadoop/raw/04516d1318fc68feb2cc2657d51c2d2d75f0d845/Section-03/Oldest%20five-star%20movies.pig

<br/>

```
ratings = LOAD 'user/maria_dev/ml-100k/u.data'
            AS (userID:int, movieID:int, rating:int, ratingTime:int);

metadata = LOAD 'user/maria_dev/ml-100k/u.item' USING PigStorage('|')
            AS (movieID:int, movieTitle:chararray, releaseDate:chararray, videoRelease:chararray, imdblink:chararray);

nameLookup = FOREACH metadata GENERATE movieID, movieTitle,
                ToUnixTime(ToDate(releaseDate, 'dd-MMM-yyyy')) AS releaseTime;

ratingsByMovie = GROUP ratings by movieID;

avgRatings = FOREACH ratingsByMovie GENERATE GROUP AS movieID, AVG(ratings.rating) AS avgRating;

fiveStarMovies = FILTER avgRatings BY avgRating > 4.0;

fiveStarsWithData = JOIN fiveStarMovies BY movieID, nameLookup BY movieID;

oldestFiveStarMovies = ORDER fiveStarsWithData BY nameLookup::releaseTime;

DUMP oldestFiveStarMovies;
```

<br/>

**Find the most-rated one-star movie**

<br/>

https://bitbucket.org/sysadm-ru/the-ultimate-hands-on-hadoop/raw/a1f724592780243a6e5068173c2926bac7df01da/Section-03/Most%20one-star%20movies.pig

<br/>

```
ratings = LOAD '/user/maria_dev/ml-100k/u.data' AS (userID:int, movieID:int, rating:int, ratingTime:int);

metadata = LOAD '/user/maria_dev/ml-100k/u.item' USING PigStorage('|') AS (movieID:int, movieTitle:chararray, releaseDate:chararray, videoRelease:chararray, imdbLink:chararray);

nameLookup = FOREACH metadata GENERATE movieID, movieTitle;

ratingsByMovie = GROUP ratings By movieID;

avgRatings = FOREACH ratingsByMovie GENERATE group AS movieID, AVG(ratings.rating) AS avgRating, COUNT(ratings.rating) AS numRatings;

oneStarMovies = FILTER avgRatings BY avgRating < 2.0;

oneStarsWithData = JOIN oneStarMovies BY movieID, nameLookup BY movieID;

finalResults = FOREACH oneStarsWithData GENERATE nameLookup::movieTitle AS movieName, oneStarMovies::avgRating AS avgRating, oneStarMovies::numRatings AS numRatings;

finalResultsSorted = ORDER finalResults BY numRatings DESC;

DUMP finalResultsSorted;
```

<br/>

## 04 - Programming Hadoop with Spark

<br/>

http://localhost:8080

login as: admin

Services --> spark2 --> configs --> Advanced spark2-log4j-properties

log4j.rootCategory=ERROR, console

Save

Restart

<br/>

    $ ssh maria_dev@127.0.0.1 -p 2222

    $ mkdir ml-100k
    $ wget http://media.sundog-soft.com/hadoop/ml-100k/u.data
    $ wget http://media.sundog-soft.com/hadoop/ml-100k/u.item
    $ cd ../


    $ wget http://media.sundog-soft.com/hadoop/Spark.zip
    $ unzip Spark.zip

<br/>

    $ spark-submit LowestRatedMovieSpark.py

<br/>

**Datasets and Spark 2.0**

    $ echo $SPARK_MAJOR_VERSION
    2

    $ spark-submit LowestRatedMovieDataFrame.py

<br/>

    # pip install numpy

    // Movie recommendations with MLLib
    // У меня выполнение завершается ошибкой.
    $ spark-submit MovieRecommendationsALS.py

    $ spark-submit LowestRatedPopularMovieSpark.py
    $ spark-submit LowestRatedPopularMovieDataFrame.py

<br/>

## 05 - Using relational data stores with Hadoop

<br/>

http://localhost:8080

Hive View

    drop table ratings;

Upload Table

    CSV
    Fild Delimiter: TAB

    File: u.data

    Table name: ratings

    columns: userID, movieID, rating, epochseconds

    upload

<br/>

Upload Table

    CSV
    Fild Delimiter: |s

    File: u.item

    Table name: names

    columns: movieID, title

    upload

<br/>

Hive

```
CREATE VIEW topMovieIDs AS
SELECT movieID, count(movieID) as ratingCount
FROM ratings
GROUP BY movieID
ORDER BY ratingCount DESC;

SELECT n.title, ratingCount
FROM topMovieIDs t JOIN names n ON t.movieID = n.movieID;
```

<br/>

```
DROP VIEW topMovieIDs;
```

```
CREATE VIEW IF NOT EXISTS avgRatings AS
SELECT movieID, AVG(rating) as avgRating, count(movieID) as ratingCount
FROM ratings
GROUP BY movieID
ORDER BY avgRating DESC;

SELECT n.title, avgRating
FROM avgRatings t JOIN names n ON t.movieID = n.movieID
WHERE ratingCount > 10;
```

**Install MySQL and import our movie data**

    $ ssh maria_dev@127.0.0.1 -p 2222

<br/>

    $ cd ~
    $ wget http://media.sundog-soft.com/hadoop/movielens.sql

<br/>

    // Пароль hortonworks1
    // Пароль hadoop не подошел
    $ mysql -u root -p

<br/>

    SET NAMES 'utf8';
    SET CHARACTER SET utf8;

    mysql> create database movielens;

    mysql> show databases;
    mysql> exit

    mysql> use movielens

    mysql> source movielens.sql

    mysql> show tables;

    mysql> select * from movies limit 10;

    mysql> describe ratings;

<br/>

```
SELECT movies.title, COUNT(ratings.movie_id) AS ratingCount
FROM movies
INNER JOIN ratings
ON movies.id = ratings.movie_id
GROUP BY movies.title
ORDER BY ratingCount;

```

<br/>

    mysql> exit

<br/>

**Use Sqoop to import data from MySQL to HFDS_Hive**

    $ mysql -uroot -phortonworks1

    mysql> GRANT ALL PRIVILEGES ON movielens.* to root@'localhost';

    mysql> exit

    $ sqoop import --connect jdbc:mysql://localhost/movielens --driver com.mysql.jdbc.Driver --table movies -m 1 --username root --password hortonworks1

<br/>

    http://localhost:8080

    Fles view

    В каталоге movies появился файл part-m-00000.
    Посмотрели на файл и удалили весь каталог.

<br/>

Теперь в hive

    $ sqoop import --connect jdbc:mysql://localhost/movielens --driver com.mysql.jdbc.Driver --table movies -m 1 --username root --password hortonworks1 --hive-import

    Ну у меня FAILED

<br/>

    http://localhost:8080

    Hive view

    SELECT * FROM movies LIMIT 100;

<br/>

**Use Sqoop to export data from Hadoop to MySQL**

Не стал делать

https://bitbucket.org/sysadm-ru/the-ultimate-hands-on-hadoop/src/master/Section-05/Section5-39%20UseSqoopToExportDataFromHadoop.txt

<br/>

## 06 - Using non-relational data stores with Hadoop

http://localhost:8080

Как Admin и запустили Hbase service

<br/>

    $ ssh maria_dev@127.0.0.1 -p 2222

    $ sudo su -

    # /usr/hdp/current/hbase-master/bin/hbase-daemon.sh start -p 8000 --infoport 8001

Выполнили удаленно:

    https://bitbucket.org/sysadm-ru/the-ultimate-hands-on-hadoop/src/master/Section-06/HBaseExamples.py

Вырубили сервис

    # /usr/hdp/current/hbase-master/bin/hbase-daemon.sh stop rest

<br/>

**Use HBase with Pig to import data at scale**

// admin
http://localhost:8080/

FilesView --> user --> maria_dev --> ml-100k --> Upload

ml-100k/u.user

    $ ssh maria_dev@127.0.0.1 -p 2222

    $ hbase shell

    create 'users','userinfo'
    list

    exit

    $ wget http://media.sundog-soft.com/hadoop/hbase.pig
    $ pig hbase.pig

    $ hbase shell
    $ scan 'users'

    $ disable 'users'

    $ drop 'users'

    exit

<br/>

**Installing Cassandra**

    $ sudo su -

Требуется python 2.7
У меня уже все было ок.

<br/>

    # python -V
    Python 2.7.5

Если нет, то

    # yum install -y scl-utils
    # yum install -y centos-release-ci-rh
    # yum install -y python27
    # sci enbale python27 bash

<br/>

    # cd /etc/yum.repos.d/
    # vi datastax.repo

<br/>

```
[datastax]
name = DataStax Repo for Apache Cassandra
baseurl = http://rpm.datastax.com/community
enabled = 1
gpgcheck = 0
```

<br/>

    # yum install -y dsc30
    # service cassandra start

<br/>

    # pip install cqlsh

<br/>

    # cqlsh --cqlversion="3.4.0"

<br/>

    cqlsh> CREATE KEYSPACE movielens WITH replication = {'class': 'SimpleStrategy', 'replication_factor':'1'} AND durable_writes = true;

    cqlsh> USE movielens;

    cqlsh:movielens> CREATE TABLE users (user_id int, age int, gender text, occupation text, zip text, PRIMARY KEY (user_id));

    cqlsh:movielens> describe table users;

    cqlsh:movielens> SELECT * FROM users;

    cqlsh:movielens> exit

<br/>

**Write Spark output into Cassandra**

    $ wget http://media.sundog-soft.com/hadoop/CassandraSpark.py
    $ export SPARK_MAJOR_VERSION=2

    $ spark-submit --packages datastax:spark-cassandra-connector:2.0.0-M2-s_2.11 CassandraSpark.py

Выполнение завершилось ошибкой!!!

    Caused by: java.lang.ClassNotFoundException: org.apache.spark.sql.catalyst.package$ScalaReflectionLock$

Возможно, что нужно запускать как-то приблизительно следующим образом. ХЗ.

    $ spark-submit --packages datastax:spark-cassandra-connector:2.0.0-M2-s_2.11, org.apache.spark:spark-sql_2.11:2.2.0 CassandraSpark.py

<br/>

    $ cqlsh --cqlversion="3.4.0"

    cqlsh> USE movielens;

    SELECT * FROM users LIMIT 10;

    exit

<br/>

    $ sudo su -
    # service cassandra stop

<br/>

**Install MongoDB, and integrate Spark with MongoDB**

    # cd /var/lib/ambari-server/resources/stacks
    # cd HDP/2.6/services/

    # git clone https://github.com/nikunjness/mongo-ambari.git

    // так ничего не заработало
    # service ambari restart

<br/>

// admin
http://localhost:8080

Слева внизу
Actions --> Add Service --> Mongo --> next next next

<br/>

    # pip install pymongo

    $ wget http://media.sundog-soft.com/hadoop/MongoSpark.py
    $ export SPARK_MAJOR_VERSION=2
    $ spark-submit --packages org.mongodb.spark:mongo-spark-connector_2.11:2.2.0 MongoSpark.py

<br/>

**Using the MongoDB shell**

    $ export LC_ALL=C
    $ mongo
    > use movielens
    > db.users.find({user_id: 100 })

    > db.users.explain().find( {user_id: 100})

    > db.users.createIndex ( {user_id: 1} )

    > db.users.aggregate([
    { $group: { _id: {occupation: "$occupation"}, avgAge: { $avg: "$age" } } }
    ])

    > db.users.count()

<br/>

## 07 - Querying Your Data Interactively (Drill, Phoenix, Presto)

**Setting up Drill**

Drill позволяет делать SQL запросы к нереляционным базам данным.

// admin
http://localhost:8080

Hive View

<br/>

    CREATE DATABASE movielens;

<br/>

    Upload Table --> CSV

    Field Delimeter --> TAB
    Database: movielens

    u.data

    Table Name -- ratings

    user_id movie_id rating epoch_seconds

    UPLOAD

<br/>

    SELECT * FROM ratings LIMIT 100;

<br/>

Также мы будем работать с mongo, подготовленную как в предыдушем примере.

<br/>

**Устанавливаем Drill**

https://drill.apache.org/download/

    $ sudo su -
    # wget http://apache-mirror.rbc.ru/pub/apache/drill/drill-1.16.0/apache-drill-1.16.0.tar.gz
    # tar -xvf apache-drill-1.16.0.tar.gz
    # cd apache-drill-1.16.0/bin/
    # ./drillbit.sh start -Ddrill.exec.http.port=8765

<br/>

http://localhost:8765

    Storage

    Enable Mongo, Hive

    Hive --> Update

<br/>

**Querying across multiple databases with Drill**

DRILL --> QUERY

    SHOW DATABASES;

    SELECT * FROM hive.movielens.ratings LIMIT 10;

Не видит у меня таблицы hive.movielens.ratings

    SELECT * FROM mongo.movielens.users LIMIT 10;

    // Разумеется у меня это работать тоже не будет
    SELECT u.occupation, COUNT(*)
    FROM hive.movielens.ratings r
    JOIN mongo.movielens.user u ON r.user_id = u.user_id
    GROUP BY u.occupation;

<br/>

    # cd apache-drill-1.16.0/bin/
    # ./drillbit.sh stop

<br/>

**Install Phoenix and query HBase with it**

Ambari --> Service Hbase Start

<br/>

    # yum install -y phoenix

    # cd /usr/hdp/current/phoenix-client/bin/

    # python sqlline.py

    > !tables

<br/>

    CREATE TABLE IF NOT EXISTS us_population(state CHAR(2) NOT NULL,
    city VARCHAR NOT NULL,
    population BIGINT
    CONSTRAINT my_pk PRIMARY KEY (state,city));

    UPSERT INTO US_POPULATION
    VALUES ('NY', 'New Yourk', 8143197);

    UPSERT INTO US_POPULATION
    VALUES ('CA', 'Los Angeles', 3844829);

    SELECT * FROM US_POPULATION;

    SELECT * FROM US_POPULATION
    WHERE STATE='CA';

    DROP TABLE US_POPULATION;

    !quit

<br/>

**Integrate Phoenix with Pig**

    # python sqlline.py

    CREATE TABLE IF NOT EXISTS users(USERID INTEGER NOT NULL, AGE INTEGER, GENDER CHAR(1), OCCUPATION VARCHAR, ZIP VARCHAR
    CONSTRAINT my_pk PRIMARY KEY (USERID));

    !quit

<br/>

    # su - maria_dev
    $ cd ml-100k/
    $ wget http://media.sundog-soft.com/hadoop/ml-100k/u.user
    $ cd ../

<br/>

    $ wget http://media.sundog-soft.com/hadoop/phoenix.pig

    $ pig phoenix.pig

<br/>

    // Возвращаемся в консоль phoenix

    # cd /usr/hdp/current/phoenix-client/bin/

    # python sqlline.py

    SELECT * FROM USERS LIMIT 10;

    DROP TABLE users;

    !quit

<br/>

Ambari --> Service Hbase Stop

<br/>

**Install Presto, and query Hive with it**

https://prestodb.github.io/docs/current/installation/deployment.html

    # cd ~

    # wget https://repo1.maven.org/maven2/com/facebook/presto/presto-server/0.219/presto-server-0.219.tar.gz

    # tar -xvf presto-server-0.219.tar.gz

    # cd presto-server-0.219

    $ wget http://media.sundog-soft.com/hadoop/presto-hdp-config.tgz

    # tar -xvf presto-hdp-config.tgz

https://prestodb.github.io/docs/current/installation/cli.html

    # cd presto-server-0.219/bin

    # wget https://repo1.maven.org/maven2/com/facebook/presto/presto-cli/0.219/presto-cli-0.219-executable.jar

    # mv presto-cli-0.219-executable.jar presto

    # chmod +x presto

    # ./launcher start

http://localhost:8090/ui/

<br/>

    # ./presto --server 127.0.0.1:8090 --catalog hive

<br/>

    presto> show tables from default;

    presto> SELECT * FROM default.ratings limit 10;

    presto> SELECT count(*) FROM default.ratings where rating = 5;

    presto> quit

<br/>

    # ./launcher stop

<br/>

**Query both Cassandra and Hive using Presto**

    # service cassandra start

    # nodetool enablethrift

    # cqlsh --cqlversion="3.4.0"

    cqlsh> describe keyspaces;

    cqlsh> use movielens;

    cqlsh:movielens> describe tables;

    cqlsh:movielens> select * from users limit 10;

<br/>

Ранее у меня импорт не отработал и я не стал разбираться. Может потом.

    # cd presto-server-0.219/etc/catalog
    # vi cassandra.properties

connector.name=cassandra
cassandra.contact-points=127.0.0.1

    # cd presto-server-0.219/bin

    # ./launcher start

    # ./presto --server 127.0.0.1:8090 --catalog hive,cassandra

<br/>

    presto> show tables from cassandra.vovielens;

    presto> describe cassandra.moveilens.users;

    presto> select * from cassandra.movielens.users limit 10;

    presto> select * from hive.default.ratings limit 10;

    presto> select u.occuation, count(*)from hive.default.ratings r join cassandra.moviewlens.users u on r.user_id = u.user_id group by u.occupation;

<br/>

    # ./launcher stop

    # service cassandra stop

<br/>

## 08 - Managing your Cluster

**Use Hive on Tez and measure the performance benefit**

// admin
Ambari --> Hive View --> Upload Table

    Field Delimiter: |
    Database: movielens
    FilenNaa: u.name
    Table name: names
    Columns: movie_id, name, release_date
    Upload

<br/>

```
DROP VIEW IF EXISTS topMovieIDs;

CREATE VIEW topMovieIDs AS
SELECT movie_id, count(movie_id) as ratingCount
FROM movielens.ratings
GROUP BY movie_id
ORDER BY ratingCount DESC;

SELECT n.name, ratingCount
FROM topMovieIDs t JOIN movielens.names n ON t.movie_id = n.movie_id;
```

<br/>

Hive --> Query --> Settings

hive.execution.engine --> tez

<br/>

С tez вроде выполняется быстрее, чем с mr (map reduce)

<br/>

**Mesos explained**

Лучше сразу docker/kubernetes

<br/>

**Simulating a failing master with ZooKeeper**

    # cd /usr/hdp/current/zookeeper-client/bin
    # ./zkCli.sh

<br/>

    create -e /testmaster "127.0.0.1:223"

    get /testmaster

    quit

<br/>

**Set up a simple Oozie workflow**

Базу для mysql сделали ранее.

    $ cd ~
    $ wget http://media.sundog-soft.com/hadoop/oldmovies.sql
    $ wget http://media.sundog-soft.com/hadoop/workflow.xml
    $ wget http://media.sundog-soft.com/hadoop/job.properties

    $ hadoop fs -put workflow.xml /user/maria_dev
    $ hadoop fs -put oldmovies.sql /user/maria_dev

Путь посмотреть в ambari --> Files View --> /user/oozie/share/lib/

    $ hadoop fs -put /usr/share/java/mysql-connector-java.jar /user/oozie/share/lib/lib_20180618160835/sqoop

ambari --> Oozie --> restart all

    $ oozie job --oozie http://localhost:11000/oozie --config /home/maria_dev/job.properties -run

    // ошибка

Error: E1603 : java.net.UnknownHostException: sandbox-hdp.hortonworks.com

//

По идее, далее нужно подключиться к

localhost:11000/oozie/

<br/>

**Use Zeppelin to analyze movie ratings**

Уже установлен

http://localhost:9995

Что-то вроде jupyter notebook но на java

(не стал разбираться).

<br/>

## 09 - Feeding Data to your Cluster (Kafka, Flume)

<br/>

### Setting up Kafka, and publishing some data

ambary --> kafka --> start (если сервис не запущен ранее)

    $ ssh maria_dev@127.0.0.1 -p 2222

<br/>

    $ cd /usr/hdp/current/kafka-broker/bin
    $ ./kafka-topics.sh --create --zookeeper sandbox-hdp.hortonworks.com:2181 --replication-factor 1 --partitions 1 --topic fred

Опять ничего не заработало.
Далее просто записываю команды.

<br/>

    $ ./kafka-topics.sh --list --zookeeper sandbox-hdp.hortonworks.com:2181

    $ ./kafka-console-producer.sh --broker-list sandbox-hdp.hortonworks.com:6667 --topic fred

Пишем в топик:

    This is a line of data
    I am sending this on the fred topic

<br/>

Открываем еще одну консоль maria_dev

    $ cd /usr/hdp/current/kafka-broker/bin
    $ ./kafka-console-consumer.sh --bootstrap-server sandobx.hortonworks.com:6667 --zookeeper localhost:2181 --topic fred --from-beginning

Должны получить что отправили в topic.

<br/>

### Publishing web logs with Kafka

    $ cd /usr/hdp/current/kafka-broker/conf

    $ cp connect-standalone.properties ~/
    $ cp connect-file-sink.properties ~/
    $ cp connect-file-source.properties ~/
    $ cd ~

<br/>

    $ vi connect-standalone.properties
    bootstrap.servers=sandbox-hdp.hortonworks.com:6667

<br/>

    $ vi connect-file-sink.properties
    file=/home/maria_dev/logout.txt
    topics=log-test

 <br/>

    $ vi connect-file-source.properties
    file=/home/maria_dev/access_log_small.txt
    topics=log-test

 <br/>

    $ wget http://media.sundog-soft.com/hadoop/access_log_small.txt

<br/>

1 терминальная сессия:

    $ cd /usr/hdp/current/kafka-broker/bin
    $ ./kafka-console-consumer.sh --bootstrap-server sandobx.hortonworks.com:6667 --topic log-test --zookeeper localhost:2181

2 терминальная сессия:

    $ ./connect-standalone.sh ~/connect-standalone.properties ~/connect-file-source.properties ~/connect-file-sink.properties

3 терминальная сессия:

    $ less logout.txt
    $ echo "This is a new line" >> access_log_small.txt

<br/>

### Set up Flume and publish logs with it

    $ wget http://media.sundog-soft.com/hadoop/example.conf

1 терминальная сессия:

    $ cd /usr/hdp/current/flume-server/bin/
    $ ./flume-ng agent --conf conf --conf-file ~/example.conf --name a1 -Dflume.root.logger=INFO,console

2 терминальная сессия:

    $ sudo yum install -y telnet
    $ telnet localhost 44444
    Hello there, how are you today?

<br/>

Опять ничего не заработало!

<br/>

### Set up Flume to monitor a directory and store its data in HDFS

    $ cd ~
    $ wget http://media.sundog-soft.com/hadoop/flumelogs.conf
    $ mkdir spool

Ambari --> Files View --> user --> maria_dev

Создали каталог "flume"

<br

1 терминальная сессия:

    $ cd /usr/hdp/current/flume-server/bin/
    $ ./flume-ng agent --conf conf --conf-file ~/flumelogs.conf --name a1 -Dflume.root.logger=INFO,console

2 терминальная сессия:

    $ cp access_log_small.txt ./spool/fred.txt
    $ cd spool/
    $ ls
    fred.txt.COMPLETED

<br/>

Ambari --> Files View --> user --> maria_dev --> flume

Появились какие-то данные

<br/>

## 10 - Analysing Streams of Data (Spark, Storm, Flink)

<br/>

### Analyze web logs published with Flume using Spark streaming

    $ cd ~
    $ wget http://media.sundog-soft.com/hadoop/sparkstreamingflume.conf
    $ wget http://media.sundog-soft.com/hadoop/SparkFlume.py

<br/>

1 терминальная сессия:

    $ mkdir checkpoint
    $ export SPARK_MAJOR_VERSION=2

    $ spark-submit --packages org.apache.spark:spark-streaming-flume_2.11:2.2.0 SparkFlume.py

Exception

2 терминальная сессия:

    $ cd /usr/hdp/current/flume-server/
    $ bin/flume-ng agent --conf conf --conf-file ~/sparkstreamingflume.conf --name a1

Exception

<br/>

    $ wget http://media.sundog-soft.com/hadoop/access_log.txt

<br/>

    $ cp access_log.txt spool/log22.txt

<br/>

### Aggregating HTTP access codes with Spark Streaming

    $ wget http://media.sundog-soft.com/hadoop/SparkFlumeExercise.py

1 терминальная сессия:

    $ spark-submit --packages org.apache.spark:spark-streaming-flume_2.11:2.2.0 SparkFlumeExercise.py

    Exception

2 терминальная сессия:

    $ cd /usr/hdp/current/flume-server/
    $ bin/flume-ng agent --conf conf --conf-file ~/sparkstreamingflume.conf --name a1

    Exception

3 терминальная сессия:

    $ cp access_log.txt ./spool/log30.txt
    $ cp access_log.txt ./spool/log31.txt

<br/>

### Count words with Storm

    ambari --> Storm --> Start
    ambari --> Kafka --> Start

<br/>

    $ cd /usr/hdp/current/storm-client/contrib/storm-starter/src/jvm/org/apache/storm/starter

    $ storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-*.jar org.apache.storm.starter.WordCountTopology wordcount

Exception

http://localhost:8744/index.html

<br/>

    $ cd /usr/hdp/current/storm-client/logs
    $ cd workers-artifacts
    $ cd wordcount-1-1557834606
    $ cd 6700/
    $ tail -f worker.log

<br/>

### Counting words with Flink

https://flink.apache.org/downloads.html

<br/>

    $ wget https://archive.apache.org/dist/flink/flink-1.2.0/flink-1.2.0-bin-hadoop27-scala_2.10.tgz

    $ tar -xvf flink-1.2.0-bin-hadoop27-scala_2.10.tgz
    $ cd flink-1.2.0/conf/
    $ vi flink-conf.yaml
    jobmanager.web.port: 8082

<br/>

    $ cd ../
    $ ./bin/start-local.sh

http://localhost:8082

<br/>

1 терминальная сессия:

    $ nc -l 9000
    i am a rock[Enter]

<br/>

2 терминальная сессия:

    $ cd flink-1.2.0
    $ ./bin/flink run examples/streaming/SocketWindowWordCount.jar --port 9000

В веб интерфейсе появилась job

<br/>

3 терминальная сессия:

    $ cd flink-1.2.0/log/
    $ cat flink-maria_dev-jobmanager-0-sandbox-hdp.hortonworks.com.out
