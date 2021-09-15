---
layout: page
title: Hyperleger Fabric - Using Private Data in Fabric
description: Hyperleger Fabric - Using Private Data in Fabric
keywords: Blockchain, Hyperleger, Fabric, Официальная документация, Using Private Data in Fabric
permalink: /blockchain/hyperledger/fabric/official-docs/tutorials/using-private-data-in-fabric/
---

# 7.4 Using Private Data in Fabric

Делаю:

27.08.2020

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

    $ cd fabric-samples/chaincode/marbles02_private/go/
    $ GO111MODULE=on go mod vendor
    $ cd ../../../test-network

    // This command will deploy a Fabric network consisting of a single channel named mychannel with two organizations (each maintaining one peer node) and an ordering service while using CouchDB as the state database.
    $ ./network.sh down && ./network.sh up createChannel -s couchdb

<br/>

// Org1

    $ {
        export PATH=${PWD}/../bin:$PATH
        export FABRIC_CFG_PATH=$PWD/../config/
        export CORE_PEER_TLS_ENABLED=true
        export CORE_PEER_LOCALMSPID="Org1MSP"
        export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
        export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
        export CORE_PEER_ADDRESS=localhost:7051
    }

<br/>

    // package the marbles private data chaincode
    $ peer lifecycle chaincode package marblesp.tar.gz \
        --path ../chaincode/marbles02_private/go/ \
        --lang golang \
        --label marblespv1


    // install the chaincode on the Org1 peer
    $ peer lifecycle chaincode install marblesp.tar.gz

// Org2

    $ {
        export CORE_PEER_LOCALMSPID="Org2MSP"
        export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
        export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
        export CORE_PEER_ADDRESS=localhost:9051
    }

    // install the chaincode on the Org2 peer
    $ peer lifecycle chaincode install marblesp.tar.gz

<br/>

### Approve the chaincode definition

Each channel member that wants to use the chaincode needs to approve a chaincode definition for their organization.
Since both organizations are going to use the chaincode in this tutorial, we need to approve the chaincode definition for both Org1 and Org2 using the peer lifecycle chaincode approveformyorg command. The chaincode definition also includes the private data collection definition that accompanies the marbles02_private sample. We will provide the path to the collections JSON file using the --collections-config flag.

