---
layout: page
title: [Орельен Жерон] Прикладное машинное обучение с помощью Scikit-Learn, Keras и TensorFlow  2-e издание [RUS, 2020]
description: [Орельен Жерон] Прикладное машинное обучение с помощью Scikit-Learn, Keras и TensorFlow 2-e издание [RUS, 2020]
keywords: Орельен Жерон, Прикладное машинное обучение с помощью Scikit-Learn, Keras и TensorFlow
permalink: /books/ds/ml/ru/hands-on-machine-learning-with-scikit-learn-and-tensorflow/
---

# [Орельен Жерон] Прикладное машинное обучение с помощью Scikit-Learn, Keras и TensorFlow 2-e издание [RUS, 2020]

<br/>

**Коды**  
https://githuЬ.com/ageron/handson-ml2

<br/>

**Все иллюстрации к книге в цветном варианте:**  
http://go.dialektika.com/mlearning

<br/>

<br/>

**Наиболее важные алгоритмы обучения с учителем:**

• k ближайших соседей (k-Nearest Neighbors)
• линейная регрессия (Linear Regression)
• логистическая регрессия (Logistic Regression)
• метод опорных векторов (Support Vector Machine - SVM)
• деревья принятия решений (Decision Tree) и случайные леса (Random Forest)
• нейронные сети (neural network)

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

````



<br/>

https://github.com/ageron/handson-ml

<br/>

**В ubuntu:**

Подготовил окружение как <a href="/ds/devtools/python/virtualenv/">здесь</a>

<br/>

**Импортируем данные**

// Инфа
https://github.com/ageron/handson-ml/blob/master/02_end_to_end_machine_learning_project.ipynb

<br/>

```python
import os
import tarfile
from six.moves import urllib

DOWNLOAD_ROOT = "https://raw.githubusercontent.com/ageron/handson-ml/master/"
HOUSING_PATH = os.path.join("datasets", "housing")
HOUSING_URL = DOWNLOAD_ROOT + "datasets/housing/housing.tgz"

def fetch_housing_data(housing_url=HOUSING_URL, housing_path=HOUSING_PATH):
    os.makedirs(housing_path, exist_ok=True)
    tgz_path = os.path.join(housing_path, "housing.tgz")
    urllib.request.urlretrieve(housing_url, tgz_path)
    housing_tgz = tarfile.open(tgz_path)
    housing_tgz.extractall(path=housing_path)
    housing_tgz.close()
````

<br/>

```
fetch_housing_data()
```

<br/>

```python
import pandas as pd

def load_housing_data(housing_path=HOUSING_PATH):
    csv_path = os.path.join(housing_path, "housing.csv")
    return pd.read_csv(csv_path)
```

```python
housing = load_housing_data()
housing.head()
```
