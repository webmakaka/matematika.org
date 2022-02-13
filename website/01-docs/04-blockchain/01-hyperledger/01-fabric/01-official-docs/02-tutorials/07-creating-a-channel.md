---
layout: page
title: Hyperleger Fabric - Creating a channel
description: Hyperleger Fabric - Creating a channel
keywords: Blockchain, Hyperleger, Fabric, Официальная документация, Creating a channel
permalink: /blockchain/hyperledger/fabric/official-docs/tutorials/creating-a-channel/
---

# 7.7 Creating a channel

Делаю:

28.08.2020

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

In order to create and transfer assets on a Hyperledger Fabric network, an organization needs to join a channel. Channels are a private layer of communication between specific organizations and are invisible to other members of the network. Each channel consists of a separate ledger that can only be read and written to by channel members, who are allowed to join their peers to the channel and receive new blocks of transactions from the ordering service. While the peers, nodes, and Certificate Authorities form the physical infrastructure of the network, channels are the process by which organizations connect with each other and interact.

<br/>

### Setting up the configtxgen tool

Channels are created by building a channel creation transaction and submitting the transaction to the ordering service. The channel creation transaction specifies the initial configuration of the channel and is used by the ordering service to write the channel genesis block. While it is possible to build the channel creation transaction file manually, it is easier to use the configtxgen tool. The tool works by reading a configtx.yaml file that defines the configuration of your channel, and then writing the relevant information into the channel creation transaction. Before we discuss the configtx.yaml file in the next section, we can get started by downloading and setting up the configtxgen tool.

    $ cd fabric-samples/test-network
    $ export PATH=${PWD}/../bin:$PATH
    $ export FABRIC_CFG_PATH=${PWD}/configtx

<br/>

### Start the network

    $ ./network.sh down && ./network.sh up

Our instance of the test network was deployed without creating an application channel.

<br/>

### Creating an application channel

    $ configtxgen -profile TwoOrgsChannel -outputCreateChannelTx ./channel-artifacts/channel1.tx -channelID channel1

<br/>

    $ export FABRIC_CFG_PATH=$PWD/../config/

<br/>

    $ {
        export CORE_PEER_TLS_ENABLED=true
        export CORE_PEER_LOCALMSPID="Org1MSP"
        export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
        export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
        export CORE_PEER_ADDRESS=localhost:7051
    }

<br/>

    // create the channel
    $ peer channel create \
    -o localhost:7050  \
    --ordererTLSHostnameOverride orderer.example.com \
    -c channel1 \
    -f ./channel-artifacts/channel1.tx \
    --outputBlock ./channel-artifacts/channel1.block \
    --tls \
    --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

<br/>

### Join peers to the channel

// Org1

    $ peer channel join -b ./channel-artifacts/channel1.block

    // verify that the peer has joined the channel
    $ peer channel getinfo -c channel1

// Org2

    $ {
        export CORE_PEER_TLS_ENABLED=true
        export CORE_PEER_LOCALMSPID="Org2MSP"
        export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
        export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
        export CORE_PEER_ADDRESS=localhost:9051
    }

<br/>

    // get the genesis block for Org2
    $ peer channel fetch 0 ./channel-artifacts/channel_org2.block -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com -c channel1 --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

<br/>

    $ peer channel join -b ./channel-artifacts/channel_org2.block

<br/>

### Set anchor peers

We will use the configtxlator tool to update
the channel configuration and select an anchor peer for Org1 and Org2.

// Org1

    $ {
        export FABRIC_CFG_PATH=$PWD/../config/
        export CORE_PEER_TLS_ENABLED=true
        export CORE_PEER_LOCALMSPID="Org1MSP"
        export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
        export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
        export CORE_PEER_ADDRESS=localhost:7051
    }

<br/>

    // fetch the channel configuration
    $ peer channel fetch config channel-artifacts/config_block.pb -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com -c channel1 --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

<br/>

    $ cd channel-artifacts

<br/>

Утилита jq должна быть установлена для выполнения следующих команд.

<br/>

    $ configtxlator proto_decode --input config_block.pb --type common.Block --output config_block.json

    $ jq .data.data[0].payload.data.config config_block.json > config.json

    $ cp config.json config_copy.json

