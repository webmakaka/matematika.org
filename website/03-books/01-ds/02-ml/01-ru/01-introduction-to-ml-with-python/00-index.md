---
layout: page
title: Мюллер.А, Гвидо. С. - Введение в машинное обучение с помощью Python
description: Введение в машинное обучение с помощью Python. Руководство для специалистов по работе с данными
keywords: Мюллер, Гвидо, Введение в машинное обучение с помощью Python
permalink: /books/ds/ml/ru/scikit-learn/introduction-to-ml-with-python/
---

# Мюллер.А, Гвидо. С. - Введение в машинное обучение с помощью Python. Руководство для специалистов по работе с данными [RUS, 2017]

https://github.com/amueller/introduction_to_ml_with_python

<br/>

Программный код включает в себя не только все примеры, приведенные в этой книге, но и библиотеку mglearn. Она представляет собой библиотеку, включающую разные полезные функции.

Если вы видите вызов mglearn в программном коде, то речь, как правило, идет о быстром способе построить красивую картинку или загрузить некоторые интересные данные.

Библиотека немного протухла, поэтому ее следует удалить и использовать версию, что устанавливается pip3.

<br/>

Кроме того, существует видеокурс Андреаса Мюллера «Advanced Machine Learning with scikit-learn», дополняющий эту книгу. Вы можете найти его на торрент трекерах, ознакомиться и при желании купить.

<br/>

Запускать буду в <a href="/ds/devtools/python/docker/">docker</a> под ubuntu linux в специально подготовленном контейнере для tensorflow. Да не суть. Главное, что jupyter notebook запускается одной командой.

UPD. Больше ничего не запускаю в контейнерах. Все в виртуальном окружении для python.

<br/>

    # apt install -y graphviz

    # pip3 install --upgrade graphviz
    # pip3 install --upgrade mglearn

<!--

    // То, что следующие пакеты нужны, уверенности на 100% нет
    // Поставить если попросят
    # pip3 install --upgrade pillow
    # pip3 install --upgrade imageio

-->

<br/>

    # cd /tf
    # git clone https://github.com/amueller/introduction_to_ml_with_python
    # cd introduction_to_ml_with_python

    // В общем в проекте, что на github устаревшая библиотека.
    // Точнее там какая-то зависимость поменялась и чтобы библиотека
    // подгружалась из стандартного каталога библиотек /usr/local/lib/python3.6/dist-packages/
    // нужно удалить из скачанного проекта mglearn.
    # rm -rf mglearn/

<br/>

Далее в jupyter выполнил задачи, которые отработали нормально. За исключением одного места, где неизвестный параметр, я просто удалил.

    boxplot() got an unexpected keyword argument 'manage xticks'

В конечном итоге заработало без ошибок но с сообщениями, что некоторые пакеты протухли.

<br/>

### [Машинное обучения с учителем (supervised learning)](/books/ds/ml/ru/scikit-learn/introduction-to-ml-with-python/supervised-learning/)

<br/>

### [Машинное обучения без учителя (unsupervised learning)](/books/ds/ml/ru/scikit-learn/introduction-to-ml-with-python/unsupervised-learning/)