<br/>

    $ peer lifecycle chaincode queryinstalled

    $ export CC_PACKAGE_ID=marblespv1:3f19c08fe56c85fb85c346330bf96a7ab9ecd8ac970d6c7c81e1c5dc84a1a895

    $ export ORDERER_CA=${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem

// Org1

    $ {
        export CORE_PEER_LOCALMSPID="Org1MSP"
        export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
        export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
        export CORE_PEER_ADDRESS=localhost:7051
    }

    $ peer lifecycle chaincode approveformyorg \
    -o localhost:7050 \
    --ordererTLSHostnameOverride orderer.example.com \
    --channelID mychannel \
    --name marblesp \
    --version 1.0 \
    --collections-config ../chaincode/marbles02_private/collections_config.json \
    --signature-policy "OR('Org1MSP.member','Org2MSP.member')" --package-id $CC_PACKAGE_ID \
    --sequence 1 \
    --tls \
    --cafile $ORDERER_CA

// Org2

    $ {
        export CORE_PEER_LOCALMSPID="Org2MSP"
        export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
        export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
        export CORE_PEER_ADDRESS=localhost:9051
    }

    $ peer lifecycle chaincode approveformyorg \
        -o localhost:7050 \
        --ordererTLSHostnameOverride orderer.example.com \
        --channelID mychannel \
        --name marblesp \
        --version 1.0 \
        --collections-config ../chaincode/marbles02_private/collections_config.json \
        --signature-policy "OR('Org1MSP.member','Org2MSP.member')" \
        --package-id $CC_PACKAGE_ID \
        --sequence 1 \
        --tls \
        --cafile $ORDERER_CA

<br/>

### Commit the chaincode definition

// commit the definition of the marbles private data chaincode to the channe

    $ {
        export ORDERER_CA=${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem
        export ORG1_CA=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
        export ORG2_CA=${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
    }

<br/>

    $ peer lifecycle chaincode commit \
        -o localhost:7050 \
        --ordererTLSHostnameOverride orderer.example.com \
        --channelID mychannel \
        --name marblesp \
        --version 1.0 \
        --sequence 1 \
        --collections-config ../chaincode/marbles02_private/collections_config.json \
        --signature-policy "OR('Org1MSP.member','Org2MSP.member')" \
        --tls \
        --cafile $ORDERER_CA \
        --peerAddresses localhost:7051 \
        --tlsRootCertFiles $ORG1_CA \
        --peerAddresses localhost:9051 \
        --tlsRootCertFiles $ORG2_CA

<br/>

### Store private data

    $ {
        export CORE_PEER_LOCALMSPID="Org1MSP"
        export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
        export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
        export CORE_PEER_ADDRESS=localhost:7051
    }

<br/>

Invoke the marbles initMarble function which creates a marble with private data — name marble1 owned by tom with a color blue, size 35 and price of 99. Recall that private data price will be stored separately from the private data name, owner, color, size. For this reason, the initMarble function calls the PutPrivateData() API twice to persist the private data, once for each collection. Also note that the private data is passed using the --transient flag. Inputs passed as transient data will not be persisted in the transaction in order to keep the data private. Transient data is passed as binary data and therefore when using CLI it must be base64 encoded. We use an environment variable to capture the base64 encoded value, and use tr command to strip off the problematic newline characters that linux base64 command adds.

    $ export MARBLE=$(echo -n "{\"name\":\"marble1\",\"color\":\"blue\",\"size\":35,\"owner\":\"tom\",\"price\":99}" | base64 | tr -d \\n)

    $ peer chaincode invoke \
        -o localhost:7050 \
        --ordererTLSHostnameOverride orderer.example.com \
        --tls \
        --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem \
        -C mychannel \
        -n marblesp \
        -c '{"Args":["InitMarble"]}' \
        --transient "{\"marble\":\"$MARBLE\"}"

<br/>

### Query the private data as an authorized peer

Our collection definition allows all members of Org1 and Org2 to have the name, color, size, owner private data in their side database, but only peers in Org1 can have the price private data in their side database. As an authorized peer in Org1, we will query both sets of private data.

    $ peer chaincode query -C mychannel -n marblesp -c '{"Args":["ReadMarble","marble1"]}' | python -m json.tool

```
{
    "docType": "Marble",
    "name": "marble1",
    "color": "blue",
    "size": 35,
    "owner": "tom"
}
```

    $ peer chaincode query -C mychannel -n marblesp -c '{"Args":["ReadMarblePrivateDetails","marble1"]}' | python -m json.tool

```
{
    "docType": "MarblePrivateDetails",
    "name": "marble1",
    "price": 99
}

```

<br/>

### Query the private data as an unauthorized peer

    $ {
        export CORE_PEER_LOCALMSPID="Org2MSP"
        export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org2.example.com/peers/peer0.org2.example.com/tls/ca.crt
        export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org2.example.com/users/Admin@org2.example.com/msp
        export CORE_PEER_ADDRESS=localhost:9051
    }


    $ peer chaincode query -C mychannel -n marblesp -c '{"Args":["ReadMarble","marble1"]}' | python -m json.tool

```
{
    "docType": "Marble",
    "name": "marble1",
    "color": "blue",
    "size": 35,
    "owner": "tom"
}

```

<br/>

    $ peer chaincode query -C mychannel -n marblesp -c '{"Args":["ReadMarblePrivateDetails","marble1"]}'

```
Error: endorsement failure during query. response: status:500 message:"failed to read from marble details GET_STATE failed: transaction ID: a6a7911205df61f71fe413353404973faf67a4a5715f6003c07a6a538c838de7: tx creator does not have read access permission on privatedata in chaincodeName:marblesp collectionName: collectionMarblePrivateDetails"
```

<br/>

### Purge Private Data

    $ {
        export CORE_PEER_LOCALMSPID="Org1MSP"
        export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
        export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
        export CORE_PEER_ADDRESS=localhost:7051
    }

<br/>

Отдельный терминал для docker

    $ docker logs peer0.org1.example.com 2>&1 | grep -i -a -E 'private|pvt|privdata'

<br/>

    $ peer chaincode query -C mychannel -n marblesp -c '{"Args":["ReadMarblePrivateDetails","marble1"]}'

The price data is still in the private data ledger.

    $ export MARBLE=$(echo -n "{\"name\":\"marble2\",\"color\":\"blue\",\"size\":35,\"owner\":\"tom\",\"price\":99}" | base64 | tr -d \\n)
    peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n marblesp -c '{"Args":["InitMarble"]}' --transient "{\"marble\":\"$MARBLE\"}"

<br/>

    $ docker logs peer0.org1.example.com 2>&1 | grep -i -a -E 'private|pvt|privdata'

<br/>

    $ peer chaincode query -C mychannel -n marblesp -c '{"Args":["ReadMarblePrivateDetails","marble1"]}'

Ничег не изменилось.

<br/>

Transfer marble2 to “joe” by running the following command. This transaction will add a second new block on the chain.

<br/>

    $ export MARBLE_OWNER=$(echo -n "{\"name\":\"marble2\",\"owner\":\"joe\"}" | base64 | tr -d \\n)

    $ peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n marblesp -c '{"Args":["TransferMarble"]}' --transient "{\"marble_owner\":\"$MARBLE_OWNER\"}"

<br/>

    $ docker logs peer0.org1.example.com 2>&1 | grep -i -a -E 'private|pvt|privdata'

<br/>

    $ peer chaincode query -C mychannel -n marblesp -c '{"Args":["ReadMarblePrivateDetails","marble1"]}'

Ничего не поменялось.

Transfer marble2 to “tom” by running the following command. This transaction will create a third new block on the chain.

    $ export MARBLE_OWNER=$(echo -n "{\"name\":\"marble2\",\"owner\":\"tom\"}" | base64 | tr -d \\n)

    $ peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n marblesp -c '{"Args":["TransferMarble"]}' --transient "{\"marble_owner\":\"$MARBLE_OWNER\"}"

<br/>

    $ docker logs peer0.org1.example.com 2>&1 | grep -i -a -E 'private|pvt|privdata'

<br/>

    $ peer chaincode query -C mychannel -n marblesp -c '{"Args":["ReadMarblePrivateDetails","marble1"]}'

You should still be able to see the price data.

<br/>

Finally, transfer marble2 to “jerry” by running the following command. This transaction will create a fourth new block on the chain. The price private data should be purged after this transaction.

    $ export MARBLE_OWNER=$(echo -n "{\"name\":\"marble2\",\"owner\":\"jerry\"}" | base64 | tr -d \\n)

    $ peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n marblesp -c '{"Args":["TransferMarble"]}' --transient "{\"marble_owner\":\"$MARBLE_OWNER\"}"


    $ docker logs peer0.org1.example.com 2>&1 | grep -i -a -E 'private|pvt|privdata'

    $ peer chaincode query -C mychannel -n marblesp -c '{"Args":["ReadMarblePrivateDetails","marble1"]}'

```
Error: endorsement failure during query. response: status:500 message:"marble1 does not exist"
```

Because the price data has been purged, you should no longer be able to see it.

<br/>

### Рекомендуют к просмотру видео:

https://www.youtube.com/watch?v=qyjDi93URJE