<br/>

    // add the Org1 anchor peer to the channel configuration
    $ jq '.channel_group.groups.Application.groups.Org1MSP.values += {"AnchorPeers":{"mod_policy": "Admins","value":{"anchor_peers": [{"host": "peer0.org1.example.com","port": 7051}]},"version": "0"}}' config_copy.json > modified_config.json

<br/>

Convert both the original and modified channel configurations back into protobuf format and calculate the difference between them

    $ configtxlator proto_encode --input config.json --type common.Config --output config.pb

    $ configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb

    $ configtxlator compute_update --channel_id channel1 --original config.pb --updated modified_config.pb --output config_update.pb

<br/>

    $ configtxlator proto_decode --input config_update.pb --type common.ConfigUpdate --output config_update.json

    $ echo '{"payload":{"header":{"channel_header":{"channel_id":"channel1", "type":2}},"data":{"config_update":'$(cat config_update.json)'}}}' | jq . > config_update_in_envelope.json

    $ configtxlator proto_encode --input config_update_in_envelope.json --type common.Envelope --output config_update_in_envelope.pb

<br/>

    $ cd ../

<br/>

    // add the anchor peer by providing the new peer
    $ peer channel update \
        -f channel-artifacts/config_update_in_envelope.pb \
        -c channel1 \
        -o localhost:7050  \
        --ordererTLSHostnameOverride orderer.example.com \
        --tls \
        --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

<br/>

// Org2

set the anchor peers for Org2

    $ {
        export CORE_PEER_TLS_ENABLED=true
        export CORE_PEER_LOCALMSPID="Org2MSP"
        export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
        export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
        export CORE_PEER_ADDRESS=localhost:9051
    }


    // pull the latest channel configuration block, which is now the second block on the channel
    $ peer channel fetch config channel-artifacts/config_block.pb -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com -c channel1 --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

<br/>

    $ cd channel-artifacts

    $ configtxlator proto_decode --input config_block.pb --type common.Block --output config_block.json

    $ jq .data.data[0].payload.data.config config_block.json > config.json

    $ cp config.json config_copy.json

<br/>

    // Add the Org2 peer that is joined to the channel as the anchor peer in the channel configuration:
    $ jq '.channel_group.groups.Application.groups.Org2MSP.values += {"AnchorPeers":{"mod_policy": "Admins","value":{"anchor_peers": [{"host": "peer0.org2.example.com","port": 9051}]},"version": "0"}}' config_copy.json > modified_config.json

<br/>

We can now convert both the original and updated channel configurations back into protobuf format and calculate the difference between them.

    $ configtxlator proto_encode --input config.json --type common.Config --output config.pb

    $ configtxlator proto_encode --input modified_config.json --type common.Config --output modified_config.pb

    $ configtxlator compute_update --channel_id channel1 --original config.pb --updated modified_config.pb --output config_update.pb

Wrap the configuration update in a transaction envelope to create the channel configuration update transaction:

    $ configtxlator proto_decode --input config_update.pb --type common.ConfigUpdate --output config_update.json

    $ echo '{"payload":{"header":{"channel_header":{"channel_id":"channel1", "type":2}},"data":{"config_update":'\$(cat config_update.json)'}}}' | jq . > config_update_in_envelope.json

    $ configtxlator proto_encode --input config_update_in_envelope.json --type common.Envelope --output config_update_in_envelope.pb

<br/>

    $ cd ..

Update the channel and set the Org2 anchor peer by issuing the following command:

    $ peer channel update -f channel-artifacts/config_update_in_envelope.pb -c channel1 -o localhost:7050  --ordererTLSHostnameOverride orderer.example.com --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

    // confirm that the channel has been updated successfully
    $ peer channel getinfo -c channel1

<br/>

### Deploy a chaincode to the new channel

    $ ./network.sh deployCC -ccn basic -c channel1 -cci InitLedger

    // confirm the data was added with the below query
    $ peer chaincode query \
        -C channel1 \
        -n basic \
        -c '{"Args":["getAllAssets"]}' \
        | python -m json.tool

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
