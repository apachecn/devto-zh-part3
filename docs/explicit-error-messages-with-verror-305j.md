# 带有 VError 的显式错误消息

> 原文：<https://dev.to/sacha/explicit-error-messages-with-verror-305j>

今天我将向大家展示我最喜欢的 NPM 模块之一。VError 让你的错误更加明显。

我不会在这里展示所有的特性，只是解释为什么我认为这个模块非常有用。所以不要犹豫，阅读官方文档[这里](https://www.npmjs.com/package/verror)

我喜欢 VError 的一点是，它可以在错误经过的所有应用层中构建一个复合错误消息。

让我们看一个例子:

```
const VError = require('verror')

function model(json) {
  return JSON.parse(json)
}

function controller(json) {
  try {
    model(json)
  } catch (err) {
    const error = new VError(err, 'Model fail to parse json')
    throw error
  }
}

function routeHandler(rawJSON) {
  try {
    const data = controller(rawJSON)
    return data
  } catch (err) {
    const error = new VError(err, 'Controller fail to use json')
    throw error
  }
}

routeHandler('invalid json') 
```

Enter fullscreen mode Exit fullscreen mode

该脚本将生成以下错误消息:
`VError: Controller fail to use json: Model fail to parse json: Unexpected token i in JSON at position 0`

这比:`SyntaxError: Unexpected token i in JSON at position 0`要清楚得多

你也可以为每个错误层添加一些上下文信息，并在最后用`VError.info()`函数得到它们(见文档[这里](https://www.npmjs.com/package/verror#verrorinfoerr)

例子:

```
const error = new VError({
  name: 'SERVICE_UNAVAILABLE',
  info: { json: rawJSON },
  cause: err,
}, 'Controller fail to use json')

console.log(VError.info(error)) // { json: 'invalid json' }
console.log(error.name) // SERVICE_UNAVAILABLE 
```

Enter fullscreen mode Exit fullscreen mode

请在评论中告诉我你是否已经在使用它，或者你将来是否会使用它，以及你对 VError 有什么反馈。