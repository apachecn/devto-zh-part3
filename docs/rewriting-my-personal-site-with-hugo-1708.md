# 和雨果一起重写我的个人网站

> 原文：<https://dev.to/restoreddev/rewriting-my-personal-site-with-hugo-1708>

几个月前，我创建了名为 [andrewdavis.me](https://andrewdavis.me) 的个人网站。我在 Go 中将它构建为一个自定义的服务器端应用程序，由 MySQL 保存内容。我写了在[用 Go 和 TailwindCSS](https://andrewdavis.me/post/building-my-personal-site-with-go-and-tailwindcss/) 建立个人网站的经历。我喜欢这个项目，但是，我最近想做一些 SEO 改进，并决定维护一个自定义应用程序比我想要的更多。我开始寻找不同的静态网站发电机，发现[雨果](https://gohugo.io/)。这是一个真正快速的网站生成器，使用 HTML 的 Go 模板和内容的 Markdown。Hugo 非常灵活，但也很容易使用，所以我能够在一天内将我的网站转移到它上面！事实上，我开源了代码，所以你可以在 GitHub 上看到所有的[。现在，我的站点由 GitHub 页面上的 HTML 文件提供服务，使得渲染速度更快。以下是我如此喜欢 Hugo 的几个原因，也是你可能喜欢在下一个项目中使用它的原因。](https://github.com/restoreddev/me)

## 易于安装和启动的项目

Hugo 是一个简单的二进制，[可以安装在任何 OS 上](https://gohugo.io/getting-started/installing/)(我在 MacOS 上用的是家酿`brew install hugo`)。要启动一个新项目，打开一个终端并运行`hugo new site blog`。Hugo 将创建一个名为`blog`的文件夹，并生成一个项目框架，这样你就可以开始添加内容了。要开始构建模板，在`layouts`目录中添加一个`_default`文件夹。在那里你可以创建一个名为`baseof.html`的新文件，它将成为你所有页面的基础模板。之后你可以创建一个`index.html`、`list.html`和`single.html`模板。索引模板将用于主页，列表模板用于任何列表页面，单一模板用于内容页面。

接下来，您可以开始在内容文件夹中添加 Markdown。您可以创建类似于`post.md`或`post/index.md`的降价文件，它们将分别创建不同的路径:`/post.html`和`/post/`。每个 markdown 文件必须有 frontmatter，但它可以用 YAML、TOML 或 JSON 编写。我用 TOML 写了我的，它让一个页面看起来像这样:

```
+++
title = "My Blog Post"
date = "2019-03-01"
+++
The content of my blog post goes here. 
```

为了构建页面，你可以在你的终端上运行`hugo server`，它会启动一个服务器让你在`localhost:1313`看到这个站点。服务器也会在你修改内容时自动重新加载。Hugo 获取 Markdown 页面的内容，并使用 go 模板将它们构建到单独的 HTML 页面中。在模板中，将使用预定义的变量注入降价文件，因此您可以使用`{{ .Title}}`和`{{ .Content }}`来指示降价数据应该出现在模板中的什么位置。

当然，还有更多模板和内容，您可以在[文档](https://gohugo.io/content-management/organization/)中找到。Hugo 是非常可配置的，可以根据您的需要做多做少。

## 自动代码高亮显示

在我网站的上一次迭代中，我使用了 [Prism.js](https://prismjs.com/) JavaScript 库来实现代码高亮显示。它工作得很好，但所有的风格都是客户端添加的，这对浏览器来说是不必要的工作。Hugo 使用了一个名为 [Chroma](https://github.com/alecthomas/chroma) 的 go 语法高亮器，它会在构建过程中将 CSS 添加到 HTML 中。要让它在 Markdown 代码块上运行，只需在项目根目录的`config.toml`中添加`pygmentsCodefences = true`。`config.toml`文件是 Hugo 在构建站点时使用的主要配置文件。

## 综合 CSS 处理

我喜欢在我的样式表中使用 PostCSS，Hugo 很容易使用它。我刚刚创建了一个安装了`postcss-cli`和我的 PostCSS 插件的`package.json`。然后，我创建了一个`postcss.config.js`文件(Hugo 不需要，只是 PostCSS 需要的)。之后，我在资产文件夹中创建了一个 CSS 文件，然后使用
将它导入到我的`baseof.html`模板中

```
{{ $css := resources.Get "site.v01.css" }}
{{ $style := $css | resources.PostCSS }}

<link rel="stylesheet" type="text/css" href="{{ $style.Permalink }}"> 
```

现在，当我用 Hugo 构建时，它会自动编译我的样式表并链接到我的基本模板中。

## 轻松部署

Hugo 有多种不同的方法来部署站点。当您在项目的根目录中运行`hugo`时，它将在`public`文件夹中构建文件。我只是按照他们的指南将[部署到 GitHub](https://gohugo.io/hosting-and-deployment/hosting-on-github/) 并创建了一个`publish.sh`脚本，该脚本获取公共文件夹的内容并将其提交到一个单独的 git 分支。然后 GitHub 页面使用部署分支来提供文件。

## 结论

我真的很喜欢 Hugo，它非常简单，不需要像其他生成器一样编写 JavaScript 来构建静态站点。另外，我现在可以使用 frontmatter 变量轻松地更新模板和添加更多功能，而无需编写太多代码。生成的 HTML 文件允许我不维护服务器设置，减少了维护时间来保持我的网站运行。总而言之，我算一个成功的举动，打算以后用[雨果](https://gohugo.io/)！