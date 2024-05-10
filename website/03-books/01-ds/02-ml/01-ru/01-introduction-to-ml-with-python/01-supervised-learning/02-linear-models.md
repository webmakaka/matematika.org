---
layout: page
title: Линейные модели (Linear Models)
description: Линейные модели (Linear Models)
keywords: Машинное обучения, Линейные модели, Linear Models
permalink: /books/ds/ml/ru/scikit-learn/introduction-to-ml-with-python/supervised-learning/linear-models/
---

# Линейные модели (Linear Models)

<br/>

### Линейные модели для регрессии

- Линейная регрессия (Обычный метод наименьших квадратов) (ordinary least squares, OLS). Линейная регрессия находит параметры w и b , которые минимизируют среднеквадратическую ошибку ( mean squared error ) между спрогнозированными и фактическими ответами у в обучающем наборе. Среднеквадратичная ошибка равна сумме квадратов разностей между спрогнозированными и фактическими значениями.

Линейная регрессия проста, что является преимуществом, но в то же время у нее нет инструментов, позволяющих контролировать сложность модели.

Параметры «наклона» ( w ), также называемые весами или коэффициентами ( coefficients ), хранятся в атрибуте coef*, тогда как сдвиг ( offset ) или константа ( intercept ), обозначаемая как b , хранится в атрибуте intercept*.

Атрибут intercept* - это всегда отдельное число с плавающей точкой, тогда как атрибут coef* - это массив NumPy, в котором каждому элементу соответствует входной признак.

<br/>

### Linear models for classification

Двумя наиболее распространенными алгоритмами линейной классификации являются:

- логистическая регрессия (logistic regression), реализованная в классе linear_model.LogisticRegression
- линейный метод опорных векторов (linear support vector machines) или линейный SVM, реализованный в классе svm.LinearSVC (SVC расшифровывается как support vector classifier – классификатор опорных векторов).

Несмотря на свое название, логистическая регрессия является алгоритмом классификации, а не алгоритмом регрессии, и его не следует путать с линейной регрессией.