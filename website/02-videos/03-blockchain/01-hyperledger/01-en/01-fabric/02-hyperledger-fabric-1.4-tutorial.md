---
layout: page
title: Horea Porutiu - Hyperledger Fabric 1.4 Tutorial
description: Horea Porutiu - Hyperledger Fabric 1.4 Tutorial
keywords: Blockchain, Hyperleger, Fabric, Tutorial, 1.4, YouTube, Horea Porutiu
permalink: /videos/blockchain/hyperledger/en/fabric/hyperledger-fabric-1.4-tutorial/
---

# [YouTube, Horea Porutiu] Hyperledger Fabric 1.4 Tutorial [ENG, 2019]

https://www.youtube.com/watch?v=2moCQeHCx-g&list=PLVztKpIRxvQU-deHiJYmSLK83fEGQt96k

<br/>

**Сначала какие-то неинтересные проекты. Последние 2 норм.**

<br/>

    // Нужна версия node 8 или 10. С 12 некоторые пакеты не компилятся.

    -- installation

    $ LATEST_VERSION=$(curl --silent "https://api.github.com/repos/nvm-sh/nvm/releases/latest" | grep '"tag_name"' | sed -E 's/.*"([^"]+)".*/\1/')

    $ curl -o- https://raw.githubusercontent.com/creationix/nvm/${LATEST_VERSION}/install.sh | bash

<br/>

    $ export NVM_DIR="$HOME/.nvm"
    [ -s "$NVM_DIR/nvm.sh" ] && \. "$NVM_DIR/nvm.sh"  # This loads nvm
    [ -s "$NVM_DIR/bash_completion" ] && \. "$NVM_DIR/bash_completion"  # This loads nvm bash_completion

    // варианты ноды
    $ nvm ls-remote


    $ {
      nvm install v10.22.0
      nvm use v10.22.0
      nvm alias default v10.22.0
    }

    $ node --version
    v10.22.0

<br/>

### 01 - Hyperledger Fabric 1.4 Tutorial - FabCar Sample Application

https://www.youtube.com/watch?v=2moCQeHCx-g&list=PLVztKpIRxvQU-deHiJYmSLK83fEGQt96k&index=1

<br/>

https://hyperledger-fabric.readthedocs.io/en/release-1.4/write_first_app.html

<br/>

    $ mkdir -p ~/projects/dev/hyperledger
    $ cd ~/projects/dev/hyperledger

    $ curl -sSL https://raw.githubusercontent.com/hyperledger/fabric/master/scripts/bootstrap.sh | bash -s

Или с указанием версии. Оба варианта работают.

    $ curl -sSL https://raw.githubusercontent.com/hyperledger/fabric/master/scripts/bootstrap.sh | bash -s 1.4.8


    $ cd fabric-samples/fabcar
    $ ./startFabric.sh  javascript

    $ cd javascript
    $ npm install
    $ node enrollAdmin.js
    $ node registerUser.js
    $ node query.js

<br/>

```
Wallet path: /home/marley/projects/dev/hyperledger/fabric-samples/fabcar/javascript/wallet
Transaction has been evaluated, result is: [{"Key":"CAR0","Record":{"color":"blue","docType":"car","make":"Toyota","model":"Prius","owner":"Tomoko"}},{"Key":"CAR1","Record":{"color":"red","docType":"car","make":"Ford","model":"Mustang","owner":"Brad"}},{"Key":"CAR2","Record":{"color":"green","docType":"car","make":"Hyundai","model":"Tucson","owner":"Jin Soo"}},{"Key":"CAR3","Record":{"color":"yellow","docType":"car","make":"Volkswagen","model":"Passat","owner":"Max"}},{"Key":"CAR4","Record":{"color":"black","docType":"car","make":"Tesla","model":"S","owner":"Adriana"}},{"Key":"CAR5","Record":{"color":"purple","docType":"car","make":"Peugeot","model":"205","owner":"Michel"}},{"Key":"CAR6","Record":{"color":"white","docType":"car","make":"Chery","model":"S22L","owner":"Aarav"}},{"Key":"CAR7","Record":{"color":"violet","docType":"car","make":"Fiat","model":"Punto","owner":"Pari"}},{"Key":"CAR8","Record":{"color":"indigo","docType":"car","make":"Tata","model":"Nano","owner":"Valeria"}},{"Key":"CAR9","Record":{"color":"brown","docType":"car","make":"Holden","model":"Barina","owner":"Shotaro"}}]

```

    $ node invoke.js
    $ node query.js

