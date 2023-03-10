# DLT 基数- Alpha 桌面钱包 API 文档

> 原文：<https://dev.to/radixdlt/radix-dlt-alpha-desktop-wallet-api-documentation-277>

# 简介

Radix Wallet 提供了一个本地主机 websocket 服务器，它允许任何本地运行或在浏览器中运行的应用程序通过用户的 Wallet 对分类帐执行操作，而无需用户向应用程序公开他们的私钥。

这些操作包括发送和接收事务、消息和自定义应用程序数据。

用户必须明确批准任何想要这样做的应用程序。

# 概述

服务器正在 ws://localhost:54345 上运行

它是一个 JsonRPC 2.0 服务器。

在发出任何请求之前，应用程序必须注册它的权限。这将通过一个对话框提示用户允许你的应用程序做某些事情。如果成功，该请求将返回一个令牌，用于验证任何进一步的请求。令牌的生命周期为 30 分钟，每次请求成功后都会重置。

然后，将令牌作为`token`包含在请求参数中。

# API

# 寄存器

注册您的应用程序。用户将看到一个弹出窗口，显示您的申请详细信息，并可以批准或拒绝您的访问。

方法:`register`

参数:

```
{
name: 'Cashgrab',
description: 'Takes all your money, I don't even know why you would accept this',
permissions: [
  'address',
  'balance',
  'send_transactions'
  ]
} 
```

成功响应:

```
{
result: { token: 'asdfg' }
...
} 
```

# 平

检查 Radix Wallet 是否正在运行，以及令牌是否仍然有效。

方法:`ping`

参数:

```
{
token: 'asdfg'
} 
```

成功响应:

```
{
result: 'pong'
...
} 
```

# 发送交易

返回发送的原子的 hid

方法:`send_transaction`

权限:`['send_transactions']`

参数:

```
{
token: 'asdfgh',
recipient: '9i9hgAyBQuKvkw7Tg5FEbML59gDmtiwbJwAjBgq5mAU4iaA1ykM',
asset: 'TEST',
quantity: 123.45
} 
```

成功响应:

```
{
result: {hid: 123}
...
} 
```

# 发送消息

发送聊天信息。返回发送的原子的 hid。

方法:`send_message`

权限:`['send_messages']`

参数:

```
{
token: 'asdfgh',
recipient: '9i9hgAyBQuKvkw7Tg5FEbML59gDmtiwbJwAjBgq5mAU4iaA1ykM',
message: 'Hi honey, I'm home'
} 
```

成功响应:

```
{
result: {hid: 123}
...
} 
```

# 发送申请消息

发送应用程序消息。应用程序消息允许在分类帐中存储任意数据。返回发送的原子的 hid

方法:`address`

权限:`['address']`

参数:

```
{
token: 'asdfgh',
application_id: 'custom-app',
recipients: [
  '9egJPK7zk2LhPok8VM4noAwVGGS7LKtvTYGJvtF41wrwZAMxjKj',
  '9hFccFfB3Y3A7gToBcD3b9y1ZRnoZfSdHTWVdRPCn3zpKSoVubf',
 ],
payload: {//anything}
} 
```

成功响应:

```
{
result: {
  result: {hid: 123}
  }
...
} 
```

# 获得平衡

这是一个订阅，每次余额更新时都会向您发送更新信息

方法:`balance`

权限:`['balance']`

参数:

```
{
token: 'asdfgh',
} 
```

成功响应:

```
{
result: 'OK'
...
} 
```

更新通知:

```
{
jsonrpc: '2.0',
method: 'balance.update',
params: { balance: {TEST: 123.45} }
} 
```

# 获取交易

这是一个订阅，每次有新的交易发生时都会向您发送更新

方法:`transactions`

权限:`['transactions']`

参数:

```
{
token: 'asdfgh',
} 
```

成功响应:

```
{
result: 'OK'
...
} 
```

更新通知:

```
{
jsonrpc: '2.0',
method: 'transaction.update',
params: { //transaction details }
} 
```

# 获取消息

这是一个订阅，每次发送或接收新消息时都会向您发送更新

方法:`messages`

权限:`['messages']`

参数:

```
{
token: 'asdfgh',
} 
```

成功响应:

```
{
result: 'OK'
...
} 
```

更新通知:

```
{
jsonrpc: '2.0',
method: 'message.update',
params: { //message details }
} 
```

# 获取申请消息

这是一个订阅，每次发送或接收新消息时都会向您发送更新

方法:`application_messages`

权限:`['application_messages']`

参数:

```
{
token: 'asdfgh',
application_id: 'custom-app'
} 
```

成功响应:

```
{
result: 'OK'
...
} 
```

更新通知:

```
{
jsonrpc: '2.0',
method: 'application_message.update',
params: { 
  applicationId: 'custom-app',
  payload: {//anything}
 }
} 
```

# 加入 Radix 社区

[电报](https://t.me/radix_dlt)普通聊天
[不和](https://discord.gg/7Q7HSZZ)开发者聊天
[Reddit](https://reddit.com/r/radix) 普通讨论
[论坛](https://forum.radixdlt.com/)技术讨论
[推特](https://twitter.com/radixdlt)发布公告
[电子邮件](https://radixdlt.typeform.com/to/nyKvMV)简讯每周更新
邮件[hello@radixdlt.com](//hello@radixdlt.com)普通查询