---
layout: page
title: Запуск контейнера с TensorFlow в docker
description: Запуск контейнера с TensorFlow в docker
keywords: python, docker, tensorflow
permalink: /ds/devtools/python/docker/
---

# Запуск контейнера с TensorFlow в docker

https://www.tensorflow.org/install/docker

<br/>

### [Инсталляция docker в ubuntu linux](https://sysadm.ru/devops/containers/docker/install/ubuntu/)

<br/>

    // Запуск контейнера для работы
    $ docker run -it -p 8888:8888 -p 6006:6006 tensorflow/tensorflow:latest-py3-jupyter

<!--

docker run -p 8888:8888 jupyter/all-spark-notebook

-->

<br/>

**Назначение портов:**

    8888 - jupyter
    6006 - tensorboard

<br/>

Строку подключения браузером посмотреть в консоли:

http://localhost:8888/?token=7aec80a332055a8812f1ee22605cb007c1e4fe7b1fa00b29

<br/>

### Установка / Обновление пакетов

Открываю еще одну терминальную сессию:

    $ docker ps

    $ docker exec -it 3459bce16e61 bash

<br/>

Удалить все ненужное, если мешает:

    # cd /tf/
    # rm -rf *

<br/>

    # apt install -y git vim wget
    # pip install --upgrade pip

<br/>

С версии 1.6 tf использует AVX инструкции.

<br/>

Если в вашем проце таких инструкций нет, то будет ошибка "The kernel appears to have died. It will restart automatically".

Выход, можно установить версию tensorflow==1.5. Я в свою очередь нашел cpu, где эти инструкции имеются.

    // Установить версию tensorflow==1.5
    # pip3 install tensorflow==1.5

<!--    # pip3 install --upgrade tensorflow

-->

<br/>

### Библиотеки для Scikit-learn

```
# {
    pip3 install --upgrade sklearn
    pip3 install --upgrade numpy
    pip3 install --upgrade pandas
    pip3 install --upgrade seaborn
    pip3 install --upgrade matplotlib
}
```

<br/>

### Библиотеки для TensorFlow / Keras

```
# {
    pip3 install --upgrade theano
    pip3 install --upgrade keras
    pip3 install --upgrade h5py
    pip3 install --upgrade image
    pip3 install --upgrade tensorflow==2.0.0-beta1
}

```

<br/>

### Библиотеки для PyTorch

```
# {
    pip3 install --upgrade torch
    pip3 install --upgrade torchvision
}

```

<br/>

```
// Для работы с excel
# {
    pip3 install --upgrade xlrd
}

```

<br/>

    // check tensorflow version
    # pip3 show tensorflow

```

<br/>

Была необходимость добавлять requests для скачивания файлов из интернет:

    # pip3 install --upgrade requests
```
