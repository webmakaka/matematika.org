---
layout: page
title: Hyperleger Fabric - Deploying a smart contract to a channel
description: Hyperleger Fabric - Deploying a smart contract to a channel
keywords: Blockchain, Hyperleger, Fabric, Официальная документация, Deploy
permalink: /blockchain/hyperledger/fabric/official-docs/tutorials/deploying-a-smart-contract-to-a-channel/
---

# 7.1 Hyperleger Fabric - Deploying a smart contract to a channel

**Делаю:**  
31.08.2020

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

    $ cd fabric-samples/test-network/

    // Чего-то как-то не каждый раз срабатывает. Приходится по несколько раз делать down/up
    $ ./network.sh down && ./network.sh up createChannel

<br/>

```
$ docker ps
CONTAINER ID        IMAGE                               COMMAND             CREATED             STATUS              PORTS                              NAMES
b30252361fcc        hyperledger/fabric-orderer:latest   "orderer"           31 seconds ago      Up 29 seconds       0.0.0.0:7050->7050/tcp             orderer.example.com
b6c92736704e        hyperledger/fabric-peer:latest      "peer node start"   31 seconds ago      Up 29 seconds       7051/tcp, 0.0.0.0:9051->9051/tcp   peer0.org2.example.com
0c1b84ff5e47        hyperledger/fabric-peer:latest      "peer node start"   31 seconds ago      Up 30 seconds       0.0.0.0:7051->7051/tcp             peer0.org1.example.com

```

<br/>

We can now use the Peer CLI to deploy the asset-transfer (basic) chaincode to the channel using the following steps:

• Step one: Package the smart contract
• Step two: Install the chaincode package
• Step three: Approve a chaincode definition
• Step four: Committing the chaincode definition to the channel

<br/>

**Запускаем утилиту для мониторинга контейнеров в отдельном терминале**

Терминал 2

    $ cp ../commercial-paper/organization/digibank/configuration/cli/monitordocker.sh .
    $ ./monitordocker.sh net_test

<br/>

### 7.1.3 Package the smart contract

**Пример с Go**

    $ cd ../asset-transfer-basic/chaincode-go/
    $ GO111MODULE=on go mod vendor

If the command is successful, the go packages will be installed inside a vendor folder.

    $ cd ../../test-network
    $ export PATH=${PWD}/../bin:$PATH
    $ export FABRIC_CFG_PATH=$PWD/../config/

    $ peer version

    $ peer lifecycle chaincode package basic.tar.gz \
        --path ../asset-transfer-basic/chaincode-go/ \
        --lang golang \
        --label basic_1.0

Создался basic.tar.gz в текущем каталоге.

<br/>

**JavaScript**

    $ cd ~/projects/dev/hyperledger/
    $ cd fabric-samples/asset-transfer-basic/chaincode-javascript
    $ npm install

<br/>

    $ cd ../../test-network
    $ export PATH=${PWD}/../bin:$PATH
    $ export FABRIC_CFG_PATH=$PWD/../config/

<br/>

    $ peer lifecycle chaincode \
        package basic.tar.gz \
        --path ../asset-transfer-basic/chaincode-javascript/ \
        --lang node \
        --label basic_1.0

Создался basic.tar.gz в текущем каталоге.

<br/>

**Typescript**

    $ cd ~/projects/dev/hyperledger/
    $ cd fabric-samples/asset-transfer-basic/chaincode-typescript
    $ npm install

    // В интрукции этого пункта нет
    $ npm run build

    $ cd /home/marley/projects/dev/hyperledger/fabric-samples/test-network/

    $ {
        export PATH=${PWD}/../bin:$PATH
        export FABRIC_CFG_PATH=$PWD/../config/
    }

<br/>

    $ peer lifecycle chaincode \
        package basic.tar.gz \
        --path ../asset-transfer-basic/chaincode-typescript/ \
        --lang node \
        --label basic_1.0

Создался basic.tar.gz в текущем каталоге.

<br/>

### 7.1.4 Install the chaincode package

**Org1**

    $ {
        export CORE_PEER_TLS_ENABLED=true
        export CORE_PEER_LOCALMSPID="Org1MSP"
        export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
        export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
        export CORE_PEER_ADDRESS=localhost:7051
    }

<br/>

    $ peer lifecycle chaincode install basic.tar.gz

<br/>

**Org2**

    $ {
        export CORE_PEER_LOCALMSPID="Org2MSP"
        export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
        export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
        export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
        export CORE_PEER_ADDRESS=localhost:9051
    }

<br/>

    $ peer lifecycle chaincode install basic.tar.gz

<br/>

### 7.1.5 Approve a chaincode definition

    $ peer lifecycle chaincode queryinstalled

    // Будет отличаться от того, что у меня
    $ export CC_PACKAGE_ID=basic_1.0:4ec191e793b27e953ff2ede5a8bcc63152cecb1e4c3f301a26e22692c61967ad

    $ echo $CC_PACKAGE_ID

    $ peer lifecycle chaincode approveformyorg \
        -o localhost:7050 \
        --ordererTLSHostnameOverride orderer.example.com \
        --channelID mychannel \
        --name basic \
        --version 1.0 \
        --package-id $CC_PACKAGE_ID \
        --sequence 1 \
        --tls \
        --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

