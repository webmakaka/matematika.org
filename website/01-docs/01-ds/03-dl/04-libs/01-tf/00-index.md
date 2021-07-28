---
layout: page
title: TensorFlow
description: TensorFlow
keywords: Deep Learning, TensorFlow
permalink: /ds/dl/tf/
---

# TensorFlow (Deep Learning)

TensorFlow - библиотека от Google для ML/DL.

TensorFlow, вроде как, был сложен для программистов и была написана библиотека упрощающая работу с ним - keras. Библиотека набрала популярность и в Google решили добавить наработки из этой библиотеки во вторую версию TensorFlow. В общем, если выбирать библиотеку для изучения DL, наверное лучше сразу начинать копаться со второй версии TensorFlow.

<br/>

![Tensor](/img/docs/ds/dl/tensor.png 'Tensor'){: .center-image }

<br/>

**Визуальный пример от разработчиков, как работает tensorflow**  
https://playground.tensorflow.org/

<br/>

С версии 1.6 tf использует AVX инструкции. Поддерживает ваш проц такие или нет можно посмотреть в ubuntu здесь:

    $ cat /proc/cpuinfo

Чтобы использовать tf на процессоре, который не поддерживает AVX инструкции, нужно использовать версию 1.5.

<br/>

### Примеры:

https://github.com/GoogleCloudPlatform/training-data-analyst/blob/master/quests/scientific/coastline.ipynb

<br/>

**Пример предсказания цены на дом**  
https://github.com/vijaykyr/tensorflow_teaching_examples/blob/master/housing_prices/cloud-ml-housing-prices.ipynb

<br/>

**Predicting Baby Weight with TensorFlow on Cloud ML Engine (GSP013)**  
https://github.com/GoogleCloudPlatform/training-data-analyst/blogs/babyweight/train_deploy.ipynb
