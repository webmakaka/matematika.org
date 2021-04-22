---
layout: page
title: Deep Learning School
description: Deep Learning School
keywords: Deep Learning School, продвинутый поток
permalink: /schools/deep-learning-school/advanced/
---

# Deep Learning (семестр 1, весна 2021): продвинутый поток

Deep Learning начинается где-то в середине курса.

<br/>

### Основы машинного обучения

**[Лелейтнер Григорий] Лекция 1. Введение в машинное обучение**  
https://www.youtube.com/watch?v=LcvKd42DGQU

На этой вводной лекции про машинное обучение вы узнаете, что такое обучение с учителем, как выглядит решение задачи машинного обучения и что такое обучение модели.

<br/>

![Лекция 1. Введение в машинное обучение - разделение на Train / Validation / Test ](/img/schools/deep-learning-school/lecture-01-pic-01.png 'Лекция 1. Введение в машинное обучение - разделение на Train / Validation / Test'){: .center-image }

<br/><br/>

![Лекция 1. Введение в машинное обучение - Cross-validation](/img/schools/deep-learning-school/lecture-01-pic-02.png 'Лекция 1. Введение в машинное обучение - Cross-validation'){: .center-image }

(С нейросетями практически никогда не делается. Для других методов, почти всегда.)

<br/>

**[Лелейтнер Григорий] Семинар 1. Введение в машинное обучение**  
https://www.youtube.com/watch?v=YMtsgx8oB24

В этом семинаре вы познакомитесь с библиотекой sklearn и узнаете основные этапы обработки данных. Также мы будем подбирать гиперпараметры модели с помощью метрик и кросс валидации.

