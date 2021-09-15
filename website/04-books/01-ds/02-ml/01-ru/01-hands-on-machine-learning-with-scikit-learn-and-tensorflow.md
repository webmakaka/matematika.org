---
layout: page
title: Орельен Жерон - Прикладное машинное обучение с помощью Scikit-Learn, Keras и TensorFlow  2-e издание [RUS, 2020]
description: Орельен Жерон - Прикладное машинное обучение с помощью Scikit-Learn, Keras и TensorFlow 2-e издание [RUS, 2020]
keywords: Орельен Жерон, Прикладное машинное обучение с помощью Scikit-Learn, Keras и TensorFlow
permalink: /books/ds/ml/ru/hands-on-machine-learning-with-scikit-learn-and-tensorflow/
---

# [Орельен Жерон] Прикладное машинное обучение с помощью Scikit-Learn, Keras и TensorFlow 2-e издание [RUS, 2020]

<br/>

**Коды**  
https://github.com/ageron/handson-ml2

<br/>

**Все иллюстрации к книге в цветном варианте:**  
http://go.dialektika.com/mlearning

<br/>

Самыми распространенными задача­ми обучения с учителем являются регрессия (прогнозирование значений) и классификация (прогнозирование классов).

<br/>

**Наиболее важные алгоритмы обучения с учителем:**

<br/>

- k ближайших соседей (k-Nearest Neighbors)
- линейная регрессия (Linear Regression)
- логистическая регрессия (Logistic Regression)
- метод опорных векторов (Support Vector Machine - SVM)
- деревья принятия решений (Decision Tree) и случайные леса (Random Forest)
- нейронные сети (neural network)

<br/>

**Наиболее важные алгоритмы обучения без учите­ля:**

• Кластеризация:

- K-Means (К-средние)
- DBSCAN
- иерархический кластерный анализ (Hierarchical Cluster Analysis - HCA)

• Обнаружение аномалий и обнаружение новизны:

- одноклассовый SVM
- изолирующий лес

• Визуализация и понижение размерности:

- анализ главных компонентов (Principal Component Analysis - РСА)
- ядерный анализ главных компонентов (Kernel РСА)
- локальное линейное вложение (Locally-Linear Embedding - LLE)
- стохастическое вложение соседей с t-распределением (t-distributed Stochastic Neighbor Embedding - t-SNE)

• Обучение ассоциативным правилам (association rule learning):

- Apriori
- Eclat

<br/>

## Глава 2: Полный проект машинного обучения

<br/>

В главе исследуется задача регрессии, прогнозирующая стоимость домов с использованием раз­нообразных алгоритмов, таких как линейная регрессия, деревья принятия решений и случайные леса.

<br/>

[colab](https://colab.research.google.com/github/ageron/handson-ml2/blob/master/02_end_to_end_machine_learning_project.ipynb)

Критерий качества: RMSE

<br/>

Notebook'и можно запускать локально или в colab.  
Локально больше часа выполнялись вычисления и так и не завершил их.

<br/>

**В ubuntu:**

Подготовил окружение как <a href="/ds/devtools/python/virtualenv/">здесь</a>

<br/>

```
$ git clone https://github.com/ageron/handson-ml2
```

<br/>

```python
$ jupyter notebook --ip 0.0.0.0 --port 8888
```

<br/>

## Глава 3: Кnассификация

<br/>

[colab](https://colab.research.google.com/github/ageron/handson-ml2/blob/master/03_classification.ipynb)

Применяется набор данных MNIST (Mixed National Institute of Standards and Technology), который содер­
жит 70 ООО небольших изображений цифр, написанных от руки учащимися средних школ и служащими Бюро переписи населения США. Каждое изоб­ражение помечено цифрой, которую оно представляет.

Каждое изображение имеет размер 28х28 пикселей, а каждый признак просто представляет интенсивность одного пикселя, от О (белый) до 255 (черный).

Набор данных MNIST уже разделен на обучающий набор(первые 60 ООО изображений) и испытательный набор (последние 10 ООО изображений). Обучающий набор уже перетасован.

Confusion Matrix

Анализ ошибок

<br/>

## Глава 4: Обучение модеnей

В главе изучаем

- Linear Regression
- Polynomial Regression
- Logistic Regression
- Softmax Regression (Многомерная логистическая регрессия)

<br/>

[colab](https://colab.research.google.com/github/ageron/handson-ml2/blob/master/04_training_linear_models.ipynb)
