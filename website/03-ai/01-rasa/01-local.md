---
layout: page
title: RASA на локалхосте
description: RASA на локалхосте
keywords: ai, nlp, nlu, natural language understanding, rasa, local
permalink: /ai/nlp/nlu/rasa/local/
---

# RASA на локалхосте

<br/>

https://www.youtube.com/watch?v=PfYBXidENlg

<br/>

[Поднимаю виртуальное окружение](/dev/tools/python/virtualenv/)

<br/>

    $ python -m venv ./venv
    $ source ./venv/bin/activate

<br/>

    $ pip install -U pip

    // С последней версией были ошибки
    // с этой версией работало
    $ pip install rasa==3.0.9

<br/>

```
$ rasa --version
Rasa Version      :         3.0.9
```

<br/>

```
$ git clone https://github.com/wildmakaka/Rasa-3.0-rock-paper-scissors-chatbot
```

<br/>

```
$ cd Rasa-3.0-rock-paper-scissors-chatbot/
```

<br/>

```
$ rm -rf .rasa/cache/
$ rm -rf models/
```

<br/>

```
$ rasa train
```

<br/>

```
$ rasa run actions
```

<br/>

```
// Еще 1 терминал
$ rasa shell
$ hi
$ rock
```

<br/>

```
Your input ->  hi
Type 'rock', 'paper' or 'scissors' to play.
Your input ->  rock
You chose rock
The computer chose scissors
Congrats, you won!
Do you want play again?
```

<br/>

## Другие варианты:

### Init

<br/>

    $ rasa init

<br/>

    ? Please enter a path where the project will be created [default: current directory] [./rasa-assistant]

    ? Do you want to train an initial model? [Yes]

    ? Do you want to speak to the trained assistant on the command line? [Yes]

    // Чтобы повторно запустить shell
    $ cd ./rasa-assistant
    $ rasa shell

<br/>

### Команды

    $ rasa train nlu
    $ rasa shell nlu

    $ rasa shell train
