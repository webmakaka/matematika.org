---
layout: page
title: Книга Deep Learning with PyTorch
description: Книга Deep Learning with PyTorch
keywords: Книга, Deep Learning with PyTorch, DeepLearning, PyTorch, английский язык, бесплатно
permalink: /books/ds/dl/pytorch/deep-learning-with-pytorch/en/
---

# [Бесплатная книга] Deep Learning with PyTorch [ENG]

Можно рассматривать как официальное руководство.

https://pytorch.org/deep-learning-with-pytorch

Добавления и исправления ошибок приветствуется. Обсуждение в телеграм чате.

<br/>

**Исходники:**
https://github.com/deep-learning-with-pytorch/dlwpt-code

<!--

https://colab.research.google.com/notebooks/intro.ipynb

-->

<br/>

### Подготовка окружения:

**Ubuntu 20.04.1 LTS**

<br/>

Книга писалась и тестилась под версию 3.6.8

<br/>

    $ sudo apt-get update && sudo apt-get upgrade -y

    $ sudo apt-get install -y \
        python3-venv \
        python3.6-venv \
        python3-dev \
        python3.6-dev \
        cmake \
        g++

<br/>

Вот это тоже поставил, но скорее всего не нужно. (Ставил все подряд, не знал почему не компилится). Если действительно не нужны, дайте знать!

    $ sudo apt  install -y libboost-tools-dev  magics++ libboost-thread1.67-dev

    $ sudo apt-get install libboost-all-dev

<br/>

    $ python3 --version
    Python 3.8.2

<br/>

    $ mkdir -p  ~/projects/dev/dl/pytorch && cd ~/projects/dev/dl/pytorch
    $ mkdir deep-learning-with-pytorch && cd deep-learning-with-pytorch

    $ python3.6 -m venv .venv
    $ source ./.venv/bin/activate
    $ python --version
    Python 3.6.12

    $ git clone https://github.com/deep-learning-with-pytorch/dlwpt-code

    $ pip install --upgrade pip

```
$ {
    pip install --upgrade torchvision
    pip install --upgrade wheel
}
```

    $ pip install -r dlwpt-code/requirements.txt

    $ cd dlwpt-code

    $ jupyter notebook --ip 0.0.0.0 --port 8888
