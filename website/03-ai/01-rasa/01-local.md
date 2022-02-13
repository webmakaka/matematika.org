---
layout: page
title: RASA на локалхосте
description: RASA на локалхосте
keywords: ai, nlp, nlu, natural language understanding, rasa, local
permalink: /ai/nlp/nlu/rasa/local/
---

# RASA на локалхосте

<br/>

<div align="center">
    <iframe width="853" height="480" src="https://www.youtube.com/embed/rlAQWbhwqLA" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen></iframe>
</div>

<br/>

## Перед тем как начать

<br/>

### Installing Rasa Open Source on Ubuntu

https://www.youtube.com/watch?v=tXiYJM2vGJk&list=PL75e0qA87dlEWUA5ToqLLR026wIkk2evk&index=3

<!--

<br/>

Поднимаю виртуальное окружение как [здесь](/dev/tools/python/virtualenv/)

-->

<br/>

**Используется версия python - 3.6.9**

<br/>

    $ python3 -m venv ./venv
    $ source ./venv/bin/activate

<br/>

    $ pip install -U pip

    // С последней версией были ошибки
    // с этой версией работало
    $ pip install rasa==2.5.0
    $ rasa --version
    $ rasa init

<br/>

    ? Please enter a path where the project will be created [default: current directory] [./rasa-assistant]

    ? Do you want to train an initial model? [Yes]

    ? Do you want to speak to the trained assistant on the command line? [Yes]

    // Чтобы повторно запустить shell
    $ cd ./rasa-assistant
    $ rasa shell

<br/>

### (Ep #1 - Rasa Masterclass) Intro to conversational AI and Rasa | Rasa 1.8.0

<br/>

    $ rasa init

    ? Please enter a path where the project will be created [default: current directory] [./rasabot]

    Hello

<br/>

### (Ep #2 - Rasa Masterclass) Creating the NLU training data | Rasa 1.8.0

    $ rasa train nlu
    $ rasa shell nlu

    $ rasa shell train

    $ rasa run actions && rasa shell
