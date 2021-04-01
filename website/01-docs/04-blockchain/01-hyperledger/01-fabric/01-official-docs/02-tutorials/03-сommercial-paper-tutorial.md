---
layout: page
title: Hyperleger Fabric - Commercial paper tutorial
description: Hyperleger Fabric - Commercial paper tutorial
keywords: Blockchain, Hyperleger, Fabric, Официальная документация, Commercial paper tutorial
permalink: /blockchain/hyperledger/fabric/official-docs/tutorials/сommercial-paper-tutorial/
---

# 7.3 Hyperleger Fabric - Commercial paper tutorial

This tutorial will show you how to install and use a commercial paper sample application and smart contract.

In this tutorial two organizations, MagnetoCorp and DigiBank, trade commercial paper with each other using Paper-Net, a Hyperledger Fabric blockchain network.

Делаю:

01.09.2020

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

    $ curl -sSL https://raw.githubusercontent.com/hyperledger/fabric/master/scripts/bootstrap.sh | bash -s 2.2.0

<br/>

    $ cd fabric-samples/commercial-paper
    $ ./network-starter.sh

In this tutorial, we will operate Org1 of the test network as DigiBank and Org2 as MagnetoCorp

The commercial paper tutorial allows you to act as two organizations by providing two separate folders for DigiBank and MagnetoCorp. The two folders contain the smart contracts and application files for each organization. Because the two organizations have different roles in the trading of the commercial paper, the application files are different
for each organization.

    $ cd ~/projects/dev/hyperledger/
    $ cd fabric-samples/commercial-paper/organization/magnetocorp/
    $ ./configuration/cli/monitordocker.sh  net_test

**Install and approve the smart contract as MagnetoCorp**

    $ cd ~/projects/dev/hyperledger/
    $ cd fabric-samples/commercial-paper/organization/magnetocorp/
    $ source magnetocorp.sh
    $ peer lifecycle chaincode package cp.tar.gz \
     --lang node \
     --path ./contract \
     --label cp_0

<br/>

    $ peer lifecycle chaincode install cp.tar.gz

<br/>

    $ peer lifecycle chaincode queryinstalled

```
Installed chaincodes on peer:
Package ID: cp_0:7c36d35cceb8179e20a228b1e66b94254f419741c7bfd3d6d92639006991e576, Label: cp_0
```

<br/>

    // Установить свой индивидуальнй PACKAGE_ID
    $ export PACKAGE_ID=cp_0:7c36d35cceb8179e20a228b1e66b94254f419741c7bfd3d6d92639006991e576

<br/>

    // approve the chaincode definition
    $ peer lifecycle chaincode approveformyorg \
        --orderer localhost:7050 \
        --ordererTLSHostnameOverride orderer.example.com \
        --channelID mychannel \
        --name papercontract \
        -v 0 \
        --package-id $PACKAGE_ID \
        --sequence 1 \
        --tls \
        --cafile $ORDERER_CA

<br/>

### Install and approve the smart contract as DigiBank

    $ cd ~/projects/dev/hyperledger/
    $ cd fabric-samples/commercial-paper/organization/digibank/
    $ source digibank.sh

<br/>

    $ peer lifecycle chaincode package cp.tar.gz \
        --lang node \
        --path ./contract \
        --label cp_0

<br/>

    $ peer lifecycle chaincode install cp.tar.gz

<br/>

    $ peer lifecycle chaincode queryinstalled

```
Installed chaincodes on peer:
Package ID: cp_0:61b9d0e7ee57237ed0dfa8e4f6a7bd574d21aa75feb435e152c7ae1122072c7c, Label: cp_0
```

<br/>

    // Установить свой индивидуальнй PACKAGE_ID
    $ export PACKAGE_ID=cp_0:61b9d0e7ee57237ed0dfa8e4f6a7bd574d21aa75feb435e152c7ae1122072c7c

<br/>

    $ peer lifecycle chaincode approveformyorg \
        --orderer localhost:7050 \
        --ordererTLSHostnameOverride orderer.example.com \
        --channelID mychannel \
        --name papercontract \
        -v 0 \
        --package-id $PACKAGE_ID \
        --sequence 1 \
        --tls \
        --cafile $ORDERER_CA

<br/>

### Commit the chaincode definition to the channel

    $ peer lifecycle chaincode commit \
        -o localhost:7050 \
        --ordererTLSHostnameOverride orderer.example.com \
        --peerAddresses localhost:7051 \
        --tlsRootCertFiles ${PEER0_ORG1_CA} \
        --peerAddresses localhost:9051 \
        --tlsRootCertFiles ${PEER0_ORG2_CA} \
        --channelID mychannel \
        --name papercontract \
        -v 0 \
        --sequence 1 \
        --tls \
        --cafile $ORDERER_CA \
        --waitForEvent

<br/>

### Выпускаем ценные бумаги

    $ cd ~/projects/dev/hyperledger/
    $ cd fabric-samples/commercial-paper/organization/magnetocorp/application
    $ npm install
    $ node enrollUser.js

Сгенерировался ключик isabella.id

    $ ls ../identity/user/isabella/wallet/

<br/>

Времени на раскачку нет, выпускаем ценные бумаги!

    $ node issue.js

<br/>

### Покупаем ценные бумаги

    $ cd ~/projects/dev/hyperledger/
    $ cd fabric-samples/commercial-paper/organization/digibank/application
    $ npm install
    $ node enrollUser.js

<br/>

    $ node buy.js

<br/>

    $ node redeem.js
