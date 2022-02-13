---
layout: page
title: Deep Learning. Библиотека Keras
description: Deep Learning. Библиотека Keras
keywords: Deep Learning, Keras
permalink: /ds/dl/keras/
---

# Deep Learning. Библиотека Keras

Keras (может использовать либо tensorflow, либо theano)

<br/>

### [Подготовка окружения для работы с библиотекой keras](/ds/dl/keras/setup/)

<br/>

**Наборы**

- Обучающий набор
- Контрольный набор
- Тестовый набор

<br/>

**Целевые функции (функции потерь)**

Верность, используемая в задачах классификации. Таких функций 4:

<br/>

- binary_accuracy - средняя верность по всем предсказаниям в задачах бинарной классификации.
- categorical_accuracy - средняя верность по всем предсказаниям в задачах многоклассовой классификации
- sparse_categorical_accuracy - используется когда метки разреженные
- top_k_categorical_accuracy - успехом считается случай, когда истинный целевой класс находится среди первых top_k предсказаний.

<br/>

**Ошибка, измеряющая различие между предсказанными и фактическими значениями**

- MSE (среднеквадратическая ошибка)
- RMSE (квадратный корень из среднеквадратической ошибки)
- MAE (средняя абсолютная ошибка)
- MAPE (средняя ошибка в процентах)
- MSLE (средняя квадратично-логарифмиеская ошибка)

<br/>

Кусочно-линейная функция потерь, которая обычно применяется для обучения классификаторов.

- кусочно-линейная
- квадратичная кусочно-линейная

<br/>

Классовая потеря - используется для вычисления перекрестной энтропии в задачах классификации

- Бинарная перекрестная энтропия
- Категориальная перекрестная энтропия

<br/>

keras: keras.io/objectives/

???

keras: keras.io/losses/

<br/>

**Показатели качества**

Функции показателей качества аналогичны целевым функциям. Единственное различием между ними в том, что результаты вычисления показателей не используются на этапе обучения модели.

keras: keras.io/metrics/

- верность - отношение числа правильных предсказаний к общему числу меток
- точность (accuracy) - доля правильных ответов модели
- полнота - доля обнаруженных истинных событий

<br/>

**Методы оптимизации**

- Stochastic Gradient Descent (Стохастический градиентный спуск) (SGD)
- Adaptive moment estimation (Adam) - это стохастический метод градиентного спуска, основанный на адаптивной оценке моментов первого и второго порядка.
- RMSprop - этот метод пытается решить сильно уменьшающиеся темпы обучения с использованием скользящего среднего квадратичного градиента.
- Nadam – метод, комбинирующий Adam с импульсом Нестерова.

keras: keras.io/optimizers/

<br/>

### Компиляция модели

```
model.compile(loss='categorical_crossentropy', optimizer=OPTIMIZER, metrics=['accuracy'])
```

<br/>

### Обучение модели

Для обучения откомпилированной модели служит функция fit(), принимающая следующие параметры:

- epochs - сколько раз обучающий набор предъявляется модели. На каждой итерации оптимизатор пытается подкорректировать веса, стремясь минимизировать целевую функцию.
- batch_size - сколько обучающих примеров должен увидеть оптимизатор, прежде чем он обновит веса.

```
history = model.fit(X_train, Y_train, batch_size=BATCH_SIZE, epochs=NB_EPOSH, verbose=VERBOSE, validation_split=VALIDATION_SPLIT)

```

### Проверка на тестовом наборе

```
score = model.evaluate(X_test, Y_test, verbose=VERBOSE)
print("Test score:", score[0])
print("Test accuracy:", score[1])
```

<br/>

### Регуляризация

Цель регуляризации - предотвратить переобучение.

- Регуляризация по норме L1 (lasso): сложность модели выражается в виде суммы модулей весов.
- Регуляризация по норме L2 (гребневая): сложность модели выражается в виде суммы квадратов весов.
- Эластичная сеть: для выражения сложности модели применяется комбинация двух предыдущих способов.

keras: keras.io/regularizers/

<br/>

### Предсказание выхода

```
predictions = model.predict(X)
```

Для заданного входного вектора можно вычислить несколько значений:

- model.evaluate() - вычислияет потерю.
- model.predict_classes() - вычислияет категориальные выходы.
- model.predict_proba() - вычисляет вероятность классов.

<br/>

### Базовые шаги при создании модели нейронной сети

<br/>

![Deep Learning](/img/docs/ds/dl/dl-02.png 'Deep Learning'){: .center-image }

<br/>

![Deep Learning](/img/docs/ds/dl/dl-03.png 'Deep Learning'){: .center-image }

<br/>

### Примеры

**Convolutional Neural Networks for Image Classification**
https://github.com/matematika-org/Python-for-Computer-Vision-with-OpenCV-and-Deep-Learning/blob/master/06-Deep-Learning-Computer-Vision/01-Keras-CNN-MNIST.ipynb

**CIFAR-10 Multiple Classes**
https://github.com/matematika-org/Python-for-Computer-Vision-with-OpenCV-and-Deep-Learning/blob/master/06-Deep-Learning-Computer-Vision/02-Keras-CNN-CIFAR-10.ipynb

**Классификация кошек и собак:**
https://github.com/matematika-org/Python-for-Computer-Vision-with-OpenCV-and-Deep-Learning/blob/master/06-Deep-Learning-Computer-Vision/03-Deep-Learning-Custom-Images.ipynb

**YOLO v3 Object Detection (На картинке находит объекты, обводит их в квадрат и присваивает метки)**
https://github.com/matematika-org/Python-for-Computer-Vision-with-OpenCV-and-Deep-Learning/blob/master/06-Deep-Learning-Computer-Vision/06-YOLOv3/06-YOLO-Object-Detection.ipynb

**Считает количество пальцев, которые показывает человек на камеру**
https://github.com/matematika-org/Python-for-Computer-Vision-with-OpenCV-and-Deep-Learning/tree/master/07-Capstone-Project
