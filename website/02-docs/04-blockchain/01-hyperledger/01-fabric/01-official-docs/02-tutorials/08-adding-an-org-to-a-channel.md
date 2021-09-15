---
layout: page
title: Hyperleger Fabric - Adding an Org to a Channel
description: Hyperleger Fabric - Adding an Org to a Channel
keywords: Blockchain, Hyperleger, Fabric, Официальная документация, Adding an Org to a Channel
permalink: /blockchain/hyperledger/fabric/official-docs/tutorials/adding-an-org-to-a-channel/
---

# 7.8 Adding an Org to a Channel

Делаю:

29.08.2020

<br/>

### Все удаляю

    $ cd ~/projects/dev/hyperledger/
    $ sudo rm -rf *

<br/>

    $ {
        docker stop $(docker ps -aq)
        docker rm $(docker ps -aq)
        docker system prune -a
    }

<br/>

    $ curl -sSL https://raw.githubusercontent.com/hyperledger/fabric/master/scripts/bootstrap.sh | bash -s

<br/>
