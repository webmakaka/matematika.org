---
layout: page
title: Nakul Shah - Blockchain for Business with Hyperledger Fabric - Chaincode in Hyperledger Fabric
description: Nakul Shah - Blockchain for Business with Hyperledger Fabric - Chaincode in Hyperledger Fabric
keywords: Blockchain, Hyperleger, Fabric, Nakul Shah, Blockchain for Business with Hyperledger Fabric, Chaincode in Hyperledger Fabric
permalink: /books/blockchain/hyperledger/fabric/blockchain-for-business-with-hyperledger-fabric/chaincode-in-hyperledger-fabric/en/
---

# [Nakul Shah] Blockchain for Business with Hyperledger Fabric: A complete guide to enterprise Blockchain implementation using Hyperledger Fabric [ENG, 2019]

<br/>

## Chaincode in Hyperledger Fabric

**Код лежит здесь (чтобы не копипастить из pdf):**  
https://github.com/SateDev/hyperledger_chaincode_asset_registry

<br/>

The requirements are simple — we need to write a smart contract that will register the property details, change the ownership, query the property details, and execute a few other operations on the properties.

<br/>

We are going to build an asset registry chaincode application that has the following common attributes linked to a property:

    • Value of the property
    • Owner of the property
    • Property area
    • Location of the property
    • Type of property

<br/>

Now, let’s understand what we want this chaincode to do:

1. Create an initial ledger with some initial property details.
2. Provide functionality to create more properties using the “Create a Property” function.
3. Query the ledger for a property on the basis of property ID.
4. Allow querying all the properties on the basis of indexes.
5. Allow changing the owner of a property.

<br/>

https://fabric-shim.github.io/release-1.4/fabric-shim.ChaincodeStub.html

<br/>

### Deploying and testing the chaincode

<br/>

    $ npm i -g typescript

<br/>

Терминал 1:

    $ {
        docker stop $(docker ps -aq)
        docker rm $(docker ps -aq)
        docker system prune -a
    }

    $ cd /home/marley/projects/dev/hyperledger

    $ curl -sSL https://raw.githubusercontent.com/hyperledger/fabric/master/scripts/bootstrap.sh | bash -s 1.4.8

    $ cd fabric-samples/chaincode

    $ mkdir asset-registry
    $ cd asset-registry

    $ git clone https://github.com/SateDev/hyperledger_chaincode_asset_registry .

<br/>

    $ cd /home/marley/projects/dev/hyperledger/fabric-samples/chaincode-docker-devmode
    $ docker-compose -f docker-compose-simple.yaml up

Не нужно ждать, можно сразу переключаться на другой терминал.

<br/>

Терминал 2:

    $ docker exec -it chaincode bash

    # cd asset-registry
    # npm install
    # npm run build
    $ npm rebuild
    # npm run start -- --peer.address "peer:7052" "--chaincode-id-name" "asset-cc:0.1"

```
Command succeeded

2020-08-20T01:08:46.247Z INFO [lib/handler.js] Successfully registered with peer node. State transferred to "established"
2020-08-20T01:08:46.249Z INFO [lib/handler.js] Successfully established communication with peer node. State transferred to "ready"


```

Our chaincode is now listening for any requests for peers and bash containers in
order to run operations.

<br/>

### Installing, instantiating, and invoking the chaincode:

Терминал 3:

    $ docker exec -it cli bash

    # peer chaincode install -p chaincode/asset-registry/ -n asset-cc -l node -v 0.1

```
2020-08-18 03:43:28.457 UTC [chaincodeCmd] install -> INFO 05c Installed remotely response:<status:200 payload:"OK" >
```

<br/>

    // Init
    # peer chaincode instantiate \
        -n asset-cc \
        -v 0.1 \
        -l node \
        -c '{"Args":["initLedger"]}' \
        -C myc

Ok

<br/>

    // Найти по ID
    # peer chaincode query \
        -n asset-cc \
        -c '{"Args":["queryAsset", "P100001"]}' \
        -C myc

Ok

<br/>

    // createProperty:
    # peer chaincode invoke \
        -n asset-cc \
        -c '{"Args":["createProperty", "P100003", "howbe", "2838", "somehg", "asdf", "2323", "someowner"]}' \
        -C myc

Ok

    // change the owner
    # peer chaincode invoke \
        -n asset-cc \
        -c '{"Args":["changePropertyOwner", "P100003", "marley"]}' \
        -C myc

Ok

    # peer chaincode query \
        -n asset-cc \
        -c '{"Args":["queryAsset", "P100003"]}' \
        -C myc

Ok

<br/>

Дальше у меня было не все хорошо с расширением для vscode.
