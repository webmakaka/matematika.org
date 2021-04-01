---
layout: page
title: Hyperleger Fabric - Разбираемся с примерами из официальной документации
description: Hyperleger Fabric - Разбираемся с примерами из официальной документации
keywords: Blockchain, Hyperleger, Fabric, Официальная документация
permalink: /blockchain/hyperledger/fabric/official-docs/getting-started/
---

# Hyperleger Fabric - Разбираемся с примерами из официальной документации:

Делаю:

25.08.2020

## CHAPTER 5: Getting Started

    $ cd ~/projects/dev/hyperledger/
    $ curl -sSL https://raw.githubusercontent.com/hyperledger/fabric/master/scripts/bootstrap.sh | bash -s

    $ export PATH=~/projects/dev/hyperledger/fabric-samples/bin:$PATH
    $ export FABRIC_CFG_PATH=~/projects/dev/hyperledger/fabric-samples/config/

    $ cd ~/projects/dev/hyperledger/fabric-samples/test-network

    // This command creates a Fabric network that consists of two peer nodes, one ordering node. No channel is created
    $ ./network.sh up

    // Creating a channel
    $ ./network.sh createChannel -c channel1

<br/ >

    // The deployCC subcommand will install the asset-transfer (basic) chaincode on peer0.org1.example.com and peer0.org2.example.com and then deploy the chaincode on the channel specified using the channel flag (or mychannel if no channel is specified).

    $ ./network.sh deployCC

<br/>

### Interacting with the network

    # Environment variables for Org1
    $ {
      export CORE_PEER_TLS_ENABLED=true
      export CORE_PEER_LOCALMSPID="Org1MSP"
      export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
      export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
      export CORE_PEER_ADDRESS=localhost:7051
    }

<br/>

    $ peer chaincode invoke \
      -o localhost:7050 \
      --ordererTLSHostnameOverride orderer.example.com \
      --tls \
      --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n basic \
      --peerAddresses localhost:7051 \
      --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt \
      --peerAddresses localhost:9051 \
      --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt \
      -c '{"function":"InitLedger","Args":[]}'

<br/>

    $ peer chaincode query -C mychannel -n basic -c '{"Args":["GetAllAssets"]}'

<br/>

Use the following command to change the owner of an asset on the ledger by invoking the asset-transfer (basic) chaincode

    $ peer chaincode invoke \
      -o localhost:7050 \
      --ordererTLSHostnameOverride orderer.example.com \
      --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem \
      -C mychannel \
      -n basic \
      --peerAddresses localhost:7051 \
      --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt \
          --peerAddresses localhost:9051 \
          --tlsRootCertFiles ${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt \
      -c '{"function":"TransferAsset","Args":["asset6","Christopher"]}'

<br/>

В целях обучения, меняем переменные, чтобы обратиться к chaincode на Org2 peer.

    # Environment variables for Org2
    $ {
        export CORE_PEER_TLS_ENABLED=true
        export CORE_PEER_LOCALMSPID="Org2MSP"
        export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
        export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
        export CORE_PEER_ADDRESS=localhost:9051
    }

<br/>

    $ peer chaincode query -C mychannel -n basic -c '{"Args":["ReadAsset","asset6"]}'
