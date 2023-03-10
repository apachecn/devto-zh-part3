# 将应用部署到 Heroku 的指南

> 原文：<https://dev.to/sofiajonsson/deploying-your-app-to-heroku-ob6>

## 什么是 Heroku？

Heroku 是一个免费的云平台，可以让您构建、交付、监控和扩展应用程序。它支持几种不同的编程语言，但是对于这篇博文，我将分解如何将 Ruby on Rails 应用程序部署到 Heroku。

将你的应用程序部署到 Heroku 有点棘手，有这么多分散的资源可用，所以我开始创建一个博客帖子，其中包含了我迄今为止学到的所有技巧和诀窍。这篇特定的文章主要围绕部署 Rails 应用程序，因此您的终端中的命令可能会有所不同，但要点应该是相同的(我在底部链接了一篇关于部署 React 应用程序的文章)

## 部署您的应用

### 选项 1:使用 PostgreSQL 创建您的应用程序

#### 第一步:创建你的应用

`Rails new blog_app -- database=postgresql`

指定`-- database=postgresql`会更改应用程序中的默认数据库 sqlite3，使其与 Heroku 兼容，当您最终决定将它部署为与朋友/家人共享/用于您的作品集时，这将使您的生活变得更加轻松。

#### 第二步:下载安装 Heroku

[![heroku download](img/d0e82de8704f3d648914dd48d6d03c0d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6Z2c91pF--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lf2pq9msdk9265ymf3fa.png)
[【https://devcenter . heroku . com/articles/heroku-CLI】](https://devcenter.heroku.com/articles/heroku-cli)

下载后，运行`Heroku login`并按回车键完成登录。

如果您还没有帐户，请创建您的免费帐户。(你可以部署的免费应用数量是有限的，因此强调免费)

#### 第三步:在 GitHub 中创建新的资源库

继续在您的个人 GitHub 中创建一个新的资源库。要进一步分解它:

*   点击右上角的图标
*   选择您的存储库，然后按绿色的“新建”按钮。在这里，为您的存储库创建一个名称，对于这个实例，我们将使用“blog_app ”,添加一个简短的描述，并确保它在我们创建存储库之前是公开的。

从这里，我们可以通过命令行推送我们已经存在的代码，或者上传现有的代码。
[![github](img/73e1d6a86be167a69f3988946ac68143.png)T3】](https://res.cloudinary.com/practicaldev/image/fetch/s--pycK7yWO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/qwtde1eqxv7ytlu6ep4o.png)

#### 第四步:通过 CLI 创建你的 Heroku App

从这里我们`heroku create`。这将为您的网站创建一些模糊的内容，您可以在以后更改。

#### 第五步:Heroku 远程

`git remote –v`将让您看到哪些遥控器连接到您的项目，如果 Heroku 已列出，您可以继续操作，`git push heroku master`这类似于`git push origin master`,其中原点通常指您的 GitHub。

#### 第六步:最终步骤

`heroku run rake:db migrate`

如果您收到与 rake 相关的错误消息，请尝试:

*   `heroku pg:reset DATABASE`
*   `heroku run rake db:migrate`
*   `heroku run rake db:seed`

最后:`heroku open`

确保你的项目有一个默认的主页，否则你需要记得添加`/login`路径或任何你的登陆页面，以查看应用程序是否正确部署。

## 选项 2:将数据库从 Sqlite3 转换为 PostgreSQL

如果你没有指定 PostgreSQL 就创建了你的应用程序，它很可能会默认为 sqlite3，这与 Heroku 不兼容。

#### **行动方针**:

1.  查找 sqlite3 的所有实例。你可以`command + T` *搜索你的项目或者在通常的地方搜索它:gemfile 和 database.yml(在 config 文件夹中)
    (*我使用 Mac，并把 Atom 设置为我的文本编辑器)

2.  用 PostgreSQL 替换 sqlite3 的实例

3.  `Rake db:reset`是不是**没有**被 Heroku 支持所以要绕过这个:
    运行`heroku pg:reset DATABASE`
    `heroku run rake db:migrate`
    最后`heroku run rake db:seed`。
    这将使用新的 PostgreSQL 数据库重新设定种子并迁移您的数据库。这类似于运行`git status`并检查您最近的迁移是否成功。对于这个特定的 Ruby on Rails 项目，使用`rails s`命令启动您的本地服务器。如果此数据库更改有效，您可以继续。

4.  安装 Heroku CLI 并查看上面列出的登录、创建远程、推送、迁移和打开的步骤。

5.  如果你从未使用过 PostgreSQL 数据库，你*可能会遇到的最后一个错误很简单。打开您的 Postico 数据库客户端(大象图标),并确保将其打开！一旦你打开了它，它就会一直运行，你再也不用去想它了。这很容易被忽视，因为这是一次性的事情。【https://eggerapps.at/postico/】下载:[T4](https://eggerapps.at/postico/)*

### 附加帮助:

一件毫无价值的事:

作为附加资源，我建议查看这篇关于部署 React 应用程序的文章。作者克里斯(Chris)降低了他的 Ruby 版本，以使他的应用程序兼容，但这只能作为最后的选择。他还为他的前端和后端部署指定了不同的行动方案，这些方案对我的应用程序的部署很有帮助。

[https://medium . com/coding-tidbits/react-app-deployment-heroku-4a 91 f 8903 c 6](https://medium.com/coding-tidbits/react-app-deployment-heroku-44a91f8903c6)

我知道 Heroku 可能会令人沮丧，所以我希望这篇文章能解决你的一些问题！