---
layout: page
title: Sonatype Nexus Repository Manager (Nexus) для Python
description: Sonatype Nexus Repository Manager (Nexus) для Python
keywords: dev, tools, python, nexus
permalink: /dev/tools/python/nexus/
---

# Sonatype Nexus Repository Manager (Nexus)

<br/>

Были проблемы с работой в docker. Установил на хост.

<br/>

### [Инсталляция nexus в ubuntu linux](//javadev.org/devtools/repository-management/nexus/3/installation-in-linux/)

<br/>

![Nexus Repo](/img/docs/devtools/python/nexus/pic-nexus3-python-01.png 'Nexus Repo'){: .center-image }

<br/>

```
login: admin
```

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
$ vi /home/marley/.config/pip/pip.conf
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
$ pip install --index-url http://localhost:8081/repository/python/simple/ flask
```

<br/>

<!--

https://stackoverflow.com/questions/56592918/how-to-upload-the-python-packages-to-nexus-sonartype-private-repo

-->
