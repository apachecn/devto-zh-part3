# 步步 React，NodejS 和 MySQL 简单全栈应用 2018(第六部分)

> 原文：<https://dev.to/kmaryam27/step-by-step-react-nodejs-and-mysql-simple-full-stack-application-2018-part-6-1ak2>

#### 在这篇文章中，我试图解决第 5 部分的错误，上传到 Github，并在 heroku.com 展示这个项目。

现在，在我们尝试将项目的第一个版本部署到 Github 和 Heroku 之前，我们可以一步一步地设计和改进项目。首先:去[Github.com](https://github.com/)为你的项目建一个账户 **Github** 是一个基于网络的托管服务，使用 Git 进行版本控制。它主要用于计算机代码。它提供了 Git 的所有分布式版本控制和源代码管理(SCM)功能，并添加了自己的特性。它为每个项目提供了访问控制和几个协作特性，比如 bug 跟踪、特性请求、任务管理和 wikis。在终端中打开客户端文件夹，使用 npm 运行构建命令，使其准备好部署: [![](img/809eed4afcb6898150a2bca78d45eedf.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hJh5mwtw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/gs9nymedit5va785d1kc.png) 打开您的 GitHub 帐户，转到“资源库”选项卡，然后按“新建”按钮，为您的项目创建一个新的资源库:

[![](img/6ae8256d326895cbd247c92803a2aa0c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8Fkr2yui--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/xh2mokk6hbikspgz52ef.png)

键入您的存储库名称，并添加使用带有清单的自述文件初始化此存储库，然后单击 pes。gitignore 按钮忽略要部署的节点模块文件夹，这样下载和上传项目会更快，但请记住，在 VSCode 终端中使用(npm install)命令下载项目后，应该添加包含项目所有要求的节点模块文件夹。在文本框上键入 Node，然后按“创建存储库”按钮:

[![](img/a7bb3d313dd7d61d8e778e0d24b369fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--aE5Bh-Pt--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/0zuwlpqz1phnjmuslmxu.png)

现在在 heroku 网站上开一个账户(Heroku 是一个平台即服务(PaaS)，使开发者能够完全在云端构建、运行和操作应用:www.heroku.com)

然后在你的仪表板上按下新建按钮，然后创建新的应用程序按钮来设置 heroku 新的应用程序

[![](img/229208c469c734ddb6547d10df42e014.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3hoG5-Z4--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rqjjgc85h6wcf5jck3gr.png) 
下一步:
[![](img/e6efb19c8f9ae9f200e5285dcbe67438.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6vISud_q--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/lu920q893t3lfuahdje3.png)

现在在部署选项卡中点击 github 按钮，并在搜索位置尝试找到您的 github 资源库

[![](img/803de3c13c7b8dfc3c9f76749a2e00ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Hv4qwEXH--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/2ryhttepvfwg1fzmoq82.png) 
[![](img/b7a941ce5b8ae8495da5dfed3beb7659.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9B-2zILg--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nz43jecgbfwhclfp8oaa.png) 
然后按连接键。
现在，在页面的底部点击(启用自动部署)以在 github 上自动部署更改后的应用程序。
下一步是在 heroku app 上点击资源并添加 mysql 数据库。
记得添加 jawsDB mysql 免费版:
[![](img/1a6ef1576cc9d5803db49799079058e5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IH-tpBag--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/wgesfvnpygo91xwymp66.png) 
然后点击 mysql 转到另一个页面访问 heroku mysql 用户名和密码:
[![](img/6da487b546da72c30462ba528062518d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---geqQAyZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l62qsq8jr4h4m7pzol69.png) 
现在我们必须复制连接字符串并将其粘贴到我们的项目中作为 mysql 数据库地址，所以下面的代码在 app.js 的第 33-38 行:

```
const connection = mysql.createConnection({
  host: 'localhost',
  user:'root',
  password:'myjs123',
  database:'simple-react-sql-db'
}); 
```

应该更改连接字符串的主机用户密码和数据库地址。现在测试你的应用程序，确保它能正常工作。
我们必须看到这个错误，因为我们现在没有新数据库上的用户表来映射它。因此，在 MySQL 工作台上，转到主页选项卡，在上面添加 heroku 数据库，并在新数据库上添加您的表:
[![](img/123b10872cf03e3a41c8291c9a04b6c3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bZqJc9u7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ru1b3c299l91dw58u3g3.png) 
现在，在本地主机数据库中，使用 heroku 应用程序数据库上的复制表和粘贴表语句，我们可以在 heroku 上拥有该表:
[![](img/aa02fb7fb7e8eb9644c81e3da6ff3019.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--8Pq_gsFz--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/94btpr37ebwzztv2onib.png)
[![](img/f17c2200142f4524a91a8f32695c1cc4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--D3i2Lamf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/x3w7i1e2o7zef8yngx28.png)
现在，将您的数据插入数据库:
[![](img/11bcc6958ea1364af8060a40b034176f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hgp_76iM--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l69ofu0f5f3yyrgg19ki.png) 
最后的更改是在 html 中的后端 routes 文件夹上我们必须将数据库名称更改为 heroku app 数据库:
[![](img/a18761c7d640147a15a3a59fc9d07dfb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Nn-8nuFK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3vyzr73tufpt8dmc0wic.png) 
再次测试您的应用程序，它运行正常，没有任何错误。现在我们必须建立这个项目...