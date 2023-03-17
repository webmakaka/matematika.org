---
layout: page
title: Проверка лицензий python пакетов
description: Проверка лицензий python пакетов
keywords: dev, tools, python, nexus, licenses check,
permalink: /dev/tools/python/nexus/licenses-check/
---

# Проверка лицензий python пакетов

<br/>

https://pypi.org/project/pip-licenses/

<br/>

```
$ cd ~/tmp/
$ git clone https://github.com/raimon49/pip-licenses.git
$ cd pip-licenses/
```

<br/>

```
$ vi Dockerfile
```

<br/>

```
FROM python:3.8.12-slim-bullseye
```

<br/>

```
$ vi docker/requirements.txt
```

<br/>

```
mlflow==1.22.*
psycopg2-binary==2.8.*
protobuf==3.20.*
```

<br/>

```
$ docker build . -t myapp-licenses
$ docker run --rm myapp-licenses --order=license --format=markdown
```
