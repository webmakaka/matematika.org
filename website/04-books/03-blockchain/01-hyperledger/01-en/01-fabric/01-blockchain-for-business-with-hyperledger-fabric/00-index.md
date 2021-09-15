---
layout: page
title: Nakul Shah - Blockchain for Business with Hyperledger Fabric
description: Nakul Shah - Blockchain for Business with Hyperledger Fabric
keywords: Blockchain, Hyperleger, Fabric, Nakul Shah, Blockchain for Business with Hyperledger Fabric
permalink: /books/blockchain/hyperledger/fabric/blockchain-for-business-with-hyperledger-fabric/en/
---

# [Nakul Shah] Blockchain for Business with Hyperledger Fabric: A complete guide to enterprise Blockchain implementation using Hyperledger Fabric [ENG, 2019]

Если кто будет изучать, присоединяйтесь. Может у вас лучше получится и вы подскажете как сделать, чтобы все работало.

За индусом нужен глаз да глаз. То там где-то напутает, то сям.
А вообще этот мудазвон заставил меня копипастить код из pdf. И только в следующей главе, он дал ссылку на репо, где хранится код.

К сожалению, исходных кодов к книге нет. Да еще и при копи пасте команды в консоли не выполняются и необходимо предварительно их в текстовом редакторе подправлять.

Также мной были замечены ошибки в приведенных исходниках, как впрочем и в командах приведенных в книге.

<br/>

**Далее все будет запускаться в ubuntu 18 TLS.**

Редактор кода - vscode.

<br/>

Необходимы, чтобы были установлены следующие программы:

    $ docker -v
    $ docker-compose -v
    $ go version

<br/>

Также нужно установить node.js:

    -- installation

    $ LATEST_VERSION=$(curl --silent "https://api.github.com/repos/nvm-sh/nvm/releases/latest" | grep '"tag_name"' | sed -E 's/.*"([^"]+)".*/\1/')

    $ curl -o- https://raw.githubusercontent.com/creationix/nvm/${LATEST_VERSION}/install.sh | bash

<br/>

    $ export NVM_DIR="$HOME/.nvm"
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
    [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion

<br/>

    $ nvm --version
    0.35.3

Инсталляция подходящей версии node.js для проектов из книги.

    $ {
      nvm install v8.17.0
      nvm use v8.17.0
      nvm alias default v8.17.0
    }

<br/>

### [Frameworks, Network Topologies, and Modeling](/books/blockchain/hyperledger/fabric/blockchain-for-business-with-hyperledger-fabric/frameworks-network-topologies-and-modeling/en/)

<br/>

### [Chaincode in Hyperledger Fabric](/books/blockchain/hyperledger/fabric/blockchain-for-business-with-hyperledger-fabric/chaincode-in-hyperledger-fabric/en/)

<br/>

### Fabric SDK: Interaction with Fabric Network

Все, что делается в данной главе, позднее повторяется в следующей. Особого смысла разбирать примеры, которые мало чем отличаются от того, что будет использоваться в уже готовом приложении, я не вижу.

Нужно отметить, что у меня при запуске были ошибки и все падало. Для версии 2.2 обновился sdk и примеры из книги не стали работать для этой версии.

Если всеже интересно, то вот здесь остались <a href="/books/blockchain/hyperledger/fabric/blockchain-for-business-with-hyperledger-fabric/fabric-sdk-interaction-with-fabric-network/en/">наработки и ошибки</a>.

<br/>

### [Fabric SDK: Building End-to-End Application with Fabric Network](/books/blockchain/hyperledger/fabric/blockchain-for-business-with-hyperledger-fabric/fabric-sdk-building-end-to-end-application-with-fabric-network/en/)

<br/>

### Fabric in Production

Не до production пока
