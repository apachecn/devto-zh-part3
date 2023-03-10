# 读取和解析 POST/PATCH/PUT 请求 JSON 或 form 主体，具有 Express 和无依赖关系

> 原文：<https://dev.to/hugo__df/read-and-parse-post-patch-put-request-json-or-form-body-with-express-and-no-dependencies-3meh>

当被要求处理请求体中的数据时，以前使用过 Express(用于 [Node.js](https://nodejs.org/en/) )的快速、非个性化、极简的 web 框架)的开发人员会使用[体解析器](https://github.com/expressjs/body-parser)库。

他们可能不知道的是，body-parse 是 Express 的一个依赖项，它的主要 JSON 解析和 url 编码的主体解析功能被公开为`express.json()`和`express.urlencoded()`。一个解析内置在 Express 中的 JSON 请求体的中间件。

> 注:`express.json`和`express.urlencoded`存在于 Express 4.16+中

不要将全局`express.json()`中间件与`res.json()`函数混淆，后者将主体作为 JSON 发送回来(并将内容格式头设置为 JSON)。

json bodies 的代码在[https://codesandbox.io/s/olrn6x3n19?fontsize=14](https://codesandbox.io/s/olrn6x3n19?fontsize=14)，你可以在[https://olrn6x3n19.sse.codesandbox.io/](https://olrn6x3n19.sse.codesandbox.io/)查看 app。

URL 编码的表单体的代码在[https://codesandbox.io/s/6njqzmyw4k?fontsize=14](https://codesandbox.io/s/6njqzmyw4k?fontsize=14)，你可以在[https://6njqzmyw4k.sse.codesandbox.io/](https://6njqzmyw4k.sse.codesandbox.io/)测试。

在项目中的真实使用，你也可以通过 Hugo 找到更多关于代码的快速帖子

*   `express.urlencoded`:[https://github . com/Hugo df/express-nunjucks-tailwind/blob/master/server . js](https://github.com/HugoDF/express-nunjucks-tailwind/blob/master/server.js)
*   `express.json`:[https://github . com/Hugo df/express-postgres-starter/blob/master/server . js](https://github.com/HugoDF/express-postgres-starter/blob/master/server.js)

## 解析 JSON 体

### 插播示例

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

你可以看到它在[https://olrn6x3n19.sse.codesandbox.io/](https://olrn6x3n19.sse.codesandbox.io/)运行，测试它使用:

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

### 扩展配置选项

您可以像这样将选项传递给它(所有的值都是这些选项的默认值):

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

## 解析 URL 编码的表单主体

我们可以用同样的方式使用`express.json`来使用`express.urlencoded`。

### 插播示例

我们通过`extended: false`来避免警告。

```
const express = require('express')
const app = express()

app.use(express.urlencoded({ extended: false }))
app.post(
  '/test',
  (req, res) => res.json(req.body)
)

const PORT = process.env.PORT || 3000
app.listen(PORT, () => {
  console.log(`Server listening on port ${PORT}`)
}) 
```

你可以看到它在[https://6njqzmyw4k.sse.codesandbox.io/](https://6njqzmyw4k.sse.codesandbox.io/)运行，使用页面上的表格测试它。

### 扩展配置选项

您可以像这样将选项传递到`express.urlencoded`(所有值都是这些选项的默认值):

```
app.use(express.json({
  extended: true,
  inflate: true,
  limit: '100kb',
  parameterLimit: 1000,
  type: 'application/x-www-form-urlencoded',
  verify: undefined
})) 
```

`express.json`的完整 API 文档位于[expressjs.com/en/api.html#express.urlencoded](https://expressjs.com/en/api.html#express.urlencoded)。

下面是选项的运行过程。

`extended`在`qs`(当`true`)和`querystring`(当`false`)库之间切换。`qs`允许对丰富的对象和数组进行编码，`querystring`不允许。对于简单的形式(所有的键-值对),保持这个为`false`,当你发送数组/值映射时，保持这个为`true`。

`inflate`控制是否处理压缩/收缩的请求体。当它被设置为假时，压缩/放气的物体将被拒绝。

`limit`控制最大身体尺寸。它可以是与[字节](https://www.npmjs.com/package/bytes)库兼容的字节数或字符串。

`parameterLimit`控制我们试图从 url 编码的表单主体中解析的字段的最大数量。

`type`控制中间件试图解析的内容类型。此选项的值可以是字符串、字符串数组或函数。在某种程度上支持内容类型通配符，因为字符串被传递给了 [type-is](https://www.npmjs.com/package/type-is) 库。

`verify`是带有`verify(req, res, buf, encoding)`签名的可选函数。`buf`是包含原始请求体的缓冲区。`verify`可用于通过抛出错误来中止解析。

## 关于主体解析器和其他主体解析库的更多信息

这意味着我们不需要安装 body-parser 来加载我们的大部分主体。Express 内置的 JSON 和 URL 编码的表单主体解析涵盖了大多数用例。我们可能需要一个多部分主体的库，有替代库来处理这个用例(通常用于文件上传)。

仍然有一些非常特殊的情况，可能仍然需要 body-parser(参见 github.com/expressjs/body-parser 的文档):

*   [JSON 主体解析器](https://github.com/expressjs/body-parser#bodyparserjsonoptions)
*   [原始体解析器](https://github.com/expressjs/body-parser#bodyparserrawoptions)
*   [正文解析器](https://github.com/expressjs/body-parser#bodyparsertextoptions)
*   [URL 编码的表单正文解析器](https://github.com/expressjs/body-parser#bodyparserurlencodedoptions)