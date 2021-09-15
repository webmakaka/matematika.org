---
layout: page
title: Пример запуска MapReduce (Java)
description: Пример запуска MapReduce (Java)
keywords: BigData, Hadoop, MapReduce
permalink: /ds/bigdata/hadoop/mapreduce/example1/
---

# Пример запуска MapReduce (Java)

<br/>

### Набор метеорологических данных

В качестве примера мы напишем программу для анализа метеорологических дан­ных. Метеорологические датчики ежечасно собирают данные во многих точках по всему земному шару. Собранные данные хорошо подходят для анализа средствами MapReduce, потому что они частично структурированы и ориентированы на рабо­ту с записями.

<br/>

### Формат данных

Используемые данные были получены в Национальном центре климатических данных (NCDC, http://www.ncdc.noaa.gov/). Данные хранятся в строчно-ориенти­рованном формате ASCII, в котором каждая строка соответствует одной записи.

Файлы данных упорядочены по дате и метеостанции. Для каждого года с 1901 до 2001 имеется отдельный каталог, в котором находится gzip-архив для каждой метеостанции с данными за этот год.

<br/>

### Анализ данных в Hadoop

Чтобы воспользоваться преимуществами параллельной обработки данных, которые перед нами открывает Hadoop, необходимо представить запрос в виде за­дания MapReduce.

<br/>

### Отображение и свертка

Работа MapReduce основана на разбиении обработки данных на две фазы: фазу отображения (map) и фазу свертки (reduce). Каждая фаза использует в качестве входных и выходных данных пары «ключ-значение», типы которых выбираются
программистом. Программист также задает две функции: функцию отображения и функцию свертки.

Входными данными для фазы отображения в нашем примере являются исходные данные NCDC. Мы выбираем текстовый формат входных данных, при котором каждая строка набора данных интерпретируется как текстовое значение. Ключом является смещение начала строки от начала файла, но так как эта информация нам не нужна, мы ее просто игнорируем.

Функция отображения устроена просто. Мы извлекаем год и температуру воздуха, потому что нас интересуют только эти поля. В данном случае функция отобра­ жения всего лишь готовит данные к использованию так, чтобы функция свертки могла выполнить свою работу: определение максимальной температуры за каждый год. Функция отображения также хорошо подходит для исключения нежелатель­ ных записей: здесь отфильтровываются отсутствующие, сомнительные или оши­бочные значения температуры.

<br/>

### Программа MapReduce на языке Java

Нам понадобятся:

- функция отображения (<a href="https://github.com/tomwhite/hadoop-book/blob/master/ch02-mr-intro/src/main/java/MaxTemperatureMapper.java">Mapper<a>)
- функция свертки (<a href="https://github.com/tomwhite/hadoop-book/blob/master/ch02-mr-intro/src/main/java/MaxTemperatureReducer.java">Reducer<a>)
- код выполнения задания (<a href="https://github.com/tomwhite/hadoop-book/blob/master/ch02-mr-intro/src/main/java/MaxTemperature.java">MapReduce<a>).

<br/>

### Собственно запуск всего этого добра

Взял виртуальную машину <a href="/ds/bigdata/hadoop/ambari/hortonworks">hortonworks 2.5</a>

<br/>

Нужен maven 3 версии. Поставил <a href="//javadev.org/devtools/assembly-tools/maven/linux/centos/7/">Maven 3.6.2</a>.

    $ git clone https://github.com/tomwhite/hadoop-book
    $ cd hadoop-book/ch02-mr-intro/
    $ mvn package -DskipTests
    $ cd target

// Не знаю пока как сделать, чтобы работало с локальными файлами. Копирую файл в HDFS

    $ hadoop fs -mkdir -p /user/maria_dev/input/ncdc/

    $ hadoop fs -copyFromLocal /home/maria_dev/hadoop-book/input/ncdc/sample.txt /user/maria_dev/input/ncdc/

    $ hadoop fs -ls /user/maria_dev/input/ncdc/

// Запускаю из каталога, где лежит jar.

    $ export HADOOP_CLASSPATH=ch02-mr-intro-4.0.jar
    $ hadoop MaxTemperature input/ncdc/sample.txt output

// Результаты

    $ hadoop fs -ls /user/maria_dev/output/
    $ hadoop fs -cat /user/maria_dev/output/part-r-00000
    1949	111
    1950	22
