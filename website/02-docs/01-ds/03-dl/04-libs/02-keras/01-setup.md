---
layout: page
title: Подготовка окружения для работы с библиотекой keras
description: Подготовка окружения для работы с библиотекой keras
keywords: Подготовка окружения для работы с библиотекой keras
permalink: /ds/dl/keras/setup/
---

# Подготовка окружения для работы с библиотекой keras

## Запуск в Docker контейнере

Запускать буду в <a href="/ds/devtools/python/docker/">docker</a> под ubuntu linux в специально подготовленном контейнере для tensorflow. Да не суть. Главное, что jupyter notebook запускается одной командой.

<br/>

Библиотеки для работы со sklearn, pandas, numpy etc. тоже лучше сразу поставить!

<br/>

```
import theano
from theano import tensor

a = tensor.dscalar()
b = tensor.dscalar()

c = a + b
f = theano.function([a,b], c)
result = f(1.5, 2.5)
print(result)

```

    // Если ошибка
    # pip3 install numpy==1.14

<br/>

```
import tensorflow as tf

a = tf.placeholder(tf.float32)
b = tf.placeholder(tf.float32)

add = tf.add(a,b)
sess = tf.Session()

binding = {a: 1.5, b: 2.5}
result = sess.run(add, feed_dict=binding)
print(result)

```

    // Если ошибка
    # pip3 install tensorflow==1.5

```
from keras import backend
print(backend._BACKEND)

```

<br/>

    # cd ~/.keras/

    # cat keras.json
    {
        "floatx": "float32",
        "epsilon": 1e-07,
        "backend": "tensorflow",
        "image_data_format": "channels_last"
    }

<br/>

    # python -c "from keras import backend; print(backend_BACKEND);"
    Using TensorFlow backend.
    tensorflow

<br/>

При желании tensorflow можно заменить на theano
