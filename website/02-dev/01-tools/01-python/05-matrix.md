---
layout: page
title: Matrix
description: Matrix
keywords: dev, tools, python, virtualenv, ubuntu
permalink: /dev/tools/python/matrix/
---

# Matrix

<br/>

Делаю:  
27.01.2023

<br/>

```
$ pip install matrix-synapse authlib
```

<br/>

```
// Если нужно работать с базой postgres
$ pip install psycopg2
```

Возможно потребуется psycopg2-binary

<br/>

Подключитесь, например, с помощью программы dbeaver к СУБД и создайте базу данных для сервиса matrix следующей командой:

```
CREATE DATABASE matrix ENCODING 'UTF8' LC_COLLATE='C' LC_CTYPE='C' template=template0 OWNER postgres;
```

<br/>

```
$ python -m synapse.app.homeserver \
    --server-name matrix \
    --config-path homeserver.yaml \
     --generate-config \
    --report-stats=no
```

<br/>

Отредактируйте конфиг файл homeserver.yaml

<br/>

```
bind_addresses: ['0.0.0.0']

database:
  name: psycopg2
  args:
    user: postgres
    password: password
    database: matrix
    host: localhost

suppress_key_server_warning: true

enable_registration_without_verification: true
enable_registration: true
```

<br/>

Создайте пользователей. Со следующими значениями login / password:
guest / guest

```
$ register_new_matrix_user -c homeserver.yaml
```

<br>

### Запуск

```
cd /yourconfig/folder && synctl start > /yourlogsfolder/logs/matrix.log &
```

<br/>

```
$ python -m synapse.app.homeserver \
    --server-name matrix \
    --config-path homeserver.yaml
```

<br/>

```
http://localhost:8008/_matrix/static/
```
