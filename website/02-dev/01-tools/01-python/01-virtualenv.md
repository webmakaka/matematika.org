---
layout: page
title: Подготовка виртуального окружения для запуска jupyter notebook приложений в изолированной среде в Ubuntu 20.04 LTS
description: Подготовка виртуального окружения для запуска jupyter notebook приложений в изолированной среде в Ubuntu 20.04 LTS
keywords: dev, tools, python, virtualenv, ubuntu
permalink: /dev/tools/python/virtualenv/
---

# Подготовка виртуального окружения для запуска jupyter notebook приложений в изолированной среде в Ubuntu 20.04 LTS

<br/>

```
$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 20.04.3 LTS
Release:	20.04
Codename:	focal
```

<br/>

<!--

sudo apt install -y python3-venv

-->

```
$ sudo apt update && sudo apt upgrade -y
$ sudo apt install -y python3-dev python3-pip
$ sudo apt install -y virtualenv

// Вроде лишнее, но иногда требуется
$ sudo apt install -y python3.8-venv
```

<br/>

```
$ python3 --version
Python 3.8.10
```

<br/>

```
$ ls -ls /usr/bin/python*
```

<!--

// $ sudo update-alternatives --install /usr/bin/python python \
/usr/bin/python3.5 3

-->

<br/>

### Запуск jupyter notebook и стандартных библиотек

```python
$ mkdir -p ~/projects/dev/ml
$ cd ~/projects/dev/ml

$ export PROJECT_NAME=<MY_NEW_PROJECT_NAME>

$ mkdir ${PROJECT_NAME} && cd ${PROJECT_NAME}

$ virtualenv --system-site-packages -p python ${PROJECT_NAME}-env

$ source ${PROJECT_NAME}-env/bin/activate
```

<br/>

```python
$ python --version
Python 3.8.10

$ pip install --upgrade pip

$ pip --version
pip 22.0.3
```

<br/>

### Если нужна специфическая версия (Какая-то старая)

Найду поэлегантней решение, обязательно напишу.

<br/>

Инсталляция brew / homebrew [в ubuntu](//sysadm.ru/desktop/linux/ubuntu/brew/)

<br/>

```
$ sudo apt-get install build-essential zlib1g-dev libffi-dev libssl-dev libbz2-dev libreadline-dev libsqlite3-dev liblzma-dev

$ brew install pyenv
$ pyenv install --list
$ pyenv install 3.6.9
```

<br/>

```
$ /home/marley/.pyenv/versions/3.6.9/bin/python --version
Python 3.6.9
```

<br/>

```
$ virtualenv --system-site-packages --python=/home/marley/.pyenv/versions/3.6.9/bin/python ${PROJECT_NAME}-env --no-pip

$ source ${PROJECT_NAME}-env/bin/activate
```

<br/>

```
$ python --version
Python 3.6.9
```

<br/>

Как-то хреново pip работал.

<br/>

### Установка нужных пакетов

```python
$ {
    pip install --upgrade jupyter
    pip install --upgrade matplotlib
    pip install --upgrade numpy
    pip install --upgrade pandas
    pip install --upgrade scipy
    pip install --upgrade scikit-learn
    pip install --upgrade seaborn
}
```

<br/>

```python
$ jupyter notebook --ip 0.0.0.0 --port 8888
```

<br/>

```python
import matplotlib
import numpy
import pandas
import scipy
import sklearn


print('matplotlib',  matplotlib.__version__)
print('numpy', numpy.__version__)
print('pandas', pandas.__version__)
print('scipy', scipy.__version__)
print('sklearn', sklearn.__version__)

```

<br/>

```
matplotlib 3.5.1
numpy 1.22.2
pandas 1.4.1
scipy 1.8.0
sklearn 1.0.2
```

<br/>

### Проверка версии python и библиотеки tensorflow

<br/>

```
from platform import python_version
print('python: ' + python_version())

import tensorflow as tf
print('tf: ' + tf.__version__)
```

<br/>

```
python: 3.8.10
tf: 2.3.4
```

<br/>

**Пока не пофиксил:**

```
2022-02-13 18:58:32.873638: W tensorflow/stream_executor/platform/default/dso_loader.cc:59] Could not load dynamic library 'libcudart.so.10.1'; dlerror: libcudart.so.10.1: cannot open shared object file: No such file or directory
2022-02-13 18:58:32.873678: I tensorflow/stream_executor/cuda/cudart_stub.cc:29] Ignore above cudart dlerror if you do not have a GPU set up on your machine.
```

<br/>

### Install NVIDIA CUDA on Ubuntu

https://docs.vmware.com/en/VMware-vSphere-Bitfusion/3.0/Example-Guide/GUID-ABB4A0B1-F26E-422E-85C5-BA9F2454363A.html

Не установилось.

Broken чего-то

<br/>

### NVIDIA Data Science Stack

https://github.com/NVIDIA/data-science-stack

<br/>

```
$ npm install -g node-gyp@3.6.2

$ node-gyp --version
v3.6.2


$ ./data-science-stack setup-system
```

<br/>

Не установилось.

<br/>

```
Depends: node-gyp (>= 3.6.2~) but it is not going to be installed
```

<!--
<br/>

### Use nvcc to check CUDA version on Ubuntu

```
$ sudo apt install nvidia-cuda-toolkit
$ nvcc --version
``` -->