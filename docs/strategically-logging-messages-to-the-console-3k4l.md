# 战略性地将消息记录到控制台

> 原文：<https://dev.to/jsmanifest/strategically-logging-messages-to-the-console-3k4l>

开发人员经常出于各种原因将消息记录到控制台。最常见的原因是，它提供了有用的输出，以帮助他们确保他们的应用程序按照他们的预期工作。这篇文章将展示几个在*控制台. log* 和*控制台. error* 中战略性使用的例子。

## console.log

将消息记录到控制台最常用的方法是 *console.log* 。它的简单性使得在您的开发流程中使用它们变得更加容易(在我看来)。Console.log 接受一个值(大多数情况下您将传递一个*字符串*，但是您也可以传递其他类型，比如对象和数字)作为参数，并使用它将它记录到控制台。

```
const logSomething = (str) => {
  console.log(str)
}

logSomething('this is going to the console') 
```

Enter fullscreen mode Exit fullscreen mode

结果:

[![console log logSomething](img/ae54c798f9dfec926529bb45f50eb465.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--86Zyyqtp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-us-west-1.amazonaws.com/jsmanifest/posts/strategically-logging-messages-to-console/1.jpg)

使这些控制台消息更有用的一个方便的小技巧是在控制台中给它们的输出着色。通过传入一个前缀为`%c`的字符串作为第一个参数，它将充当 CSS 值的占位符，并自动将您提供的样式应用于第二个参数。styles 参数是一个由分号“`;`”分隔的字符串，用于对将要显示的消息应用多种样式。

例如，如果每当函数从 http 请求超时 3 秒后，您就用橙色记录消息“三秒已过”，您可以这样做:

```
// timeout in milliseconds
const message =
  '%cThis message will turn orange when the timeout gets to zero after 3 seconds'
setTimeout(() => {
  const styles = 'color:orange;'
  console.log(message, styles)
}, 3000) 
```

Enter fullscreen mode Exit fullscreen mode

输出:

[![log output orange](img/caabb9b87e80d56a49ac0aa8bf0b6904.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lq0zzSWq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-us-west-1.amazonaws.com/jsmanifest/posts/strategically-logging-messages-to-console/2.jpg)

现在，如果你想知道除了使控制台消息看起来漂亮之外，这在什么时候会有用，你可以使用颜色来定制和区分你的应用程序的哪一部分正在做什么，哪一行代码可能很重要，哪一行代码给出了异常行为——这个列表是无穷无尽的。

假设您正在开发一个应用程序，并且需要为将来的请求设置标题。您创建了一个管理 headers 对象的实用函数，该对象可用于构造请求对象。

您决定将这个实用函数命名为 **makeHeaders** ，它将返回 3 个方法:

| 方法 | 描述 |
| --- | --- |
| 得到 | 从 headers 对象中检索键/值对 |
| 设置 | 为 headers 对象设置一个键/值 |
| 返回标题 | 返回整个头对象 |

代码中的构造函数:

```
const makeHeaders = function(options) {
  const headers = {}

  return {
    get: function(key) {
      return headers[key] || null
    },
    set: function(key, value) {
      if (key === undefined) return
      if (value === null) {
        console.warn(
          `WARNING! You tried to set "${key}" to headers but the value was null or undefined. The operation was skipped.`,
        )
        return
      }
      headers[key] = value
    },
    returnHeaders: function() {
      return headers
    },
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，在你的应用程序中，你可能会有这样的代码:

```
const headers = makeHeaders()
headers.set('Content-Type', 'application/json') 
```

Enter fullscreen mode Exit fullscreen mode

对于我们的示例用例，我们想要向`https://something.com/v1/api/user/`(这不是一个真正的端点)发出一个 *post* 请求来验证我们自己，这样我们就可以从将来的 API 调用中访问数据。一个基本的认证服务通常要求一个*用户名*和*密码*作为 post 请求主体的参数:

```
const url = 'https://someapi.com'
const method = 'post'
const data = {
  username: 'myUsername',
  password: 'myPassword123',
} 
```

Enter fullscreen mode Exit fullscreen mode

然后将它与 API 请求的其他选项一起应用于请求对象。对于这个例子，我们将使用[轴](https://github.com/axios/axios) :

```
const callApi = async (params) => {
  try {
    const response = await axios(params)
    if (response.data) {
      console.log(response.data) // result: { "token": "..." }
    }
    return response.data
  } catch (error) {
    // do something with error
  }
}

callApi({ url, method, data, headers })
  .then(({ token }) => console.log(token))
  .catch((error) => console.error(error)) 
```

Enter fullscreen mode Exit fullscreen mode

身份认证服务将验证凭据，并返回一个过期令牌，默认过期日期为令牌创建后 1 小时。一切都很好，但是假设它们支持可选的第三个参数，`expires_in`。通过传入一个以毫秒为单位的数字，可以覆盖默认的截止日期:

```
const headers = makeHeaders()
headers.set('Content-Type', 'application/json')

const url = 'https://someapi.com'
const method = 'post'

const expiringMinutesToMs = (mins) => {
  return mins * 60000
}

const data = {
  username: 'myUsername',
  password: 'myPassword123',
  expires_in: expiringMinutesToMs(75), // 1 hour and 15 minutes
}

const callApi = async (params) => {
  try {
    const response = await axios(params)
    if (response.data) {
      console.log(response.data) // result: { "token": "..." }
    }
    return response.data
  } catch (error) {
    // do something with error
  }
}

callApi({ url, method, data, headers })
  .then(({ token }) => console.log(token))
  .catch((error) => console.error(error)) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们试图向`minutesToMs`传递一个数字并接收到一个`undefined`值，我们可以用一个普通的 ole `console.log('some message here')`来记录它，但是如果有几个消息因为不同的原因被发送到控制台，如果有很多输出到控制台，它可能会看起来混乱和难以阅读。

通过加入颜色，您可以区分并更快地将注意力吸引到来自控制台的重要消息上。这通过使你的开发过程更快更有趣来提高你的开发流程的效率！

```
const headers = makeHeaders()
headers.set('Content-Type', 'application/json')

const url = 'https://someapi.com'
const method = 'post'

const expiringMinutesToMs = (mins) => {
  if (mins === undefined) {
    const msg =
      'WARNING! You tried to pass in "mins" to the "expiringMinutesToMs" call but the value was undefined. It was defaulted to 60 minutes (1 hour) instead'
    console.log(`%c${msg}`, 'color:#FF4500')
  }
  return mins * 60000
}

let expiresInMinutes

const data = {
  username: 'myUsername',
  password: 'myPassword123',
  expires_in: expiringMinutesToMs(expiresInMinutes),
}

const callApi = async (params) => {
  try {
    const response = await axios(params)
    if (response.data) {
      console.log(response.data) // result: { "token": "..." }
    }
    return response.data
  } catch (error) {
    // do something with error
  }
}

callApi({ url, method, data, headers })
  .then(({ token }) => console.log(token))
  .catch((error) => console.error(error)) 
```

Enter fullscreen mode Exit fullscreen mode

[![colored console output](img/2fed5bbe406b6f0e926c9ea0b7c0908a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--QgKAiSkV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-us-west-1.amazonaws.com/jsmanifest/posts/strategically-logging-messages-to-console/3.jpg)

## 控制台.错误

使用`console.error`将错误打印到控制台。这将尝试将您传递给它的任何内容格式化为错误。

[![strategilly logging console messages](img/75d3f93126b25f19dc9fd5f334b37b97.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8O8_iBP---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s3-us-west-1.amazonaws.com/jsmanifest/posts/strategically-logging-messages-to-console/4.jpg)

我喜欢的是`console.error`包含了一个堆栈跟踪，它允许你导航到导致错误的先前调用。除了将错误记录到控制台，我不会使用它，否则它可能会误导编辑您代码的其他开发人员。

这篇文章到此结束。剩下的就交给你去玩吧！