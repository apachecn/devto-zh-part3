# 使用 Now.sh 部署静态 Gatsby 应用程序

> 原文：<https://dev.to/p0oker/deploying-a-static-gatsby-app-using-now-sh-32ob>

如果您正在用 Gatsby 创建新的静态网站，您可能正在寻找一种简单的方法来构建和部署它。有两种通用的方法可以做到这一点，使用 Github 与 Now.sh 平台集成，或者手动使用 CLI。在本文中，我将解释这两种方法，并反映我使用 Now.sh 部署您正在阅读的同一个博客的经验。

我对本文的假设是，您熟悉 Gatsby，并且最近开始使用 Now.sh。希望您也向该服务注册了一个域，以便为您的部署创建一个别名，但这不是必需的。

## 使用 CLI

Now.sh 有一个非常方便和强大的命令行工具。无论你多么喜欢与 Github 集成或使用桌面应用程序，你总是需要回到 cli 来获得高级功能。我也不想这么说，但它比 Github 集成可靠得多，因为有时这些服务会因为技术原因而停止工作。

为了进行部署，您需要首先从 npm:
下载命令行工具

```
npm install -g now 
```

Enter fullscreen mode Exit fullscreen mode

现在您需要在终端中使用这个命令登录到您的 now.sh 帐户:

```
now login youremail@gmail.com 
```

Enter fullscreen mode Exit fullscreen mode

输入密码，一切就绪。如果您在每个文件夹中输入`now`,它会将该文件夹中的所有文件上传到一个可分配给您的域的新地址。然而，如果你在 Gatsby 博客上这么做，你最终会上传你的源目录中的所有文件，包括`package.json`，但不上传静态文件。如果您想要在云中运行构建，您需要为 now.sh 指定一个配置文件，以了解如何构建您的文件。

在 Gatsby 目录的根目录下添加一个名为`now.json`的文件(在`package.json`旁边)。这是一个样本，这个文件应该是:

```
// now.json
{
  "version": 2, // using version 2.0 of now.sh
  "name": "Your Gatsby App Name",
  "alias": "yourdomain.com", // remove it if you don't own a domain with now.sh
  "builds": [
    {
      "src": "package.json",
      "use": "@now/static-build",
      "config": {
        "distDir": "public" // default folder for Gatsby builds
      }
    }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

再走一步，我们就没事了。转到你的`package.json`，添加一个名为`now-build`的新脚本，它应该指向`gatsby build`，就像这样:

```
// package.json
"scripts": {
    "dev": "gatsby develop",
    "now-build": "gatsby build",
    // other scripts ....
} 
```

Enter fullscreen mode Exit fullscreen mode

我们都准备好了。现在，您可以简单地在 Gatsby 根文件夹中运行下面的命令:

```
now --target production 
```

Enter fullscreen mode Exit fullscreen mode

此命令将准备一个新的构建，并使用 Now.sh 将您的 Gatsby 应用程序部署到您的域中。生活是美好的！

## 使用 Github

是的，使用 Github 编辑器来写一篇新的博客文章或者只是编辑以前的文章是一个很好的主意，Now.sh 可以在每次新提交后立即触发新的部署。这里的经验法则是，如果你已经为你的域设置了一个别名，那么 Github 主分支的每一次提交都会触发一次对你的网站 URL 的部署，而其他分支只会将一个版本部署到一个新的地址。这个地址可以在你的 Github 环境标签或者 Now.sh 面板中找到。

为了使用 Github 做到这一点，你需要首先[将你的 Github 账户](https://zeit.co/docs/v2/integrations/now-for-github/)连接到 Now.sh。之后，我们需要做的就是添加一个类似于上一步的`now.json`文件:

```
// now.json
{
  "version": 2, // using version 2.0 of now.sh
  "name": "Your Gatsby App Name",
  "alias": "yourdomain.com", // remove it if you don't own a domain with now.sh
  "builds": [
    {
      "src": "package.json",
      "use": "@now/static-build",
      "config": {
        "distDir": "public" // default folder for Gatsby builds
      }
    }
  ]
} 
```

Enter fullscreen mode Exit fullscreen mode

还要确保将`now-build`脚本添加到你的`package.json`文件:

```
// package.json
"scripts": {
    "dev": "gatsby develop",
    "now-build": "gatsby build",
    // other scripts ....
} 
```

Enter fullscreen mode Exit fullscreen mode

从现在开始，每次提交到 Github 的主分支都会触发一个到你的网站 URL 的部署。干得好，你不是一个懒惰的开发人员！

这篇文章最初是为我的个人博客 [fsociety](https://fsociety.at) 写的🙃