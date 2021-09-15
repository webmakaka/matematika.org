---
layout: page
title: Шакла Нишант - Машинное обучение и TensorFlow (Библиотека программиста) - 2019
permalink: /books/ds/ml/ru/tf/machine-learning-with-tensorflow/
---

# Шакла Нишант - Машинное обучение и TensorFlow (Библиотека программиста) - 2019

<br/>

**src:**  
https://github.com/BinRoot/TensorFlow-Book

- Переобучен (overfit)
- Недо­статочно обучен (underfitting)

<!--

<br/>

### Клонировать репозиторий книги

В новом окне jupyter выполнить

    !git clone https://github.com/BinRoot/TensorFlow-Book

-->

<br/>

### [Запуск контейнера с TensorFlow в docker](/ds/devtools/python/docker/)

<br/>

    $ docker exec -it 3459bce16e61 bash

<br/>

    # cd /tf
    # git clone https://github.com/BinRoot/TensorFlow-Book
    # cd TensorFlow-Book/ch02_basics/
    # mkdir logs

<br/>

Jupyter > TensorFlow-Book/ch02_basics/Concept08_TensorBoard.ipynb

<br/>

Cells > Run all

<br/>

    # tensorboard --logdir=./logs --port=6006

```
Traceback (most recent call last):
  File "/usr/local/bin/tensorboard", line 6, in <module>
    from tensorboard.main import run_main
  File "/usr/local/lib/python3.6/dist-packages/tensorboard/main.py", line 40, in <module>
    from tensorboard import default
  File "/usr/local/lib/python3.6/dist-packages/tensorboard/default.py", line 39, in <module>
    from tensorboard.plugins.beholder import beholder_plugin_loader
  File "/usr/local/lib/python3.6/dist-packages/tensorboard/plugins/beholder/__init__.py", line 22, in <module>
    from tensorboard.plugins.beholder.beholder import Beholder
  File "/usr/local/lib/python3.6/dist-packages/tensorboard/plugins/beholder/beholder.py", line 199, in <module>
    class BeholderHook(tf.estimator.SessionRunHook):
AttributeError: module 'tensorflow.python.estimator.estimator_lib' has no attribute 'SessionRunHook'
```

<br/>

Тут вроде понятно. Нужна постарше версия.

Но при вызове, получаю.

    Illegal instruction (core dumped)

<br/>

## Часть 2: Основные алгоритмы обучения

Для оценки эффективности алгоритма обучения необходимо использовать
такие понятия, как дисперсия и смещение.

- Дисперсия (variance) показывает, насколько чувствителен прогноз к ис­пользованному обучающему набору. В идеале выбор обучающего набора не
  должен играть существенной роли, то есть дисперсия должна быть низкой.
- Смещение (Ьias) показывает эффективность сделанных предположений
  относительно обучающего набора данных. Слишком большое число пред­
  положений может привести к невозможности обобщения моделью, поэтому
  предпочтительно добиваться низкого значения смещения.

<br/>

Модель должна хорошо аппроксимировать обу­чающие и проверочные данные. Если оказалось, что она плохо аппроксимирует проверочные и обучающие данные, то есть вероятность, что модель недостаточно хорошо обучена.
Если же она плохо аппроксимирует проверочные данные, но хорошо обучающие, говорят, что модель избыточно обучена (переобучена).

![Пример недостаточной и чрезмерной аппроксимации данных](/img/books/ds/ml/machine-learning-with-tensorflow/pic1.png 'Пример недостаточной и чрезмерной аппроксимации данных'){: .center-image }

<br/>

### [Регрессия (Regression)](/books/ds/ml/ru/tf/machine-learning-with-tensorflow/regression/)

### [Классификация (Classification)](/books/ds/ml/ru/tf/machine-learning-with-tensorflow/classification/)
