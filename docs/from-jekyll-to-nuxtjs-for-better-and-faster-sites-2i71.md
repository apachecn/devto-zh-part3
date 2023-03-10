# 从 Jekyll 到 NuxtJS，更好更快的网站

> 原文：<https://dev.to/teleyinex/from-jekyll-to-nuxtjs-for-better-and-faster-sites-2i71>

静态网站现在变得非常流行。基本上，因为你不需要有服务器，而且网上有很多免费的托管服务:Netlify、Github Pages、Zeit now 等等。

你实际上可以从超过 200 个框架中选择。实际上，您应该能够找到一种符合您偏好的语言，因为几乎每种编程语言都有一个框架。

最受欢迎的是[哲基尔](https://www.staticgen.com/jekyll)。它在 Github 中拥有超过 36000 颗星星，紧随其后的是 [Next](https://www.staticgen.com/next) 和 [Hugo](https://www.staticgen.com/hugo) 。

正如你所猜测的，我已经使用 Jekyll 很长时间了。事实上，我所有的网站都是用它建立的:[Scifabric.com](https://scifabric.com)， [PYBOSSA](https://pybossa.com) 显然还有我的主页(你现在所在的位置)。

《哲基尔》的简单令人惊叹。任何人都可以自动跳转，并在几分钟内开始建立一个网站。但是，如果你是一个有经验的 JS 开发者，或者你想创建一个渐进式的 Web 应用，Jekyll 并不是最好的选择。这是我个人的情况。

因此，我能做什么？嗯，我从 VueJS 的 1.0 版本开始用它编码，然后是 2.0，然后我爱上了 NuxtJS。

NuxtJS 非常棒，因为它可以为您处理大量您不想处理的东西，并且有一个不错的模板作为起点:

*   性能:它使用 VueJS 和 NodeJS 的最佳实践，所以你可以为你的站点得到最好的和最小的应用程序。
*   模块化:它有 50 多个模块，将节省你大量的工作。您可以构建渐进式 Web 应用程序(PWA ),而无需编写任何代码。多酷啊。
*   愉快:可能是最好的功能之一。正如我之前所说，我深深地爱上了这个框架和 VueJS。它提供有意义的信息、好的文档和更好的社区。

我对 NuxtJS 的热爱推动了我对 Scifabric 的后期前端开发。基本上，我们为每一个客户使用，因为我们可以非常快速地构建任何符合客户需求的 PWA。一个例子是我们为西班牙绿色和平组织做的项目:[https://solarmaps.greenpeace.org](https://solarmaps.greenpeace.org)。

随着我越来越多地使用 NuxtJS，我想知道将我的主页迁移到 NuxtJS 并离开 Jekyll 会有多困难。为什么？因为我已经习惯了 Axios、组件、手写笔和现代工具来构建网站，所以我想为我自己的网站做这件事。

唯一的问题是:我的 Jekyll 站点有几个文件夹，里面有 Markdown 文件，这些文件在很大程度上依赖于它们的前端来构建站点。无论如何，我开始寻找这是否可能，显然，这是:D

## 在 NuxtJS 中处理降价

有很多解决方案可以导入和处理你的降价文件。例如，您可以使用 awesome[web pack Loader for Markdown](https://www.npmjs.com/package/frontmatter-markdown-loader)将您的文件导入 NuxtJS。

这是我的第一次尝试。利用它。虽然在开发模式下一切正常，但当我构建静态站点时，问题出现了。网络浏览器的控制台抱怨说，它缺少 Markdown 文件的 webpack 加载器，因此，我的博客帖子没有出现。

然后，我想，如果我让事情变得更简单。如果我能把 Markdown 文件转换成 JSON 呢？我在 npm 注册表中快速搜索了一下，发现了这个惊人的库: [markdown-to-json](https://www.npmjs.com/package/markdown-to-json) 。

这个图书馆很完美。为什么？因为您可以传递一个包含所有 Markdown 文件的文件夹，它会生成一个 JSON 对象，其中包含您所有的博客文章。前面的内容被解析，你也可以得到 JSON 格式的内容。因为一切都是 JSON，所以可以使用 HTTP 请求来请求数据，这样一切都就绪了。

我试着写了我现在的博客，一切都很好。因此，我所做的如下:在我的 NuxtJS 项目中，我创建了一个名为 **content** 的文件夹。在这个文件夹中，我有我的博客文章和项目子文件夹，里面有所有的 Markdown 文件。然后，我可以运行这样的命令来创建包含所有信息的 JSON 文件:

```
$ m2j -c content/blogposts/*.md -o static/blogposts.json 
```

通过将 blogposts.json 文件放在/static 文件夹中，我可以在 NuxtJS、Axios 中轻松地获取所有的博客文章。唯一的问题是，当您生成静态站点时，您需要实际生成它的所有路线。

## 为 NuxtJS 创建路线

因此，我们该如何处理呢？很容易。我们只需要修改我们的 package.json 文件来添加一些构建命令:

```
 "dev": "yarn md2json && nuxt",
    "build": "yarn md2json && nuxt build",
    "md2json": "m2j -c content/blogposts/*.md -o static/blogposts.json" 
```

多亏了这个解决方案，当你运行 yarn dev(或 npm run dev)时，你的博客文章将被转换成 JSON，网站将准备好使用它。同样的方法也用于构建静态站点。这太棒了，因为你可以用它和 Zeit、Github Pages 或 Netlify 一起自动构建。

解决了这个问题，我只需要建立我的网站。这或多或少很简单，因为我已经在我的 Jekyll 站点上使用了 webpack 和 JS 中的组件。在大多数情况下，我复制并粘贴代码块，然后使它们适应 NuxtJS 世界。

结果呢？这页。如你所见，这非常快(它部署在 Zeit 上)。如果你在 Android(或 iOS)上用手机浏览网站，并且你用的是现代网络浏览器，你会看到你可以在手机里安装我的网站；-)

此外，审计真的很酷。查看它们:

[![Audits from Google](img/3de84a2c5f2b49fba7d235d363c80ff4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--z_6wtgwf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ink4ruhyh17feepombmr.png)

## 一个模板

当我在迁移我的站点时，我意识到这对其他人也很有用，所以我创建了一个你可以重复使用的基本模板。这里有代码:[https://github.com/teleyinex/jekyll2nuxt](https://github.com/teleyinex/jekyll2nuxt)。

这个模板使用了 [VuetifyJS](http://vuetifyjs.com/) ，这样你就可以为你的网站设计一个现成的材料。它还启用了 PWA 和 Markdown 模块，因此一切都可以开箱即用。你只需要将你的博客文章复制到内容文件夹中，然后运行 yarn dev。就这么简单！

如果你读到这里，非常感谢你的时间🙌。如果你能在你的推特(或脸书)朋友中分享这篇博文，让更多人知道，我将不胜感激。