<br/>

You need to approve a chaincode definition with an identity that has an admin role.

    $ {
        export CORE_PEER_LOCALMSPID="Org1MSP"
        export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
        export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
        export CORE_PEER_ADDRESS=localhost:7051
    }

<br/>

    $ peer lifecycle chaincode approveformyorg \
        -o localhost:7050 \
        --ordererTLSHostnameOverride orderer.example.com \
        --channelID mychannel \
        --name basic \
        --version 1.0 \
        --package-id $CC_PACKAGE_ID \
        --sequence 1 \
        --tls \
        --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

<br/>

### 7.1.6 Committing the chaincode definition to the channel

    $ peer lifecycle chaincode checkcommitreadiness \
        --channelID mychannel \
        --name basic \
        --version 1.0 \
        --sequence 1 \
        --tls \
        --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem \
        --output json

<br/>

```
{
	"approvals": {
		"Org1MSP": true,
		"Org2MSP": true
	}
}

```

<br/>

    $ peer lifecycle chaincode commit \
        -o localhost:7050 \
        --ordererTLSHostnameOverride orderer.example.com \
        --channelID mychannel \
        --name basic \
        --version 1.0 \
        --sequence 1 \
        --tls \
        --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem \
        --peerAddresses localhost:7051 \
        --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt \
        --peerAddresses localhost:9051 \
        --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt

<br/>

You can use the peer lifecycle chaincode querycommitted command to confirm that the chaincode definition has been committed to the channel.

    $ peer lifecycle chaincode querycommitted \
        --channelID mychannel \
        --name basic \
        --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

```
Committed chaincode definition for chaincode 'basic' on channel 'mychannel':
Version: 1.0, Sequence: 1, Endorsement Plugin: escc, Validation Plugin: vscc, Approvals: [Org1MSP: true, Org2MSP: true]

```

<br/>

### 7.1.7 Invoking the chaincode

    $ peer chaincode invoke \
        -o localhost:7050 \
        --ordererTLSHostnameOverride orderer.example.com \
        --tls \
        --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem \
        -C mychannel \
        -n basic \
        --peerAddresses localhost:7051 --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt \
        --peerAddresses localhost:9051 \
        --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt \
        -c '{"function":"initLedger","Args":[]}'

<br/>

**Для TypeScript и JavaScript**

Когда ошибка:

```
Error: endorsement failure during invoke. response: status:500 message:"error in simulation: transaction returned with failure: Error: You've asked to invoke a function that does not exist: initLedger"
```

    // Нужно вызывать функцию следующим образом! Я на этой @#$%^& часа 3-4 потерял!
    -c '{"function":"InitLedger","Args":[]}'

<br/>

    $ peer chaincode query -C mychannel -n basic -c '{"Args":["getAllAssets"]}' | python -m json.tool

<br/>

**Для TypeScript и JavaScript**

    $ peer chaincode query -C mychannel -n basic -c '{"Args":["GetAllAssets"]}' | python -m json.tool

```
[
    {
        "ID": "asset1",
        "color": "blue",
        "size": 5,
        "owner": "Tomoko",
        "appraisedValue": 300
    },
    {
        "ID": "asset2",
        "color": "red",
        "size": 5,
        "owner": "Brad",
        "appraisedValue": 400
    },
    {
        "ID": "asset3",
        "color": "green",
        "size": 10,
        "owner": "Jin Soo",
        "appraisedValue": 500
    },
    {
        "ID": "asset4",
        "color": "yellow",
        "size": 10,
        "owner": "Max",
        "appraisedValue": 600
    },
    {
        "ID": "asset5",
        "color": "black",
        "size": 15,
        "owner": "Adriana",
        "appraisedValue": 700
    },
    {
        "ID": "asset6",
        "color": "white",
        "size": 15,
        "owner": "Michel",
        "appraisedValue": 800
    }
]

```

    $ docker ps

Должны быть контейнеры с chaincode.

