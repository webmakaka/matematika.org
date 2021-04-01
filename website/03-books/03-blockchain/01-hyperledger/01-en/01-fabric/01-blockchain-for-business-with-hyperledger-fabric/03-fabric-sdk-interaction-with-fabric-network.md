---
layout: page
title: Nakul Shah - Blockchain for Business with Hyperledger Fabric - Fabric SDK Interaction with Fabric Network
description: Nakul Shah - Blockchain for Business with Hyperledger Fabric - Fabric SDK Interaction with Fabric Network
keywords: Blockchain, Hyperleger, Fabric, Nakul Shah, Blockchain for Business with Hyperledger Fabric, Fabric SDK
permalink: /books/blockchain/hyperledger/fabric/blockchain-for-business-with-hyperledger-fabric/fabric-sdk-interaction-with-fabric-network/en/
---

# [Nakul Shah] Blockchain for Business with Hyperledger Fabric: A complete guide to enterprise Blockchain implementation using Hyperledger Fabric [ENG, 2019]

<br/>

## Fabric SDK: Interaction with Fabric Network

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

    // Репо ушло вперед. Использую другой вариант. В книге предлагают
    // $ git clone https://github.com/hyperledger/fabric-samples.git

    // It is a one-organization network comprising one peer, one orderer, Fabric CA, CouchDB, and CLI container.
    $ curl -sSL https://raw.githubusercontent.com/hyperledger/fabric/master/scripts/bootstrap.sh | bash -s 1.4.0

    $ cd fabric-samples

    $ cd chaincode

    $ mkdir asset-registery
    $ cd asset-registery


    $ git clone https://github.com/SateDev/hyperledger_chaincode_asset_registry .

    // Ошибка дальше, если не выполнить. (На моем ПК)
    // "Duplicate identifier 'IteratorResult'"
    $ npm update --save-dev @types/node

<br/>

    $ cd ../../

    $ mkdir startup
    $ cd startup

    $ curl -OL https://raw.githubusercontent.com/SateDev/asset-registry-sdk/master/scripts/startup.sh

<br/>

<!--
    $ chmod 777 ./startup.sh
-->

    $ chmod +x ./startup.sh
    $ ./startup.sh typescript

<br/>

Хм. Дальше ошибка которая не позволит выполнить все, что происходит в книге.

<br/>

```
$ docker ps
CONTAINER ID        IMAGE                        COMMAND                  CREATED             STATUS              PORTS                                        NAMES
ef7af113107b        hyperledger/fabric-ca        "sh -c 'fabric-ca-se…"   2 minutes ago       Up 2 minutes        0.0.0.0:7054->7054/tcp                       ca.example.com
e17c2bd7afc4        hyperledger/fabric-couchdb   "tini -- /docker-ent…"   2 minutes ago       Up 2 minutes        4369/tcp, 9100/tcp, 0.0.0.0:5984->5984/tcp   couchdb
6e100f7ac277        hyperledger/fabric-orderer   "orderer"                2 minutes ago       Up 2 minutes        0.0.0.0:7050->7050/tcp                       orderer.example.com

```

Фабрика запустилась.
Но потом упала.

<br/>

### Enrolment and registration of admin and user

using CA server

    $ cd ~/projects/dev/hyperledger/

    $ git clone https://github.com/webmakaka/Blockchain-for-Business-with-Hyperledger-Fabric

Нужно переключиться на branch origin (git checkout origin)

    $ cd Blockchain-for-Business-with-Hyperledger-Fabric/app/api/

    $ npm install
    $ node enrollAdmin.js

This will fetch the certificates from the CA server and put them in the path

Создается каталог hfc-key-store с приватным и публичным ключом.

    $ ls ./hfc-key-store/
    37f942a7a1440f93a980c2273e15898e390738ebcb99e9d139eac8766343cf74-priv
    37f942a7a1440f93a980c2273e15898e390738ebcb99e9d139eac8766343cf74-pub
    6adf7f30861af5ec871e3935e124902df33235106474f9e53a6e53749f16c1bc-priv

<br/>

### Registration and enrolment of the user

    $ node registerUser.js

В hfc-key-store добавились ключи для созданного пользователя.

<br/>

### Chaincode invoke and query

Как сделал индус, из коробки у меня не заработало.

Это из-за того, что исходники для следующей части. Нужно. Чтобы запустить нужно сопоставить коды из проекта с тем, что у индуса на картинках в книге.

Если не хочется разбираться, можно отправиться к следующей главе книги.

    $ cp invoke.js invoke.js.orig

<br/>

    $ vi invoke1.js

<br/>

