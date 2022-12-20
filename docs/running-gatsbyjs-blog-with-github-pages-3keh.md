# 使用 github 页面运行 Gatsby.js 博客

> 原文：<https://dev.to/konstantin/running-gatsbyjs-blog-with-github-pages-3keh>

有很多关于如何使用 github 页面运行 gatsby 网站的教程，但大多数(如果不是全部的话)都集中在如何为您的存储库生成网站上。

Github 有一个功能，可以让你托管你的静态网站，比如 username.github.io，并链接一个域名。

为此，您可以创建一个存储库`username.github.io`。你可以在这里找到更多关于那个[的信息](https://pages.github.com)

现在 gatsby 和 github 页面的问题是，生成的资源不在您的存储库的根文件夹中，如果您使用`gh-pages` package，它将在您发布时重写所有的源文件。

要解决这个问题，您需要创建一个包含所有源代码的`dev`分支，并将`deploy`脚本添加到包含以下内容的`package.json`中:

```
{  "scripts":  {  "deploy":  "gatsby build && gh-pages -d public -b master"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

因此，当我们在 dev 分支上运行`npm run deploy`时，gatsby 从`dev`分支构建源代码，gh-pages 将结果发布到`master`分支的根文件夹中。

## 自定义域名

如果您已经为 github 页面设置了一个自定义域，您会注意到在您的存储库的根文件夹中必须有一个包含自定义域名的`CNAME`文件。

如果我们运行`npm run deploy`，这个文件将被生成的网站删除。