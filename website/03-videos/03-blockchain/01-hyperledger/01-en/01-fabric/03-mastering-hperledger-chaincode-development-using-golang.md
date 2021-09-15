---
layout: page
title: Rajeev Sakhuja - Mastering Hyperledger Chaincode Development using GoLang
description: Rajeev Sakhuja - Mastering Hyperledger Chaincode Development using GoLang
keywords: Blockchain, Hyperleger, Fabric, Mastering Hyperledger Chaincode Development using GoLang
permalink: /videos/blockchain/hyperledger/en/fabric/mastering-hperledger-chaincode-development-using-golang/
---

# [Rajeev Sakhuja] Mastering Hyperledger Chaincode Development using GoLang [ENG, 2019, Latest updates 16/06/2020]

Язык вроде норм. Лучше большинства индусов.

Похоже, что альтернатив для изучения hyperledger вообще нет. Надеюсь, что пока. Все что есть, устаревшее.

Автор обновил курс до Fabric 2.1. Уметь работать с Fabric 1.X не требуется.

С первого раза не зашло, от слова совсем.

**Сайт автора:**  
http://www.bcmentors.com/courses/

<br/>

Странный этот индус какой-то. Типа код попрятал.

<br/>

**Код для проекта:**  
http://www.bcmentors.com/download/hlf-dev-chaincode-v1-4-1-zip/

**Пароль:**
04-25-2020

**Fabric 2.0 Sample Chaincode used in the course by Raj.**  
https://github.com/acloudfan/HLF-GO-2.0

<br/>

Автор написал свои скрипты и мы (по крайней мере по началу) дергаем их. Также у начале используем какой-то его готовый пример, что он делает непонятно. (Надеюсь по началу).

<br/>

2 способа развернуть оружение:

- простой - с помощью подготовленной виртуалки и скриптов
- самостоятельно все настраивать. (Может когда понадобится, тогда и посмотрим).

<br/>

### Простой - с помощью подготовленной виртуалки и скриптов

Виртуалка качалась 3 часа.

<br/>

    $ cd HLF-Dev-Chaincode-V2.1-1.0

<br/>

    $ vi Vagrantfile

```
# 1. Use this for "Standard setup"
# config.vm.box = "bento/ubuntu-18.04"

# 2. Use this for "VirtualBox Express Setup"
config.vm.box = "acloudfan/hlfdev2.0-0"
```

<br/>

    $ vagrant up

<!--

    // password vagrant
    $ vagrant ssh

-->

<br/>

