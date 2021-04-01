---
layout: page
title: Nakul Shah - Blockchain for Business with Hyperledger Fabric - Frameworks, Network Topologies, and Modeling
description: Nakul Shah - Blockchain for Business with Hyperledger Fabric - Frameworks, Network Topologies, and Modeling
keywords: Blockchain, Hyperleger, Fabric, Nakul Shah, Blockchain for Business with Hyperledger Fabric, Frameworks, Network Topologies, and Modeling
permalink: /books/blockchain/hyperledger/fabric/blockchain-for-business-with-hyperledger-fabric/frameworks-network-topologies-and-modeling/en/
---

# [Nakul Shah] Blockchain for Business with Hyperledger Fabric: A complete guide to enterprise Blockchain implementation using Hyperledger Fabric [ENG, 2019]

<br/>

## Frameworks, Network Topologies, and Modeling

<br/>

**Configuration files:**

    a. configtx.yml
    b. crypto-config.yml
    c. docker-compose files

<br/>

### Подготовка окружения

    // Удаляю все контейнеры и имиджи докера, которые есть в системе.
    $ {
        docker stop $(docker ps -aq)
        docker rm $(docker ps -aq)
        docker system prune -a
    }

    $ mkdir -p ~/projects/dev/hyperledger

    $ cd ~/projects/dev/hyperledger/
    $ curl -sSL https://raw.githubusercontent.com/hyperledger/fabric/master/scripts/bootstrap.sh | bash -s 1.4.8

<br/>

What does this script do?

a. It clones the Fabric sample repo we discussed earlier, the same you have in your editor.

b. Then it pulls the binaries that we discussed, like confitxgen, cryptogen, and so on, and puts them in the bin folder inside the fabric-sample folder cloned in the last step.

c. It then pulls all the images and tags them with the version that we provided, i.e., 1.4.0.

<!--
    $ git clone https://github.com/hyperledger/fabric-samples.git
-->

<br/>

$ cd fabric-samples/bin/
$ ls

```
configtxgen    cryptogen  fabric-ca-client  get-docker-images.sh  orderer
configtxlator  discover   fabric-ca-server  idemixgen             peer
```

<br/>

    $ pwd
    /home/marley/projects/dev/hyperledger/fabric-samples/bin

<br/>

    $ export PATH=/home/marley/projects/dev/hyperledger/fabric-samples/bin/:$PATH

<br/>

    // Проверка
    $ cryptogen --help

<br/>

### 2. Generate crypto material and channel configuration files

We need to write crypto-config.yaml

    $ cd ~/projects/dev/hyperledger/fabric-samples/first-network
    $ cryptogen generate --config=./crypto-config.yaml

Now you should see a folder crypto-config generated in the first-network directory.

<br/>

### 3. Start the network using Docker Compose YAML

Next, we need to write configtx.yaml to generate the channel-related artefacts in YAML.

    // we need to generate the artefacts for the orderer system channel, which is required for the orderer to work properly
    $ configtxgen \
        -profile TwoOrgsOrdererGenesis \
        -channelID byfn-sys-channel \
        -outputBlock ./channel-artifacts/genesis.block

    $ ls ./channel-artifacts/genesis.block

<br/>

    // generate channel.tx file using the configuration defined in twoOrgsChannel
    $ configtxgen \
        -profile TwoOrgsChannel \
        -outputCreateChannelTx ./channel-artifacts/channel.tx \
        -channelID mychannel

<br/>

    // generate a TX file for the anchor peers in the channel

    // Org1
    $ configtxgen \
        -profile TwoOrgsChannel \
        -outputAnchorPeersUpdate ./channel-artifacts/Org1MSPanchors.tx \
        -channelID mychannel \
        -asOrg Org1MSP

    // Org2
    $ configtxgen \
        -profile TwoOrgsChannel \
        -outputAnchorPeersUpdate ./channel-artifacts/Org2MSPanchors.tx \
        -channelID mychannel \
        -asOrg Org2MSP

<br/>

    $ ls ./channel-artifacts/
    channel.tx  genesis.block  Org1MSPanchors.tx  Org2MSPanchors.tx

<br/>

### 4. Start the network using Docker Compose files.

    $ IMAGE_TAG=1.4.8 docker-compose -f docker-compose-cli.yaml up -d

<br/>

Создаются следующие peer

```
peer0.org1.example.com
peer1.org1.example.com
peer0.org2.example.com
peer1.org2.example.com
```

-

orderer.example.com

<br/>

    // При необходимости остановить docker-compose и удалить все
    // $ docker-compose -f docker-compose-cli.yaml down --volumes

<br/>

**Для инсталляция chaincode из примеров на разных языках:**

    // golang - ok
    $ docker exec cli scripts/script.sh mychannel 3 golang 10 true

    // node - ошибка
    $ docker exec cli scripts/script.sh mychannel 3 node 10 true

    // java - ошибка
    $ docker exec cli scripts/script.sh mychannel 3 node 10 true

<br/>

```
Error: could not assemble transaction, err proposal response was not successful, error code 500, msg timeout expired while starting chaincode mycc:1.0 for transaction
!!!!!!!!!!!!!!! Chaincode instantiation on peer0.org2 on channel 'testchannel' failed !!!!!!!!!!!!!!!!
========= ERROR !!! FAILED to execute End-2-End Scenario ===========
```

<br/>

