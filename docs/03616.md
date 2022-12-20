# 快递 i18n 轻松搞定！

> 原文：<https://dev.to/bmanley91/express-i18n-made-easy-2d2o>

*更新:一个示例项目演示了如何工作，可以在这里找到-[https://github.com/bmanley91/express-i18n-example](https://github.com/bmanley91/express-i18n-example)T3】*

最近，我的团队负责我们产品的国际化。我们需要在具有多个面向用户的客户端的应用程序中同时支持英语和西班牙语。因为我们唯一的共同点是我们的 Express 后端，我们决定它将提供消息字符串供我们的客户使用。这让我们减少了项目的代码影响，并保证了客户将拥有一致的用户信息。

本文将介绍我们国际化快递后端所采用的方法。

# 设置舞台

假设我们已经有了一个非常简单的 express 应用程序。它有一个`/greeting`端点，用一条消息来响应`GET`请求。

```
const express = require('express');
const app = express();

app.get('/greeting', (req, res) => {
    const response = 'hello!';
    res.status(200);
    res.send(response);
});

app.listen(8080, () => console.log('App listening on port 8080!')); 
```

如果您以前使用过 Express，这个例子可能看起来非常熟悉。如果没有，我推荐[这篇教程](https://medium.com/@adnanrahic/hello-world-app-with-node-js-and-express-c1eb7cfa8a30)，它帮助我在学习 Node 时入门并运行。

# 进入消息文件

通常，您会希望避免在代码中设置直接发送给最终用户的字符串。消息文件或消息文件集合背后的思想是集中保存消息字符串的位置，以便可以容易地定位和更新它们。正如我们将在本文后面看到的，处理 i18n 的库通常需要利用消息文件。

我们将创建一个名为`message.json`的消息文件，其中包含我们的问候。

```
{  "greeting":  "hello!"  } 
```

为了保持有序，我们还将创建一个`resources`目录来保存我们的新文件。在这之后，我们的目录结构看起来像这样。

```
.
├── server.js
├── resources
|   └── message.json
├── package.json
├── package-lock.json
├── node_modules 
```

我们将修改我们的应用程序，像这样从这个文件中提取数据。

```
...

const messages = require('./resources/messages.json');

app.get('/greeting', (req, res) => {
    const response = messages.greeting;
    res.status(200);
    res.send(response);
});
... 
```

到目前为止，没有什么真正的改变。我们只是集中了我们的信息所在的位置。

# 国际化！

接下来，我们将介绍为我们完成大部分工作的模块。我们将使用以下内容:

*   **i18 下一个** -我们的核心 i18n 框架
*   **i18 next-express-middleware**-为我们的快速路线增加 i18n 功能
*   **i18 next-node-fs-back end**-让我们从消息文件中提取消息

在介绍完这些依赖项之后，我们需要做一个组织上的修改。i18next 可以使用我们的目录结构来决定什么语言使用什么文件。我们将把我们的`messages.json`文件重命名为`translation.json`，并把它移动到一个新的目录`resources/locales/en/`。

我们的目录结构现在应该是这样的。

```
.
├── server.js
├── resources
|   └── locales
|        └── en
|            └── translation.json
├── package.json
├── package-lock.json
├── node_modules 
```

现在我们已经拥有了所有需要的东西，让我们来看看如何让它运行起来。

## 初始化

我们这样初始化`i18next`。

```
const i18next = require('i18next');
const Backend = require('i18next-node-fs-backend');
const i18nextMiddleware = require('i18next-express-middleware');

i18next
    .use(Backend)
    .use(i18nextMiddleware.LanguageDetector)
    .init({
        backend: {
            loadPath: __dirname + '/resources/locales/{{lng}}/{{ns}}.json'
        },
        fallbackLng: 'en',
        preload: ['en']
    });

const app = express();

app.use(i18nextMiddleware.handle(i18next)); 
```

这里有一个相当大的数量，所以让我们走一遍。

首先，对于`use(Backend)`，我们告诉 i18next 使用 **i18next-node-fs-backend** 作为它的后台资源。这意味着我们将从文件系统中获取字符串。

其次，我们正在用`use(i18nextMiddleware.LanguageDetector)`设置语言检测。这让我们的应用程序根据消费者发送的`Accept-Language`头决定使用什么语言。

接下来，我们实际上初始化 i18next。

`backend`对象指定了 i18next 将从哪个路径加载我们的消息。花括号中的参数将在以后发挥作用。`{{lng}}`代表目录中的语言，`{{ns}}`代表文件中字符串的“名称空间”。命名空间对于需要提供大量字符串的大型应用程序非常有用。因为我们只提供几个字符串，所以这里只使用一个名称空间。

`preload`参数接受 i18next 将在初始化时加载的语言数组。`fallback`定义您的默认语言，如果某个消息没有翻译的字符串，将使用该语言。

最后，通过`app.use(i18nextMiddleware.handle(i18next));`，我们告诉 Express 使用 i18next 的中间件。

好了 i18n 都设置好了！让我们现在实际使用它。

## t

下一个快速中间件带给我们的东西之一是我们快速请求的`t`功能。这个函数将在 i18next 已经加载的消息中寻找一个键，并以指定的语言返回它。

下面是我们如何在示例项目中使用`t`。

```
const express = require('express');
const i18next = require('i18next');
const Backend = require('i18next-node-fs-backend');
const i18nextMiddleware = require('i18next-express-middleware');

i18next
    .use(Backend)
    .use(i18nextMiddleware.LanguageDetector)
    .init({
        backend: {
            loadPath: __dirname + '/resources/locales/{{lng}}/{{ns}}.json'
        },
        fallbackLng: 'en',
        preload: ['en']
    });

const app = express();

app.use(i18nextMiddleware.handle(i18next));

app.get('/greeting', (req, res) => {
    const response = req.t('greeting');
    res.status(200);
    res.send(response);
});

app.listen(8080, () => console.log('Example app listening on port 8080!')); 
```

现在，我们的应用程序正在发送回一个从 i18next 获得的字符串！这并不令人兴奋，因为到目前为止我们只有我们的`en`语言文件。所以让我们设置另一种语言。

(很遗憾，我只会一种语言。所以我们在这里用“你好”和“你好”。😔)

像这样在`resources/locales/es/translation.json`中创建一个新的语言文件。

```
{  "greeting":  "¡hola!"  } 
```

接下来，通过将`es`区域添加到`preload`数组来修改 i18next init 调用。

```
...
.init({
        backend: {
            loadPath: __dirname + '/resources/locales/{{lng}}/{{ns}}.json'
        },
        fallbackLng: 'en',
        preload: ['en', 'es']
    });
... 
```

## 测试出来

我们已经设置了翻译文件，并配置 i18next 来使用它们。接下来，我们需要测试它。让我们用`node server.js`启动 express 服务器。

我们的应用程序将根据`Accept-Language`头决定使用什么语言。正如我们在这里设置的那样，它将为`es`返回西班牙语，为任何其他内容返回英语，包括没有发送语言头的情况。

我们将使用`curl localhost:8080/greeting`来测试我们的基本情况。没有标题，我们应该得到这个响应。

```
hello! 
```

现在让我们实际测试一下我们的 i18n 与`curl localhost:8080/greeting -H "Accept-Language: es"`一起工作。我们应该得到这样的回应。

```
¡hola! 
```

我们做到了！我们现在可以用多种语言为用户显示字符串了！现在开始享受翻译应用程序中每条消息的乐趣。

翻译愉快！

如果你想看这里列出的所有东西的工作示例，请查看 https://github.com/bmanley91/express-i18n-example。