<br/>

### 02 - Hyperledger 1.4 Tutorial - Adding a UI Layer - Commercial Paper Example

https://www.youtube.com/watch?v=1Evy4Zuppm0&list=PLVztKpIRxvQU-deHiJYmSLK83fEGQt96k&index=2

https://hyperledger-fabric.readthedocs.io/en/release-1.4/tutorial/commercial_paper.html

https://github.com/horeaporutiu/commercialPaperLoopback

<br/>

    $ cd ~/projects/dev/hyperledger/
    $ git clone https://github.com/horeaporutiu/commercialPaperLoopback
    $ cd commercialPaperLoopback/

<br/>

    $ npm install
    $ npm rebuild
    $ npm start

http://127.0.0.1:3000/

<br/>

    Issue Controller -->

```
{
  "issuer": "Spencer",
  "paperNumber": "7",
  "issueDateTime": "2020-05-31",
  "maturityDateTime": "2020-11-30",
  "faceValue": "200000"
}
```

Ничего не получилось. Т.к. нужно еще и поднимать "backend"

    $ node query

Зачем-то генерировали. Вроде не понадобилось. Наверное, чтобы показать, что UI достаточно легко создать.

    $ cd ~/projects/dev/hyperledger/
    $ npm i -g @loopback/cli
    $ lb4 example todo

<br/>

### 03 - Hyperledger 1.4 App - Supply Chain Transparency - Local Proof of Concept Demo

https://www.youtube.com/watch?v=mG2TCIPlvk0&list=PLVztKpIRxvQU-deHiJYmSLK83fEGQt96k&index=3

https://github.com/IBM/blockchainbean2

Сначала демонстрация, потом показывает как работать. Используется расширение VS Code для Blockchain. Если нет желания добавлять расширения, то и пошагово делать, не имеет смысла. Запись запутанная и непонятная.

<br/>

    $ cd ~/projects/dev/hyperledger/
    $ git clone https://github.com/IBM/blockchainbean2
    $ cd blockchainbean2/web-app
    $ npm install
    $ npm start

http://127.0.0.1:8080

Дальше не стал.

<br/>

### 04-05 - Hyperledger 1.4 Vue.js web-app walkthrough

https://www.youtube.com/watch?v=8uJSHW3Rpzs&list=PLVztKpIRxvQU-deHiJYmSLK83fEGQt96k&index=4

<br/>

**Более подробно:**  
https://www.youtube.com/watch?v=7pcTE5M0fAs

<br/>

**Исходники проекта:**  
https://github.com/IBM/evote

<br/>

Все видео рассказывает про работу кода. Никакой практики.

<br/>

**Пытаемся поднять локально.**

<br/>

    $ cd ~/projects/dev/hyperledger/
    $ git clone https://github.com/IBM/evote
    $ cd contract/
    $ npm install
    $ code .

<br/>

Стартуем NetWork

- install
- instantiate

<br/>

    $ cd web-app/server/
    $ npm install

<br/>

FABRIC GATEWAYS -> 1 Org Local Fabric - Org1 gateway -> admin.

После коннекта -> Три точки -> Export Connection Profile

Сохранить в файл ibpConnection.json в корне server

<br/>

FABRIC WALLETS -> 1 Org Local Fabric -> Org1 -> Правой кнопкой мыши -> Export and save в каталог wallet

<br/>

    $ vi config.json

Предположу, что конфиг должен выглядеть следующим образом:

```
{
  "connection_file": "ibpConnection.json",
  "appAdmin": "app-admin",
  "appAdminSecret": "app-adminpw",
  "orgMSPID": "Org1MSP",
  "caName": "http://localhost:17055",
  "userName": "user1",
  "gatewayDiscovery": { "enabled": true, "asLocalhost": true }
}
```

    $ node enrollAdmin.js

У меня ошибка.

```
Failed to enroll admin user ' + app-admin + : Error: Enrollment failed with errors [[{"code":20,"message":"Authentication failure"}]]
```

<br/>

Хз, что делать. Жду посказок от разобравшихся.

    $ npm start

<br/>

    $ cd web-app/client/
    $ npm install
    $ npm run build
    $ npm run serve

<br/>

http://localhost:8080/

<br/>

### 06 - Hyperledger Fabric 1.4 Tutorial - How to listen to events

https://www.youtube.com/watch?v=KLWLCjKf5Xw&list=PLVztKpIRxvQU-deHiJYmSLK83fEGQt96k&index=6

