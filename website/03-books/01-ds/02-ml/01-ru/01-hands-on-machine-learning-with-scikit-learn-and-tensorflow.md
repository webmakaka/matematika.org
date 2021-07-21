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

**Наиболее важные алгоритмы обучения с учителем:**

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

Критерий качества: RMSE

<br/>

Notebook'и можно запускать локально или в colab.

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

https://colab.research.google.com/github/ageron/handson-ml2/blob/master/02_end_to_end_machine_learning_project.ipynb
