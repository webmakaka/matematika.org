---
layout: page
title: Tensorflow for Beginners
description: Tensorflow for Beginners
keywords: Видеокурс, Data Science, Tensorflow for Beginners
permalink: /videos/ds/dl/tensorflow/en/tensorflow-for-beginners/
---

# Tensorflow for Beginners

<br/>

Первые ведео доступны бесплатно. Остальные можно найти в интернете на файлопомойках. Или купить, если есть такое желание.

<br/>

<div align="center">
    <iframe width="853" height="480" src="https://www.youtube.com/embed/RJlI3trJd90" frameborder="0" allow="accelerometer; autoplay; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

<br/>

**Исходники:**  
https://bitbucket.org/matematika/tensorflow-for-beginners/

<br/><br/>

![Estimators](/img/videos/ds/dl/tf/en/tensorflow-for-beginners/estimators.png 'Estimators'){: .center-image }

<br/><br/>

<br/>

### [Запуск контейнера с TensorFlow в docker](/ds/devtools/python/docker/)

<br/>

В docker контейнере

    # cd /tf
    # git clone https://bitbucket.org/matematika/tensorflow-for-beginners/

<br/>

### Machine Learning Lifecycle Example

![Machine Learning Lifecycle Example](/img/videos/ds/dl/tf/en/tensorflow-for-beginners/machine-learning-lifecycle.png 'Machine Learning Lifecycle Example'){: .center-image }

<br/>

Jupyter > tensorflow-for-beginners > ml_lifecycle_tboard.ipynb

А файлика с демонстрацией tensorboard и нет.
Неужели придется самостоятельно что-то делать?

<br/>

### 03 Logistic Regression & NN Basics

Файлы с данными о титанике добавлены в репо.

Достаточно заменить строку на

    data = pd.read_csv("train.csv")

И закомментить все, что касается получения данных с сайта kaggle.