### Чтобы скрипты индуса выполнялись

    $ cd /vagrant/
    $ chmod +x * ./network/bin/*.sh
    $ chmod +x * ./network/setup/vexpress/*.sh
    $ chmod +x * ./network/setup/*.sh

<br/>

### Подготовка окружения для работы

    $ cd /vagrant/network/setup/vexpress/
    $ ./init-vexpress.sh


    $ cd /vagrant/network/setup/
    $ ./validate-setup.sh
    $ ./update-git-repo.sh

<br/>

### Запуск всего этого добра

    $ cd /vagrant/

    $ dev-init.sh

    // $ dev-start.sh

    $ docker ps

    $ dev-validate.sh

    $ dev-validate.sh skip

    $ dev-mode.sh

    $ dev-stop.sh

<br/>

### 6. Hands On Setting the environment & executing peer commands

    $ source set-env.sh  acme
    // $ source set-env.sh  budget

    $ show-env.sh

    // Чтобы обновить
    $ export FABRIC_LOGGING_SPEC=debug

    $ show-env.sh

<br/>

    $ export  FABRIC_LOGGING_SPEC=info

    $ peer channel list

    $ export  FABRIC_LOGGING_SPEC=error

    $ peer channel getinfo -c airlinechannel

    $ peer lifecycle chaincode queryinstalled

<br/>

### HyperLedger Explorer

    $ dev-init.sh -e

    http://localhost:8080/

    $ dev-stop.sh

<br/>

### 8. Hands On Chaincode execution utility

    $ set-chain-env.sh
    $ show-chain-env.sh

    $ reset-chain-env.sh

    $ source set-env.sh acme
    $ chain.sh queryinstalled
    $ chain.sh query
    $ chain.sh invode -o

    copy command and paste

    $ chain.sh querycommitted -o

<br/>

## 05. Fabric 2.x Chaincode Lifecycle

<br/>

### 02. Exercise- Chaincode Packaging & Installation

cahin.sh package - создает пакет
chain.sh install - инсталлирует пакет
chian.sh install -p - создает а потом инсталлирует default пакет

<br/>

    $ dev-init.sh
    $ source  set-env.sh  acme
    $ chain.sh package -o
    $ chain.sh package
    $ ls ${HOME}/packages

<br/>

    $ chain.sh install -p
    $ chain.sh queryinstalled

<br/>

### 04. Exercise - Chaincode Approval & Commitment

    $ dev-init.sh
    $ source  set-env.sh  acme
    $ chain.sh install -p

    $ chain.sh check
    $ chain.sh approve
    $ chain.sh check

    $ chain.sh querycommitted
    $ chain.sh commit
    $ chain.sh querycommitted
    $ chain.sh list

<br/>

    $ chain.sh instantiate -o
    $ set-chain-env.sh -I false
    $ chain.sh instantiate -o

<br/>

### 06. Exercise - Chaincode Invoke & Query

```
$ {
    dev-init.sh
    source  set-env.sh  acme
    chain.sh install -p
    chain.sh approve
    chain.sh commit
}
```

    $ chain.sh query
    $ chain.sh init -o
    $ chain.sh init
    $ chain.sh query

    $ chain.sh list
    $ set-chain-env.sh -q '{"Args": ["query", "a"]}'
    $ chain.sh query
    $ set-chain-env.sh -q '{"Args": ["query", "b"]}'
    $ chain.sh query
    $ set-chain-env.sh -i '{"Args": ["invoke", "a","b","s"]}

    // У меня ошибка. У автора все ОК.
    $ chain.sh invoke

    $ chain.sh query

<br/>

### 08. Exercise - Chaincode Upgrade

```
$ {
    dev-init.sh
    source  set-env.sh  acme
    chain.sh install -p
    chain.sh instantiate
}

```

    $ chain.sh list
    $ set-chain-env.sh -v 2.0
    $ chain.sh package
    $ chain.sh install
    $ chain.sh approve -o

    // s - sequence
    $ set-chain-env.sh -s 2
    $ chain.sh approve
    $ chain.sh commit
    $ chain.sh list

<br/>

    $ chain.sh queryinstalled
    $ chain.sh install -p
    $ chain.sh list
    $ chain.sh approve
    $ chain.sh upgrade-auto
    $ chain.sh list

<br/>

### 09. Chaincode Net versus Dev Mode

    $ dev-init.sh -d
    $ source  set-env.sh acme
    $ set-chain-env.sh
    $ chain.sh install
    $ cc-run.sh

В отдельной сесссии

    $ source  set-env.sh acme
    $ chain.sh instantiate
    $ chain.sh invoke
    $ chain.sh query

<br/>

## 06. Go Chaincode Interface

<br/>

### 02. Logging from Chaincode

    $ go run acflogger/test

<br/>

### 03. Hands On Chaincode Install Commit Logging Walkthrough

Получение логов из контейнера

Terminal session 1

    $ dev-init.sh
    $ source  set-env.sh acme
    $ set-chain-env.sh -n token  -v 1.0  -p token/v2 -c '{"Args":["init"]}'
    $ chain.sh install -p
    $ chain.sh instantiate
    $ cc-logs.sh -f

Terminal session 2

    $ source  set-env.sh acme
    $ chain.sh invoke

При выполнении в первом терминале получаем сообщения.

<br/>

network/config/core.yaml

Устанавливаем logging level в error

```
    logging:
      # Default level for all loggers within the chaincode container
      level:  error
```

Terminal session 1

    $ dev-stop.sh
    $ dev-start.sh
    $ cc-logs.sh -f

Terminal session 2

    $ chain.sh invoke

В терминале 1 только сообщения об ошбках Error и Fatal

Вернули level: info

<br/>

### 04. Hands On Chaincode Interface

    $ dev-init.sh -e
    $ source set-env.sh acme
    $ set-chain-env.sh   -n  token   -p token/v1    -c '{"Args": ["init"]}'

    $ chain.sh package
    $ chain.sh install

    $ chain.sh approveformyorg
    $ chain.sh commit

http://localhost:8080/#/transactions

    $ cc-logs.sh -t 10 -f

Terminal session 2

    $ source set-env.sh acme
    $ chain.sh  init
    $ chain.sh  query
    $ chain.sh invoke
    $ chain.sh query

<br/>

### 05. Sending the Success & Error Response

    $ chain.sh install -p
    $ set-chain-env.sh -s 2
    $ chain.sh instantiate -n
    $ chain.sh invoke

<br/>

## 07. Go Chaincode Stub

    $ dev-init.sh
    $ source set-env.sh acme

    $ set-chain-env.sh -n token -p token/v3
    $ chain.sh install -p
    $ chain.sh instantiate
    $ cc-logs.sh -f

Terminal session 2

    $ source set-env.sh acme
    $ chain.sh invoke
    $ chain.sh query

<br/>

#### Part-2

Разкомментили код в файле: v3/goken.go

    $ go get github.com/golang/protobuf/proto

<br/>

    $ chain.sh install-auto
    $ set-chain-env.sh -s 2
    $ chain.sh instantiate
    $ cc-logs.sh -f

Terminal session 2

    $ chain.sh invoke

<br/>

### 05. Hands On Using Arguments function

    $ dev-init.sh
    $ source set-env.sh acme
    $ cc-logs.sh -f

Terminal session 2

    $ set-chain-env.sh   -n token   -v 1.0   -p token/v4

    $ chain.sh install -p
    $ source  set-env.sh  acme
    $ chain.sh install -p
    $ chain.sh instantiate

    $ set-chain-env.sh   -i    '{"args":["func-name"]}'

    $ chain.sh invoke

    $ set-chain-env.sh   -i    '{"args":["func-name","param-1","param-2"]}'
    $ chain.sh   invoke

<br/>

### 08. Hands On Chaincode State Functions GetState, PutState & DelState

"token/v5/token.go"

    $ dev-init.sh
    $ source  set-env.sh acme
    $ set-chain-env.sh -n token -v 1.0 -p token/v5
    $ set-chain-env.sh   -i   '{"args":["set"]}' -q   '{"args":["get"]}'
    $ chain.sh install -p
    $ chain.sh instantiate
    $ chain.sh query
    $ chain.sh invoke

<br/>

### 09. Exercise Add the delete function to V5 Token implementation

"token/v5/token.go"

В token.go добавить

```
 else if(funcName == "delete"){
		return DeleteToken(stub)
	}
```

    $ dev-init.sh
    $ source  set-env.sh acme
    $ set-chain-env.sh -n token -v 1.0 -p token/v5
    $ set-chain-env.sh   -i   '{"args":["set"]}' -q   '{"args":["get"]}'
    $ chain.sh install -p
    $ set-chain-env.sh  -c  '{"args":[]}'
    $ chain.sh instantiate
    $ chain.sh query
    $ chain.sh invoke
    $ chain.sh query

<br/>

    $ set-chain-env.sh -i '{"args":["delete"]}'
    $ chain.sh invoke
    $ chain.sh query

<br/>

### 12. Hands On InvokeChaincode function

"token/v6/caller.go"

Terminal session 1

    $ chmod +x $GOPATH/src/token/v6/setup-cc.sh
    $ $GOPATH/src/token/v6/setup-cc.sh

<br/>
    
    $ source  set-env.sh acme
    $ set-chain-env.sh -n token
    $ cc-logs.sh -f

Terminal session 2

    $ source  set-env.sh acme
    $ set-chain-env.sh -n caller
    $ cc-logs.sh -f

Terminal session 3

    $ source  set-env.sh acme
    $ set-chain-env.sh  -n caller
    $ set-chain-env.sh  -n caller -i '{"args":["setOnCaller"]}'  -q '{"args":["getOnCaller"]}'
    $ chain.sh query
    $ chain.sh invoke
    $ chain.sh query

<br/>

### 15. Hands On Event function usage

"token/v8/token.go"

    $ dev-init.sh
    $ source  set-env.sh acme
    $ set-chain-env.sh  -n token -v 1.0 -p token/v8 -c '{"Args": ["init"]}'
    $ chain.sh install -p
    $ chain.sh instantiate

    $ events.sh -t chaincode -n token -e TokenValueChanged -c airlinechannel

Terminal session 2

    $ source  set-env.sh acme
    $ set-chain-env.sh   -i   '{"args":["set"]}' -q   '{"args":["get"]}'
    $ chain.sh invoke

<br/>

## 08. Writing Unit Test Cases for Network Applications

<br/>

    $ dev-init.sh -e
    $ cd gocc/src/testing/cctest/
    $ chmod +x ./test.sh
    $ ./test.sh

Не отработало у меня

<br/>

## 09. Mini Project Develop the ERC20 Token on Fabric

<br/>

### 03. Hands-on Solution walkthrough

В общем индус зачем-то копировал файлы из каталога "token/ERC20/" в отдельный и запускал. test.sh

<br/>

    $ dev-init.sh
    $ cd gocc/src/token/ERC20/
    $ chmod +x ./test.sh
    $ export  FABRIC_LOGGING_SPEC=error
    $ ./test.sh

Хз, ошибкой завершается.

<br/>

### 04. Exercise Test out the transfer events

<br/>

**"token/ERC20/README.md"**

<br/>

Terminal session 1

    $ dev-init.sh
    $ source set-env.sh    acme
    $ set-chain-env.sh       -n erc20  -v 1.0   -p  token/ERC20
    $ chain.sh install -p

    $ set-chain-env.sh        -c   '{"Args":["init","ACFT","1000", "A Cloud Fan Token!!!","john"]}'
    $ chain.sh  instantiate
    $ events.sh -t chaincode -n erc20 -e transfer -c airlinechannel

Terminal session 2

    $ source set-env.sh    acme
    $ set-chain-env.sh         -i   '{"Args":["transfer", "john", "sam", "10"]}'
    $ export  FABRIC_LOGGING_SPEC=error
    $ chain.sh invoke

Terminal session 1

**Получаем:**

```
✅  event= transfer
 block# 6
 status= VALID
 payload= {"from":"john", "to":"sam","amount":10}
```

**Можем повторить**

```
✅  event= transfer
 block# 7
 status= VALID
 payload= {"from":"john", "to":"sam","amount":10}
```

<br/>

## 10. Transaction Flow & Chaincode Endorsement Policies

<br/>

### 06. Chaincode Lifecycle EP in Dev Setup

    $ set-chain-env.sh -g "OR('BudgetMSP.member')"
    $ set-chain-env.sh -e budget
    $ show-chain-env.sh

<br/>

### 07. Hands On Chaincode Endorsement Policies

"token/v9/token.go"

<br/>

Terminal session 1

    $ dev-init.sh
    $ export  FABRIC_LOGGING_SPEC=error
    $ source set-env.sh    acme
    $ set-chain-env.sh  -n token -v 1.0 -p token/v9 -c '{"Args": ["init"]}'
    $ set-chain-env.sh -q   '{"args":["get"]}'
    $ chain.sh install -p
    $ set-chain-env.sh -g "OR('BudgetMSP.member')"
    $ chain.sh approve
    $ chain.sh check
    $ chain.sh commit
    $ events.sh -t chaincode -n token -e SetToken -c airlinechannel

<br/>

Terminal session 2

    $ source set-env.sh acme
    $ set-chain-env.sh -i   '{"args":["set", "UnProtectedToken","Acme"]}'
    $ chain.sh invoke

// Ошибка. Так и должно быть  
invalid invocation: chaincode 'token' has not been initialized for this version, must call as init first"

    $ source set-env.sh budget
    $ chain.sh install

    $ chain.sh approveformyorg
    $ chain.sh list

    $ source set-env.sh acme
    $ set-chain-env.sh -e budget

    $ chain.sh init
    $ chain.sh init -o

    $ set-chain-env.sh -i '{"args":["set", "UnProtectedToken","Budget"]}'
    $ chain.sh invoke

<br/>

Terminal session 1

```
✅  event= SetToken
 block# 7
 status= VALID
 payload= { "Token":"UnProtectedToken",   "Value":":Budget"}
```

<br/>

Terminal session 2

    $ set-chain-env.sh -e acme
    $ chain.sh invoke

<br/>

Terminal session 1

```
✅  event= SetToken
 block# 8
 status= ENDORSEMENT_POLICY_FAILURE
 payload= { "Token":"UnProtectedToken",   "Value":":Budget"}
```

<br/>

### 08. Exercise Chaincode Endorsement Policy Expressions

Не заработало у меня.

<br/>

    $ dev-init.sh
    $ export  FABRIC_LOGGING_SPEC=error

    $ set-chain-env.sh -g "AND('AcmeMSP.member','BudgetMSP.member')"

    $ set-chain-env.sh -s 2
    $ source set-env.sh acme

    // Наверное нужно
    // chain.sh install -p
    $ chain.sh approve
    $ chain.sh check

```
AcmeMSP: true
BudgetMSP: false
```

// Budget approves

    $ set-chain-env.sh budget
    $ chain.sh approve
    $ chain.sh check

    $ chain.sh commit

    $ set-chain-env.sh -e both
    $ chain.sh invoke

Должно быть ОК

<br/>

    $ set-chain-env.sh -e auto
    $ chain.sh invoke

Должна быть ошибка

<br/>

## 11. Transaction and Data confidentiality

<br/>

### 09. Hands-On Setting up the PDC Definition in JSON file

"token/priv/pcollection.json"

    $ dev-init.sh
    $ export  FABRIC_LOGGING_SPEC=error

    $ set-chain-env.sh -p token/priv
    $ set-chain-env.sh -R pcollection.json

    $ source set-env.sh acme

    $ chain.sh approveformyorg -o
    $ chain.sh commit -o

<br/>

### 10. Hands-On Private Data Collection in action

"token/priv/priv.go"

Подготовка окружения

    $ dev-init.sh

    $ set-chain-env.sh  -n priv -v 1.0 -p token/priv  -c '{"Args": ["init"]}' -C airlinechannel
    $ set-chain-env.sh -R pcollection.0.json

    $ source set-env.sh acme
    $ chain.sh install -p
    $ chain.sh instantiate

    $ source set-env.sh budget
    $ chain.sh install
    $ chain.sh approveformyorg

Тест 1

    $ source set-env.sh acme
    $ set-chain-env.sh -i '{"Args": ["Set","AcmeBudgetOpen", "Acme has set the OPEN data"]}'
    $ chain.sh invoke

    $ set-chain-env.sh -i '{"Args": ["Set","AcmePrivate", "Acme has set the SECRET data"]}'

    $ chain.sh invoke

    $ set-chain-env.sh -q '{"Args": ["Get"]}'

    $ chain.sh query

```
{open:"Acme has set the OPEN data", secret:"Acme has set the SECRET data" , error:"N.A."}
```

Тест 2

    $ source set-env.sh budget
    $ set-chain-env.sh -i '{"Args": ["Set","AcmeBudgetOpen", "Acme has set the OPEN data"]}'
    $ chain.sh invoke

    $ set-chain-env.sh -i '{"Args": ["Set","AcmePrivate", "Acme has set the SECRET data"]}'
    $ chain.sh invoke

    $ chain.sh query

```
{open:"Acme has set the OPEN data", secret:"**** Not Allowed ***" , error:"GET_STATE failed: transaction ID: e6ffdbf84d89b8856a2291515fc5028da5eab07e2823457d726d55e4d5617125: private data matching public hash version is not available. Public hash version = {BlockNum: 10, TxNum: 0}, Private data version = <nil>"}

```

Тест 3

    $ source set-env.sh acme
    $ chain.sh query

```
{open:"Acme has set the OPEN data", secret:"Acme has set the SECRET data" , error:"N.A."}

```

<br/>

### 11. Exercise Update the PDC Definition

Не нужно пересоздавать dev окружени. Предыдущее подходит.

    $ get-cc-info.sh -e

Не захотелось разбираться.

<br/>

### 14. Exercise Extend the PDC Sample Code

Не захотелось разбираться.
