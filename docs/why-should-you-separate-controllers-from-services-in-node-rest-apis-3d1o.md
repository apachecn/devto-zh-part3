# 为什么要在节点 REST API 中将控制器与服务分开？

> 原文：<https://dev.to/ccleary00/why-should-you-separate-controllers-from-services-in-node-rest-apis-3d1o>

***原载于 [coreycleary.me](https://www.coreycleary.me/why-should-you-separate-controllers-from-services-in-node-rest-apis/)** 。这是我的内容博客的交叉帖子。我每一两周发布一次新内容，如果你想直接在收件箱里收到我的文章，你可以[注册我的时事通讯](https://www.coreycleary.me/about/)！我也定期发送备忘单和其他赠品。*

这是我上一篇文章[的后续，节点 REST API 中的控制器和服务有什么区别？](https://dev.to/ccleary00/what-is-the-difference-between-controllers-and-services-in-node-rest-apis-2c9)。在那篇文章中，我们讨论了两者之间的区别，以及哪种逻辑适用于哪种情况，但只是简单地提到了**为什么**你可能想要这样做。

你可能还在疑惑，*“为什么把两者分开是个好主意？”*。当**控制器**已经在工作的时候，为什么还要使用**服务**？

*为什么*是我们将在这篇文章中深入探讨的。

### 仅使用控制器

如果您有一个非常小的应用程序，比如只有几个简单的路由和控制器，并且没有将您的业务逻辑提取到任何服务中，您可能还没有对您当前的结构感到太烦恼。需要澄清的是，我们讨论的是项目中的服务**文件**，而不是单独的 REST 服务。

但是，如果您的应用程序已经超出了这个范围，我敢打赌您已经经历了以下几个难题:

*   控制器里面有很多代码，做很多事情——也叫做“胖控制器”。
*   与前一个密切相关，您的代码看起来很混乱。控制器进行 4 次或 5 次或更多次数据库/模型调用，处理可能随之而来的错误，等等。，那段代码可能看起来很难看。
*   您甚至不知道从哪里开始编写测试。
*   需求改变了，或者你需要添加一个新的特性，重构就变得非常困难。
*   代码重用变得几乎*不存在*。

### 分离有什么帮助？

重复上一篇关于这个主题的文章，你将从控制器和服务中分离出来的正是 web/HTTP 逻辑中的业务逻辑。

因此，您的控制器处理一些基本的事情，比如验证，从 HTTP 请求中提取出需要的数据(如果您使用 Express，那就是`req`对象),并决定数据应该到达什么服务。当然，最终会返回一个响应。

而服务负责繁重的工作，如调用数据库、处理和格式化数据、处理基于业务规则的算法等。不是针对 HTTP 层的，而是针对您自己的业务领域的。

做了这种分离之后，上面提到的那些痛苦会大大减轻，如果不是完全消失的话。这就是使用服务的美妙之处。是的，总会有重构和难以测试的东西，但是把东西放到服务中会使这变得容易得多。

而这就是*为什么*的原因。

让我们一个一个地检查一下这些问题。下面是一个代码示例，其中所有的逻辑都在控制器中，来自上一篇文章:

```
const registerUser = async (req, res, next) => {
  const {userName, userEmail} = req.body
  try {
    // add user to database
    const client = new Client(getConnection())
    await client.connect()

    await client.query(`INSERT INTO users (userName) VALUES ('${userName}');`)
    await client.end()

    // send registration confirmation email to user
    const ses = new aws.SES()

    const params = { 
      Source: sender, 
      Destination: { 
        ToAddresses: [
          `${userEmail}` 
        ],
      },
      Message: {
      Subject: {
        Data: subject,
        Charset: charset
      },
      Body: {
        Text: {
          Data: body_text,
          Charset: charset 
        },
        Html: {
          Data: body_html,
          Charset: charset
        }
      }
    }

    await ses.sendEmail(params) 

    res.sendStatus(201)
    next()
  } catch(e) {
    console.log(e.message)
    res.sendStatus(500) && next(error)
  }
} 
```

### 拥有大量代码的控制器，臃肿而混乱——又名“胖控制器”

你可能听说过“胖控”这个词。它是当你的控制器有太多的代码在里面，它看起来，嗯，胖。

这显然增加了阅读和理解代码的难度。拥有长而复杂的代码有时是不可避免的，但是我们希望这些代码是独立的，并且只负责一件事情。

因为控制器应该协调几个不同的东西，如果你没有把这些不同的东西提取到服务中，它们将全部在控制器中结束，增加那里包含的代码量。

通过将业务逻辑提取到服务中，控制器变得非常易于阅读。让我们来看看上面使用服务的代码的重构版本:

简化控制器:

```
const {addUser} = require('./registration-service')
const {sendEmail} = require('./email-service')

const registerUser = async (req, res, next) => {
  const {userName, userEmail} = req.body
  try {
    // add user to database
    await addUser(userName)

    // send registration confirmation email to user
    await sendEmail(userEmail)

    res.sendStatus(201)
    next()
  } catch(e) {
    console.log(e.message)
    res.sendStatus(500) && next(error)
  }
}

module.exports = {
  registerUser
} 
```

注册服务:

```
const addUser = async (userName) => {
  const client = new Client(getConnection())
  await client.connect()

  await client.query(`INSERT INTO users (userName) VALUES ('${userName}');`)
  await client.end()
}

module.exports = {
  addUser
} 
```

电子邮件服务:

```
const ses = new aws.SES()

const sendEmail = async (userEmail) => {
  const params = { 
    Source: sender, 
    Destination: { 
      ToAddresses: [
        `${userEmail}`
      ],
    },
    Message: {
      Subject: {
        Data: subject,
        Charset: charset
      },
      Body: {
        Text: {
          Data: body_text,
          Charset: charset 
        },
        Html: {
          Data: body_html,
          Charset: charset
        }
      }
    }
  }

  await ses.sendEmail(params) 
}

module.exports = {
  sendEmail
} 
```

现在我们有了一个“瘦控制器”,可以更容易地弄清楚发生了什么。

### 不能重用代码

另一个大问题是你不能重用你的代码。假设我们想在其他地方的另一个控制器中使用相同的电子邮件发送代码，可能是一个支持在 Reddit 风格的论坛上发送电子邮件进行后续评论的 API 路由的控制器。

我们必须复制代码并做一些调整，而不是仅仅制作一个通用的电子邮件服务来发送不同类型的电子邮件，然后将该服务导入到每个需要它的控制器中。

### 难以重构

根据以上两个问题，当我们没有将业务逻辑隔离到服务中时，重构和/或添加新功能会变得更加困难。

如果代码混乱且臃肿，那么在不意外破坏附近其他代码的情况下进行重构会困难得多。那是比较明显的一个。

但是，如果我们必须添加一个新特性或新功能呢？想象一下，如果我们现在有两个控制器，它们都在某个事件被触发后发送电子邮件(用户注册，用户收到对其帖子的后续评论，等等)。如果我们有两段非常相似的电子邮件代码，并且我们想改变电子邮件提供商(比如从 AWS 到 Sendgrid)。我们现在必须在两个地方做出改变！并且在两个地方改变测试。

### 难以编写测试

最后，这是一个大问题，当你不使用服务时，为你试图覆盖的逻辑编写测试变得更加困难。

当您的控制器中有多个不同的逻辑部分时，您必须覆盖多个代码路径。我甚至不知道从哪里开始为上面的纯控制器示例编写测试。因为它在做多件事情，我们不能孤立地测试每一件事情。

但是当代码更加孤立时，测试就变得更加容易。

对于服务，我们不需要处理 HTTP 请求对象或 web 框架。所以我们的测试不需要考虑这个。我们不必嘲弄 [`req`](https://expressjs.com/en/api.html#req) 和/或 [`res`](https://expressjs.com/en/api.html#res) 的对象。

一旦业务逻辑被提取到服务中，并且您已经为这些服务编写了测试，我认为您甚至可能不需要对控制器本身进行测试。如果存在决定将请求路由到哪个服务的逻辑，那么您可能需要为此进行测试。但是您甚至可以通过使用 [supertest](https://github.com/visionmedia/supertest) 编写一些端到端测试来测试它，并且只需调用 API route 来确保您得到正确的响应。

### 包扎

那么，您应该先从控制器开始，然后再将业务逻辑引入服务吗？还是应该从头开始跟他们学？我的建议是，在每个需要添加控制器的项目/新特性开始时，将它分成控制器和服务。我做的每一个应用程序都是这样。

如果您已经有一个不使用服务的应用程序，对于您需要添加的每个新功能，如果是一个新的路由/控制器，从服务方法开始。如果它不需要新的控制器，尝试重构现有的控制器来使用服务。

出于上面讨论的所有原因，从长远来看，你会让自己变得更容易，而且你会习惯于用这种方式练习构建项目。

我正在编写许多新内容，以帮助 Node 和 JavaScript 更容易理解。更容易，因为我觉得不需要像有时候那么复杂。如果你喜欢这篇文章，并发现它很有帮助[这里是再次链接](https://www.coreycleary.me/about/)订阅我的时事通讯！