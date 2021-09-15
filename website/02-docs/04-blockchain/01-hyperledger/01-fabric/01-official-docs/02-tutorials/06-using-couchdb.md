---
layout: page
title: Hyperleger Fabric - Using CouchDB
description: Hyperleger Fabric - Using CouchDB
keywords: Blockchain, Hyperleger, Fabric, Официальная документация, Using CouchDB
permalink: /blockchain/hyperledger/fabric/official-docs/tutorials/using-couchdb/
---

# 7.6 Using CouchDB

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

Fabric supports two types of peer state databases. LevelDB is the default state database embedded in the peer node. LevelDB stores chaincode data as simple key-value pairs and only supports key, key range, and composite key queries. CouchDB is an optional, alternate state database that allows you to model data on the ledger as JSON and issue rich queries against data values rather than the keys. The CouchDB support also allows you to deploy indexes with your chaincode to make queries more efficient and enable you to query large datasets.

In order to leverage the benefits of CouchDB, namely content-based JSON queries, your data must be modeled in JSON format. You must decide whether to use LevelDB or CouchDB before setting up your network. Switching a peer from using LevelDB to CouchDB is not supported due to data compatibility issues. All peers on the network must use the same database type.

<br/>

### Start the network

    $ cd fabric-samples/asset-transfer-ledger-queries/chaincode-go/
    $ GO111MODULE=on go mod vendor
    $ cd ../../test-network

    // This will create two fabric peer nodes that use CouchDB as the state database. It will also create one ordering node and a single channel named mychannel
    $ ./network.sh down && ./network.sh up createChannel -s couchdb

<br/>

### Deploy the smart contract

    // deploy the smart contract to mychannel
    $ ./network.sh deployCC -ccn ledger -ccep "OR('Org1MSP.peer','Org2MSP.peer')"

Note that we are using the -ccep flag to deploy the smart contract with an endorsement policy of “OR(‘Org1MSP.peer’,’Org2MSP.peer’)”. This allows either organization to create an asset without receiving an endorsement from the other organization.

<br/>

**Verify index was deployed**

    $ docker logs peer0.org1.example.com 2>&1 | grep "CouchDB index"

<br/>

### Query the CouchDB State Database

    $ {
        export PATH=${PWD}/../bin:${PWD}:$PATH
        export FABRIC_CFG_PATH=$PWD/../config/
        export CORE_PEER_TLS_ENABLED=true
    }

<br/>

