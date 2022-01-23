---
title: JavaScript中使用websocket + protobuf
date: 2022-01-23 20:31:00
tags:
  - JavaScript
  - protobuf
categories:
  - FrontEnd
---

## 背景

因业务需求，其实是因为CPU太高了(经过后端人员测试，相比使用json，使用protobuf后，CPU降低显著)，需要使用protobuf作为数据交换格式，替换原来的json格式

## 使用场景

JavaScript(Vue) + WebSocket + ProtoBuf

## 踩坑记录

最开始参考的 [如何在前端中使用protobuf（vue篇）](https://juejin.cn/post/6844903699458818062)，使用的是 [protobuf.js](https://github.com/protobufjs/protobuf.js)  
其实就是使用js库将 .proto 文件转成 .json 或 .js，[protobuf.js](https://github.com/protobufjs/protobuf.js) 两者都支持  

## .proto 文件

```javascript
// BaseMsg.proto
syntax = "proto3";
import "google/protobuf/any.proto";
package websocket_proto;

message BaseMsg {
  int32 msg_id = 1;
  int64 seq_num = 2;
  int64 timestamp = 3;
  google.protobuf.Any data = 4
}
```

## 将所有的 .proto 文件生成为 单个的 proto.js 或 proto.json 文件

protobuf.js提供了一个非常好用的命令行工具 [pbjs](https://protobufjs.github.io/protobuf.js/#pbjs-for-javascript)，通过提供不同的参数它可以将所有的 .proto 文件打包生成单个的 filename.json 或 filename.js 文件

打包生成 proto.js 文件:
```bash
npx pbjs -t json-module -w commonjs -o src/protobuf/proto.js src/protobuf/carry/*.proto
```
打包生成 proto.json 文件
```bash
npx pbjs -t json -w commonjs -o src/protobuf/proto.json src/protobuf/carry/*.proto
```
为了方便使用，将命令添加到package.json的scripts中:

```json
{
  "scripts": {
    "proto:js": "pbjs -t json-module -w commonjs -o src/protobuf/proto.js src/protobuf/carry/*.proto",
    "proto:json": "pbjs -t json -w commonjs -o src/protobuf/proto.json src/protobuf/carry/*.proto",
  } 
}
```
如果需要重新生成 proto.json
```bash
npm run proto:json
```
或 proto.js
```bash
npm run proto:js
```

## 使用

这里以 json 格式的为例子

```javascript
// handler.js

const jsonDescriptor = require('@/protobuf/proto.json')
const protoRoot = protobuf.Root.fromJSON(jsonDescriptor)
const BaseMessage = protoRoot.lookupType("websocket_proto.BaseMessage")
```






