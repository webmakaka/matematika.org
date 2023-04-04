---
layout: page
title: Sonatype Nexus Repository Manager (Nexus) - Загрузка пакетов в nexus (hosted)
description: Sonatype Nexus Repository Manager (Nexus) - Загрузка пакетов в nexus (hosted)
keywords: dev, tools, python, nexus, hosted
permalink: /dev/tools/python/nexus/hosted/
---

# Sonatype Nexus Repository Manager (Nexus) - Загрузка пакетов в nexus (hosted)

<br/>

```
$ cd ~/tmp
$ pip download flask

// С указанием специальной версии
// $ pip download python-magic==0.4.27

// Тоже работает
$ pip download -r ./requirements.txt
```

<br/>

```
// Когда нужна специфичная версия.
$ pip download --only-binary=:all: --python-version=37 --abi=cp37m --platform=manylinux2010_x86_64 numpy==1.18.0
```

<br/>

```
$ pip show flask
Name: Flask
Version: 2.1.1
Summary: A simple framework for building complex web applications.
Home-page: https://palletsprojects.com/p/flask
Author: Armin Ronacher
Author-email: armin.ronacher@active-4.com
License: BSD-3-Clause
Location: /home/marley/.local/lib/python3.8/site-packages
Requires: Jinja2, click, itsdangerous, Werkzeug, importlib-metadata
Required-by: prometheus-flask-exporter, mlflow, Flask-Script, flask-restx
```

<br/>

```
$ ls
click-8.1.2-py3-none-any.whl
Flask-2.1.1-py3-none-any.whl
importlib_metadata-4.11.3-py3-none-any.whl
itsdangerous-2.1.2-py3-none-any.whl
Jinja2-3.1.1-py3-none-any.whl
MarkupSafe-2.1.1-cp38-cp38-manylinux_2_17_x86_64.manylinux2014_x86_64.whl
Werkzeug-2.1.0-py3-none-any.whl
zipp-3.7.0-py3-none-any.whl
```

<br/>

```
$ vi ~/.pypirc
```

<br/>

```
[distutils]
index-servers:
   nexus

[nexus]
repository: http://192.168.1.9:8081/repository/pypi-internal/
username: admin
password: admin
```

<br/>

```

UPD! Делал в env от conda. Не работал. Вышел командой, $ conda deactivate, стало OK!

// Если нужно без проверки сертификатов
$ export CURL_CA_BUNDLE=""
```

<br/>

```
$ pip install twine
```

<br/>

```
// UPLOAD 1
$ twine upload --repository nexus ./Flask-2.1.1-py3-none-any.whl

// UPLOAD multiple
$ twine upload --repository nexus dist/* --skip-existing --verbose

// Или еше вариант
// OK!
$ python -m twine upload --repository nexus dist/* --skip-existing --verbose
```

<br/>

```
// Download
$ pip download example-package --index-url http://192.168.1.9:8081/repository/pypi-internal/simple/ --trusted-host 192.168.1.9
```

<br/>

```
// INSTALL
$ pip install example-package --index-url http://192.168.1.9:8081/repository/pypi-internal/simple/ --trusted-host 192.168.1.9
```
