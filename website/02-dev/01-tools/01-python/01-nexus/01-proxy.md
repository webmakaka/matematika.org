---
layout: page
title: Sonatype Nexus Repository Manager (Nexus) - Прокси сервер (proxy)
description: Sonatype Nexus Repository Manager (Nexus) - Прокси сервер (proxy)
keywords: dev, tools, python, nexus, proxy
permalink: /dev/tools/python/nexus/proxy/
---

# Sonatype Nexus Repository Manager (Nexus) - Прокси сервер (proxy)

<br/>

Были проблемы с работой в docker. Установил на хост.
Может быть, просто забыл выбрать "trusted certs"

<br/>

Allow anonymous users to access the server

<br/>

Create PyPi Proxy Repo

<br/>

![Nexus Repo](/img/docs/devtools/python/nexus/pic-nexus3-python-02.png 'Nexus Repo'){: .center-image }

<br/>

```
$ cd ~/.config
$ mkdir pip
$ cd pip
```

<br/>

```
$ vi ~/.config/pip/pip.conf
```

<br/>

```
[global]
index = http://localhost:8081/repository/pypi-proxy/pypi
index-url = http://localhost:8081/repository/pypi-proxy/simple
```

<br/>

```
$ export PIP_CONFIG_FILE=/home/marley/.config/pip/pip.conf
```

<br/>

```
$ pip config list
```

<br/>

```
$ pip install yolk3k
```

<br/>

```
$ pip install flask
```

<br/>

![Nexus Repo](/img/docs/devtools/python/nexus/pic-nexus3-python-03.png 'Nexus Repo'){: .center-image }

<br/>

```
$ pip install flask --index-url http://192.168.1.9:8081/repository/pypi-proxy/simple/ --trusted-host 192.168.1.9
```

<br/>

```
$ pip install -r ./requirements.txt --index-url http://192.168.1.9:8081/repository/pypi-proxy/simple/ --trusted-host 192.168.1.9
```

<!--

https://stackoverflow.com/questions/56592918/how-to-upload-the-python-packages-to-nexus-sonartype-private-repo

-->