[Ссылка на папку с материалами](https://drive.google.com/drive/folders/1VtuJtgZx4N8CYMiiV7PaKXo2J-PYSwuK)

Инструкция по подключению своего Google drive к Google colab

https://yadi.sk/i/sqqI2xVBcztIDw

<br/>

### Линейные модели

**[Лелейтнер Григорий] Лекция 2.1: Линейная регрессия**  
https://www.youtube.com/watch?v=khdaLtu9i-s

[Ссылка на папку с материалами](https://drive.google.com/drive/folders/1Z08RAmIhL2UGgx8Gz4C5KQRxOgBk_wJo)

**[Лелейтнер Григорий] Лекция 2.2: LogLoss**  
https://www.youtube.com/watch?v=HV4Bm8UJwIs

**[Лелейтнер Григорий] Лекция 2.3: Логистическая регрессия**  
https://www.youtube.com/watch?v=FssgYm7FYM8

<br/>

![Лекция 2.3: Логистическая регрессия](/img/schools/deep-learning-school/lecture-02-pic-01.png 'Лекция 2.3: Логистическая регрессия'){: .center-image }

<br/>

**[Лелейтнер Григорий] Лекция 2.4: Градиентный спуск**  
https://www.youtube.com/watch?v=YWr3S1IqnlQ

**[Лелейтнер Григорий] Лекция 2.5: Регуляризация в линейной регрессии**  
https://www.youtube.com/watch?v=7lKwdPwqVHc

**[Лелейтнер Григорий] Лекция 2.6: Нормализация**  
https://www.youtube.com/watch?v=tOiwAyilk3I

<br/>

**[Ямалутдинов Артём] Семинар. Линейная и логистическая регрессия**  
https://www.youtube.com/watch?v=5VOp5xmBvds

[Ссылка на папку с материалами](https://drive.google.com/drive/folders/1Z08RAmIhL2UGgx8Gz4C5KQRxOgBk_wJo)

**[Ямалутдинов Артём] Семинар. Регуляризация в линейной регрессии**  
https://www.youtube.com/watch?v=56YdhX0xj3k

<br/>

### Композиции алгоритмов и выбор модели

В этом модуле вы познакомитесь с решающими деревьями, а также научитесь строить композиции алгоритмв: бэггинг, стекинг и бустинг. Также вы научитесь выбирать оптимальную модель машинного обучения.

**[Яровиков Юрий] Лекция. Решающие деревья**  
https://www.youtube.com/watch?v=MJwAoWFTMWw

[Ссылка на презентацию](https://drive.google.com/file/d/1OKHxmyBjWLFJFBPOc5uj0MGOMQVAyo-B/view)

**[Яровиков Юрий] Лекция. Композиции алгоритмов**  
https://www.youtube.com/watch?v=vqF8wrWjR5s

Бэггинг, Стекинг и Бустинг используют принцип композиции

- Бэггинг - принимает решение простым голосованием
- Стекинг - обучает метаалгоритм над разноплановыми алгоритмами
- Бустинг - строит базовые модели, компенсирующие ошибки предыдущих.

<br/>

**[Яровиков Юрий] Лекция. Градиентный бустинг**  
https://www.youtube.com/watch?v=JElfEE1OrSU

Градиентный бустинг - эффективный способ построения композиции решающих деревьев.

- Деревья строятся послдеовательно.
- Каждое следующее дерево стремится компенсировать ошбику уже построенных.
- Решение затем принимается взвешенным голосованием.

<br/>

**[Боков Аркадий] Семинар. Выбор моделей и цикл разработки.**  
https://www.youtube.com/watch?v=B03CrtpYDi4

[Ссылка на материалы](https://drive.google.com/file/d/1OKHxmyBjWLFJFBPOc5uj0MGOMQVAyo-B/view)

Интересная лекция, нужно пересмотреть.

<br/>

### Введение в нейронные сети

В этом модуле мы начинаем знакомство с нейронными сетями. Вы узнаете о том, что такое полносвязные нейронные сети, изучите алгоритм обратного распространения ошибки для обучения нейронных сетей, а также изучите библиотеку Pytorch.

<br/>

Ссылки на сайты:

- https://cs231n.github.io/
- http://playground.tensorflow.org/

<br/>

**[Нейчев Радослав] Лекция. Введение в нейронные сети. Часть 1. История развития Deep Learning**  
https://www.youtube.com/watch?v=ZfXpX8tMg-w

<br/>

**[Нейчев Радослав] Лекция. Введение в нейронные сети. Часть 2. Механизм обратного распространения ошибки**  
https://www.youtube.com/watch?v=-yiq1DRX9K0

Интересная лекция, нужно пересмотреть.

<br/>

**[Нейчев Радослав] Лекция. Введение в нейронные сети. Часть 3. Функции активации. Краткий обзор применений CNN и RNN**  
https://www.youtube.com/watch?v=3F7rydcAa0w

Интересная лекция, нужно пересмотреть.

<br/>

**[Садыков Дмитрий] Семинар. Основы PyTorch. Работа с тензорами**  
https://www.youtube.com/watch?v=aW9BgoKalY0

Official PyTorch tutorials:  
https://pytorch.org/tutorials/beginner/blitz/tensor_tutorial.html#sphx-glr-beginner-blitz-tensor-tutorial-py

Useful repo with different tutorials:  
https://github.com/yunjey/pytorch-tutorial

[Ссылка на материалы](https://drive.google.com/drive/folders/1A7ExyT4ntKf4kS0pqk3pmfmrCAdLG70t)

<br/>

**[Садыков Дмитрий] Семинар. Основы PyTorch. Обучение нейронных сетей**  
https://www.youtube.com/watch?v=afGOdNmRF_s

<br/>

### Свёрточные нейросети

Свёрточные нейросети - это особый вид нейросетей, который подходит для обработки структурированных данных - например, изображений.

<br/>

**[Гайнцева Татьяна] Лекция. Лекция. История развития сверточных нейронных сетей**  
https://www.youtube.com/watch?v=Xq76hQHCkvQ

[Ссылка на материалы](https://docs.google.com/presentation/d/1o3t6fClA7bKZ2r5KmzGMuyjLiQK0a588TFpBdlw4a8Q/edit)

<br/>

**[Гайнцева Татьяна] Лекция. Лекция. Сверточные нейронные сети**  
https://www.youtube.com/watch?v=HpKGv-kYurk

<br/>

**[Гайнцева Татьяна] Лекция. Операция пулинга**  
https://www.youtube.com/watch?v=IxLuPHtZBTY

<br/>

**[Гайнцева Татьяна] Лекция. Задачи компьютерного зрения**  
https://www.youtube.com/watch?v=3IPRcBIsgNA

<br/>

### Продвинутое обучение нейросетей

<br/>

**[Нейчев Радослав] Лекция. Градиентная оптимизация в Deep Learning**  
https://www.youtube.com/watch?v=6CvpMOO-DB4

[Ссылка на материалы](https://drive.google.com/file/d/13wCieD5_aiWRJUr2PEY2tP1dcn-4-B-1/view?usp=sharing)

<br/>

**[Нейчев Радослав] Лекция. Лекция. Регуляризация в Deep Learning**  
https://www.youtube.com/watch?v=x72-oUjv1ew

<br/>

**[Лелейтнер Григорий] Семинар. PyTorch. Batch Normalization и Dropout**  
https://www.youtube.com/watch?v=Y9a5EfqM7RM

[Ссылка на материалы](https://drive.google.com/file/d/1iU1uocibhyTAw_1aC4WrDvOWhaUa7RBb/view?usp=sharing)

<br/>

**[Лелейтнер Григорий] Семинар. PyTorch. Оптимизаторы**  
https://www.youtube.com/watch?v=Yh1VoUhS5MY

<br/>

### Классификация изображений

<br/>

### Семантическая сегментация

**[Гайнцева Татьяна] Лекция. Семантическая сегментация. Введение**  
https://www.youtube.com/watch?v=tIqndofykgc

[Ссылка на материалы](https://drive.google.com/drive/folders/1dT0moLoneFiXDBzMPOy0lBwXHz1ajYX1?usp=sharing)

<br/>

**[Гайнцева Татьяна] Лекция. Семантическая сегментация. Трюки: Deconvolution, Dilated Convolution**  
https://www.youtube.com/watch?v=K73tZxH9nvE

Как улучшить имеющиеся архитектуры для задачи сегментации

<br/>

**[Гайнцева Татьяна] Лекция. Семантическая сегментация. Архитектура UNet**  
https://www.youtube.com/watch?v=yEuIV5FsRMs

<br/>

### Детекция объектов на изображениях
