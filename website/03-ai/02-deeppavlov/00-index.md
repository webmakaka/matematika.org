---
layout: page
title: DeepPavlov
description: DeepPavlov
keywords: ai, nlp, nlu, natural language understanding, DeepPavlov
permalink: /ai/nlp/nlu/deeppavlov/
---

# DeepPavlov

http://docs.deeppavlov.ai/en/master/intro/installation.html

<br/>

http://docs.deeppavlov.ai/en/master/features/skills/faq.html

<br/>

```
// Возникшие ошибки:
libraries mkl_rt not found
libraries blis not found
```

<br/>

```
// Не уверен, что поможет.
// Можно попробовать python версии 3.6
$ sudo apt install -y libmkl-dev libblis-dev
$ pip install Cython
```

<br/>

Пробовал в контейнерах.

<br/>

```
FROM python:3.6-slim OK!
FROM python:3.8-slim FAIL!
FROM deeppavlov/base-gpu OK!
```

<br/>

### Пробуем

[Установка виртуального окружения](/dev//aiops.ru/tools/python/virtualenv/ubuntu/)

<br/>

```
$ mkdir -p ~/${HOME}/projects/dev/ai
$ cd ~/${HOME}/projects/dev/ai
$ python3 -m venv ./venv
$ source ./venv/bin/activate
```

<br/>

```
$ pip install -U pip
```

<br/>

```
$ pip install deeppavlov
```

<!--

```
$ sudo apt install -y libblas-dev liblapack-dev libatlas-base-dev gfortran
```


sudo apt install -y gfortran libopenblas-dev liblapack-dev
>

https://forums.developer.nvidia.com/t/pip-install-something-but-error-with-could-not-find-a-satisfies-version/66300/3

-->

<br/>

```
$ {
    python -m deeppavlov install fasttext_avg_autofaq
    python -m deeppavlov install fasttext_tfidf_autofaq
    python -m deeppavlov install tfidf_autofaq
    python -m deeppavlov install tfidf_logreg_autofaq
    python -m deeppavlov install tfidf_logreg_en_faq
}
```

<br/>

**Training**

```
$ python -m deeppavlov train tfidf_autofaq
```

**Interacting**

Запускается и ждет ввода в консоли.

```
$ python -m deeppavlov interact tfidf_autofaq -d
```

<br/>

### ХЗ

```
// pre-trained cosine similarity classifier for classifying input question (vectorized by tfidf)
$ wget http://files.deeppavlov.ai/faq/school/tfidf_cos_sim_classifier.pkl
```

<br/>

```
$ python -m deeppavlov interact tfidf_cos_sim_classifier.pkl -d
```

<br/>

Далее нужно настроить в либе конфиг по образцу.

<br/>

И скачать подготовленную модель.

http://files.deeppavlov.ai/faq/school/tfidf_cos_sim_classifier.pkl