```
$ docker ps
CONTAINER ID        IMAGE                                                                                                                                                                    COMMAND                  CREATED             STATUS              PORTS                              NAMES
3570e77abece        dev-peer0.org1.example.com-basic_1.0-ba2cf24a6d127ece5cf5b287c5dc19e39649c695539e455c10f760ac9e9b90a8-da0dc2176d3e64b66e2110369ab6a453d2d86e8fe828bffa81ef9a36fe7e5c24   "docker-entrypoint.s…"   2 minutes ago       Up 2 minutes                                           dev-peer0.org1.example.com-basic_1.0-ba2cf24a6d127ece5cf5b287c5dc19e39649c695539e455c10f760ac9e9b90a8
fe56e624c0f5        dev-peer0.org2.example.com-basic_1.0-ba2cf24a6d127ece5cf5b287c5dc19e39649c695539e455c10f760ac9e9b90a8-ff8b4282f1e26ed874801675b2dddb3b40efefc8ef61b74fe58fc113631378b1   "docker-entrypoint.s…"   2 minutes ago       Up 2 minutes                                           dev-peer0.org2.example.com-basic_1.0-ba2cf24a6d127ece5cf5b287c5dc19e39649c695539e455c10f760ac9e9b90a8
b30252361fcc        hyperledger/fabric-orderer:latest                                                                                                                                        "orderer"                9 minutes ago       Up 9 minutes        0.0.0.0:7050->7050/tcp             orderer.example.com
b6c92736704e        hyperledger/fabric-peer:latest                                                                                                                                           "peer node start"        9 minutes ago       Up 9 minutes        7051/tcp, 0.0.0.0:9051->9051/tcp   peer0.org2.example.com
0c1b84ff5e47        hyperledger/fabric-peer:latest                                                                                                                                           "peer node start"        9 minutes ago       Up 9 minutes        0.0.0.0:7051->7051/tcp             peer0.org1.example.com

```

<br/>

### 7.1.8 Upgrading a smart contract

Предполагается, что бы задеплоили chaincode на go, теперь обновляем его на javascript

    $ cd ../asset-transfer-basic/chaincode-javascript
    $ npm install
    $ cd ../../test-network

<br/>

    $ {
        export PATH=${PWD}/../bin:$PATH
        export FABRIC_CFG_PATH=$PWD/../config/
        export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
    }

    $ peer lifecycle chaincode package basic_2.tar.gz --path ../asset-transfer-basic/chaincode-javascript/ --lang node --label basic_2.0

<br/>

**Org1**

    $ {
        export CORE_PEER_TLS_ENABLED=true
        export CORE_PEER_LOCALMSPID="Org1MSP"
        export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
        export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
        export CORE_PEER_ADDRESS=localhost:7051
    }

    $ peer lifecycle chaincode install basic_2.tar.gz

    $ peer lifecycle chaincode queryinstalled

```
Installed chaincodes on peer:
Package ID: basic_1.0:4ec191e793b27e953ff2ede5a8bcc63152cecb1e4c3f301a26e22692c61967ad, Label: basic_1.0
Package ID: basic_2.0:58b24c58b05c1be32b529d4d64e9ec569bc4d7c7e7acb6810aeef3b3f1200a9c, Label: basic_2.0
```

    $ export NEW_CC_PACKAGE_ID=basic_2.0:58b24c58b05c1be32b529d4d64e9ec569bc4d7c7e7acb6810aeef3b3f1200a9c

    $ echo $NEW_CC_PACKAGE_ID

<br/>

    $ peer lifecycle chaincode approveformyorg \
        -o localhost:7050 \
        --ordererTLSHostnameOverride orderer.example.com \
        --channelID mychannel \
        --name basic \
        --version 2.0 \
        --package-id $NEW_CC_PACKAGE_ID \
        --sequence 2 \
        --tls \
        --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

**Org2**

    $ {
        export CORE_PEER_LOCALMSPID="Org2MSP"
        export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
        export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
        export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
        export CORE_PEER_ADDRESS=localhost:9051
    }

    $ peer lifecycle chaincode install basic_2.tar.gz

    $ peer lifecycle chaincode approveformyorg \
        -o localhost:7050 \
        --ordererTLSHostnameOverride orderer.example.com \
        --channelID mychannel \
        --name basic \
        --version 2.0 \
        --package-id $NEW_CC_PACKAGE_ID \
        --sequence 2 \
        --tls \
        --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem


    $ peer lifecycle chaincode checkcommitreadiness \
        --channelID mychannel \
        --name basic \
        --version 2.0 \
        --sequence 2 \
        --tls \
        --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem \
        --output json

```
{
	"approvals": {
		"Org1MSP": true,
		"Org2MSP": true
	}
}

```

<br/>

    $ peer lifecycle chaincode commit \
        -o localhost:7050 \
        --ordererTLSHostnameOverride orderer.example.com \
        --channelID mychannel \
        --name basic \
        --version 2.0 \
        --sequence 2 \
        --tls \
        --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem \
        --peerAddresses localhost:7051 \
        --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt \
        --peerAddresses localhost:9051 \
        --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt

<br/>

    $ docker ps

<br/>

    $ peer chaincode invoke \
        -o localhost:7050 \
        --ordererTLSHostnameOverride orderer.example.com \
        --tls \
        --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem \
        -C mychannel \
        -n basic \
        --peerAddresses localhost:7051 \
        --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt --peerAddresses localhost:9051 \
        --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt \
        -c '{"function":"createAsset","Args":["asset8","blue","16","Kelley","750"]}'

<br/>

**Для TypeScript и JavaScript**

    -c '{"function":"CreateAsset","Args":["asset8","blue","16","Kelley","750"]}'

<br/>

    $ peer chaincode query \
        -C mychannel \
        -n basic \
        -c '{"Args":["getAllAssets"]}'

<br/>

**Для TypeScript и JavaScript**

    $ peer chaincode query \
        -C mychannel \
        -n basic \
        -c '{"Args":["GetAllAssets"]}'
