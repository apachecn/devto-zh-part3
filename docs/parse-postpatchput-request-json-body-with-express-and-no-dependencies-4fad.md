# 解析 POST/PATCH/PUT 请求 JSON 主体，带有 Express 且无依赖性

> 原文：<https://dev.to/hugo__df/parse-postpatchput-request-json-body-with-express-and-no-dependencies-4fad>

当被要求处理请求体中的数据时，以前使用过 Express(用于 [Node.js](https://nodejs.org/en/) )的快速、非个性化、极简的 web 框架)的开发人员会使用[体解析器](https://github.com/expressjs/body-parser)库。

使用`express.json()`可以实现相同的功能。一个解析内置在 Express 中的 JSON 请求体的中间件。

> 注意:`express.json`只存在于 Express 4.16+中

下面是一个在响应中反映请求帖子正文的示例应用程序:

```
const express = require('express')
const app = express()

app.use(express.json())
app.post(
  '/test',
  (req, res) => res.json(req.body)
)

const PORT = process.env.PORT || 3000
app.listen(PORT, () => {
  console.log(`Server listening on port ${PORT}`)
}) 
```

你可以看到它在[https://olrn6x3n19.sse.codesandbox.io/](https://olrn6x3n19.sse.codesandbox.io/)运行，使用
测试它

```
curl --request POST \
  --url https://olrn6x3n19.sse.codesandbox.io/test \
  --header 'content-type: application/json' \
  --data '{
    "json-parsing": "just with Express",
    "no": "body-parser"
}' 
```

它将输出:

```
{"json-parsing":"just with Express","no":"body-parser"} 
```

这正是我们在 curl 请求的`--data`属性中发送的内容。

您可以像这样向它传递选项(所有值都是这些选项的默认值):

```
app.use(express.json({
  inflate: true,
  limit: '100kb',
  reviver: null,
  strict: true,
  type: 'application/json',
  verify: undefined
})) 
```

`express.json`的完整 API 文档位于[expressjs.com/en/api.html#express.json](https://expressjs.com/en/api.html#express.json)。

下面是选项的运行过程。

`inflate`控制是否处理压缩/收缩的请求体。当它被设置为假时，压缩/放气的物体将被拒绝。

`limit`控制最大身体尺寸。它可以是与[字节](https://www.npmjs.com/package/bytes)库兼容的字节数或字符串。

`strict`是关于将 JSON.parse 锁定到对象和数组。如果为真，只允许使用`JSON.stringify`编码的对象和数组，如果为假，JSON.parse 接受的任何内容都将被解析。

`type`控制中间件试图解析的内容类型。此选项的值可以是字符串、字符串数组或函数。在某种程度上支持内容类型通配符，因为字符串被传递给了 [type-is](https://www.npmjs.com/package/type-is) 库。

`verify`是带有`verify(req, res, buf, encoding)`签名的可选函数。`buf`是包含原始请求体的缓冲区。`verify`可用于通过抛出错误来中止解析。

尽管不需要解析 JSON 主体，body-parser 仍然是一个很好的库，为其他主体类型提供解析器(参见 github.com/expressjs/body-parser 的文档):

*   [JSON 主体解析器](https://github.com/expressjs/body-parser#bodyparserjsonoptions)
*   [原始体解析器](https://github.com/expressjs/body-parser#bodyparserrawoptions)
*   [正文解析器](https://github.com/expressjs/body-parser#bodyparsertextoptions)
*   [URL 编码的表单正文解析器](https://github.com/expressjs/body-parser#bodyparserurlencodedoptions)

例外是多部分主体，有替代库来处理这个用例(通常用于文件上传)。