// Org1

    $ {
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
        --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem \
        -C mychannel \
        -n ledger \
        -c '{"Args":["CreateAsset","asset1","blue","5","tom","35"]}'

<br/>

    // query for all assets owned by tom
    $ peer chaincode query \
        -C mychannel \
        -n ledger \
        -c '{"Args":["QueryAssets", "{\"selector\":{\"docType\":\"asset\",\"owner\":\"tom\"}, \"use_index\":[\"_design/indexOwnerDoc\", \"indexOwner\"]}"]}' \
        | python -m json.tool

```
[
    {
        "docType": "asset",
        "ID": "asset1",
        "color": "blue",
        "size": 5,
        "owner": "tom",
        "appraisedValue": 35
    }
]
```

<br/>

### Use best practices for queries and indexes

```
    // Example one: query fully supported by the index
    $ export CHANNEL_NAME=mychannel

    $ peer chaincode query -C $CHANNEL_NAME -n ledger -c '{"Args":["QueryAssets", "{\"selector\":{\"docType\":\"asset\",\"owner\":\"tom\"}, \"use_index\":[\"indexOwnerDoc\", \"indexOwner\"]}"]}'
```

<br/>

```
    // Example two: query fully supported by the index with additional data
    $ peer chaincode query -C $CHANNEL_NAME -n ledger -c '{"Args":["QueryAssets", "{\"selector\":{\"docType\":\"asset\",\"owner\":\"tom\",\"color\":\"blue\"}, \"use_index\":[\"/indexOwnerDoc\", \"indexOwner\"]}"]}'
```

<br/>

```
    // Example three: query not supported by the index
    $ peer chaincode query -C $CHANNEL_NAME -n ledger -c '{"Args":["QueryAssets", "{\"selector\":{\"owner\":\"tom\"}, \"use_index\":[\"indexOwnerDoc\", \"indexOwner\"]}"]}'
```

<br/>

```
    // Example four: query with $or supported by the index
    $ peer chaincode query -C $CHANNEL_NAME -n ledger -c '{"Args":["QueryAssets", "{\"selector\":{\"$or\":[{\"docType\":\"asset\"},{\"owner\":\"tom\"}]}, \"use_index\":[\"indexOwnerDoc\", \"indexOwner\"]}"]}'
```

<br>

```
    // Example five: Query with $or not supported by the index
    $ peer chaincode query -C $CHANNEL_NAME -n ledger -c '{"Args":["QueryAssets", "{\"selector\":{\"$or\":[{\"docType\":\"asset\",\"owner\":\"tom\"},{\"color\":\"yellow\"}]}, \"use_index\":[\"indexOwnerDoc\", \"indexOwner\"]}"]}'
```

<br/>

### Query the CouchDB State Database With Pagination

    $ {
        export CORE_PEER_LOCALMSPID="Org1MSP"
        export CORE_PEER_TLS_ROOTCERT_FILE=${PWD}/organizations/peerOrganizations/org1.example.com/peers/peer0.org1.example.com/tls/ca.crt
        export CORE_PEER_MSPCONFIGPATH=${PWD}/organizations/peerOrganizations/org1.example.com/users/Admin@org1.example.com/msp
        export CORE_PEER_ADDRESS=localhost:7051
    }

<br/>

    $ peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n ledger -c '{"Args":["CreateAsset","asset2","yellow","5","tom","35"]}'

    $ peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile  ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n ledger -c '{"Args":["CreateAsset","asset3","green","6","tom","20"]}'

    $ peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n ledger -c '{"Args":["CreateAsset","asset4","purple","7","tom","20"]}'

    $ peer chaincode invoke -o localhost:7050 --ordererTLSHostnameOverride orderer.example.com --tls --cafile  ${PWD}/organizations/ordererOrganizations/example.com/orderers/orderer.example.com/msp/tlscacerts/tlsca.example.com-cert.pem -C mychannel -n ledger -c '{"Args":["CreateAsset","asset5","blue","8","tom","40"]}'

<br/>

    // Rich Query with index name explicitly specified and a page size of 3:
    $ peer chaincode query \
    -C mychannel \
    -n ledger \
    -c '{"Args":["QueryAssetsWithPagination", "{\"selector\":{\"docType\":\"asset\",\"owner\":\"tom\"}, \"use_index\":[\"_design/indexOwnerDoc\", \"indexOwner\"]}","","3"]}'

Ошибка у меня!

```
Error: endorsement failure during query. response: status:500 message:"GET_QUERY_RESULT failed: transaction ID: 3af1c3934a3156d33b5a1dab2ec2c2c1e110a9ed1f6e6d4980266b2dc80547ee: error handling CouchDB request. Error:invalid_bookmark,  Status Code:400,  Reason:Invalid bookmark value: \"3\""
```

    $ peer chaincode query -C $CHANNEL_NAME -n ledger -c '{"Args":["QueryAssetsWithPagination", "{\"selector\":{\"docType\":\"asset\",\"owner\":\"tom\"}, \"use_index\":[\"_design/indexOwnerDoc\", \"indexOwner\"]}","g1AAAABLeJzLYWBgYMpgSmHgKy5JLCrJTq2MT8lPzkzJBYqz5yYWJeWkGoOkOWDSOSANIFk2iCyIyVySn5uVBQAGEhRz","3"]}'

Здесь тоже ошибка.
В нее еще нужно поставить bookmark из предыдущего запроса.

    $ peer chaincode query -C $CHANNEL_NAME -n ledger -c '{"Args":["QueryAssetsWithPagination", "{\"selector\":{\"docType\":\"asset\",\"owner\":\"tom\"}, \"use_index\":[\"_design/indexOwnerDoc\", \"indexOwner\"]}","g1AAAABLeJzLYWBgYMpgSmHgKy5JLCrJTq2MT8lPzkzJBYqz5yYWJeWkmoKkOWDSOSANIFk2iCyIyVySn5uVBQAGYhR1","3"]}'

Можно даже не пробовать!

<br/>

### Update an Index

In order for an index to be updated, the original index definition must have included the design document ddoc attribute and an index name. To update an index definition, use the same index name but alter the index definition. Simply edit the index JSON file and add or remove fields from the index. Fabric only supports the index type JSON. Changing the index type is not supported. The updated index definition gets redeployed to the peer’s state database when the chaincode definition is committed to the channel. Changes to the index name or ddoc attributes will result in a new index being created and the original index remains unchanged in CouchDB until it is removed.

    // Index for docType, owner.
    // Example curl command line to define index in the CouchDB channel_chaincode database
    $ curl -i \
    -X POST \
    -H "Content-Type: application/json" \
    -d "{\"index\":{\"fields\":[\"docType\",\"owner\"]},
        \"name\":\"indexOwner\",
        \"ddoc\":\"indexOwnerDoc\",
        \"type\":\"json\"}" http://localhost:5984/mychannel_ledger/_index

<br/>

Опять ошибка

```
{"error":"unauthorized","reason":"You are not authorized to access this db."}
```

<br/>

### Delete an Index

    $ curl \
        -X DELETE http://localhost:5984/mychannel_ledger/_index/indexOwnerDoc/json/indexOwner \
        -H  "accept: */*" \
        -H  "Host: localhost:5984"

<br/>

Опять ошибка

```
{"error":"unauthorized","reason":"You are not authorized to access this db."}
```
