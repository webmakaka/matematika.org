---
layout: page
title: Hyperleger Fabric - Writing Your First Application
description: Hyperleger Fabric - Writing Your First Application
keywords: Blockchain, Hyperleger, Fabric, Официальная документация, Writing Your First Application
permalink: /blockchain/hyperledger/fabric/official-docs/tutorials/writing-your-first-application/
---

# 7.2 Hyperleger Fabric - Writing Your First Application

Делаю:

26.08.2020

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

Работаем с fabric-samples/asset-transfer-basic

У нас есть:

- Приложение на javascript (asset-transfer-basic/application-javascript)
- Smart Contract на javascript, java, go, typescript (chaincode-\*)

<br/>

```

$ cd fabric-samples/test-network/

// This command will deploy the Fabric test network with two peers, an ordering service, and three certificate authorities (Orderer, Org1, Org2). Instead of using the cryptogen tool, we bring up the test network using Certificate Authorities, hence the -ca flag. Additionally, the org admin user registration is bootstrapped when the Certificate Authority is started. In a later step, we will show how the sample application completes the admin enrollment.

// Еще и не с первого раза запускается
$ ./network.sh down && ./network.sh up createChannel -c mychannel -ca

// This script uses the chaincode lifecycle to package, install, query installed chaincode, approve chaincode for both Org1 and Org2, and finally commit the chaincode.
$ ./network.sh deployCC -ccn basic -ccl javascript
```

В общем мы завершили работу над созданием окружения и задеплоили туда chaincode.

<br/>

### Sample application

    $ cd ~/projects/dev/hyperledger/
    $ cd fabric-samples/asset-transfer-basic/application-javascript/
    $ npm install

    $ node app.js

```
Loaded the network configuration located at /home/marley/projects/dev/hyperledger/fabric-samples/test-network/organizations/peerOrganizations/org1.example.com/connection-org1.json
Built a CA Client named ca-org1
Built a file system wallet at /home/marley/projects/dev/hyperledger/fabric-samples/asset-transfer-basic/application-javascript/wallet
Successfully enrolled admin user and imported it into the wallet
Successfully registered and enrolled user appUser and imported it into the wallet

--> Submit Transaction: InitLedger, function creates the initial set of assets on the ledger
*** Result: committed

--> Evaluate Transaction: GetAllAssets, function returns all the current assets on the ledger
*** Result: [
  {
    "Key": "asset1",
    "Record": {
      "ID": "asset1",
      "Color": "blue",
      "Size": 5,
      "Owner": "Tomoko",
      "AppraisedValue": 300,
      "docType": "asset"
    }
  },
  {
    "Key": "asset2",
    "Record": {
      "ID": "asset2",
      "Color": "red",
      "Size": 5,
      "Owner": "Brad",
      "AppraisedValue": 400,
      "docType": "asset"
    }
  },
  {
    "Key": "asset3",
    "Record": {
      "ID": "asset3",
      "Color": "green",
      "Size": 10,
      "Owner": "Jin Soo",
      "AppraisedValue": 500,
      "docType": "asset"
    }
  },
  {
    "Key": "asset4",
    "Record": {
      "ID": "asset4",
      "Color": "yellow",
      "Size": 10,
      "Owner": "Max",
      "AppraisedValue": 600,
      "docType": "asset"
    }
  },
  {
    "Key": "asset5",
    "Record": {
      "ID": "asset5",
      "Color": "black",
      "Size": 15,
      "Owner": "Adriana",
      "AppraisedValue": 700,
      "docType": "asset"
    }
  },
  {
    "Key": "asset6",
    "Record": {
      "ID": "asset6",
      "Color": "white",
      "Size": 15,
      "Owner": "Michel",
      "AppraisedValue": 800,
      "docType": "asset"
    }
  }
]

--> Submit Transaction: CreateAsset, creates new asset with ID, color, owner, size, and appraisedValue arguments
*** Result: committed

--> Evaluate Transaction: ReadAsset, function returns an asset with a given assetID
*** Result: {
  "ID": "asset13",
  "Color": "yellow",
  "Size": "5",
  "Owner": "Tom",
  "AppraisedValue": "1300"
}

--> Evaluate Transaction: AssetExists, function returns "true" if an asset with given assetID exist
*** Result: true

--> Submit Transaction: UpdateAsset asset1, change the appraisedValue to 350
*** Result: committed

--> Evaluate Transaction: ReadAsset, function returns "asset1" attributes
*** Result: {
  "ID": "asset1",
  "Color": "blue",
  "Size": "5",
  "Owner": "Tomoko",
  "AppraisedValue": "350"
}

--> Submit Transaction: UpdateAsset asset70, asset70 does not exist and should return an error
2020-08-26T00:03:28.980Z - error: [Transaction]: Error: No valid responses from any peers. Errors:
    peer=peer0.org2.example.com:9051, status=500, message=error in simulation: transaction returned with failure: Error: The asset asset70 does not exist
    peer=peer0.org1.example.com:7051, status=500, message=error in simulation: transaction returned with failure: Error: The asset asset70 does not exist
*** Successfully caught the error:
    Error: No valid responses from any peers. Errors:
    peer=peer0.org2.example.com:9051, status=500, message=error in simulation: transaction returned with failure: Error: The asset asset70 does not exist
    peer=peer0.org1.example.com:7051, status=500, message=error in simulation: transaction returned with failure: Error: The asset asset70 does not exist

--> Submit Transaction: TransferAsset asset1, transfer to new owner of Tom
*** Result: committed

--> Evaluate Transaction: ReadAsset, function returns "asset1" attributes
*** Result: {
  "ID": "asset1",
  "Color": "blue",
  "Size": "5",
  "Owner": "Tom",
  "AppraisedValue": "350"
}
```

