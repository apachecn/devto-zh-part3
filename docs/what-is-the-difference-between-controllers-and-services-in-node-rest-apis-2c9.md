# 节点 REST API 中的控制器和服务有什么区别？

> 原文：<https://dev.to/ccleary00/what-is-the-difference-between-controllers-and-services-in-node-rest-apis-2c9>

***原载于 [coreycleary.me](https://www.coreycleary.me/what-is-the-difference-between-controllers-and-services-in-node-rest-apis/)** 。这是我的内容博客的交叉帖子。我每一两周发布一次新内容，如果你想直接在收件箱里收到我的文章，你可以[注册我的时事通讯](https://www.coreycleary.me/about/)！我也定期发送备忘单和其他赠品。*

如果您已经在 Node(或其他语言)中构建了 REST API，那么您可能已经使用了“控制器”的概念来帮助组织您的应用程序。也许您将调用放在您的数据库或模型中，调用一些其他端点，并添加一些业务逻辑来处理返回。

那个控制器大概是这样的:

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

但是你可能没怎么用过，甚至没听说过“服务”这个概念。或者你可能听说过这个概念，并且听说你应该使用它们，但是你在问，与你的控制器相比，那里有什么逻辑。

在 API 中使用服务是我在 Node-land 中不常见到的，但它是对 API 结构的一个强大补充，将使测试、代码组织和代码重用变得更加容易。

那么，如果它们是构建你的 API 的一种有用的方式，那么 **服务**到底是什么呢？

为了回答这个问题，我们将进入**控制器和服务之间的区别是什么**和**什么去哪里**，以便您可以更恰当地构建您的节点 API。

### 经理/工人的类比

我能想到的解释这两者之间差异的最有帮助的方法之一是使用一个来自商业世界的类比——“经理”/“工人”二分法。我们将使用简化的刻板印象来描述经理和工人的工作——我绝不是说所有的经理都有一种角色，而工人有另一种角色！

我们打个比方，**控制者**就是**管理者**，而**服务者**就是**工作者**。

如果你想一想经理的角色是什么，他/她通常:

*   管理收到的工作请求
*   决定哪个工人应该做这项工作
*   将工作分成相当大的单元
*   通过那项工作
*   如果工作需要多人处理多件事情，协调工作
*   但是**自己不做**工作(这里再次使用一个基本的刻板印象！)

而且，工人通常:

*   接收来自经理的请求
*   找出完成请求所涉及的各个细节
*   通常只关心他/她必须完成的任务
*   不负责对“更大”的图景做出决策
*   完成任务/要求所需的实际工作
*   将完成的工作返回给经理

这里最重要的主题是,**经理/控制者**接收工作，决定谁应该做，然后传递要完成的请求。而**工作者/服务**是接受请求并实际完成请求的人。您可能有多个工作人员处理不同的请求/任务，以完成更大的画面，经理将它们结合在一起，这样就有意义了。

### 什么逻辑去哪里？

利用这个类比，让我们从技术角度来看一下控制器与服务:

控制器:

*   管理传入的~~工作~~ HTTP 请求
*   决定~~哪个工人~~什么服务应该做这项工作
*   将工作分成相当大的单元
*   将来自 HTTP 请求的必要数据传递给服务
*   如果工作需要多名~~人~~服务处理多件事情，那么编排~~工作~~那些服务调用
*   但是**不是自己做**工作~~(这里再次使用一个基本的刻板印象！)~~(这里不是刻板印象，控制者不应该做这项工作)

综上所述，控制器[从 Express](https://www.coreycleary.me/why-you-should-isolate-express-from-the-rest-of-your-node-application/) (或者您正在使用的任何框架)中获取它需要的东西，进行一些检查/验证，以确定来自请求的数据应该发送到哪个(哪些)服务，并编排这些服务调用。

因此，控制器中有一些逻辑，但服务并不负责业务逻辑/算法/数据库调用等。同样，控制者是经理/主管。

还有一项服务:

*   从管理器接收所需的~~请求~~数据，以便执行其任务
*   计算出完成请求所涉及的~~个人细节~~算法/业务逻辑/数据库调用等
*   通常只关心他/她必须完成的任务
*   不负责~~就“更大的”画面做出决策~~协调不同的服务调用
*   完成任务/要求所需的实际工作
*   返回~~已完成的工作~~作为对管理器的响应

现在总结一下服务，服务负责完成工作并将其返回给控制器。它包含实际满足需求并返回 API 消费者所请求的内容所必需的业务逻辑。

### 关于什么是“业务逻辑”的说明

我喜欢将业务逻辑视为更“纯粹”的逻辑形式。是逻辑不(通常！)关心验证请求或处理任何特定于框架的事情。它只是处理用于处理数据、存储数据、获取数据、格式化数据等的算法/规则。这些规则通常由业务需求决定。

例如，如果您有一个 API，它返回过去 X 天内有多少用户在您的平台上注册，那么这里的业务逻辑将查询数据库，并在将数据返回给控制器之前对数据进行格式化。

### 控制器和服务分离的示例

让我们重构我们最初的纯控制器代码，看看控制器和服务之间的这种关注点分离可能看起来像什么:

首先，我们将拉出将用户添加到服务中的逻辑。

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

接下来，我们将推出向用户发送注册电子邮件的逻辑。

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

最后，我们将大大简化控制器，只需进行这两个服务调用:

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

### 概括起来

差不多结束了。希望现在您对控制器中的逻辑和服务中的逻辑有了更好的理解。记住它的简单方法是:

*   控制者:管理者/协调工作
*   服务:执行工作

像这样的分离成为代码重用和代码组织的有力工具。用您正在构建的下一个 REST API 来尝试一下，我想您会发现它很有帮助。

我正在编写许多新内容，以帮助 Node 和 JavaScript 更容易理解。更容易，因为我觉得不需要像有时候那么复杂。如果你喜欢这篇文章，并发现它很有帮助[这里是再次链接](https://www.coreycleary.me/about/)订阅我的时事通讯！