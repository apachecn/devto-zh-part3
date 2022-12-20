# 如何立即将 Next.js 无服务器部署列入白名单

> 原文：<https://dev.to/janhoogeveen/how-to-whitelist-your-nextjs-serverless-deployments-on-now-5d3f>

最初发布于 [janhoogeveen.eu](https://janhoogeveen.eu/blog/whitelisting-access-to-now-serverless-deployments)

我喜欢 Next.js，它允许我在 React 中构建丰富的数据驱动的 web 应用程序，这些应用程序是在服务器端生成的。我不喜欢的是为我的开发和登台服务器设置服务器和安装流程管理器来运行节点应用程序。为了解决这个问题，Zeit 的聪明人开发了 Now，一个无需服务器的平台，在这里你可以轻松地部署你的网络应用。我很想更多地使用它，但目前我只认为它是生产环境的有效选择。

我的一个简单要求是，当我还在工作的时候，我想把我的 web 应用程序从公共世界屏蔽掉。不幸的是，这是现在的事情。上海没有提供便利。

现在处理安全性的方法是在你的项目名前面加上一个随机的唯一标识符。例如，如果您的项目被命名为 *my-hidden-project* ，您可以通过类似[http://my-hidden-project-* * kdoe 2 jfh 2 * * now . sh](http://my-hidden-project-**kdoe2jfh2**.now.sh)的 url 访问您的部署

> " UID 是从一个编码为 9 个字母数字字符的大的加密随机数中得到的。但是，如果您想要额外的隐私，您也可以将. now.sh 后缀替换为您选择的任何自定义域，使 URL 100%不可访问，这样第三方甚至无法验证部署的存在。”

对我来说，这听起来像是[通过默默无闻获得安全](https://en.wikipedia.org/wiki/Security_through_obscurity)。这根本不够好。我想 100%确定我可以部署一个 web 应用程序，并且它只对我或我的团队成员可见，即使它有一个可预测的 URL。

我已经在谷歌上搜索过了，但是我找不到这个问题的解决方案。所以，这是我对这个严重问题的简单解决方案，我希望它能尽快以一种本土的方式得到解决。

# 在您的 Now Lambdas 中检查访问者 IP 地址

当在 Next.js 构建器(@now/next)中运行带有无服务器目标的 Next.js 项目时，它会将请求和响应对象，或者是文档调用它们时的 [IncomingMessage 和 ServerResponse](https://nextjs.org/docs/#serverless-deployment) 传递到您的 lambdas。它还将这些参数传递给 getInitialProps 方法，该方法总是在服务器返回任何 DOM 表示之前在服务器上执行。

这里有一个不允许任何人访问你的页面的简单例子。

示例:

```
const Page = props => {
  return <div>Welcome to next.js!</div>
}
Page.getInitialProps = async ({ req, res }) => {
  res.statusCode = 403
  res.end(`Not allowed`)
}

export default Page 
```

去试试吧。这还不是很有用，但它证明了你可以提前退出渲染，并向浏览器返回 403 响应。从这里开始，您可以根据自己的喜好扩展示例。

这是一个增强版本，默认情况下不允许任何人进入，并将一些 IP 地址列入白名单。

```
const Page = props => {
  return <div>Welcome to next.js! Your IP address is: {props.ip}</div>
}
Page.getInitialProps = async ({ req, res }) => {
  // Disallow access by default
  let shouldDisallowAccess = true

  // Specify IP's that you want to whitelist. This could be an office/VPN IP address.
  const allowedIps = ['192.168.100.100', '192.168.100.101']

  // Read the request objects headers to find our who's trying to access this page
  var ip = req.headers['x-forwarded-for'] || req.connection.remoteAddress

  // This if statement is only valid if the app is deployed through Now. It gets ignored on other environments.
  if (process.env.NOW_REGION) {
    // Check if the visitors IP address is allowed
    if (!allowedIps.includes(ip)) {
      shouldDisallowAccess = false
    }
  }

  // Return the 403 status code
  if (shouldDisallowAccess) {
    res.statusCode = 403
    res.end(`Not allowed for ${ip}`)
    return
  }
  // This object gets injected into the Pages props, if you want to use them
  return { shouldDisallowAccess, ip }
}

export default Page 
```

## 备选方案

1.  如果不想用 lambdas，应该可以用 [Next.js 中间件](https://nextjs.org/docs/#custom-server-and-routing)包装在自己的服务器里。我还没有尝试过，但我想用这个解决方案为不允许的 IP 地址添加基本身份验证会很容易。

2.  如果你对自己的自定义域有控制权，应该可以将它指向 Cloudflares DNS 服务器，并使用它们的防火墙只允许来自特定 IP 地址甚至 IP 范围的访问。那样的话，应该有可能为域下的所有项目提供一个保护真理的单一来源，而不必在每一页上调用 getInitialProps 方法。当然，您会失去让 Now 管理您的域的好处，但是您的开发服务器真的需要它吗？