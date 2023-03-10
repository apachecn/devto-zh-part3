# 通过构建一个 URL Shortener 应用程序来学习 Node.js 和 MongoDB

> 原文：<https://dev.to/ayoisaiah/learn-nodejs-and-mongodb-by-building-a-url-shortener-app-4kk8>

在本文中，您将学习如何使用 Node.js 和 MongoDB 构建 URL Shortener 应用程序。这是我们将要建造的东西的现场演示。你可以在这个 [GitHub repo](https://github.com/freshman-tech/url-shortener) 中找到该项目的完整源代码。

## 先决条件

我假设对 JavaScript 和命令行有基本的了解。如果您以前没有构建过一个基本的节点应用程序，您可能希望先从这里的开始，然后稍后再回到本教程。

您还需要在计算机上安装 Node.js 和 npm。您可以访问 [Node.js 网站](https://nodejs.org/en/download/)查看您的操作系统的安装说明。 [npm](https://npmjs.com) 与 Node 捆绑在一起，所以一旦你安装了 Node，你也可以使用`npm`命令。

我在构建这个项目时使用的版本如下:

*   Node.js v11.2.0
*   npm 版本 6.6.0

您可以通过在终端中运行以下命令来查看已经安装的 Node 和`npm`的版本:

```
node -v
npm -v 
```

Enter fullscreen mode Exit fullscreen mode

## 抓取启动文件

在这个 [GitHub 库](https://github.com/Freshman-tech/url-shortener-starter-files)获取这个项目的启动文件。将 repo 克隆到您的计算机上，并将`cd`复制到创建的目录中。然后从项目根目录运行`npm install`，安装`package.json`文件中指定的所有依赖项。随着我们的进展，我将在后面讨论每个依赖项的作用。

## 安装 MongoDB

MongoDB 是现代 web 应用程序中常用的免费开源 NoSQL 文档数据库。你需要在你的机器上安装它。在撰写本文时，最新的稳定版本是 **4.0.5** 。这是我在整个教程中使用的版本。

下面是 [Linux](https://docs.mongodb.com/manual/administration/install-on-linux/) 、 [macOS](https://docs.mongodb.com/manual/tutorial/install-mongodb-on-os-x/) 和 [Windows](https://docs.mongodb.com/v3.2/tutorial/install-mongodb-on-windows/) 的安装说明。如果你像我一样在 Ubuntu 上，你可以用`apt` :
安装 MongoDB

```
sudo apt update
sudo apt install -y mongodb 
```

Enter fullscreen mode Exit fullscreen mode

您可以使用`mongo --version`来检查您安装了什么版本的 mongoDB。

数据库服务器应该在安装过程之后自动启动，但是您应该在从这一步继续之前验证这一点。在 Ubuntu 上，可以使用下面的命令来检查 mongoDB 服务器的状态:

```
sudo systemctl status mongodb 
```

Enter fullscreen mode Exit fullscreen mode

您应该会看到以下输出:

[![Terminal showing active status of MongoDB server](img/7c13117aacf24fc53a81b75f8808256f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yBMNSrVh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freshman.tech/assets/diimg/learn-mongodb/mongodb-status-ubuntu.png)

如果没有，您可以使用以下命令启动它:

```
sudo systemctl start mongodb 
```

Enter fullscreen mode Exit fullscreen mode

## 设置一个基本的节点服务器

浏览项目目录中的`src`文件夹，您会看到我们有一个`server.js`文件和一个`public`文件夹，其中包含应用程序前端的标记和样式。`server.js`文件是大部分应用程序代码将被写入的地方。

与之前的 Node 教程不同，在之前的教程中，我使用内置的 http 模块来设置节点服务器，我们将使用 [Express](https://expressjs.com/) ，这是一个流行的 Node.js web 应用程序框架。

还有其他的 web 框架，但是 Express 足够简单，有很好的文档记录和很好的支持，所以在您的应用程序中使用它时应该不会遇到很多问题。

如果您浏览一下`package.json`文件，您会看到`express`包是我们之前安装的依赖项的一部分。让我们继续使用它在`server.js` :
中设置节点服务器

```
const express = require('express');

const app = express();

app.set('port', process.env.PORT || 4100);
const server = app.listen(app.get('port'), () => {
  console.log(`Express running → PORT ${server.address().port}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过在终端中运行`npm start`来启动服务器。我正在利用 [Nodemon](https://www.npmjs.com/package/nodemon) 包在节点服务器发生变化时自动重启，这样我们就不用自己动手了。

## 设置应用前端

如前所述，应用前端位于`public`文件夹中。我们需要在服务器上设置一个新的路由，这样当用户访问应用程序时，HTML 文件将被发送并呈现在浏览器中。

将`server.js`文件修改成这样:

```
const express = require('express');
const path = require('path');

const app = express();

app.get('/', (req, res) => {
  const htmlPath = path.join(__dirname, 'public', 'index.html');
  res.sendFile(htmlPath);
});

app.set('port', process.env.PORT || 4100);
const server = app.listen(app.get('port'), () => {
  console.log(`Express running → PORT ${server.address().port}`);
}); 
```

Enter fullscreen mode Exit fullscreen mode

[path](https://nodejs.org/api/path.html) 是 Node.js 中的内置模块，它允许我们链接到 Node.js 中的目录和文件路径，`sendFile()`方法取文件的绝对路径，所以`__dirname`用来避免硬编码路径。`__ dirname`是执行文件所在的目录，所以`path.join(__dirname, 'public', 'index.html')`会解析到`src/public/index.html`。

在浏览器中导航至`http://localhost:4100`。请注意，HTML 已正确呈现。然而，尽管`style.css`在`index.html`中被正确链接，样式还是丢失了。

[![Missing styles on the Application frontend](img/100138dc4075baf36a9b2883a8c6a5cd.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ndlc-J3v--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freshman.tech/assets/diimg/learn-mongodb/frontend-no-styles.png)

当浏览器遇到对`style.css`的引用时，它向服务器发出一个文件请求。但是由于我们没有配置我们的服务器来处理静态文件(比如图像、CSS 和 JavaScript)的请求，服务器什么也不做，请求失败，页面没有样式。

要修复这种情况，我们需要配置 express 来正确处理静态文件的请求。我们可以使用 express 中的内置中间件函数来做到这一点，如下所示:

```
// beginning of the file

const app = express();

app.use(express.static(path.join(__dirname, 'public')))

// rest of the file 
```

Enter fullscreen mode Exit fullscreen mode

现在重新加载页面。它应该正常工作:

[![Application styles are properly loaded](img/5ed7dda91ef68b10b2427120b84914f3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YER13rgJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freshman.tech/assets/diimg/learn-mongodb/frontend-with-styles.png)

## 向服务器提交表单

当用户提交表单时，我们需要编写客户端 JavaScript 来将表单输入的内容提交给服务器。

我们可以在不使用任何客户端 JavaScript 的情况下做到这一点，方法是将表单的`action`属性设置为服务器上的路由，并将`method`属性设置为`POST`，但是我选择在这里使用 JavaScript，这样我们就可以处理响应并向用户显示缩短的 url，而无需刷新整个页面。

在`public`目录下创建一个新的`main.js`文件，并在其中添加以下代码:

```
const form = document.querySelector('.url-form');
const result = document.querySelector('.result-section');
form.addEventListener('submit', event => {
  event.preventDefault();

  const input = document.querySelector('.url-input');
  fetch('/new', {
    method: 'POST',
    headers: {
      'Accept': 'application/json',
      'Content-Type': 'application/json'
    },
    body: JSON.stringify({
      url: input.value,
    })
  })
    .then(response => {
      if (!response.ok) {
        throw Error(response.statusText);
      }
      return response.json();
    })
    .then(data => {
      while (result.hasChildNodes()) {
        result.removeChild(result.lastChild);
      }

      result.insertAdjacentHTML('afterbegin', `
        <div class="result">
          <a target="_blank" class="short-url" rel="noopener" href="/${data.short_id}"> ${location.origin}/${data.short_id} </a>
        </div>
      `)
    })
    .catch(console.error)
}); 
```

Enter fullscreen mode Exit fullscreen mode

这段代码监听表单上的`submit`事件，阻止表单提交，并向服务器发出 POST 请求，在请求体中输入表单的值。我们将 body 对象包装在`JSON.stringify`中的原因是，我们可以在服务器上将它作为 JSON 使用。

请注意，我们将数据发送到尚未在服务器上创建的`/new`路线。我们将在下一节中创建它。在此之前，确保在您的`index.html`文件中，在结束主体标签:
之前引用`main.js`

```
<!DOCTYPE html>
<html lang="en">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <meta http-equiv="X-UA-Compatible" content="ie=edge">
  URL Shortener
  <link rel="stylesheet" href="style.css" type="text/css">
</head>
<body>
  // rest of the code

  <script src="main.js"></script>
</body>
</html> 
```

Enter fullscreen mode Exit fullscreen mode

## 访问服务器上的表单体

让我们继续创建将处理要缩短的 URL 的`/new`路由。将此添加到`server.js` :
中的根路由下

```
app.post('/new', (req, res) => {

}); 
```

Enter fullscreen mode Exit fullscreen mode

我们需要做的第一件事是在请求体中访问客户机发送的 JSON 数据。为此，我们需要使用[主体解析器](https://www.npmjs.org/package/body-parser)包。这个包解析所有传入的请求体，并使它们在`req.body`上可访问。

因为这个包已经安装好了，我们可以马上在`server.js` :
中使用它

```
const express = require('express');
const path = require('path');
const bodyParser = require('body-parser');

const app = express();
app.use(express.static(path.join(__dirname, 'public')))
app.use(bodyParser.json());

// rest of the file 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们应该能够在`/new`路由:
的`req.body`属性中访问请求体

```
app.post('/new', (req, res) => {
  console.log(req.body);
}); 
```

Enter fullscreen mode Exit fullscreen mode

您可以通过在表单中输入一个 URL 并提交来尝试一下。然后导航到运行服务器的终端，查看终端中打印的 JSON 数据。

[![Request body is printed to the terminal](img/109d537858962997dec8f968330f681a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--qlmcS2Rm--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freshman.tech/assets/diimg/learn-mongodb/json-data-in-terminal.png)

这意味着我们可以使用`req.body.url`访问 url。

## 验证 URL

在我们缩短 URL 之前，我们需要验证提交的 URL 是否有效。客户端验证是在浏览器中为我们处理的，因为我们已经将输入的类型设置为`url`，所以如果值没有有效的 URL 结构，表单将不会被提交。

为了使应用程序更加健壮，我们还需要在服务器上验证 URL。有几个 npm 包可以处理这个问题，但是我选择使用几个内置的节点模块。

我们需要做的第一件事是检查 URL 是否具有有效的结构，然后我们执行 DNS 查找以查看该域是否正常运行。像`https://google.com`这样的域名将通过两个测试，但是`http://jidfsdm.com`将无法通过第二个测试，因为那个站点不存在。

要求`server.js` :
顶部内置 [dns](https://nodejs.org/api/dns.html) 模块

```
const dns = require('dns'); 
```

Enter fullscreen mode Exit fullscreen mode

然后将`/new`路线更改如下:

```
app.post('/new', (req, res) => {
  let originalUrl;
  try {
    originalUrl = new URL(req.body.url);
  } catch (err) {
    return res.status(400).send({ error: 'invalid URL' });
  }

  dns.lookup(originalUrl.hostname, (err) => {
    if (err) {
      return res.status(404).send({ error: 'Address not found' });
    };
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果输入 URL 具有有效的结构， [URL 类](https://nodejs.org/api/url.html#url_the_whatwg_url_api)将返回一个新的具有多个属性的`URL`对象。否则，它抛出一个错误，我们可以`catch`并将其发送回客户端。

如果 URL 输入通过了第一个测试，那么我们通过将 URL(域)的[主机名](https://developer.mozilla.org/en-US/docs/Web/API/URL/hostname)部分传递给`dns.lookup`来检查域是否运行。如果是这样，我们可以连接到我们的 MongoDB 实例，并创建 url 的简化版本，正如您将看到的那样。

## 设置环境变量

环境变量是配置你的程序应该如何工作的好方法。它们是存储在运行程序的本地系统上的键值对，可以从代码中访问。

将应用程序配置数据(如 API 密钥、令牌、密码和其他敏感细节)设置为环境变量，而不是将其硬编码到程序本身，这被认为是最佳实践。这可以防止您意外地将它暴露给其他人，并且还可以在不修改代码的情况下很容易地更改这些值。

在 Node.js 中，可以通过`process.env`对象访问环境中定义的变量。您可以通过 Node.js REPL 查看该项目的内容，如下所示:

[![Node REPL showing contents of process.env](img/3532db467a03942b3b919763d2b0f4b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WuVdxYT6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freshman.tech/asseimg/learn-mongodb/process-env-poster.png)

除了默认出现的操作系统变量，我们可以使用一个`.env`文件创建项目特定的变量。为了避免将这些文件签入源代码控制，您应该将`.env`添加到您的`.gitignore`中

在项目目录的根目录下创建一个`.env`文件，并将下面的代码粘贴到其中:

```
DATABASE=mongodb://localhost:27017 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们添加了本地 MongoDB 实例的 URL 作为环境变量。端口 27017 是 MongoDB 运行的端口。接下来要做的是将`.env`中定义的值加载到`process.env`中。最简单的方法是使用 [dotenv](https://www.npmjs.com/package/dotenv) 包，它已经是我们应用依赖的一部分。

在`server.js`的最顶端添加以下内容:

```
require('dotenv').config();

// rest of the file 
```

Enter fullscreen mode Exit fullscreen mode

这将读取项目根目录下的`.env`文件的内容，解析其内容并初始化`process.env`上的值。现在，你可以访问`process.env.<KEY>`下的任何设定变量。

## 连接 MongoDB

让我们继续并连接到我们在`server.js`中的本地 MongoDB 实例，如下所示:

```
require('dotenv').config()

const express = require('express');
const bodyParser = require('body-parser');
const path = require('path');
const dns = require('dns');
const { MongoClient } = require('mongodb');

const databaseUrl = process.env.DATABASE;

const app = express();
app.use(bodyParser.urlencoded({ extended: false }));
app.use(bodyParser.json());

app.use(express.static(path.join(__dirname, 'public')))

MongoClient.connect(databaseUrl, { useNewUrlParser: true })
  .then(client => {
    app.locals.db = client.db('shortener');
  })
  .catch(() => console.error('Failed to connect to the database'));

// rest of the file 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们从 [mongodb](https://mongodb.github.io/node-mongodb-native/) 导入`MongoClient`，它是与 Node.js 中的 mongodb 实例交互的本地驱动程序。然后，我们连接到在`DATABASE`环境变量中指定的 MongoDB 实例。

如果连接成功，我们将获得对 MongoDB 实例客户机的引用，并可以使用`client.db()`方法选择一个数据库。请注意，如果数据库尚不存在，此方法将创建数据库。

这里，我们选择一个对`shortener`数据库的引用，并将该引用存储在由`express`提供的对象`app.locals`中。这个对象允许我们设置局部变量，这些变量在应用程序的整个生命周期中保持不变，并且可以通过`req.app.locals`在其他中间件功能(可以访问`req`或`res`对象的功能)中访问。

我们在`app.locals.db`中存储数据库引用的原因是，我们可以重用 db 对象，而不必打开另一个到 MongoDB 实例的连接。

## 缩短网址

下一步是实际缩短 URL 并将其存储在数据库中。为了给每个 url 创建一个唯一的短 id，我们将使用 [nanoid](https://github.com/ai/nanoid) 包。

要求它在`server.js`的上方，其他`require`语句的下方:

```
const nanoid = require('nanoid'); 
```

Enter fullscreen mode Exit fullscreen mode

然后在`server.js`中创建新的`shortenURL`函数如下:

```
// beginning of the file

MongoClient.connect(databaseUrl, { useNewUrlParser: true })
  .then(client => {
    app.locals.db = client.db('shortener');
  })
  .catch(() => console.error('Failed to connect to the database'));

const shortenURL = (db, url) => {
  const shortenedURLs = db.collection('shortenedURLs');
  return shortenedURLs.findOneAndUpdate({ original_url: url },
    {
      $setOnInsert: {
        original_url: url,
        short_id: nanoid(7),
      },
    },
    {
      returnOriginal: false,
      upsert: true,
    }
  );
};

// rest of the file 
```

Enter fullscreen mode Exit fullscreen mode

在向数据库中添加任何数据之前，我们需要引用一个集合。我们可以通过使用`db.collection()`方法来实现。如果该集合尚不存在，则创建它。

在我们缩短 URL 并将其添加到数据库之前，我们需要检查 URL 是否已经被缩短，以防止一个 URL 出现重复的数据库条目。我们可以在集合上使用`findOneAndUpdate()`方法来实现这一点，该方法允许我们修改已经存在于数据库集合中的文档，或者创建一个不存在的文档。

这个方法需要几个参数。第一个是用于筛选集合的对象。这里我们传递一个对象，它的`original_url`属性与我们要缩短的 url 相匹配。如果数据库中的一个文档匹配这个过滤器，它将被返回并根据第二个参数中设置的[更新操作符](https://docs.mongodb.com/manual/reference/operator/update/)进行更新。

只有当文档被插入时,`$setOnInsert`操作符才允许我们设置文档的值。这意味着如果文档已经存在，它将不会被修改，但是如果不存在，它将被创建，其值被设置为我们在`$setOnInsert`中设置的值。

在这种情况下，文档将有两个属性:`original_url`是要缩短的 url，而`short_id`是该 url 的唯一的 7 个字符的 id。

我们还需要将`upsert`选项设置为`true`。这可以确保在文档不存在时创建文档。否则，`$setOnInsert`没有作用。最后，将`returnOriginal`设置为`false`确保`findOneAndUpdate`在一个文档被向上插入时返回新文档，这是我们在本例中想要的。

我们可以像这样使用`/new`路线中的`shortenURL()`功能:

```
app.post('/new', (req, res) => {
  let originalUrl;
  try {
    originalUrl = new URL(req.body.url);
  } catch (err) {
    return res.status(400).send({error: 'invalid URL'});
  }

  dns.lookup(originalUrl.hostname, (err) => {
    if (err) {
      return res.status(404).send({error: 'Address not found'});
    };

    const { db } = req.app.locals;
    shortenURL(db, originalUrl.href)
      .then(result => {
        const doc = result.value;
        res.json({
          original_url: doc.original_url,
          short_id: doc.short_id,
        });
      })
      .catch(console.error);
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

此时，插入的文档将作为 JSON 发送给客户机。它将显示在页面上，如下所示:

[![URL is shortened](img/1f7a469d54eadae052ddbc0b2a814432.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--G9zWJHbd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freshman.tech/assets/diimg/learn-mongodb/shortened-url.png)

## 为所有缩短的网址设置一个总括路由

除了`/`和`/new`路由之外，我们需要处理其他对缩短 URL 的请求。具体来说，我们需要将它们重定向到原始的 URL。下面是我们如何使用 Express:

```
app.get('/:short_id', (req, res) => {
  const shortId = req.params.short_id;

}); 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们使用命名路由参数来捕获 URL 的`short_id`部分的值。然后可以在`short_id`属性下的`req.params`对象中访问这个值。

一旦我们有了短 id，我们需要检查数据库中是否存在具有该短 id 的 url。为此，让我们在`shortenURL` :
下面创建一个新函数

```
const checkIfShortIdExists = (db, code) => db.collection('shortenedURLs')
  .findOne({ short_id: code }); 
```

Enter fullscreen mode Exit fullscreen mode

`findOne`方法返回一个与传递给它的过滤器对象相匹配的文档，如果没有匹配过滤器的文档，则返回`null`。

然后，我们可以像这样在我们的总括路线中使用函数:

```
app.get('/:short_id', (req, res) => {
  const shortId = req.params.short_id;

  const { db } = req.app.locals;
  checkIfShortIdExists(db, shortId)
    .then(doc => {
      if (doc === null) return res.send('Uh oh. We could not find a link at that URL');

      res.redirect(doc.original_url)
    })
    .catch(console.error);
}); 
```

Enter fullscreen mode Exit fullscreen mode

如果短 id 存在于我们的数据库集合中，我们将把用户重定向到与该短 id 相关联的`original_url`。否则，将向用户发送一条错误消息。

现在，你应该能够缩短长网址，访问缩短的网址，并被重定向到原来的网址。

## 用图形用户界面可视化你的数据库

一个 GUI 将允许我们连接到我们的 MongoDB 实例，并可视化所有呈现的数据。您还可以创建新数据、更新现有数据以及执行其他类似的操作。

有几个 MongoDB GUIs，但我喜欢使用的是 [NoSQLBooster](https://nosqlbooster.com/) 。它适用于 Linux，macOS 和 Windows，你可以在这里下载[。](https://nosqlbooster.com/downloads)

安装并打开应用程序后，默认情况下，它应该会连接到您的本地 MongoDB 实例。否则，您可以点击左上角的**连接**按钮，并从那里连接到它。

连接后，您将能够查看数据库中的所有集合，并与这些集合中的数据进行交互。

[![NoSQLBooster for Linux](img/546166f94bcda3073b0434c0ac1e35fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gD-ZRLKC--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freshman.tech/assets/diimg/learn-mongodb/nosqlbooster.png)

## 部署到 Heroku

在我们将应用程序部署到 Heroku 之前，我们需要首先创建一个云托管的 MongoDB 实例。我们可以使用 [MongoDB Atlas](https://www.mongodb.com/cloud/atlas) 来实现。当您被重定向到**创建新集群**页面时，在该链接处创建一个帐户并创建一个新集群。

接下来，点击**安全**标签，点击**添加新用户**按钮。给用户名和密码，然后点击**添加用户**按钮。

[![Add user in MongoDB Atlas](img/8822a77166db444c6b15bf878d2ad821.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--laTHnFJ0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freshman.tech/assets/diimg/learn-mongodb/atlas-add-user.png)

接下来，点击 **IP 白名单**，然后**添加 IP 地址**。这允许您选择哪些 IP 地址可以访问您的群集。由于这只是一个演示应用程序，您可以通过点击**允许从任何地方访问**按钮来允许从任何地方访问。

[![Add IP Address in MongoDB Atlast](img/cdf01a3bf405efdabc6dace7e0f6128e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--7t5_9JwB--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freshman.tech/assets/diimg/learn-mongodb/atlas-ip.png)

接下来，返回概览，点击**连接**按钮。选择**连接您的应用**，然后选择**短 SRV 连接字符串**。请注意这个字符串，因为我们很快就会在 Heroku 上使用它。

[![Atlas SRV connection string](img/3e09ae51b669529209fa1bccec25736c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MQhBdxQN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freshman.tech/assets/diimg/learn-mongodb/atlas-srv.png)

前往 Heroku 网站[注册一个免费账户](https://signup.heroku.com/)。激活您的账户后，[点击此链接](https://dashboard.heroku.com/new-app?org=personal-apps)创建一个新应用。给它一个唯一的名称。我把我的名字叫做**新生缩写**。

接下来，[按照这里的说明](https://devcenter.heroku.com/articles/heroku-command-line)在你的机器上安装 Heroku CLI。然后在终端运行`heroku login`命令登录到你的 Heroku 账号。

确保您已经为项目初始化了一个 git 存储库。如果没有，运行项目目录根目录下的`git init`命令，然后运行下面的命令将 heroku 设置为 git repo 的远程。用应用程序的名称替换`<app name>`。

```
heroku git:remote -a <app name> 
```

Enter fullscreen mode Exit fullscreen mode

接下来，在你的项目目录(`touch Procfile`)的根目录下创建一个`Procfile`，并粘贴以下内容:

```
web: node src/server.js 
```

Enter fullscreen mode Exit fullscreen mode

接下来，在`engines`键下的`package.json`文件中指定正在运行的节点的版本。我指定了版本`11.2.0`,因为那是我在电脑上运行的版本。您应该更改该值以匹配您计算机上的节点版本。

```
{  "name":  "url-shortener",  "version":  "1.0.0",  "description":  "URL Shortener Demo",  "main":  "src/server.js",  "scripts":  {  "start":  "npx nodemon src/server.js"  },  "repository":  {  "type":  "git",  "url":  "git+https://github.com/freshman-tech/url-shortener.git"  },  "keywords":  [  "url shortener",  "mongodb",  "nodejs"  ],  "author":  "Ayo Isaiah",  "license":  "MIT",  "bugs":  {  "url":  "https://github.com/freshman-tech/url-shortener/issues"  },  "homepage":  "https://github.com/freshman-tech/url-shortener#readme",  "devDependencies":  {  "nodemon":  "^1.18.9"  },  "dependencies":  {  "body-parser":  "^1.18.3",  "dotenv":  "^6.2.0",  "express":  "^4.16.4",  "mongodb":  "^3.1.10",  "nanoid":  "^2.0.1"  },  "engines":  {  "node":  "11.2.0"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

在你部署应用程序之前，在 Heroku 仪表盘中打开**设置**标签，点击**显示配置变量**。这是您为应用程序设置环境变量的地方。

正如我们之前对本地`.env`文件所做的那样，输入`DATABASE`作为键，输入来自 MongoDB Atlas 的 SRV 字符串作为值，然后单击 **Add** 。记住用您之前创建的用户的密码替换那个字符串中的`<PASSWORD>`。

[![Set up Heroku config values](img/fdcaf19b95b7bef2323753b1fad922a0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rc6Y308s--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://freshman.tech/assets/diimg/learn-mongodb/heroku-config.png)

请注意，根据程序运行的环境来更改数据库位置是多么容易，而无需修改应用程序代码。这是使用环境变量进行项目配置的一个巨大优势。

最后，使用以下命令提交您的代码并将其推送到 Heroku remote:

```
git add .
git commit -m "Initial commit"
git push heroku master 
```

Enter fullscreen mode Exit fullscreen mode

一旦部署过程完成，您就可以打开`https://<your-app-name>.heroku.com`来查看和测试您的项目。

## 总结起来

我们已经成功地创建了一个全功能的 URL shortener，并且在这个过程中学习了 Express 和 MongoDB 的基础知识。我们还学习了如何在 MongoDB Atlas 上设置基于云的 MongoDB 实例，以及如何将应用程序部署到 Heroku。

我希望这个练习对你有帮助。如果你对本教程有任何疑问，请在下面留下评论，我会尽快回复你。

感谢阅读！

*最初发布于 2019 年 1 月 24 日[新生科技](https://freshman.tech/url-shortener/)。[订阅我的时事通讯](https://freshman.tech/newsletter/)，将我最新的 JavaScript 和 CSS 教程发送到你的收件箱。*