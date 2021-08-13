---
layout: page
title: TensorFlow Developer Certificate in 2021
description: Изучаем видеокурс TensorFlow Developer Certificate in 2021 от Zero to Mastery
keywords: Видеокурс, TensorFlow, английский язык, TensorFlow Developer Certificate in 2021, Zero to Mastery
permalink: /videos/ds/libs/tensorflow/en/tensorflow-developer-certificate-in-2021/
---

# [Udemy] [Andrei Neagoie, Daniel Bourke] TensorFlow Developer Certificate in 2021: Zero to Mastery [ENG, 2021]

<br/>

### [Daniel Bourke] Learn TensorFlow and Deep Learning fundamentals with Python (code-first introduction) [ENG, 2021]

<br/>

Первые 14 (из 37) часов можно бесплатно посмотреть в YouTube. Далее можно или купить, или найти.

<br/>

<div align="center">
    <iframe width="853" height="480" src="https://www.youtube.com/embed/videoseries?list=PL6vjgQ2-qJFfU2vF6-lG9DlSa4tROkzt9" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

<br/>

[Course Link](https://www.udemy.com/course/tensorflow-developer-certificate-machine-learning-zero-to-mastery/)

<br/>

**GitHub:**  
https://github.com/mrdbourke/tensorflow-deep-learning

<br/>

В курсе ссылаются и рекомендуют книгу,
[Орельен Жерон, Прикладное машинное обучение с помощью Scikit-Learn, Keras и TensorFlow](/books/ds/ml/ru/hands-on-machine-learning-with-scikit-learn-and-tensorflow/)

<br/>

## [00. Getting started with TensorFlow: A guide to the fundamentals](https://colab.research.google.com/github/mrdbourke/tensorflow-deep-learning/blob/main/00_tensorflow_fundamentals.ipynb)

<br/>

## [01. Neural Network Regression with TensorFlow](https://colab.research.google.com/github/mrdbourke/tensorflow-deep-learning/blob/main/01_neural_network_regression_in_tensorflow.ipynb)

<br/>

| **Hyperparameter**       | **Typical value**                                                                                                                                                                                        |
| ------------------------ | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Input layer shape        | Same shape as number of features (e.g. 3 for # bedrooms, # bathrooms, # car spaces in housing price prediction)                                                                                          |
| Hidden layer(s)          | Problem specific, minimum = 1, maximum = unlimited                                                                                                                                                       |
| Neurons per hidden layer | Problem specific, generally 10 to 100                                                                                                                                                                    |
| Output layer shape       | Same shape as desired prediction shape (e.g. 1 for house price)                                                                                                                                          |
| Hidden activation        | Usually [ReLU](https://www.kaggle.com/dansbecker/rectified-linear-units-relu-in-deep-learning) (rectified linear unit)                                                                                   |
| Output activation        | None, ReLU, logistic/tanh                                                                                                                                                                                |
| Loss function            | [MSE](https://en.wikipedia.org/wiki/Mean_squared_error) (mean square error) or [MAE](https://en.wikipedia.org/wiki/Mean_absolute_error) (mean absolute error)/Huber (combination of MAE/MSE) if outliers |
| Optimizer                | [SGD](https://www.tensorflow.org/api_docs/python/tf/keras/optimizers/SGD) (stochastic gradient descent), [Adam](https://www.tensorflow.org/api_docs/python/tf/keras/optimizers/Adam)                     |

<br/>

### Steps in modelling with TensorFlow

In TensorFlow, there are typically 3 fundamental steps to creating and training a model.

1. **Creating a model** - piece together the layers of a neural network yourself (using the [Functional](https://www.tensorflow.org/guide/keras/functional) or [Sequential API](https://www.tensorflow.org/api_docs/python/tf/keras/Sequential)) or import a previously built model (known as transfer learning).
2. **Compiling a model** - defining how a models performance should be measured (loss/metrics) as well as defining how it should improve (optimizer).
3. **Fitting a model** - letting the model try to find patterns in the data (how does `X` get to `y`).

<br/>

```python
# Set random seed
tf.random.set_seed(42)

# Create a model using the Sequential API
model = tf.keras.Sequential([
  tf.keras.layers.Dense(50, activtion=None),
  tf.keras.layers.Dense(1)
])

# Compile the model
model.compile(loss=tf.keras.losses.mae, # mae is short for mean absolute error
              optimizer=tf.keras.optimizers.Adam(learning_rate=0.01), # SGD is short for stochastic gradient descent
              metrics=["mae"])

# Fit the model
model.fit(X, y, epochs=100)
```

<br/>

```
# Make a prediction with the model
model.predict([17.0])
```

<br/>

### Improving a model

To improve our model, we alter almost every part of the 3 steps we went through before.

1. **Creating a model** - here you might want to add more layers, increase the number of hidden units (also called neurons) within each layer, change the activation functions of each layer.
2. **Compiling a model** - you might want to choose optimization function or perhaps change the **learning rate** of the optimization function.
3. **Fitting a model** - perhaps you could fit a model for more **epochs** (leave it training for longer) or on more data (give the model more examples to learn from).

<br/>

### Evaluating a model

<br/>

### Running experiments to improve a model

<br/>

### Comparing results

<br/>

### Saving a model

<br/>

### Пробуем запустить пример из всего выше изученного на примере какой-то задачи страхования

<br/>

## [02. Neural Network Classification with TensorFlow](https://colab.research.google.com/github/mrdbourke/tensorflow-deep-learning/blob/main/02_neural_network_classification_in_tensorflow.ipynb)

<br/>

**Types of classification**

- Binary classification
- Milticlass classification (fashion_mnist)
- Multilabel classification

<br/>

## [03. Convolutional Neural Networks and Computer Vision with TensorFlow](https://colab.research.google.com/github/mrdbourke/tensorflow-deep-learning/blob/main/03_convolutional_neural_networks_in_tensorflow.ipynb)

<br/>

Традиционные Кошечки / собачки - заменены на pizza / steak

<br/>

https://poloclub.github.io/cnn-explainer/
