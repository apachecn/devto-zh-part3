# 如何使用 JSON 文件构建 RESTful Node.js API 服务器

> 原文：<https://dev.to/kendalmintcode/how-to-build-a-restful-node-js-api-server-using-json-files-33m0>

[![Node JS logo](img/4b89883f725bd2bbb3204f6e29125dc9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--txLrYFgs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://robkendal.co.uk/static/0ee1766691b654a856246275d69f0570/d801a/nodejs-new-pantone-black_o-1.png)

由于最近需要创建一个本地 API 服务器来为 React 应用程序提供一些数据服务端点，我遇到了一些大麻烦。

尽管有很多文章详细介绍了如何在本地创建节点 API 服务器，但是有些文章没有涉及足够的内容(特别是如何使用的例子)或者过于复杂。此外，我想将来自物理 JSON 文件的*真实*数据作为一个简单的文件存储数据库来提供和编辑——这将节省大量涉及 MongoDB 或 SQL Lite 的 faff 都很好，但对这份工作来说有点过了。

哎呀，制作一个将数据保存到 JSON 文件的节点 API 服务器不是很容易吗？！

在经历了十年翻遍复杂文档和数百个 StackOverflow 答案之后，我找到了 Nic Raboy 早在 2015 年写的一篇精彩的[文章！我认为这篇文章遗漏了一些必要的“真实世界”端点，比如与真实数据存储交互的 POST 在本例中是 JSON 文件。](https://www.thepolyglotdeveloper.com/2015/10/create-a-simple-restful-api-with-node-js/)

但足够的序言，让我们做这件事！系好安全带，这将是一个漫长的过程！！

节点 JS 徽标

## 1。节点 API 服务器设置

首先，你需要确保你的机器上安装了 Node.js，最好是 NPM 的最新版本。

好吗？很好。因此，让我们创建一个新文件夹并导航到其中:

`mkdir ~/some/file/path/api-server\n`

放入文件夹，运行 NPM 的`init`脚本，用一些默认文件来设置项目，比如`package.json`。

```
cd ~/some/file/path/api-server
npm init 
```

Enter fullscreen mode Exit fullscreen mode

现在，我们需要安装一些东西，即:

*   **Express**>`npm install express`*[Express](https://expressjs.com/)是一个 web 应用程序框架，它提供了许多出色的特性和 HTTP 实用方法*
*   **正文解析器** > `npm install body-parser` *[正文解析器](https://www.npmjs.com/package/body-parser)是一个正文解析中间件，帮助处理发送到 express 服务器的请求正文*
*   **Nodemon** (可选)> `npm install nodemon --save-dev`完全可选，但是 [Nodemon](https://nodemon.io/) 非常有用，因为它充当了文件监视器，当我们对文件进行更改时，它会重新加载我们的服务器——非常方便！

添加几个文件夹和文件，使你的项目结构看起来像这样:

```
/projectfolder
--/data
--/routes
  --routes.js
server.js
package.json 
```

Enter fullscreen mode Exit fullscreen mode

打开您的`package.json`文件，检查`main`条目是否被设置为我们的`server.js`文件(我们的主入口点),并添加一个‘start’脚本来启动我们的 Nodemon 重载程序。

```
"main":  "server.js",  "scripts":  {  "start":  "nodemon server.js",  } 
```

Enter fullscreen mode Exit fullscreen mode

### 安装我们的软件包

设置几乎完成了，最后一件事是确保我们已经安装了软件包及其依赖项，这样它们就可以开始运行了。运行以下命令:

`npm install`

Bosh -设置都完成了，我们准备开始构建服务器。

[![blue](img/780dcdd0058e42b3b02e6e82dcefd677.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Pl3mZZak--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.unsplash.com/photo-1543076563-8a29893e162d%3Fixlib%3Drb-1.2.1%26q%3D80%26fm%3Djpg%26crop%3Dentropy%26cs%3Dtinysrgb%26w%3D1080%26fit%3Dmax%26ixid%3DeyJhcHBfaWQiOjExNzczfQ)

妮可·巴斯特 / [Unsplash](https://unsplash.com/?utm_source=ghost&utm_medium=referral&utm_campaign=api-credit) 拍摄

## 2。构建 server.js 文件

我们的第一个文件，一个入口点，将是`server.js`。我们将在这里构建 API 服务逻辑的主体。我们稍后将处理我们的路线和路线处理。

打开`server.js`并添加以下代码:

```
// load up the express framework and body-parser helper
const express = require('express');
const bodyParser = require('body-parser');

// create an instance of express to serve our end points
const app = express();

// we'll load up node's built in file system helper library here
// (we'll be using this later to serve our JSON files
const fs = require('fs');

// configure our express instance with some body-parser settings 
// including handling JSON data
app.use(bodyParser.json());
app.use(bodyParser.urlencoded({ extended: true }));

// this is where we'll handle our various routes from
const routes = require('./routes/routes.js')(app, fs);

// finally, launch our server on port 3001.
const server = app.listen(3001, () => {
    console.log('listening on port %s...', server.address().port);
}); 
```

Enter fullscreen mode Exit fullscreen mode

对于一个相对较小的文件来说，有很多事情要做。简单地说，在创建 express 和 body-parser 模块的实例和配置一些设置之前，文件的顶部实际上是加载到这些模块中的。

下一步是加载我们的路由文件:

```
const routes = require('./routes/routes.js')(app,fs); 
```

Enter fullscreen mode Exit fullscreen mode

这实现了几个目标:

*   我们主要路线处理起点的负载`routes.js`
*   将我们的 express 实例`app`和节点文件系统库`fs`传递到路由中。我们需要这两者来 a)将我们的路由处理附加到我们正在运行的服务器实例，以及 b)使用节点的文件系统助手访问我们的 JSON 文件

现在，我们不能完全运行我们的服务器。因为我们的路由文件中没有定义任何东西，所以很可能会出现一个`TypeError: require(...) is not a function`错误。不是很有帮助，但一旦我们添加一些路线，所有问题都会得到解决。

## 3。构建路线处理系统

到目前为止，一切都很好，但是没有路由的 API 服务器有什么用呢？！没有，就是这样。让我们补救这一点，并建立一些路线处理！

打开`/routes/routes.js`，输入如下空模块代码:

```
const appRouter = (app, fs) => {

};

module.exports = appRouter; 
```

Enter fullscreen mode Exit fullscreen mode

保存文件，让我们给这个小狗一个旋转。在您的终端中输入以下命令:

`npm start`

你应该会看到一个光荣的“监听端口 3001”的消息。精彩:)

[![Node API server starting up](img/7c28a368f1eeeabf93b25d58216773e3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gkxHmIAn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://robkendal.co.uk/static/19539fb30bb70920e13ccfe41cade369/b100d/api-started.png)

我们漂亮的 API 服务器启动消息

现在，让我们通过添加第一条路线来获得一些更有意义的应用程序。

### 添加我们的第一条路线

我应该指出，在这个阶段，我们将添加完整的虚拟(如果不是真实世界的话)数据和示例路线。在我们进行的过程中，您可以随意添加任何您喜欢的内容，或者使数据和路线与您和您的需求更加相关。

首先，我们需要添加一些 JSON 数据。导航到`/data`文件夹并创建一个新的`users.json`文件。用类似下面的东西填充它:

```
{  "1":  {  "name":  "king arthur",  "password":  "password1",  "profession":  "king",  "id":  1  },  "2":  {  "name":  "rob kendal",  "password":  "password3",  "profession":  "code fiddler",  "id":  2  },  "3":  {  "name":  "teresa may",  "password":  "password2",  "profession":  "brexit destroyer",  "id":  6  }  } 
```

Enter fullscreen mode Exit fullscreen mode

接下来，在`/routes`下创建一个名为`users.js`的新文件，并填充以下内容:

```
const userRoutes = (app, fs) => {

    // variables
    const dataPath = './data/users.json';

    // READ
    app.get('/users', (req, res) => {
        fs.readFile(dataPath, 'utf8', (err, data) => {
            if (err) {
                throw err;
            }

            res.send(JSON.parse(data));
        });
    });
};

module.exports = userRoutes; 
```

Enter fullscreen mode Exit fullscreen mode

好啊，我们的第一条路线就要准备好了。虽然没什么可看的，但请注意我们包含了我们的`users.json`数据文件路径，然后定义了我们的第一个路由`/users`，它使用了 GET 请求类型。现在，当我们运行服务器并根据用户路径运行 GET 请求时，我们将得到 JSON 格式的用户列表。

这里还要提到另外几点:

1.  我们在 JavaScript 文件中使用 ES6 格式，包括箭头函数([我还有一篇关于箭头函数的文章](https://robkendal.co.uk/blog/how-to-use-arrow-functions-in-javascript-es6/)就在这里)。
2.  在 GET 处理程序的主体中，我们调用传入的节点`fs`库来调用`readFile()`方法。这有助于我们访问底层文件系统并加载文件

### 将我们的第一条路由连接到主路由处理器

虽然完成了，但是我们的第一个路由不会做任何事情，除非主路由文件，`routes.js`知道它。因此，打开文件并添加以下内容来完成连接:

```
// load up our shiny new route for users
const userRoutes = require('./users');

const appRouter = (app, fs) => {

    // we've added in a default route here that handles empty routes
    // at the base API url
    app.get('/', (req, res) => {
        res.send('welcome to the development api-server');
    });

    // run our user route module here to complete the wire up
    userRoutes(app, fs);
};

// this line is unchanged
module.exports = appRouter; 
```

Enter fullscreen mode Exit fullscreen mode

## 4。测试我们的服务器

因此，我们已经设置了我们的项目，创建了一个简单的节点 API 服务器，在 JSON 文件中添加了一些由 API 提供服务的数据，并创建了一个路由处理程序来处理 API 请求。

让我们来测试一下我们的努力。运行与前面相同的命令来启动服务器:

`npm start`

加载完成后，您可以在浏览器中访问`http://localhost:3001/users`,在浏览器中，您会看到 JSON 文件中定义的用户列表。

[![results of the user list query](img/1c7049bbe40a8f09f5f1415170186371.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x1KY3CN7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://robkendal.co.uk/static/42bd161be10499bec4750bc9bcc3c747/edca4/api-server-userlist-resultes.png)

JSON 结果来自/users 的 GET 请求

我在 FireFox 中检查了这一点，但像这样的工作下载一个专用的 API 测试应用程序是值得的。我会推荐 Postman(我自己在本地和开发中使用它)，但是那里有很多。当我们需要通过 POST 或 PUT 请求传递数据时，这将变得更加重要。

你可以从他们的网站上下载《邮差》( Postman ),几乎适用于任何环境，而且个人使用是免费的(T2 )( T3)。

下面是我们使用 Postman 的 GET 请求:

[![results of user list using Postman app](img/98a627323f9f76abd8abb19c52e90281.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y1F0Lit3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://robkendal.co.uk/static/2f86f7144f8920c9052482ab2c574345/bd372/postman-get-users.png)

/用户在使用 Postman 桌面应用程序时收到请求

## 4a。跳到完整的示例

获取结果并列出它们是很好的，但是在现实世界中，我们通常需要完成 RESTful CRUD 周期的其余部分，即创建(POST)、更新(PUT)和删除(DELETE)。

在我们开始更复杂的例子之前，如果您愿意，可以直接从 GitHub 仓库下载完整的节点 API 服务器。

[在这里](https://github.com/bpk68/api-server-starter)查看 GitHub 上带有 JSON 文件数据的完整节点 API 服务器。

## 5。更进一步，完成 CRUD

让我们继续前进。在这一节中，我们将用一些创建、更新和删除方法来充实我们有限的 API 服务器，以改变存储在磁盘上的 JSON 文件。

### 重构我们所拥有的

作为[持续重构技术](https://robkendal.co.uk/blog/continuous-refactoring-avoiding-technical-debt-in-the-here-and-now/)的超级粉丝，我一直在寻找改进代码的方法。现在来看文件`users.js`，我们将开始用`fs.readFile()`和`fs.writeFile()`方法重复许多代码。现在让我们将它们重构到`users.js`文件的顶部:

```
const userRoutes = (app, fs) => {

    //...unchanged ^^^

    // refactored helper methods
    const readFile = (callback, returnJson = false, filePath = dataPath, encoding = 'utf8') => {
        fs.readFile(filePath, encoding, (err, data) => {
            if (err) {
                throw err;
            }

            callback(returnJson ? JSON.parse(data) : data);
        });
    };

   const writeFile = (fileData, callback, filePath = dataPath, encoding = 'utf8') => {

        fs.writeFile(filePath, fileData, encoding, (err) => {
            if (err) {
                throw err;
            }

            callback();
        });
    };

    // READ
    app.get('/users', (req, res) => {
        fs.readFile(dataPath, 'utf8', (err, data) => {
            if (err) {
                throw err;
            }

            res.send(JSON.parse(data));
        });
    });
};

module.exports = userRoutes; 
```

Enter fullscreen mode Exit fullscreen mode

除了 CRUD 的 Read 部分，所有其他操作都将涉及从 JSON 文件中读取数据，修改数据(添加、编辑、删除)，然后将其写回文件。

对于读取和写入部分，这将涉及几乎相同的过程，只有更改部分(中间的位)在请求之间会有所不同。

所以把未来多余的对文件的读写操作抽出来放到单独的抽象 helper 方法中是有意义的。这就是我们所做的。

我们新的重构的读/写方法看起来非常相似。他们是这样做的:

*   两者都接受在读/写操作完成后触发的回调参数
*   它们设置了一些默认的公共变量(比如将文件的路径设置为默认的数据文件——在本例中为`users.json`)
*   在内部，它们捕捉并抛出一个错误，或者在完成后调用回调函数

你可以[在 Node 网站](https://nodejs.org/api/fs.html)上阅读更多关于各种文件系统方法的内容。

### 创建新用户

我们将从 CRUD 的 create 部分开始，创建一个新用户。在我们的 read 方法下面添加下面的代码:

```
// ...

// CREATE
    app.post('/users', (req, res) => {

        readFile(data => {
            const newUserId = Object.keys(data).length + 1;

            // add the new user
            data[newUserId] = JSON.parse(req.body.data);

            writeFile(JSON.stringify(data, null, 2), () => {
                res.status(200).send('new user added');
            });
        },
            true);
    });

// ...rest of file 
```

Enter fullscreen mode Exit fullscreen mode

这里的操作相当简单。请注意，我们已经将`app`函数调用更改为`app.post()`，因为这是对 API 服务器的 POST 请求。路由仍然是`/users`,但是当请求类型是 POST 时将使用这个方法。

首先，我们调用新的 read 方法并传入一个回调函数。当文件被读取，我们得到一个 JSON 对象，`data`时，我们需要创建一个新的`user`对象。为此，我们将使用`Object.keys(data)`获取文件中对象的数量，并使其递增 1。

接下来，我们使用我们创建的新用户 ID 将新用户`JSON.parse(req.body.data)`添加到 users 对象中——注意，我们需要将其包装在`JSON.parse`中，以将传入的请求主体强制转换为我们可以读取并添加到当前数据中的格式。

最后，我们调用重构后的`writeFile()`方法，将我们的新用户数据字符串化并传入。一旦文件被成功写入，我们告诉响应对象返回到 API 调用者，这一行是`res.status(200).send('new user added')`——我们还向调用者添加了一条好消息，让他们知道它成功了。

**注意，**在更现实的设置中，您可能会返回更新后的用户列表，但现在，保持简单更容易，所以一点“新用户已添加”消息更好。

### 更新和删除用户

更新和删除方法应该看起来非常熟悉，因为它们类似于创建方法。

下面是我们的更新函数:

```
// UPDATE
app.put('/users/:id', (req, res) => {

    readFile(data => {

        // add the new user
        const userId = req.params["id"];
        data[userId] = JSON.parse(req.body.data);

        writeFile(JSON.stringify(data, null, 2), () => {
            res.status(200).send(`users id:${userId} updated`);
        });
    },
        true);
}); 
```

Enter fullscreen mode Exit fullscreen mode

这里的主要区别是我们调用的路由和从请求中获取的参数。

*   路线从`/users`变成了`/users/:id`。`:id`部分是一个可变参数，我们可以将它附加到 API URL 中。因此，如果您使用 PUT 请求——处理更新的可接受的 RESTful 方式，您可以调用与此路径匹配的`/users/4`
*   我们获取这个`:id`值来帮助我们使用`req.params[id]`找到要编辑的正确用户
*   然后，我们通过获取请求体的 JSON 版本来更新用户列表中的匹配用户—`JSON.parse(req.body.data)`

**注意，**我们在这里没有增加验证输入数据的额外复杂性。这是生产 API 开发的**必要部分，但是为了简单起见，我们相信您会将正确的数据发送到 API 请求中！**

为了使事情圆满，下面是我们的删除函数:

```
// DELETE
app.delete('/users/:id', (req, res) => {

    readFile(data => {

        // add the new user
        const userId = req.params["id"];
        delete data[userId];

        writeFile(JSON.stringify(data, null, 2), () => {
            res.status(200).send(`users id:${userId} removed`);
        });
    },
        true);
}); 
```

Enter fullscreen mode Exit fullscreen mode

更新路径几乎相同，主要区别在于我们使用 JavaScript `delete`关键字在保存回文件之前从用户列表中删除匹配的用户。

### 使用 JSON 文件进行数据存储的全功能节点 API 服务器

所以你走吧。一个功能齐全的 API 服务器，满足您的任何需求。你可以[访问用于 starter API 服务器](https://github.com/bpk68/api-server-starter)的 GitHub 存储库，下载、签出或获取你喜欢的内容。

如果我错过了什么，或者你想知道更多，请在评论中告诉我，或者发邮件给我。