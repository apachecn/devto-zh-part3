# 向您的网络静态站点添加无服务器功能

> 原文：<https://dev.to/raymondcamden/adding-serverless-functions-to-your-netlify-static-site-41om>

在过去的一周左右，我一直在玩 [Netlify](https://www.netlify.com/) 的(有点)新的无服务器功能，内置 [Lambda 函数](https://www.netlify.com/docs/functions/)支持。我进入无服务器的一个原因是因为它能很好地处理静态网站，我很好奇 Netlify 的集成是如何工作的。我花了一段时间才把事情做好，但我不得不说，这是 Netlify 投资组合中又一个令人印象深刻的新成员。我早就说过他们是静态网站托管的“黄金标准”,这再次证明他们完全做到了。我确实很难把事情做好，所以下面是一个简单的介绍，重点放在让我困惑的事情上。

## 先决条件

就入门前需要了解的内容而言，真的没有太多必要。是的，这个特性是建立在 Amazon 的 Lambda 无服务器平台上的，但是你不需要真正了解任何关于这个平台的东西。我只接触过 Lambda，因为它太复杂了。它在我今年要做的事情清单上，但我知识的缺乏并没有妨碍我在 Netlify 中使用它。

也就是说，一些以前的无服务器经验将是有帮助的，特别是在知道什么是有意义的方面。这里什么有意义？假设您正在使用(或考虑使用)Netlify 来托管一个静态站点，可能有些情况下您仍然需要一些动态的东西。在我的例子中，我需要调用一个 API，它需要一个在客户端 JavaScript 中无法使用的键。我唯一的功能是调用 API，对结果进行一些调整，然后返回一组数据。

我可以在任何无服务器的平台上这样做，但是把它放在我的静态站点的同一个项目中是非常吸引人的。把所有东西都放在一个地方感觉更好。我不是说这总是有意义的，但对我的项目来说确实如此。

## 你的第一个函数

首先，您需要一个文件夹来存储您的所有代码。目前，Netlify 支持 JavaScript 和 Go。你可以给你的文件夹起任何你想要的名字，并把它放在你的目录树中的任何地方，但是注意你的文件不能放在你选择的目录下的子目录中。所以如果你决定把`/funcs`作为无服务器函数的位置，你**不能**为不同的函数组创建子目录。(这是蹩脚的，但是如果你最终有很多，你可以简单地使用文件名，比如 dao.get.js 和 rss.generate.js。)

您的 JavaScript 代码必须遵循以下格式:

```
exports.handler = function(event, context, callback) {
    // your server-side functionality
} 
```

`event`对象包含关于请求的信息，而`context`似乎与特定于网络的信息更相关，比如与认证支持的集成。我在测试中没有使用任何一个，所以我不能对此发表评论。`callback`参数是你如何返回信息给调用者，遵循一个非常标准的形式:`callback(error, result)`。`result`值将是一个简单的 JavaScript 对象，比如:

```
{ 
    statusCode: 200,
    body: 'I must be a string'
} 
```

这里需要注意一些事情。首先，`statusCode`似乎是可选的，*除了在本地测试时的*(更多内容即将推出)。所以我会把它包括进去。

接下来，`body`必须是一个字符串。从我在 Lambda 文档上读到的内容来看，`body`可以是任何可以传递给`JSON.stringify`的东西，对我来说，这意味着它会自动将非简单的结果转换成 JSON。也许我错了，但是对于 Netlify，你绝对必须自己使用 JSON.stringify。下面是一个完整的例子:

```
exports.handler = function(event, context, callback) {

    let data = {
        name:'ray',
        foo:[1,2,4,6],
        time:Date.now()
    };

    console.log('data is '+JSON.stringify(data));

    callback(null, {
        statusCode:200,
        body:JSON.stringify(data)
    });

} 
```

### 部署和测试

现在您有了自己的文件，您可以像其他静态站点一样将它部署到 Netlify。对我来说，我有一个与 GitHub 库相关的站点，所以我所要做的就是提交代码。在您的网络设置中，您需要进入您的**站点设置**，然后选择“功能”选项卡。在“部署设置”下，您必须设置函数目录。这告诉 Netlify 在哪里可以找到你的函数。

到目前为止还不错，但是你实际上如何调用函数呢？这是我第一次碰壁的地方。这个是有记录的，但是我感觉不是很清楚。

无论您为 functions 文件夹设置什么，URL 都将是:`yoursite/.netlify/functions/foo`，其中`foo`是您的 JavaScript 文件名减去`.js`扩展名。

一旦你完成了所有这些，如果你点击你的站点的**功能**标签，你可以看到你所有已部署功能的列表:

[![List of Netlify Functions](img/5bd8f97777e0d70d7a09d343749cb6f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--HKuXEYov--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2019/01/nf1.jpg)

点击一个特定的功能，你会得到一个实时日志。它是可用的，但如果有一个可搜索的过滤器就更好了。

[![List of Netlify Functions](img/18c486dabdfd1c0f3f383ab58b104dc7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FZUAfqlf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://static.raymondcamden.cimg/2019/01/nf2.jpg)

就是这样…简单的东西！

## 测试异步功能

这是另一件让我纠结的事情。我希望主文档已经澄清了这一点或者展示了一个快速的例子。对于 Lambda 来说，这可能是一件“已知”的事情，但是正如我所说的，一个简单的例子可以节省我一些时间。为了使用异步函数，您可以返回承诺。所以比如:

```
exports.handler = async (event, context, callback) => {

    return new Promise((resolve, reject) => {

        setTimeout(() => {
            resolve({
                statusCode:200,
                body:"promises1111"
            });
        }, 2000);

    });

}; 
```

这很好，但是我想做一个异步函数的全部原因是为了构建一个 API 包装器。为此，我想使用 NPM 或其他图书馆的`request-promise`,在那里我遇到了最困难的文档。

### 使用 NPM 模块(和本地测试)

好吧，接下来会有些艰难。我真的不知道如何使用 NPM 模块，所以我决定尝试在本地测试。Netlify 有一个您可以使用的 CLI(`netlify-lambda`)，但是它根本没有被很好地文档化。算了吧-是的-但是-它需要一些东西。:)如果我在那里工作，我会重写这一部分，让它更清楚一点。直到我读了 Travis Horn 的这个帖子，[“Netlify Lambda Functions from Scratch”](https://travishorn.com/netlify-lambda-functions-from-scratch-1186f61c659e)，我才能够弄清楚本地测试(以及通过代理，NPM)是如何工作的。

好的，首先，您需要在本地将 CLI *安装到您的文件夹中。是的，医生是这么说的，但我是在全球范围内安装的。*

其次，你需要*两个*文件夹。第一个文件夹是您的源文件夹，可以与您之前使用的文件夹相同。第二个文件夹是你的函数的“构建”版本。要进行本地测试，您必须使用一个`netlify.toml`文件，这是一种在文件中指定设置的方式(ok，哑状态),而不是在网络管理中指定。这里有一个例子:

```
[build]
    Functions = "lambda" 
```

注意，我指定的是*构建*文件夹，而不是源文件夹。那么你如何建造呢？如我所说，您需要在本地安装`netlify-lambda`命令。然后我听从了 Horn 的建议，在我的`package.json`文件中添加了两个脚本:

```
"scripts": {
    "start:lambda": "netlify-lambda serve func",
    "build:lambda": "netlify-lambda build func"
  }, 
```

然后我更新了`netlify.toml`以包含构建命令:

```
[build]
    Functions = "lambda"
    Command = "npm run build:lambda" 
```

我们快完成了。Netlify 文档说您可以使用`netlify-lambda serve func`(或`npm run start:lambda`)启动本地服务器进行测试，其中最后一个参数是源目录。**然而**直到我先运行了 build 命令，这才开始工作。**然而**(再次)看起来你只需要建造一次。之后，运行 serve 将设置服务器并注意到文件的更改。**然而**(上一次，老实说)，我相信如果你做了一个*新的*函数，你需要再次运行构建。从理论上讲，这是非常罕见的，你会花时间在一个函数上。

我要把整件事再写一遍，只是为了确保清楚。

*   您需要在初始测试之前和创建任何新文件之后运行 build 命令。
*   之后，运行 serve 命令，就可以进行本地测试了。

好的，那么你如何使用 NPM 模块呢？你`npm install`就是这样。它被添加到你的站点的本地`package.json`中，是的，它刚刚工作了。理论上你不必在本地测试。我认为只要你指定如何构建你的代码，用你的依赖项更新你的 package.json，那么你就可以提交 GitHub 并在 Netlify 的服务器上测试，但是本地测试相当快。

希望这是有意义的——现在让我们考虑一个真实的例子。

## 构建 MailChimp 包装器

几周前，我的好友布莱恩·里纳尔迪和我创办了一份名为 [CodaBreaker](https://twitter.com/codabreaker) 的音乐通讯。这是通过 MailChimp 运行的，结果他们有一个 API。Brian 建议使用 API 建立一个定制的以前问题的列表，这样我们就可以在一个比现在更好的 URL 托管一个静态页面-【https://us6.campaign-archive.com/home/? T2】u = 231 f 8 aff 82 a 1 f 82 e 4d 6 ab 23d 8&id = d 00 ad 8719 a。 [MailChimp 文档](https://developer.mailchimp.com/)做得很好，它们的 API，至少对于读取数据来说，非常简单*。下面是我构建的 API 包装器，用来获取过去问题的列表。* 

```
const axios = require('axios');

const apiRoot = 'https://us6.api.mailchimp.com/3.0/campaigns?list_id=d00ad8719a&fields=campaigns.long_archive_url,campaigns.send_time,campaigns.settings.title&status=sent';

exports.handler = (event, context, callback) => {
    axios({
        method:'get', 
        url:apiRoot,
        auth:{
            'username':'anythingreally',
            'password':process.env.MC_API
        }
    }).then(res => {
        callback(null, {
            statusCode: 200,
            body: JSON.stringify(res.data)
        });
    })
    .catch(err => {
        callback(err);
    });

}; 
```

基本上，我点击`/campaigns`结束点，过滤到一个特定的列表，过滤结果关键字到 URL、发送日期和标题。就是字面意思。另一个有趣的地方是这里使用了`process.env.MC_API`。网络功能可以访问您在站点设置中指定的环境设置。当然，我是通过网站而不是通过`netlify.toml`文件做到的。我希望它能更复杂些。就这样了。你可以在这里自己运行:[https://elated-mayer-56be7c.netlify.com/.网络/功能/问题](https://elated-mayer-56be7c.netlify.com/.netlify/functions/getIssues)。

现在我所需要做的就是建立一个一页的 HTML 前端，在上面添加一些 Vue.js(有了 Vue 一切都会更好)，Coda Breaker 的“营销网站”就完成了。正如我上面所说的，我喜欢我的 HTML 和我的无服务器功能都很好，包含在一个 GitHub repo 中，完成后我可以很容易地推送到 Netlify。(我确实为新网站获得了一个域名，但我还没有时间部署它。)

## 最后的想法

除了一些文档问题(以及我缺乏理解)，这是完美的。这太棒了。这是为你的静态站点使用 Netlify 的另一个原因！随着我探索得越来越多，想到更多的集成(可能是我博客上的一些内容)，我会与大家分享。我很想听听我的读者是如何使用它的。在下面给我一些例子的评论吧！