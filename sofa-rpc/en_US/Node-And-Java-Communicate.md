## Quick start guide

If you need to call SOFARPC through NodeJs, you can start by following this document.

### Install

First install the SOFARPC Node.

`https://github.com/sofastack/sofa-rpc-node` 

Use the following command:
```bash
$ npm install sofa-rpc-node --save
```

## Code sample

### Expose an RPC service and publish it to registry center
```js
'use strict';

const { RpcServer } = require('sofa-rpc-node').server;
const { ZookeeperRegistry } = require('sofa-rpc-node').registry;
const logger = console;

// 1. Create a Zookeeper registry client
const registry = new ZookeeperRegistry({
  logger,
  Address: '127.0.0.1:2181', // need to start a zkServer locally
});

// 2. Create an RPC Server instance
const server = new RpcServer({
  logger,
  Registry, // incoming registry client
  port: 12200,
});

// 3. Add service
server.addService({
  interfaceName: 'com.nodejs.test.TestService',
}, {
  async plus(a, b) {
    return a + b;
  },
});

// 4. Start the server and publish the service
server.start()
  .then(() => {
    server.publish();
  });
```

### Call RPC service (Get service list from registry center)
```js
'use strict';

const { RpcClient } = require('sofa-rpc-node').client;
const { ZookeeperRegistry } = require('sofa-rpc-node').registry;
const logger = console;

// 1. Create a Zookeeper registry client
const registry = new ZookeeperRegistry({
  logger,
  address: '127.0.0.1:2181',
});

async function invoke() {
  // 2. Create an RPC Client instance
  const client = new RpcClient({
    logger,
    registry,
  });
  // 3. Create a service consumer
  const consumer = client.createConsumer({
    interfaceName: 'com.nodejs.test.TestService',
  });
  // 4. Wait for the consumer ready (subscribe to the service list from registry center...)
  await consumer.ready();

  // 5. Execute generic call
  const result = await consumer.invoke('plus', [ 1, 2 ], { responseTimeout: 3000 });
  console.log('1 + 2 = ' + result);
}

invoke().catch(console.error);
```

### Call RPC service (direct call)
```js
'use strict';

const { RpcClient } = require('sofa-rpc-node').client;
const logger = console;

async function invoke() {
  // No need to pass in the registry instance
  const client = new RpcClient({
    logger,
  });
  const consumer = client.createConsumer({
    interfaceName: 'com.nodejs.test.TestService',
    serverHost: '127.0.0.1:12200', // directly specify the service address
  });
  await consumer.ready();

  const result = await consumer.invoke('plus', [ 1, 2 ], { responseTimeout: 3000 });
  console.log('1 + 2 = ' + result);
}

invoke().catch(console.error);
```

## Expose and call the protobuf interface


### Define interface

Define the interface with *.proto
```proto
syntax = "proto3";

package com.alipay.sofa.rpc.test;

// optional
option java_multiple_files = false;

service ProtoService {
  rpc echoObj (EchoRequest) returns (EchoResponse) {}
}

message EchoRequest {
  string name = 1;
  Group group = 2;
}

message EchoResponse {
  int32 code = 1;
  string message = 2;
}

enum Group {
  A = 0;
  B = 1;
}
```

### Server code

```js
'use strict';

const antpb = require('antpb');
const protocol = require('sofa-bolt-node');
const { RpcServer } = require('sofa-rpc-node').server;
const { ZookeeperRegistry } = require('sofa-rpc-node').registry;
const logger = console;

// Pass in the directory where the *.proto file is stored, load the interface definition
const proto = antpb.loadAll('/path/proto');
// set proto in protocol
protocol.setOptions({ proto });

const registry = new ZookeeperRegistry({
  logger,
  address: '127.0.0.1:2181',
});

const server = new RpcServer({
  logger,
  Protocol, // override protocol
  registry,
  codecType: 'protobuf', // set the default serialization method to protobuf
  port: 12200,
});

server.addService({
  interfaceName: 'com.alipay.sofa.rpc.test.ProtoService',
}, {
  async echoObj(req) {
    req = req.toObject({ enums: String });
    return {
      code: 200,
      message: 'hello ' + req.name + ', you are in ' + req.group,
    };
  },
});
server.start()
  .then(() => {
    server.publish();
  });
```

### Client code

```js
'use strict';

const antpb = require('antpb');
const protocol = require('sofa-bolt-node');
const { RpcClient } = require('sofa-rpc-node').client;
const { ZookeeperRegistry } = require('sofa-rpc-node').registry;
const logger = console;

// Pass in the directory where the *.proto file is stored, load the interface definition
const proto = antpb.loadAll('/path/proto');
// set proto in the protocol
protocol.setOptions({ proto });

const registry = new ZookeeperRegistry({
  logger,
  address: '127.0.0.1:2181',
});

async function invoke() {
  const client = new RpcClient({
    logger,
    protocol,
    registry,
  });
  const consumer = client.createConsumer({
    interfaceName: 'com.alipay.sofa.rpc.test.ProtoService',
  });
  await consumer.ready();

  const result = await consumer.invoke('echoObj', [{
    name: 'gxcsoccer',
    group: 'B',
  }], { responseTimeout: 3000 });
  console.log(result);
}

invoke().catch(console.error);
```