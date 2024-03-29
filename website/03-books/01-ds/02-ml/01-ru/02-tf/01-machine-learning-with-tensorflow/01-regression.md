---
layout: page
title: Machine learning with tensorflow - Регрессия (Regression)
description: Machine learning with tensorflow - Регрессия (Regression)
keywords: books, ds, ml, machine learning with tensorflow
permalink: /books/ds/ml/ru/tf/machine-learning-with-tensorflow/regression/
---

# Регрессия (Regression)

<br/>

## Линейная регрессия (Linear regression)

https://github.com/BinRoot/TensorFlow-Book/blob/master/ch03_regression/Concept01_linear_regression.ipynb

Теперь, когда есть некоторые точки данных, можно попытаться аппрокси­
мировать их линией.

Для библиотеки TensorFlow необ­ходимо предоставить оценку каждого испробованного варианта параметра. Эти оценки обычно называют функцией стоимости (cost function).

Она ис­ пользуется для вычисления ошибки значения, или «стоимости», заданной целевой функции. Чем выше стоимость, тем хуже окажется выбор параметра модели. Например, если линия наилучшего приближения у = 2х, то выбор параметра 2,01 будет давать низкую стоимость, а выбор параметра
-1 даст высокую стоимость.

После того как задача была сформулирована как задача минимизации стои­мости, TensorFlow принимается за обновление значений параметра, чтобы найти наилучшее из возможных зна­чений. Каждый шаг циклического перебора данных для обновления параметра носит название эпохи (epoch).

<br/>

## Поnиномиаnьная модель (Polynomial regression)

https://github.com/BinRoot/TensorFlow-Book/blob/master/ch03_regression/Concept02_poly_regression.ipynb

Если точки данных формируют сглаженную кривую, а не прямую линию, модель регрессии необходимо изменить с прямой линии на какое-то иное представление. Один из таких методов использует полиномиальную модель.

<br/>

## Регуляризация (Regularization)

https://github.com/BinRoot/TensorFlow-Book/blob/master/ch03_regression/Concept03_regularization.ipynb

В реальном мире исходные данные редко образуют сглаженную кривую, по­хожую на полиномиальную.
