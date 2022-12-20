# 不带“config”NPM 包的 12 因素 Node.js 应用程序配置管理

> 原文：<https://dev.to/hugo__df/12-factor-nodejs-application-configuration-management-without-the-config-npm-package-4kmj>

npm 包很棒([npmjs.com/package/config](https://www.npmjs.com/package/config))，但是它鼓励混乱和不 [12 因素应用](https://12factor.net/)兼容的模式。

我们将看看它所鼓励的一些模式，以及为什么它们会给你带来困难，以及一种简单的、单个文件的、不依赖的方式来定义你的配置。

## 杂乱无章的配置:很难确定在哪里设置配置

它鼓励的主要事情是配置蔓延:一些配置存在于 JSON 文件中，一些配置来自环境变量(并使用 JSON 文件粘合在一起)。一些配置字段根据`NODE_ENV`而改变，其他则不会。

最糟糕的是，config 是使用一个`config.get('path.in.the.json.config.object')`调用动态加载的。这为用户创建了一个具有深层嵌套配置对象的启示，这是不可取的，你的配置应该是最小的，它不应该存在于应用程序代码中。

参见“十二因素应用程序”中“配置”部分的以下内容(详见[12factor.net/config](https://12factor.net/config)):

> 应用程序有时会将配置作为常量存储在代码中。这违反了十二因素法，十二因素法要求**将配置与代码**严格分离。配置在不同的部署中有很大的不同，代码则没有。

## 非粒度配置

根据 12 因子，有一个使配置对象变得容易的包不是一个好主意的另一个原因(参见 12factor.net/config 的完整配置部分):

> 在十二因子应用程序中，环境变量是**粒度控制**，每个**与其他环境变量完全正交**。它们**从来没有作为“环境”**组合在一起，而是为每个部署独立管理。

拥有一个`default.json`、`production.json`、`test.json`、`custom-environment-variables.json`并不是 12 因素，因为你不应该将你的配置**分组**。应该是“这里有一个数据库连接 URL”，“这里有一个后台服务 URL”，“这里有一个缓存连接字符串”。

它诱使开发人员继续在`"database": {}`字段中添加开关和设置。这些关注点不会互相**正交**，更重要的是，它们很可能是应用程序级的关注点，例如“数据库客户机应该尝试重新连接吗？”。这不是您应该用环境变量覆盖或在环境间切换的东西。例如，这是一个应该硬编码到应用程序中的设置，具体取决于数据库是否关键。

## 单个 config.js 文件

`config.js`你的应用程序的根应该是这样的:

```
module.exports = {
  NAME_OF_CONFIG: process.env.NAME_OF_CONFIG || 'default-config',
  DATABASE_URL: process.env.DATABASE_URL,
  REDIS_URL: process.env.REDIS_URL || 'localhost:6379',
  X_ENABLED: process.env.X_ENABLED === 'true',
}; 
```

在上面的例子中，你将如何默认一个配置变量(`NAME_OF_CONFIG`，`REDIS_URL`)以及你将如何检查一个布尔标志(`X_ENABLED`)。

## 使 process.env 符合目的

在 Node.js 中`process.env`变量(环境变量)是字符串，JavaScript 在类型上相当宽松，但有时将`process.env`变量转换成另一种类型是有用的。

### 解析 process.env 中的数字

```
const SESSION_TIMEOUT = parseInt(process.env.SESSION_TIMEOUT, 10)
module.exports = {
  SESSION_TIMEOUT
}; 
```

### 从 process.env 转换为布尔值

与`'true'`字符串进行比较就足够了:

```
module.exports = {
  IS_DEV: process.env.IS_DEV === 'true',
}; 
```

## 消耗配置. js

从`config.js`获取数据的过程如下，我们有条件地设置一些`'json spaces'`，一个请求超时，并使用一个快速应用程序
监听一个端口

```
const { REQUEST_TIMEOUT, X_ENABLED, PORT } = require('./config')
const express = require('express')
const app = express()

if(X_ENABLED) {
  app.set('json spaces', 2)
}

app.use((req, res, next) => {
  req.setTimeout(REQUEST_TIMEOUT); 
  next()
})

app.listen(PORT, () => {
  console.log(`App listening on ${PORT}`);
}); 
```

## 奖励:从. env 文件中获取值

有时，您可能希望将. env 文件中的值导出到 shell 会话中，下面的代码片段就是这样做的，它是这个 bash cheatsheet 的摘录。

```
export $(cat .env | xargs) 
```

> 请注意，上述内容适用于*NIX 环境

菲利普·吉尔达