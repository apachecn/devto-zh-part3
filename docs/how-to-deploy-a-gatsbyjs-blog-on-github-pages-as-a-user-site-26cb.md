# 如何在 GitHub 页面上将 GatsbyJS 博客部署为用户站点

> 原文：<https://dev.to/nielsenjared/how-to-deploy-a-gatsbyjs-blog-on-github-pages-as-a-user-site-26cb>

[![How to Deploy a GatsbyJS Blog on GitHub Pages as a User Site](img/f736c04a1a92b6fc13fc9939443da9fb.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DL7xYLdi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m3dvx2xcdpxcdwgvqp16.png)

*本文原载于[jarednielsen.com](http://jarednielsen.com/deploy-gatsbyjs-github-pages-user/)T3】*

对于静态站点生成器来说，GatsbyJS 是一个很好的选择，即使您不熟悉 React。它非常容易使用，超级快，并且有一个强大的生态系统。赢赢赢！要将 GatsbyJS 博客部署为 GitHub 页面上的用户站点，需要一些额外的小配置。根据文档，[您在 GitHub Pages repository 上的个人用户站点必须从`master`分支](https://help.github.com/articles/user-organization-and-project-pages/#user-and-organization-pages-sites)部署。这给我们制造了一个问题(没有双关语)。当我们运行`build`时，我们的`master`分支将堆满文件(更不用说完全转换)。解决方案是从一个`develop`分支在本地工作，并使用`gh-pages`在远程部署到`master`。

## 安装 GatsbyJS，创建新博客

如果您对此完全陌生，请从命令行运行以下命令:

```
npm install -g gatsby-cli
gatsby new <your-gh-pages-repo.github.io> https://github.com/gatsbyjs/gatsby-starter-blog
cd <your-gh-pages-repo.github.io>
gatsby develop 
```

您的终端输出应该是:

```
You can now view gatsby-starter-blog in the browser.

  http://localhost:8000/ 
```

## 初始化 Git

如果您还没有，请在 GitHub 上创建您的存储库，并在本地初始化它。添加您的远程原点；然后添加、提交并运行:

```
git branch -m develop 
```

这将*将*你的`master`分支移动到一个名为`develop`的新分支。我将我的命名为`develop`,以反映用于本地开发的 GatsbyJS 命令，但是您可以随意命名。

如果你运行`git branch`，你会发现你不再有一个`master`分支。这很好。如果你导航回 GitHub，你会在那里找到你的`master`分支。你不会推到`master`。相反，你会跑:

```
git push -u origin develop 
```

## 安装&配置 GH-页面

安装 [`gh-pages`](https://www.npmjs.com/package/gh-pages) :

```
npm install gh-pages --save-dev 
```

向您的`package.json`添加以下脚本:

```
{
    "scripts": {
        ...
        "deploy": "gatsby build && gh-pages -d public -b master",
    }
} 
```

注意`-b master`标志。当我们运行`gh-pages`时，我们将从我们的`develop`分支执行，但是我们希望它部署到我们的`master`。

要进行部署，请运行:

```
npm run deploy 
```

## 草稿分支

当我在写一篇博客文章的草稿时，我创建一个新的分支，当我准备发布时，我将它与`develop`合并，然后添加/提交/推送和`npm run deploy`。

## 使用自定义域名与 GitHub 页面

如果您拥有自己的定制域名，并希望在您的个人 GitHub 页面中使用它，那么设置是相当容易的，但这取决于您的 DNS 提供商。对于您的具体情况，我建议您参考 GitHub 帮助文章中的[使用带有 GitHub 页面的自定义域](https://help.github.com/articles/using-a-custom-domain-with-github-pages/)。

将您的`CNAME`文件添加到您的`develop`分支的`static`目录中。`CNAME`文件只需要包含你的域名，所以，在我的例子中有一行是:

```
jarednielsen.com 
```

博客快乐！

* * *

我每周写一份时事通讯，分享关于编程、解决问题和终身学习的文章。[立即加入](http://eepurl.com/cP8CMn)