https://github.com/IBM/auction-events

https://hyperledger.github.io/fabric-sdk-node/release-1.4/tutorial-listening-to-events.html

<br/>

    $ cd ~/projects/dev/hyperledger/
    $ git clone https://github.com/IBM/auction-events
    $ cd auction-events/application/
    $ npm install
    $ node contractEvents.js

Ошибки. Пишет, что файла mychannel_auction_profile.json нет. Сейчас разбираться не хочется.

    $ node blockEvents.js
    $ node transactionEvents.js

<br/>

### [Интересное видео!] 7-8 - Hyperledger Fabric 1.4 Tutorial - Five-node Raft Ordering Service Demo

https://www.youtube.com/watch?v=Lo9UB_idBto&list=PLVztKpIRxvQU-deHiJYmSLK83fEGQt96k&index=7

https://github.com/IBM/raft-fabric-sample

<br/>

    $ cd /home/marley/projects/dev/hyperledger
    $ git clone https://github.com/IBM/raft-fabric-sample
    $ cd raft-fabric-sample
    $ cd first-network/

    // генерим ключи
    $ ./byfn.sh generate -o etcdraft

<br/>

Мдя, ему python2 нужен. У меня только 3.

    // создаем сеть
    $ ./byfn.sh up -o etcdraft -l node

<br/>

    $ cd web-app/client/
    $ npm install
    $ npm start

    $ cd web-app/server/
    $ npm install
    $ node enrollAdmin.js
    $ ./updateKeystore.sh
    $ npm start

<br/>

localhost:4200

<br/>

### [Интересное видео!] 9-10 - Hypeprledger Fabric 1.4 Tutorial - Smart Contract Access Control Demo

https://www.youtube.com/watch?v=EjJS_bRgoS8&list=PLVztKpIRxvQU-deHiJYmSLK83fEGQt96k&index=9

https://github.com/IBM/fabric-contract-attribute-based-access-control

<br/>

    $ cd ~/projects/dev/hyperledger/

    $ git clone https://github.com/IBM/fabric-contract-attribute-based-access-control.git

    $ cd fabric-contract-attribute-based-access-control/

    $ code .

<br/>

Farbic Environment --> 1 Org Local Fabric runtime --> click

View -> Open Command Pallette -> Import Package -> gensupplychainnet@0.0.1.cds

- install -> gensupplychainnet@0.0.1.cds

- instantiate -> gensupplychainnet@0.0.1.cds

<br/>

FABRIC GATEWAYS -> 1 Org Local Fabric - Org1 gateway -> admin.

После коннета -> Три точки -> Export Connection Profile

Сохранить в fabric-contract-attribute-based-access-control/gateway/local/

<br/>

FABRIC WALLETS -> 1 Org Local Fabric -> Org1 -> Правой кнопкой мыши -> Export and save в каталог fabric-contract-attribute-based-access-control/gateway/local

<br/>

    $ cd fabric-contract-attribute-based-access-control/gateway/local/

    $ mv 1OrgLocalFabricOrg1Connection.json fabric_connection.json
    $ mv Org1 gen_local_wallet

<br/>

    $ cd ../../application/server/
    $ npm install
    $ node server.js

<br/>

    $ cd ../client/
    $ npm install
    $ npm run start

localhost:4200

<br/>

admin/adminpw

<br/>

Добавляем пользователей:

```
id: GHFarm
password: GHFarm
role: producer

id: Walmart
password: Walmart
role: retailer

id: UPS
password: UPS
role: shipper

id: ACustomer
password: ACustomer
role: customer

id: FDA
password: FDA
role: regulator
```

<br/>

Logout -> Enrol для каждого созданного.

<br/>

Log in as "Walmart"

Place Order

```
Product ID: corn
Price:        10
Quantity:     10
Producer ID:  GHFarm


Product ID: avocado
Price:      5
Quantity:   15
Producer ID:  GHFarm

```

<br/>

Log out and in as "GHFarm"

```
corn order ->  Accept Order -> Assign Shipper -> UPS
```

<br/>

Log out and in as "UPS"

```
corn order -> Create Shipment -> Transport Shipment
```

<br/>

Log out and in as "Walmart" again

```
corn order -> Receive Shipment
```

<br/>

Log out and in as "FDA"

```
list of all orders
```

Посмотреть и куда-нибудь записать id заказа.

<br/>

Log out and in as "ACustomer"

В поиске вставить order-afb4