```js
'use strict';

const Fabric_Client = require('fabric-client');
const path = require('path');
const util = require('util');
const os = require('os');

async function invoke() {
  console.log('\n\n --- invoke.js - start');
  try {
    console.log('Setting up client side network objects');
    // fabric client instance
    // starting point for all interactions with the fabric network
    const fabric_client = new Fabric_Client();

    // setup the fabric network
    // -- channel instance to represent the ledger named "mychannel"
    const channel = fabric_client.newChannel('mychannel');
    console.log('Created client side object to represent the channel');
    // -- peer instance to represent a peer on the channel
    const peer = fabric_client.newPeer('grpc://localhost:7051');
    console.log('Created client side object to represent the peer');
    // -- orderer instance to reprsent the channel's orderer
    const orderer = fabric_client.newOrderer('grpc://localhost:7050');
    console.log('Created client side object to represent the orderer');

    // This sample application uses a file based key value stores to hold
    // the user information and credentials. These are the same stores as used
    // by the 'registerUser.js' sample code
    const member_user = null;
    const store_path = path.join(__dirname, 'hfc-key-store');
    console.log('Setting up the user store at path:' + store_path);
    // create the key value store as defined in the fabric-client/config/default.json 'key-value-store' setting
    const state_store = await Fabric_Client.newDefaultKeyValueStore({
      path: store_path,
    });
    // assign the store to the fabric client
    fabric_client.setStateStore(state_store);
    const crypto_suite = Fabric_Client.newCryptoSuite();
    // use the same location for the state store (where the users' certificate are kept)
    // and the crypto store (where the users' keys are kept)
    const crypto_store = Fabric_Client.newCryptoKeyStore({
      path: store_path,
    });
    crypto_suite.setCryptoKeyStore(crypto_store);
    fabric_client.setCryptoSuite(crypto_suite);

    // get the enrolled user from persistence and assign to the client instance
    //    this user will sign all requests for the fabric network
    const user = await fabric_client.getUserContext('user1', true);
    if (user && user.isEnrolled()) {
      console.log('Successfully loaded "user1" from user store');
    } else {
      throw new Error('\n\nFailed to get user1.... run registerUser.js');
    }

    console.log('Successfully setup client side');
    console.log('\n\nStart invoke processing');

    // get a transaction id object based on the current user assigned to fabric client
    // Transaction ID objects contain more then just a transaction ID, also includes
    // a nonce value and if built from the client's admin user.
    const tx_id = fabric_client.newTransactionID();
    console.log(
      util.format('\nCreated a transaction ID: %s', tx_id.getTransactionID())
    );

    const proposal_request = {
      targets: [peer],
      chaincodeId: 'asset',
      fcn: 'createProperty',
      args: ['P100003', 'howbe', '2838', 'somehg', 'asdf', '2323', 'someowner'],
      chainId: 'mychannel',
      txId: tx_id,
    };

    // notice the proposal_request has the peer defined in the 'targets' attribute.
    // Send the transaction proposal to the endorsing peers.
    // The peers will run the function requested with the arguments supplied.
    // based on the current state of the ledger. If the chaincode successfully.
    // runs this simulation it will return a positive result in the endorsement.
    const endorsement_results = await channel.sendTransactionProposal(
      proposal_request
    );

    // The results will contain a few different items
    // first is the actual endorsements by the peers, these will be the responses
    //    from the peers. In our sammple there will only be one results since
    //    only sent the proposal to one peer.
    // second is the proposal that was sent to the peers to be endorsed. This will
    //    be needed later when the endorsements are sent to the orderer.
    // second is the proposal that was sent
    const proposalResponses = endorsement_results[0];
    const proposal = endorsement_results[1];

    // check the results to decide if we should send the endorsment to be orderered
    if (proposalResponses[0] instanceof Error) {
      console.error(
        'Failed to send Proposal. Received an error :: ' +
          proposalResponses[0].toString()
      );
      throw proposalResponses[0];
    } else if (
      proposalResponses[0].response &&
      proposalResponses[0].response.status === 200
    ) {
      console.log(
        util.format(
          'Successfully sent Proposal and received response: Status - %s',
          proposalResponses[0].response.status
        )
      );
    } else {
      const error_message = util.format(
        'Invoke chaincode proposal:: %j',
        proposalResponses[i]
      );
      console.error(error_message);
      throw new Error(error_message);
    }

    // The proposal was good, now send to the orderer to have the transaction
    // committed.

    const commit_request = {
      orderer: orderer,
      proposalResponses: proposalResponses,
      proposal: proposal,
    };

    //Get the transaction ID string to be used by the event processing
    const transaction_id_string = tx_id.getTransactionID();

    // create an array to hold on the asynchronous calls to be executed at the
    // same time
    const promises = [];

    // this will send the proposal to the orderer during the execuction of
    // the promise 'all' call.

    const sendPromise = channel.sendTransaction(commit_request);
    //we want the send transaction first, so that we know where to check status

    promises.push(sendPromise);

    // get an event hub that is associated with our peer
    let event_hub = channel.newChannelEventHub(peer);

    // create the asynchronous work item

    let txPromise = new Promise((resolve, reject) => {
      // setup a timeout of 30 seconds
      // if the transaction does not get committed within the timeout period,
      // report TIMEOUT as the status. This is an application timeout and is a
      // good idea to not let the listener run forever.
      let handle = setTimeout(() => {
        event_hub.unregisterTxEvent(transaction_id_string);
        event_hub.disconnect();
        resolve({ event_status: 'TIMEOUT' });
      }, 30000);

      // this will register a listener with the event hub. THe included callbacks
      // will be called once transaction status is received by the event hub or
      // an error connection arises on the connection.

      event_hub.registerTxEvent(
        transaction_id_string,
        (tx, code) => {
          // this first callback is for transaction event status

          // callback has been called, so we can stop the timer defined above
          clearTimeout(handle);

          // now let the application know what happened
          const return_status = {
            event_status: code,
            tx_id: transaction_id_string,
          };
          if (code !== 'VALID') {
            console.error('The transaction was invalid, code = ' + code);
            resolve(return_status); // we could use reject(new Error('Problem with the tranaction, event status ::'+code));
          } else {
            console.log(
              'The transaction has been committed on peer ' +
                event_hub.getPeerAddr()
            );
            resolve(return_status);
          }
        },
        (err) => {
          //this is the callback if something goes wrong with the event registration or processing
          reject(new Error('There was a problem with the eventhub ::' + err));
        },
        { disconnect: true } //disconnect when complete
      );

      // now that we have a protective timer running and the listener registered,
      // have the event hub instance connect with the peer's event service
      event_hub.connect();
      console.log(
        'Registered transaction listener with the peer event service for transaction ID:' +
          transaction_id_string
      );
    });

    // set the event work with the orderer work so they may be run at the same time
    promises.push(txPromise);

    // now execute both pieces of work and wait for both to complete
    console.log('Sending endorsed transaction to the orderer');
    const results = await Promise.all(promises);

    // since we added the orderer work first, that will be the first result on
    // the list of results
    // success from the orderer only means that it has accepted the transaction
    // you must check the event status or the ledger to if the transaction was
    // committed
    if (results[0].status === 'SUCCESS') {
      console.log('Successfully sent transaction to the orderer');
    } else {
      const message = util.format(
        'Failed to order the transaction. Error code: %s',
        results[0].status
      );
      console.error(message);
      return false;
    }

    if (results[1] instanceof Error) {
      console.error(message);
      return false;
    } else if (results[1].event_status === 'VALID') {
      console.log(
        'Successfully committed the change to the ledger by the peer'
      );
      console.log('\n\n - try running "node query.js" to see the results');
      return true;
    } else {
      const message = util.format(
        'Transaction failed to be committed to the ledger due to : %s',
        results[1].event_status
      );
      console.error(message);
      return false;
    }
  } catch (error) {
    console.log('Unable to invoke ::' + error.toString());
  }
  console.log('\n\n --- invoke.js - end');
}

invoke();
```

