---
layout: page
title: Anaconda
description: Работа с дистрибутивом anaconda для подготовки окружения на python
keywords: python, anaconda
permalink: /ds/devtools/python/anaconda/
---

# Anaconda

Anaconda - дистрибутив Python, предназначенный для крупномасштабной обработки данных, прогнозной аналитики и научных вычислений.

Anaconda уже включает NumPy, SciPy, Matplotlib, Pandas, IPython, Jupyter Notebook и Scikit-learn. Есть версии для Mac OS, Windows и Linux.

https://www.anaconda.com/distribution/

<br/>

Хотя у меня и установлена Anaconda, мне пока требуется только jupyter notebook. Для доступа к нему, я запускаю <a href="/ds/devtools/python/docker/">docker контейнер</a>, подготовленный для работы с библиотекой tensorflow. И уже работаю с ним.

<br/>

**Создать окружение в anaconda в командной строке:**

<!--

    $ conda update conda
    $ conda update anaconda

-->

    $ conda env list

    $ conda create -y --name py3-TF2.0 python=3
    $ conda activate py3-TF2.0


    $ pip install --upgrade pip

    $ pip install --upgrade tensorflow==2.0.0-beta1
    $ pip install ipykernel


    $ conda install -y nb_conda_kernels

    $ conda list | grep tensorflow
    tensorflow                2.0.0b1                  pypi_0    pypi


    $ anaconda-navigator

<!--

$ conda env remove --name py3.5-TF2.0

conda install -c anaconda jupyter

-->

<br/>

## Установка окружения в Anaconda для работы с Keras

У меня были проблемы с установкой. Но разбираться не стал. Думаю проблемы с зависимостями.

### Устанавливаем Theano

    $ conda update -n base -c defaults conda

    $ conda install -c anaconda theano

    // Возможны ошибки и необходим downgrade numpy
    $ conda install numpy-1.14

<br/>

### Устанавливаем TensorFlow

    $ conda install -c conda-forge tensorflow

<br/>

### Устанавливаем Keras

    $ conda install -c conda-forge keras
    $ conda install h5py

<br/>

<!--

$ sudo apt-get update && sudo apt-get upgrade
$ sudo apt-get install python3.7
$ python --version
Python 3.7.3




pip3 install -U virtualenv
virtualenv --system-site-packages -p python3 tf_2
source tf_2/bin/activate

pip install --upgrade pip
pip install --upgrade tensorflow==2.0.0-beta1

(tf_2) $ python -c "import tensorflow as tf; x = [[2.]]; print('tensorflow version', tf.__version__); print('hello, {}'.format(tf.matmul(x, x)))"


---------------

from platform import python_version
print(python_version())

import tensorflow as tf
print(tf.__version__)




pip install jupyterlab

jupyter notebook

-->

<br/>

### Деактивировать окружение anaconda и отключить ее автозапуск

```
$ conda config --set auto_activate_base false
$ conda deactivate
```

<br/>

### Install Anaconda Windows

https://www.youtube.com/watch?v=a7Ylbn1ikF0&t=2s

<br/>

### Install Anaconda MacOS

https://www.youtube.com/watch?v=2JeoNlCcLOM
