---
layout: page
title: Подготовка виртуального окружения для запуска jupyter notebook приложений в изолированной среде в Ubuntu 20.04.2 LTS
description: Подготовка виртуального окружения для запуска jupyter notebook приложений в изолированной среде в Ubuntu 20.04.2 LTS
keywords: ds, devtools, python, virtualenv, ubuntu
permalink: /ds/devtools/python/virtualenv/
---

# Подготовка виртуального окружения для запуска jupyter notebook приложений в изолированной среде в Ubuntu 20.04.2 LTS

<br/>

```
$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 20.04.2 LTS
Release:	20.04
Codename:	focal
```

<br/>

```
$ sudo apt-get update && sudo apt-get upgrade -y
$ sudo apt-get install -y virtualenv
```

<br/>

```
$ python3 --version
Python 3.8.10
```

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
pip 21.1.3

```

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
matplotlib 3.3.4
numpy 1.19.5
pandas 1.2.1
scipy 1.6.0
sklearn 0.24.1
```

<br/>


### Проверка версии python и библиотеки tensorflow

```
from platform import python_version
print('python: ' + python_version())

import tensorflow as tf
print('tf: ' + tf.__version__)
```
