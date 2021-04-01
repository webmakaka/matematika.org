---
layout: page
title: Наивный байесовский классификатор (Naive Bayes Classifiers)
description: Наивный байесовский классификатор (Naive Bayes Classifiers)
keywords: Машинное обучения, Наивный байесовский классификатор, Naive Bayes Classifiers
permalink: /books/ds/ml/ru/scikit-learn/introduction-to-ml-with-python/supervised-learning/naive-bayes-classifiers/
---

# Наивный байесовский классификатор (Naive Bayes Classifiers)

В scikit-learn реализованы три вида наивных байесовских классификаторов:

- GaussianNB
- BernoulliNB
- MultinomialNB.

GaussianNB - можно применить к любым непрерывным данным, в то время как BernoulliNB принимает бинарные данные, MultinomialNB принимает счетные или дискретные данные (то есть каждый признак представляет собой подсчет целочисленных значений какой-то характеристики, например, речь может идти о частоте встречаемости слова в предложении). BernoulliNB и MultinomialNB в основном используются для классификации текстовых данных.
