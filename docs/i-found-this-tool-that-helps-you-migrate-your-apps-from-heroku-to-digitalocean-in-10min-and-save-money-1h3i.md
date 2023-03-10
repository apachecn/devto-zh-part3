# 我发现这个工具可以帮助你在 10 分钟内将你的应用从 Heroku 迁移到 DigitalOcean，并且省钱！

> 原文：<https://dev.to/vanishingstar/i-found-this-tool-that-helps-you-migrate-your-apps-from-heroku-to-digitalocean-in-10min-and-save-money-1h3i>

任何后端开发人员都知道 Heroku 为部署 app 提供了一个令人敬畏且简单的平台。您只需创建一个帐户，创建一个应用程序，git push 会处理剩下的事情！您的应用程序会自动在网络上可用。

Heroku 的主要问题是他们的定价。一旦你过了免费层，价格飞涨，变得非常非常昂贵。另一个问题是平台的刚性。它非常固执己见，不允许你做自定义的事情，比如添加 nginx 缓存等等。

出于这些原因，我开始考虑将我的应用程序转移到一个简单的 VPS(虚拟专用服务器)上。DigitalOcean 是最人性化的 VPS 提供商。市场上的其他玩家，如 AWS，没有为初学者提供易于使用的界面。相比 Heroku，DigitalOcean 超级便宜。在 Heroku 上，你为 1GB 内存支付 25 美元，在 DigitalOcean 上，你支付 5 美元。就是这样！长话短说，虽然 DigitalOcean 更便宜，但你必须手动设置 nginx，设置路由等。这是一个相对简单的过程，但是很繁琐。

情况就是这样，直到我发现了卡普洛夫。这个工具自动地将一个数字海洋实例变成一个 Heroku 克隆实例！我目前为 1GB 的内存支付了 5 美元，并在我的 VPS 上托管了多个应用程序和数据库。整个设置对我来说花了大约 10 分钟！

## 步骤 1)设置您的服务器

CapRover 安装在 DigitalOcean 上非常容易。您可以点击这个链接(从 CapRover docs)来创建一个 ca cover 实例。如果您没有 DigitalOcean 帐户，还可以获得 100 美元的免费信用。

[https://marketplace.digitalocean.com/apps/caprover?action = deploy&ref code = 6410 aa 23d 3 f 3](https://marketplace.digitalocean.com/apps/caprover?action=deploy&refcode=6410aa23d3f3)

创建 droplet 后，转到 DigitalOcean dashboard，您会看到:

[![do1](img/9107aa3afa21914fc614ee407c12e909.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--xz1ydGuz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2fsyxjjlkjjqt23t1ti1.png)

现在，打开浏览器，输入`http://IP-ADDRESS:3000`，使用默认密码`captain42`登录

确保将 IP-ADDRESS 替换为您的 VPS 的 IP 地址，即上面截图中的黄色框。登录后，您会看到一个如下所示的仪表板:

[![sc1](img/20d5a6e5442d8441896c9c4e7eb48b24.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Jaw9T3qG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/q30j65elxbkbdsdui72c.png)

如果你能看到这个仪表板，安装是好的。现在，你需要更新我们的域名。

## 步骤 2)设置您的域名

您需要在 DNS 中设置一个通配符 A 记录条目。如果这对你来说听起来很复杂，不要担心，步骤很简单。假设您已经注册了一个域名，您可以在 DNS 设置中设置一个通配符条目。例如，在 GoDaddy 上，您只需从“管理域”页面中点击管理 DNS。

[![gd](img/878c0fb8daad60dc5bc3037392918026.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--CbxYksRx--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/arypockaxhjdr8cka1j8.png)

然后，您只需添加一个指向您的服务器的 A 记录。主机或名称值应该是`*.vps`，IP 或值应该是您的 VPS 的 IP 地址。类型应为“A”。TTL 可以是任何值，通常是 600 秒。假设您的 VPS 的 IP 地址是`123.123.123.123`，这是您将在 GoDaddy 上看到的结果:

[![records](img/833a9b513e864d4bae19fa6aaa812526.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SGTSRvPd--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/coux8xoxrynqziymowsr.png)

现在您已经设置了通配符域，您可以使用 CLI 完成设置。

## 步骤 3)最终设置

使用 npm i -g caprover 安装 CapRover 命令行工具，并运行 caprover serversetup。只需回答问题，您的服务器将为您设置好！

PROTIP:如果您不熟悉 npm，也可以使用仪表板来设置您的服务器(设置根域、启用 https、强制 https 和更改密码)。

## 恭喜恭喜！你现在有了一个功能齐全的赫罗库克隆体！

让我们从仪表板创建一个测试应用程序:

[![app](img/50642e7f89eee15672f3d0b8b9cc813d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--h3QfyTGp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lo6yd5y9gn0gck73st5l.png)

接下来，下载一个 CapRover 示例应用程序。NodeJS、Python 等可以从这里下载:

[https://github . com/ca prover/ca prover/tree/master/captain-sample-apps](https://github.com/caprover/caprover/tree/master/captain-sample-apps)

单击示例应用程序，然后单击下载按钮。下载完 tar 文件后，转到 deployment 选项卡，将其中一个 CapRover 测试应用程序拖放到 upload 框中。

[![deploy](img/e6e2d5a4923d31e559cf6af112f6a80a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--gAaucAPa--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/txius2s9lgc1o4k3bhay.png)

等待几秒钟，直到应用程序构建完成。然后，您可以使用`http://appname.server.domain.com`访问您的应用程序

这只是冰山一角。CapRover 有许多其他功能，一键启用你的应用程序的 https，支持你的应用程序的许多自定义域，一键数据库和应用程序部署(如 wordpress)等等。

查看更多关于[https://caprover.com/](https://caprover.com/)的细节

*本文最初发表于媒体*