Похоже или я неправильно что-то делаю, или тестовые примеры работают только с кодом на языке GoLang.

Если разберусь как все это работает, может удастся победить.

<!--

https://hyperledger-fabric.readthedocs.io/en/release-1.4/build_network.html

-->

<br/>

### Install Node.js chaincode (Не заработало!)

Now let’s try to run install chaincode from the Node.js chaincode available in chaincode/chaincode_example02/node/

Порты нужно смотреть в docker ps.

    $ docker exec -it cli bash

<!--

# apt update -y
# apt install -y  net-tools iputils-ping

# telnet peer0.org1.example.com 7051

-->

Вот такие у нас переменные окружения по умолчанию. P.S. Индус мудак и тот кто не проверял его писанину!

<br/>

**peer0.org1.example.com:7051**

    # echo $CORE_PEER_LOCALMSPID
    Org1MSP

    # echo $CORE_PEER_ADDRESS
    peer0.org1.example.com:7051

    # echo $CORE_PEER_TLS_ROOTCERT_FILE
    /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt

    # echo $CORE_PEER_MSPCONFIGPATH
    /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp

<br/>

    # peer chaincode install -n testcc -v 1.1 -l node -p /opt/gopath/src/github.com/chaincode/chaincode_example02/node/

<br/>

**peer1.org1.example.com:8051**

    # export CORE_PEER_ADDRESS=peer1.org1.example.com:8051

    # export CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org1.example.com/peers/peer1.org1.example.com/tls/ca.crt

    # peer chaincode install -n testcc -v 1.1 -l node -p /opt/gopath/src/github.com/chaincode/chaincode_example02/node/

<br/>

**peer0.org2.example.com:9051**

    # export CORE_PEER_LOCALMSPID="Org2MSP"

    # export CORE_PEER_MSPCONFIGPATH=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp

    # export CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt

<br/>

    # export CORE_PEER_ADDRESS=peer0.org2.example.com:9051

    # peer chaincode install -n testcc -v 1.1 -l node -p /opt/gopath/src/github.com/chaincode/chaincode_example02/node/

<br/>

**peer1.org2.example.com:10051**

    # export CORE_PEER_TLS_ROOTCERT_FILE=/opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/peerOrganizations/org2.example.com/peers/peer1.org2.example.com/tls/ca.crt

    # export CORE_PEER_ADDRESS=peer1.org2.example.com:10051

    # peer chaincode install -n testcc -v 1.1 -l node -p /opt/gopath/src/github.com/chaincode/chaincode_example02/node/

<br/>

### Instantiate the chaincode (Не заработало)

    # ctrl ^D
    $ docker exec -it cli bash

<br/>

    # export FABRIC_LOGGING_SPEC=info

    # peer chaincode instantiate \
        -o orderer.example.com:7050 \
        --tls true \
        --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem \
        -C mychannel \
        -n testcc \
        -l node \
        -v 1.1 \
        -c '{"Args":["init","a","100","b","200"]}' \
        -P "AND ('Org1MSP.peer','Org2MSP.peer')"

<!--


byfn-sys-channel



-C testchannel \


peer channel create -c mychannel -f ./channel-artifacts/channel.tx --orderer orderer.example.com:7050

    // Думаю должна возвращать OK
    # peer channel list

-->

<br/>

Ошибка!!!

```
Error: error endorsing chaincode: rpc error: code = Unknown desc = access denied: channel [mychannel] creator org [Org1MSP]
```

Смотрю, что в логах контейнера.

```
WARN 037 channel [mychannel]: MSP error: channel doesn't exist
```

<!--

# peer channel join -b ./channel-artifacts/genesis.block
2020-08-20 03:41:13.923 UTC [channelCmd] InitCmdFactory -> INFO 001 Endorser and orderer connections initialized
Error: proposal failed (err: bad proposal response 500: "JoinChain" for chainID = byfn-sys-channel failed because of validation of configuration block, because of Invalid configuration block, missing Application configuration group)


-->

<br/>

Потом нужно будет:

**Повторить для:**

    # CORE_PEER_ADDRESS=peer0.org1.example.com:7051

    # CORE_PEER_ADDRESS=peer1.org1.example.com:XXXX

    # CORE_PEER_ADDRESS=peer0.org2.example.com:XXXX

    # CORE_PEER_ADDRESS=peer1.org2.example.com:XXXX

<br/>

### 3. Query the chaincode:

    # peer chaincode query -C mychannel -n testcc -c '{"Args":["query","a"]}'

This should give output as 100.

<br/>

### 4. Upgrade the chaincode:

    # peer chaincode install -n testcc -v 1.3 -l node -p /opt/gopath/src/github.com/chaincode/chaincode_example02/node/

For testing instead of installing in all the nodes, we will upgrade only in peer0 by using the below command:

    # peer chaincode upgrade -o orderer.example.com:7050 --tls true --cafile /opt/gopath/src/github.com/hyperledger/fabric/peer/crypto/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n testcc -l node -v 1.3 -c '{"Args":["init","a","90","b","200"]}' -P "AND ('Org1MSP.peer','Org2MSP.peer')"

Ошибка !!!

    # peer chaincode query -C mychannel -n testcc -c '{[" a"]}'

Наверное должно быть

    # peer chaincode query -C mychannel -n testcc -c '{"Args":["a"]}'

This will now show 90.
