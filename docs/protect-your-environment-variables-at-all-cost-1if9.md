# 不惜一切代价保护您的环境变量

> 原文：<https://dev.to/ryanaz/protect-your-environment-variables-at-all-cost-1if9>

[![image](img/7e21ad92217662047aad90d0b0376490.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--liTSnzdh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9s005jlm2yjaesznhz1a.png)

正在解决的问题是什么？
正在解决的问题是在 git 回购中意外暴露 API 密钥、密码或其他敏感数据

这是给谁的？
初学使用 Git/Node。

最近与一位开发伙伴交谈时，提到了他是如何更新一个旧的 github 项目，并在将 credentials.yml 文件添加到项目级别后意外地将他的 AWS 密钥推送到 github 的。gitignore **(提示，如果您向。gitignore 已经跟踪的回购文件)**。

不到 4 小时，亚马逊就发来了一封通知邮件，称他的账户被入侵了，并附有适当的缓解措施(更改密码、2FA、检查未授权的实例并手动关闭它们)。

第二天早上，他查看了自己的 AWS 账户，发现在美国、欧盟、韩国等每个地区都启动了 20 个 EC2 实例。不到 24 小时的数据传输费用约为 10，000 美元，预计每月的费用要高得多。

在谷歌上快速搜索会得到几个 Quora 答案，类似的结果是被包含，亚马逊正在为未经授权的使用退款，一些数据传输费用高达 25 万美元。

这就是这篇关于保护项目中最重要的方面之一，即环境变量的最佳实践的文章的灵感来源。我希望你能实施这些策略，保护你自己或你的雇主免受安全漏洞的侵害。

## 环境变量

API 键应该尽一切可能避免硬编码，因为这些值很容易被意外上传到一个共享的 git 存储库中，从而允许对 API 提供者的帐户进行未授权的访问。

环境变量可以在单独的。env/credentials 文件本地存储在项目中，存储在计算机上的系统变量中，并在生产环境中作为环境变量在服务器上受到保护。

这允许将凭据文件添加到本地。gitignore 或全局。gitignore 防止意外上传。

我建议采取以下步骤来帮助保护您和您团队的凭证。

## 全球。吉蒂尔

项目。gitignore 文件是众所周知的，但鲜为人知的更重要。gitignore 文件可以在您的机器上设置，并应用于您跟踪的每个 git 存储库。

要进行设置，请打开终端并键入:

**T2`$ git config --global core.excludesfile ~/.gitignore_global`**

现在，您可以添加任何文件或扩展名，以确保不会被添加到 git 存储库中。

您可以使用以下命令从命令行打开该文件:

**T2`$ vim ~/.gitignore_global`**

一旦进入 vim 编辑器，点击`i`插入文本，完成后点击`:q`保存/退出

根据您的项目/编码语言，一些用于凭证的常见条目包括`.env variables.env credentials.yml /node_modules`。

推荐文件扩展名的详细列表可以在这里找到:
[https://gist.github.com/octocat/9257657](https://gist.github.com/octocat/9257657)

## 本地项目。gitignore

这是最常见的。gitignore 和可以在 git 项目根目录下找到。

该文件是在运行`git init`后简单创建的

git 跟踪将忽略此文件中列出的任何文件扩展名、文件或文件夹。

[https://git-SCM . com/docs/gitignore](https://git-scm.com/docs/gitignore)

## 清除 git 缓存

如果您已经初始化了 git repo，并且想要将文件添加到您的。gitignore 在已经跟踪了文件之后，您需要使用:
清除 git 缓存

```
$ git rm -r --cached .
$ git add .`
$ git commit -m "new files added to .gitignore not being tracked" 
```

如果你忘记清除缓存并添加一个你不想被跟踪的文件到 gitignore 并做一个普通的`git add .` `git commit -m "file will be added to git"`你的文件将被推送到 git，除非你已经设置了全局的。使用正确的扩展名 gitignore。

## 与 BFG 清算你的错误

如果您不小心暴露了一个 API 密钥，那么一切希望都不会落空。确保取消并旋转密钥，这样就不会造成任何损坏。你也可以使用 BFG 轻松地从 git repo 中删除文本/文件夹

[https://rty . github . io/bfg-repo 清洗器/](https://rtyley.github.io/bfg-repo-cleaner/)

*在 Mac 上安装:*

```
$ brew install bfg 
```

*现在你可以使用*
从你的 git 提交历史中删除文本

```
$ bfg --replace-text variables_file.env
$ git add .
$ git commit -m 'text removed'
$ git push --force 
```

这将插入*删除*为文本的这个文件就像这样:
[![image](img/7e21ad92217662047aad90d0b0376490.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--liTSnzdh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/9s005jlm2yjaesznhz1a.png)

请阅读为 BFG 打印的说明，因为这不会删除文件，您必须手动删除，这只会从您的提交历史记录中删除文本。

您也可以删除整个文件/文件夹
**`$ bfg --delete-folders .git --delete-files .git --no-blob-protection my-repo.git`**

## Dotenv(.env)

所有这些都是关于如何保护你的 API 键，但是实际上如何防止硬编码你的键呢？

很简单，使用名为 **dotenv** 的 NPM 包

安装方式:

```
# with npm 
$ npm install dotenv

# or with Yarn 
$ yarn add dotenv 
```

*现在在你的应用程序中，通常在 server.js 文件的顶部使用:*

**T2`$ require('dotenv').config()`**

*在你的项目根目录下创建一个`.env`文件，结构如下:*

```
API_KEY=3nds234nds2 
```

现在您可以使用 **`process.env.API_KEY`** 来访问密钥，当您的代码被构建时，该密钥将被转换为您的密钥值。任何对 git 的提交都会看到 **`proccess.env.API_KEY`** 而不是你实际的键值。

现在您可以放心地睡觉了，因为您的钥匙受到了保护！