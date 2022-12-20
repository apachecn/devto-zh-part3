# Heroku 上的 Ghost 2 博客

> 原文：<https://dev.to/fission/ghost-2-blog-on-heroku-189b>

[Ghost](https://ghost.org/) 是用 Node.js 编写的博客软件[克比·查普尔@cobyism](https://twitter.com/cobyism) 做了最初的艰苦工作，让 [Ghost 1.x 在 Heroku](https://github.com/cobyism/ghost-on-heroku) 上运行。

我派生了这个 repo 并更新了`package.json`以引用 Ghost 2.x 的最新版本、Casper 默认主题和亚马逊 S3 存储适配器。

它现在正在运行裂变博客，所以我们知道它是有效的！

现在，您可以使用“部署到 Heroku”按钮来创建 Ghost 2 博客:

[![Ghost 2 Blog on Heroku](img/c5f2ec62efbee76fd41eb9eff7b5c1c0.png)](https://heroku.com/deploy?template=https://github.com/bmann/fission-ghost)

您需要拥有或创建亚马逊 S3 凭证才能上传文件，您可能需要设置一个自定义域，但您的博客应该已经启动并运行，您可以创建您的第一个用户。

有了 Mailgun 插件，你就可以使用沙盒模式，这种模式只能向同意接收电子邮件的电子邮件地址发送邮件。这仅用于帐户邀请和密码重置，请记住在邀请新员工之前将电子邮件地址列入白名单。

## 自定义域和 SSL 已知问题

当切换到自定义域时，需要设置`PUBLIC_URL`。在 Heroku 重新部署发生之前，Ghost 不会选择这个新的 URL(即使没有发生代码更改)。这是一个已知的问题，您需要手动重新部署 Ghost 以获取更改。见问题[裂变幽灵/1](https://github.com/bmann/fission-ghost/issues/1)

如果您将 Cloudflare 用于 DNS，并使用 SSL 设置自定义域，您可能会遇到无限重定向的问题。这是 Cloudflare 上的“灵活”SSL 设置的一个已知问题，您需要为您指向的 Heroku 自定义域关闭 Cloudflare 代理。见问题[裂变-幽灵/2](https://github.com/bmann/fission-ghost/issues/2)

如果您发现任何其他问题，请[提出问题](https://github.com/bmann/fission-ghost/issues)，或来我们的[不和谐服务器](https://discord.gg/daDMAjE)与我们聊天。

## IPFS 支持&未来研究

既然我们是用 Ghost 来运行自己的(这个！)博客，我们将把为 Ghost 创建 IPFS 存储适配器放在我们的路线图上。

Ghost 支持 SQLite 作为数据库选项。由于这是一个基于文件的数据库，这在理论上可以在 IPFS 托管。

使用 Ghost 创作内容，然后以平面文件模式发布给 IPFS 出版社，或者为每篇博客文章创建一个 IPFS 版本，这些都是其他有趣的探索领域。

让我们知道，如果你有其他想法的幽灵+ IPFS！