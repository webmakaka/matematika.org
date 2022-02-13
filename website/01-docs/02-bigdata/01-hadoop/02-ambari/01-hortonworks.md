---
layout: page
title: Виртуальная машина с дистрибутивами по направлению BigData от компании HortonWorks
description: Виртуальная машина с дистрибутивами по направлению BigData от компании HortonWorks
keywords: BigData, Hadoop, Ambari, HortonWorks
permalink: /ds/bigdata/hadoop/ambari/hortonworks/
---

# Виртуальная машина с дистрибутивами по направлению BigData от компании HortonWorks

https://hortonworks.com/downloads/#sandbox

Последняя версия HDP оказалась не такой понятной. Пришлось скачивать старую версию.

Браузеры упорно отказывались качать, ссылаясь на блокировщик рекламы. А если и качали, то со скоростью в 50 KB/сек.

<br/>

Оказалось, что можно скачать с помощью wget.

<br/>

### 2.6.5

    $ wget https://downloads-hortonworks.akamaized.net/sandbox-hdp-2.6.5/HDP_2.6.5_virtualbox_180626.ova

<br/>

### 2.5

    $ wget https://downloads-hortonworks.akamaized.net/sandbox-hdp-2.5/HDP_2.5_virtualbox.ova

<br/>

Далее импортировал машину в VirtualBox.

Подключиться к ней можно по http://localhost:8080

Или по ssh:

    $ ssh maria_dev@127.0.0.1 -p 2222

Логин/Пароль: maria_dev/maria_dev

    // Пароль root для 2.6.5: hortonworks1
    // Пароль root для 2.5: hadoop