<br/>

    $ node invoke1.js

Оказалось, что контейнер peer остановлен.

    $ docker ps -a

Поднял его руками.

    $ docker start a2d5c967340a

    $ telnet localhost 7051

OK

<br/>

Все равно ошибка.

```
Start invoke processing

Created a transaction ID: 6f7760d5fc6824372f4cf7f1d581d18589e872150a0ba2ce6d05989ba31e93f0
2020-08-20T00:00:22.309Z - error: [Peer.js]: sendProposal - timed out after:45000
Failed to send Proposal. Received an error :: Error: REQUEST_TIMEOUT
Unable to invoke ::Error: REQUEST_TIMEOUT

```

    $ docker logs f5ceffcc62dd

```
2020-08-20 00:19:56.664 UTC [couchdb] CreateCouchDatabase -> ERRO 028 Error calling CouchDB CreateDatabaseIfNotExist() for dbName: mychannel_, error: error decoding response body: json: cannot unmarshal string into Go struct field DBInfo.purge_seq of type int
panic: error during commit to txmgr: error decoding response body: json: cannot unmarshal string into Go struct field DBInfo.purge_seq of type int
	panic: runtime error: invalid memory address or nil pointer dereference
[signal SIGSEGV: segmentation violation code=0x1 addr=0x28 pc=0xcecbb6]
```

<br/>

Далее нужно подготовить код для query.js

    $ node query.js

<br/>

### Invoking ChangePropertyOwner from Fabric SDK

    $ node changeOwner.js
    $ node query.js

Вернусь и попробую победить, после завершения чтения книги.
