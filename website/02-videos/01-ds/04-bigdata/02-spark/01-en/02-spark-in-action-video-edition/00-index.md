---
layout: page
title: Spark in Action Video Edition
permalink: /videos/ds/bigdata/spark/en/spark-in-action-video-edition/
---

# Spark in Action Video Edition

    $ cd ~
    $ git clone https://github.com/spark-in-action/first-edition.git
    $ mkdir spark_project
    $ cd spark_project
    $ wget https://raw.githubusercontent.com/spark-in-action/first-edition/master/spark-in-action-box.json

    $ vagrant box add spark-in-action-box.json

    $ vagrant init manning/spark-in-action

    $ vagrant up

<br/>

    $ vagrant ssh

password: vagrant

    // Чтобы остановить
    $ vagrant halt

<br/>

    $ git clone https://github.com/spark-in-action/first-edition

<br/>

    $ /usr/locl/hadoop/sbin/start-dfs.sh
    $ /usr/locl/hadoop/sbin/stop-dfs.sh

<br/>

### Eclipse

Устанавливаем jdk8, scala

Загружаем: javaee enterprise

Help --> Install new Software

<br/>
<br/>

Add

scala-ide

http://download.scala-ide.org/sdk/lithium/e44/scala211/stable/site

Инсталлируем только: Scala IDE for Eclipse

<br/>

Add

m2eclipse-scala

http://alchim31.free.fr/m2e-scala/update-site

<br/>
<br/>

Import Maven Remote Archetype Catalogs in Eclipse

    Step 1 : Open maven preferences in eclipse. Go to Windows -> Preferences -> Maven -> Archetypes . Maven archetype option in eclipse.
    Step 2 : Add remote catalog file. Click on Add Remote Catalog button. ...
    Step 3 : Verify remote archetypes. To verify that you can now access to all archetypes, create a new maven project.

<br/>

File --> New --> Project --> Maven --> Maven Project

<br/>

Catalog File: https://raw.githubusercontent.com/spark-in-action/scala-archetype-sparkinaction/master/archetype-catalog.xml

Description: Spart in Action

![Spark in Action Video Edition](/img/videos/bigdata/spark/en/spark-in-action-video-edition/spark-in-action-video-edition-01.png 'Spark in Action Video Edition'){: .center-image }

![Spark in Action Video Edition](/img/videos/bigdata/spark/en/spark-in-action-video-edition/spark-in-action-video-edition-02.png 'Spark in Action Video Edition'){: .center-image }

![Spark in Action Video Edition](/img/videos/bigdata/spark/en/spark-in-action-video-edition/spark-in-action-video-edition-03.png 'Spark in Action Video Edition'){: .center-image }

<br/>

**Preparing the GitHub archive dataset (1.1 GB)**

    $ mkdir -p ~/sia/github-archive
    $ cd ~/sia/github-archive
    $ wget http://data.githubarchive.org/2015-03-01-{0..23}.json.gz
    $ gunzip *

    $ sudo apt install -y jq
    $ head -n 1 2015-03-01-0.json | jq '.'

<br/><br/>

![Spark in Action Video Edition](/img/videos/bigdata/spark/en/spark-in-action-video-edition/spark-in-action-video-edition-04.png 'Spark in Action Video Edition'){: .center-image }

<br/>

![Spark in Action Video Edition](/img/videos/bigdata/spark/en/spark-in-action-video-edition/spark-in-action-video-edition-05.png 'Spark in Action Video Edition'){: .center-image }
