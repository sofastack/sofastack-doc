## 快速上手


如果你有通过 NodeJs 调用 SOFARPC 的需求.可以按照如下的文档来开始.

### 安装

首先按照文档安装

`https://github.com/sofastack/sofa-rpc-node` 

使用命令.
```bash
$ npm install sofa-rpc-node --save
```

## 代码示例

### 暴露一个 RPC 服务，并发布到注册中心
```js
'use strict';

const { RpcServer } = require('sofa-rpc-node').server;
const { ZookeeperRegistry } = require('sofa-rpc-node').registry;
const logger = console;

// 1. 创建 zk 注册中心客户端
const registry = new ZookeeperRegistry({
  logger,
  address: '127.0.0.1:2181', // 需要本地启动一个 zkServer
});

// 2. 创建 RPC Server 实例
const server = new RpcServer({
  logger,
  registry, // 传入注册中心客户端
  port: 12200,
});

// 3. 添加服务
server.addService({
  interfaceName: 'com.nodejs.test.TestService',
}, {
  async plus(a, b) {
    return a + b;
  },
});

// 4. 启动 Server 并发布服务
server.start()
  .then(() => {
    server.publish();
  });
```

### 调用 RPC 服务（从注册中心获取服务列表）
```js
'use strict';

const { RpcClient } = require('sofa-rpc-node').client;
const { ZookeeperRegistry } = require('sofa-rpc-node').registry;
const logger = console;

// 1. 创建 zk 注册中心客户端
const registry = new ZookeeperRegistry({
  logger,
  address: '127.0.0.1:2181',
});

async function invoke() {
  // 2. 创建 RPC Client 实例
  const client = new RpcClient({
    logger,
    registry,
  });
  // 3. 创建服务的 consumer
  const consumer = client.createConsumer({
    interfaceName: 'com.nodejs.test.TestService',
  });
  // 4. 等待 consumer ready（从注册中心订阅服务列表...）
  await consumer.ready();

  // 5. 执行泛化调用
  const result = await consumer.invoke('plus', [ 1, 2 ], { responseTimeout: 3000 });
  console.log('1 + 2 = ' + result);
}

invoke().catch(console.error);
```

### 调用 RPC 服务（直连模式）
```js
'use strict';

const { RpcClient } = require('sofa-rpc-node').client;
const logger = console;

async function invoke() {
  // 不需要传入 registry 实例了
  const client = new RpcClient({
    logger,
  });
  const consumer = client.createConsumer({
    interfaceName: 'com.nodejs.test.TestService',
    serverHost: '127.0.0.1:12200', // 直接指定服务地址
  });
  await consumer.ready();

  const result = await consumer.invoke('plus', [ 1, 2 ], { responseTimeout: 3000 });
  console.log('1 + 2 = ' + result);
}

invoke().catch(console.error);
```

## 暴露和调用 protobuf 接口


### 接口定义

通过 *.proto 来定义接口
```proto
syntax = "proto3";

package com.alipay.sofa.rpc.test;

// 可选
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

### 服务端代码

```js
'use strict';

const antpb = require('antpb');
const protocol = require('sofa-bolt-node');
const { RpcServer } = require('sofa-rpc-node').server;
const { ZookeeperRegistry } = require('sofa-rpc-node').registry;
const logger = console;

// 传入 *.proto 文件存放的目录，加载接口定义
const proto = antpb.loadAll('/path/proto');
// 将 proto 设置到协议中
protocol.setOptions({ proto });

const registry = new ZookeeperRegistry({
  logger,
  address: '127.0.0.1:2181',
});

const server = new RpcServer({
  logger,
  protocol, // 覆盖协议
  registry,
  codecType: 'protobuf', // 设置默认的序列化方式为 protobuf
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

### 客户端代码

```js
'use strict';

const antpb = require('antpb');
const protocol = require('sofa-bolt-node');
const { RpcClient } = require('sofa-rpc-node').client;
const { ZookeeperRegistry } = require('sofa-rpc-node').registry;
const logger = console;

// 传入 *.proto 文件存放的目录，加载接口定义
const proto = antpb.loadAll('/path/proto');
// 将 proto 设置到协议中
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