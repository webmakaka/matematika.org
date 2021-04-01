---
layout: page
title: TensorFlow (Deep Learning)
description: TensorFlow (Deep Learning)
keywords: TensorFlow, Deep Learning
permalink: /ds/dl/tf/
---

# TensorFlow (Deep Learning)

TensorFlow - библиотека от Google для ML/DL. Можно запускать обычным способом, в docker контейнерах, в том числе в kubernetes кластерах (инструмент для запуска кучи контейнеров на куче серверов). Есть специальный проект kubeflow, для работы tensorflow в kubernetes. Скрипты для развертывания локального kubernetes кластера в linux у меня есть. Спросить в чате, если нужно.

TensorFlow, вроде как, был сложен для программистов и была написана библиотека упрощающая работу с ним - keras. Библиотека набрала популярность и в Google решили добавить наработки из этой библиотеки во вторую версию TensorFlow, которая пока beta. В общем, если выбирать библиотеку для изучения DL, наверное лучше сразу начинать копаться со второй версии TensorFlow.

<br/>

![Tensor](/img/docs/ds/dl/tensor.png 'Tensor'){: .center-image }

<br/>

С версии 1.6 tf использует AVX инструкции. Поддерживает ваш проц такие или нет можно посмотреть в ubuntu здесь:

    $ cat /proc/cpuinfo

Чтобы использовать tf на проце, который не поддерживает AVX инструкции, нужно использовать версию 1.5.

<br/>

**Узнать версию python3**

    from platform import python_version
    print(python_version())

**Узнать версию установленной библиотеки tensorflow**

    import tensorflow as tf
    print(tf.__version__)

<br/>

### Примеры:

https://github.com/GoogleCloudPlatform/training-data-analyst/blob/master/quests/scientific/coastline.ipynb

<br/>

Пример предсказания цены на дом

https://github.com/vijaykyr/tensorflow_teaching_examples/blob/master/housing_prices/cloud-ml-housing-prices.ipynb

<br/>

Predicting Baby Weight with TensorFlow on Cloud ML Engine (GSP013)

https://github.com/GoogleCloudPlatform/training-data-analyst/blogs/babyweight/train_deploy.ipynb
