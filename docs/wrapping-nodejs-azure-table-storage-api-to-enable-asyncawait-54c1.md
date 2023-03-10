# 包装 Node.js Azure 表存储 API 以启用异步/等待

> 原文：<https://dev.to/maximrouiller/wrapping-nodejs-azure-table-storage-api-to-enable-asyncawait-54c1>

我喜欢最新最棒的。使用新的语言语法编写代码是非常棒的。

当你最喜欢的图书馆没有的时候会发生什么？你被困在试图找到一个变通办法。我们都讨厌变通办法，但它们是在一天结束时将我们的代码粘在一起的粘合剂。

在运行时、框架、库和其他所有东西之间...我们需要所有人步调一致。

最近不得不用 [Azure Node.js 表存储 API](http://azure.github.io/azure-storage-node/#toc6__anchor) 。

首先我说*我知道*。是的，有一个 v10 存在，这就是 v2。不， [v10 还不支持表存储](https://github.com/Azure/azure-storage-js#azure-storage-sdk-v10-for-javascript)。所以，我们继续吧。

下面是我想看的代码:

```
let storage = require('azure-storage');
// ...

async function getAllFromTable() {
  let tableService = storage.createTableService(connectionString);
  let query = new storage.TableQuery()
    .where('PartitionKey eq ?', defaultPartitionKey);
  return await queryEntities(tableService, 'TableName', query, null);
} 
```

这是我的代码:

```
async function getAllFromTable() {
  return new Promise((resolve, reject) => {
    let tableService = storage.createTableService(connectionString);
    let query = new storage.TableQuery()
      .where('PartitionKey eq ?', defaultPartitionKey);

    tableService.queryEntities('TableName', query, null, function (err, result) {
      if (err) {
        reject(err);
      } else {
        resolve(result);
      }
    });
  });
} 
```

看到函数回调让我回想起代码缩进保证更宽监视器的时代。

## 变通办法

这是我目前的临时解决办法。它允许我将常用的函数包装成更简单的东西。

```
async function queryEntities(tableService, ...args) {
    return new Promise((resolve, reject) => {
        let promiseHandling = (err, result) => {
            if (err) {
                reject(err);
            } else {
                resolve(result);
            }
        };
        args.push(promiseHandling);
        tableService.queryEntities.apply(tableService, args);
    });
}; 
```

### 变通办法概述

1.  我们到处都在使用异步
2.  使用 Rest 参数`args`允许我们从该 API 捕获所有参数
3.  我们包装了适当的承诺，并将其插入到论点中
4.  我们用合适的参数调用相关的 API。

## 结论

就是这样。虽然 Node.js Storage v10 实现了表存储，但我建议将表存储代码包装成类似的结构。

这将允许您在他们更新库时使用新的语言语法。