---
layout: page
title: Подготовка виртуального окружения для запуска python приложений в изолированной среде в Ubuntu 20.04 LTS
description: Подготовка виртуального окружения для запуска python приложений в изолированной среде в Ubuntu 20.04 LTS
keywords: dev, tools, python, virtualenv, ubuntu
permalink: /dev/tools/python/virtualenv/
---

# Подготовка виртуального окружения для запуска python приложений в изолированной среде в Ubuntu 20.04 LTS

<br/>

```
$ lsb_release -a
No LSB modules are available.
Distributor ID:	Ubuntu
Description:	Ubuntu 20.04.5 LTS
Release:	20.04
Codename:	focal
```

<br/>

### pyenv (Используется, когда нужна специфическая минорная версия python)

<br/>

```
$ sudo apt update && sudo apt upgrade -y

// python3 будет по умолчанию называться python
$ sudo apt install python-is-python3
```

<br/>

```
// На astra linux требовались
# apt-get install -y patch make gcc zlib-devel libssl-devel


// WARNINGS при отсутствии
# apt-get install -y bzip2-devel ncurses-devel readline-devel liblzma-devel libsqlite3-devel


// Ошибка без этого пакета
// ModuleNotFoundError: No module named '_ctypes'
 # apt-get install -y libffi-devel
```

<br/>

```
// На ubuntu
$ sudo apt install -y build-essential zlib1g-dev libffi-dev libssl-dev libbz2-dev libreadline-dev libsqlite3-dev liblzma-dev
```

<!-- <br/>

Вариант 1.

<br/>

Инсталляция brew / homebrew [в ubuntu](//sysadm.ru/desktop/linux/ubuntu/brew/)

<br/>

```
$ brew install pyenv
``` -->

<br/>

```
$ curl https://pyenv.run | bash
```

<br/>

```
WARNING: seems you still have not added 'pyenv' to the load path.

# Load pyenv automatically by appending
# the following to
~/.bash_profile if it exists, otherwise ~/.profile (for login shells)
and ~/.bashrc (for interactive shells) :

export PYENV_ROOT="$HOME/.pyenv"
command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"

# Restart your shell for the changes to take effect.

# Load pyenv-virtualenv automatically by adding
# the following to ~/.bashrc:

eval "$(pyenv virtualenv-init -)"
```

<br/>

```

$ vi ~/.bash_profile
```

```
export PYENV_ROOT="$HOME/.pyenv"
command -v pyenv >/dev/null || export PATH="$PYENV_ROOT/bin:$PATH"
eval "$(pyenv init -)"
```

<br/>

logout / login

<br/>

```
$ echo ${PYENV_ROOT}
```

<br/>

```
// если нет
$ source ~/.bash_profile
```

<br/>

```
$ pyenv install --list | grep python

$ export PYTHON_VERSION=3.8.12

$ pyenv install ${PYTHON_VERSION}
```


<br/>

```
$ ~/.pyenv/versions/${PYTHON_VERSION}/bin/python --version
Python 3.8.12
```

<br/>

```
$ export PROJECT_NAME=<MY_NEW_PROJECT_NAME>

$ pyenv virtualenv ${PYTHON_VERSION} ${PROJECT_NAME}-env

$ source ${PYENV_ROOT}/versions/${PROJECT_NAME}-env/bin/activate
```

<br/>

```
$ pip install --upgrade pip
$ pip install setuptools --upgrade

// Посмотреть список установленных пакетов
// $ pip list -v
```

<br/>

```
$ python --version
$ pip --version
```
