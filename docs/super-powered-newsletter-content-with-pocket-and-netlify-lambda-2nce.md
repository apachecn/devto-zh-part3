# 超级强大的时事通讯内容与口袋和网络 Lambda

> 原文：<https://dev.to/hugo__df/super-powered-newsletter-content-with-pocket-and-netlify-lambda-2nce>

> 一个 Netlify Lambda 从[口袋](https://getpocket.com)中获取所有“时事通讯”文章的例子。

Pocket 是一个应用程序和网络服务，用于管理来自互联网的文章阅读列表。它被广泛使用，并紧密集成到 Firefox 浏览器中。我发现我广泛使用它来保存文章(通常是关于开发的)。

对于 [Enterprise Node.js 和 JavaScript 时事通讯](https://buttondown.email/hugo),我有一个“来自 web”部分，它是由来自 Web 的链接填充的。由于大多数链接都存储在 Pocket 上，所以我构建了一个 lambda 来从 Pocket 中获取带有“时事通讯”标签的帖子。然后我从一个 [Hugo](https://gohugo.io) 时事通讯文件生成器中获取 lambda。

见 [src/lambda/newsletter.js](https://github.com/HugoDF/pocket-newsletter-lambda/blob/master/src/lambda/newsletter.js) 中的 lambda 代码在【github.com/HugoDF/pocket-newsletter-lambda】T2 的仓库中。

在[https://pocket-newsletter-lambda.netlify.com/](https://pocket-newsletter-lambda.netlify.com/)运行它，甚至[在网络上部署你自己的]((https://app.netlify.com/start/deploy?repository=https://github.com/HugoDF/pocket-newsletter-lambda))。

该应用程序是使用 TailwindCSS 设计的，你可以在[github.com/HugoDF/netlify-lambda-tailwind-static-starter](https://github.com/HugoDF/netlify-lambda-tailwind-static-starter)看到一个启动项目。

## 口袋取数逻辑

口袋专用逻辑的主体是`fetchBookmarks`函数，它执行以下操作:

*   使用消费者密钥和访问令牌从 Pocket API 获取
    *   通过`state: 'all'`以获得存档和未存档的帖子
    *   使用`tag: 'newsletter'`来获取标签为`newsletter`的帖子
    *   `detailType: 'complete'`表示 API 返回更完整的数据
*   将响应转换成一个简单的列表`{ title, url, excerpts, authors }`(所有这些字段都是字符串)
*   归还名单

参见代码(完整源代码在[github.com/HugoDF/pocket-newsletter-lambda](https://github.com/HugoDF/pocket-newsletter-lambda))

```
async function fetchBookmarks(consumerKey, accessToken) {
  const res = await axios.post('https://getpocket.com/v3/get', {
    consumer_key: consumerKey,
    access_token: accessToken,
    tag: 'newsletter',
    state: 'all',
    detailType: 'complete'
  });
  const {list} = res.data;
  // List is a key-value timestamp->entry map
  const entries = Object.values(list);
  return entries.map(
    ({
      given_title,
      given_url,
      resolved_url,
      resolved_title,
      excerpt,
      authors,
      rest
    }) => ({
      ...rest,
      title: given_title || resolved_title,
      url: given_url || resolved_url,
      excerpt,
      authors: authors
        ? Object.values(authors)
            .map(({name}) => name)
            .filter(Boolean)
            .join(',')
        : ''
    })
  );
} 
```

## Netlify Lambda 请求验证和正文解析

### HTTP 谓词和有效负载存在验证

lambda 只支持带有主体的 POST，因此:

```
if (event.httpMethod !== 'POST') {
  return {
    statusCode: 404,
    body: 'Not Found'
  };
}

if (!event.body) {
  return {
    statusCode: 400,
    body: 'Bad Request'
  };
} 
```

### 从表单提交和 AJAX/JSON 请求中解析 POST 请求体

我们支持 URL 编码的表单 POST 请求(例如，当 JS 在演示页面上被禁用时)和 JSON 请求。

正文到达时要么是 base64 编码的(如果使用 URL 编码的表单正文请求)，要么不是。这由`event`上的`isBase64Encoded`标志表示。

使用`Buffer.from(event.body, 'base64').toString('utf-8)`解析 Node 中 base64 编码的字符串。

要将正文从 URL 编码的形式转换成对象，需要使用下面的函数，该函数适用于具有简单字段的帖子。

```
function parseUrlEncoded(urlEncodedString) {
  const keyValuePairs = urlEncodedString.split('&');
  return keyValuePairs.reduce((acc, kvPairString) => {
    const [k, v] = kvPairString.split('=');
    acc[k] = v;
    return acc;
  }, {});
} 
```

这里是 lambda 中的功能:

```
const {
    pocket_consumer_key: pocketConsumerKey,
    pocket_access_token: pocketAccessToken
  } = event.isBase64Encoded
    ? parseUrlEncoded(Buffer.from(event.body, 'base64').toString('utf-8'))
    : JSON.parse(event.body); 
```

如果消费者密钥或访问令牌丢失，我们将发送 400:

```
if (!pocketConsumerKey || !pocketAccessToken) {
  return {
    statusCode: 400,
    body: 'Bad Request'
  };
} 
```

### 发送适当的失败响应

我们试图在`fetchBookmarks`中，将这个功能分解到[【口袋抓取逻辑】](#pocket-fetching-logic)。

当 Pocket API 因请求错误而失败时，我们希望将响应信息发送回客户端。如果故障无法识别，500。当`fetchBookmarks`成功时，发送一个带数据的 200。

谢天谢地，当 axios 失败时，它有一个关于错误的`response`属性。这意味着我们的“代理后袋 API 错误”用例以及其他两种情况都很容易满足:

```
try {
  const bookmarks = await fetchBookmarks(pocketConsumerKey, pocketAccessToken);

  return {
    statusCode: 200,
    body: JSON.stringify(bookmarks)
  };
} catch(e) {
  if (e.response) {
    return {
      statusCode: e.response.statusCode,
      body: `Error while connecting to Pocket API: ${e.response.statusText}`
    }
  }
  return {
    statusCode: 500,
    body: e.message
  }
} 
```

## 样本响应

参见[github.com/HugoDF/pocket-newsletter-lambda#sample-response](https://github.com/HugoDF/pocket-newsletter-lambda#sample-response)或在[pocket-newsletter-lambda.netlify.com/](https://pocket-newsletter-lambda.netlify.com/)亲自试用该应用程序。

## 现实世界中的袖珍时事通讯

在[codewithhugo.com](https://codewithhugo.com)上，lambda 不从请求中读取访问令牌和消费者密钥。取而代之的是 GET endpoints 从环境变量中读取令牌和密钥。

这样做其实更简单。我们在网络构建配置中设置了`POCKET_CONSUMER_KEY`和`POCKET_ACCESS_TOKEN`。然后将 lambda 更新为以下内容:

```
const {POCKET_CONSUMER_KEY, POCKET_ACCESS_TOKEN} = process.env;

// keep fetchBookmarks as is

export async function handler(event) {
  if (event.httpMethod !== 'GET') {
    return {
      statusCode: 404,
      body: 'Not Found'
    };
  }

  const bookmarks = await fetchBookmarks(POCKET_CONSUMER_KEY, POCKET_ACCESS_TOKEN);
  return {
    statusCode: 200,
    body: JSON.stringify(bookmarks)
  };
} 
```

[codewithhugo.com](https://codewithhugo.com)跑在[雨果](https://gohugo.io)的身上。为了生成一个新的简讯文件，我利用了 [Hugo 自定义原型](https://gohugo.io/content-management/archetypes/)(即。具有生成它的模板的内容类型)。

原型看起来如下:

```
{{- $title := replace (replaceRE `[0-9]{4}-[0-9]{2}-[0-9]{2}-` "" .Name) "-" " " | title -}}
---
title: {{ $title }} - Code with Hugo
---

{{- $newsletterBookmarks := getJSON "https://codewithhugo.com/.netlify/functions/newsletter" }}
{{ range $newsletterBookmarks }}
[{{ .title }}]({{.url}}) by {{ .authors }}: {{ .excerpt }}
{{ end }} 
```

一旦时事通讯被生成和编辑，它就被添加到 [buttondown.email](https://buttondown.email) 中，你可以在[企业节点. js 和 JavaScript 档案](https://buttondown.email/hugo/archive)中看到这种方法的结果。

[unsplash-logo
杯子先生/杨奇煜·巴拉](https://unsplash.com/@iammrcup?utm_medium=referral&utm_campaign=photographer-credit&utm_content=creditBadge)