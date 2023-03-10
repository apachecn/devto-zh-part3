# 我试过 Nodejs，我喜欢它

> 原文：<https://dev.to/emadmokhtar/i-tried-nodejs-and-i-like-it-4a1o>

上个月有机会尝试 Node.js 框架；我决定使用 [Node.js](https://nodejs.org/en/) 和 [express.js](https://expressjs.com/) 来构建 REST APIs。除了使用 [Django](https://www.djangoproject.com/) 构建 Web 应用程序，我在日常工作中经常使用 JavaScript，但很多时候我需要开发一个前端功能，这是在我需要使用 JavaScript 的时候。我一直在用 JavaScript 写前端逻辑，但从来没有用它写过后端逻辑。我接受了挑战，使用 [express.js](https://expressjs.com/) 开发了一个简单的 REST APIs。

**开发环境**

要开始开发，我需要准备好我的机器，所以我通过 Homebrew 安装了 [node.js](https://nodejs.org/en/) (我是 macOS 用户)，这相当容易，node.js 安装自带[NPM](https://www.npmjs.com/)“NPM 是 node.js 的包管理器，它就像是 [pypi](https://pypi.org/) 或 Python 的 pip”。我使用了 [Visual Studio 代码](https://code.visualstudio.com/)作为文本编辑器。现在我准备走了💪🏻。

**我学到了什么？**

在构建 REST APIs 时，我学到了很多东西，主要是我正在体验新的开发堆栈。来看看我学过的部分。

**Node.js**

[Node.js](https://nodejs.org/en/) 是后端引擎；它使用谷歌 Chrome V8 JavaScript 引擎在你的机器上运行 JavaScript 代码，而不是浏览器。Node 不是一种语言，它是一个引擎。

**Express.js**

[Express.js](https://expressjs.com/) 是一个使用 Node.js 的 web 框架；它是一个极简主义的 web 框架“微框架”，接收请求并以响应回复，微框架意味着它没有 ORM、模板引擎等。你可以感觉它就像[的蟒蛇烧瓶框架](http://flask.pocoo.org/)。

**蛇**

由于我更熟悉 PostgreSQL DB 引擎，所以我尝试使用 [sequelize JS](http://docs.sequelizejs.com/) 来构建我的模型，但使用 RDBMS 系统实现我的功能变得非常棘手。我将 ORM 切换到[mongose JS](https://mongoosejs.com/)，将我的数据库引擎切换到 [MongoDB](https://www.mongodb.com/) 。我从一开始就错了，但是在切换到 [MongoDB](https://www.mongodb.com/) 之后，实现该特性就容易多了，因为它是 Node.js 社区中首选的数据库引擎。

**异步编程🤔**

习惯 [JavaScript 异步“承诺”](https://developers.google.com/web/fundamentals/primers/promises)的本质对我来说是一个很大的挑战，我习惯了同步编程；因此，这是一个巨大的范式转变🤯。在日常生活中，我使用的是 [Django](https://www.djangoproject.com/) ，它默认不是异步的[“Django 有一个实现异步的路线图”](https://www.aeracode.org/2018/06/04/django-async-roadmap/)。 [Python 从 3.5 版本](https://docs.python.org/3.5/library/asyncio.html)开始就有了异步编程，但是框架也需要适应它，使之成为异步的，就像 [requests-future](https://github.com/ross/requests-futures) 。

**最终想法🧐**

我喜欢使用 Node.js 社区很棒，在 NPM 上没有你找不到的包，这是一个如此活跃的社区，但我不喜欢社区中教程的质量，大多数教程针对初学者或天真的 nodejs 开发者，是的，我们需要初学者和天真的 nodejs 开发者的教程，但高级教程呢？

异步编程挑战让我大开眼界；我需要深入挖掘这种编程方式，尤其是 Python。幸运的是有一个关于 [Talk Python 培训关于 Python 中异步的视频课程](https://training.talkpython.fm/courses/explore_async_python/async-in-python-with-threading-and-multiprocessing)，我想我会看的。