<br/>

    // Посмотреть логи Certificate Authority
    $ docker logs -f ca_org1

<br/>

    $ docker ps

```


CONTAINER ID        IMAGE                                                                                                                                                                    COMMAND                  CREATED             STATUS              PORTS                              NAMES
c67c5a68e9b3        dev-peer0.org2.example.com-basic_1.0-3e6efb14d2337620b1a651cf59a421157d354148b96434fd6d0ee582388fa3d6-879b62fdf87aa5b9044e66b06c72682791aeb85f34d1f13b9a068fc2e25cb2a8   "docker-entrypoint.s…"   7 minutes ago       Up 7 minutes                                           dev-peer0.org2.example.com-basic_1.0-3e6efb14d2337620b1a651cf59a421157d354148b96434fd6d0ee582388fa3d6
4813cf3ed748        dev-peer0.org1.example.com-basic_1.0-3e6efb14d2337620b1a651cf59a421157d354148b96434fd6d0ee582388fa3d6-af17a230ea8c077372850026516c36e81ff58233c61f2dbbf351645ddd029787   "docker-entrypoint.s…"   7 minutes ago       Up 7 minutes                                           dev-peer0.org1.example.com-basic_1.0-3e6efb14d2337620b1a651cf59a421157d354148b96434fd6d0ee582388fa3d6
51e991ddea4b        hyperledger/fabric-peer:latest                                                                                                                                           "peer node start"        10 minutes ago      Up 10 minutes       0.0.0.0:7051->7051/tcp             peer0.org1.example.com
b883a5c2a889        hyperledger/fabric-peer:latest                                                                                                                                           "peer node start"        10 minutes ago      Up 10 minutes       7051/tcp, 0.0.0.0:9051->9051/tcp   peer0.org2.example.com
375da2adbe78        hyperledger/fabric-orderer:latest                                                                                                                                        "orderer"                10 minutes ago      Up 10 minutes       0.0.0.0:7050->7050/tcp             orderer.example.com
f1968ae0a534        hyperledger/fabric-ca:latest                                                                                                                                             "sh -c 'fabric-ca-se…"   10 minutes ago      Up 10 minutes       0.0.0.0:7054->7054/tcp             ca_org1
815512cb8e47        hyperledger/fabric-ca:latest                                                                                                                                             "sh -c 'fabric-ca-se…"   10 minutes ago      Up 10 minutes       7054/tcp, 0.0.0.0:9054->9054/tcp   ca_orderer
ecab55d688b7        hyperledger/fabric-ca:latest                                                                                                                                             "sh -c 'fabric-ca-se…"   10 minutes ago      Up 10 minutes       7054/tcp, 0.0.0.0:8054->8054/tcp   ca_org2
marley@workstation:~/projects/dev/hyperledger/fabric-samples/asset-transfer-basi

```

В общем скрипты отработали. Данные в Ledger из скрипта записались.

В приложении создался каталог wallet с админским и пользовательским ключами.

Нам предлагается изучить исходные коды и вывод результатов в консоль при выполнении приложения app.js
