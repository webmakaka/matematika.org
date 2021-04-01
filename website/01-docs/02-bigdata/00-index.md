---
layout: page
title: BigData
description: BigData
keywords: BigData
permalink: /ds/bigdata/
---

# BigData

<br/>

### [Hadoop](/ds/bigdata/hadoop/)

### [Spark](/ds/bigdata/spark/)

### [Kafka](/ds/bigdata/kafka/)

<br/>

Вроде как, есть бесплатное облачное решение на AWS для тестов spark в облаках. Т.е. самому ничего не нужно настраивать. Аналог jupyter notebook (если я все правильно понимаю). Не смог зарегиться, кнопка регистрации не реагировала на клик после заполнения полей.  
https://databricks.com/try-databricks

Разумеется, платное решение будет работать.

<br/>

### Zeppelin в docker контейнере

Не разобрался как добавить репо. Поэтому не особо рекомендую. Но scala/spark код выполнять можно.

    $ docker pull apache/zeppelin:0.8.1

    $ docker run --rm -it -p 7077:7077 -p 8080:8080 apache/zeppelin:0.8.1

http://localhost:8080

<br/>

### [JDK installation in linux (Ubuntu, Centos)](https://javadev.org/devtools/jdk/install/linux/)

### [Installation SCALA in linux](https://javadev.org/devtools/bigdata/scala/install/linux/)

<br/>

## Обучающие материалы:

### [Книги по BigData](/books/bigdata/)

### [Видеокурсы по BigData](/videos/ds/bigdata/)
