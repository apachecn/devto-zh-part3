# 六角局域构型

> 原文：<https://dev.to/kritner/hexo-local-configuration-3b1d>

探索如何使用合并的配置文件在 Hexo 中建立“本地”和“生产”工作流。

戴夫(来自[、https://davefollett.io/](https://davefollett.io/))和我今天在[编码区块松弛频道](https://codingblocks.net/slack)谈论了一些缩略图、特征图像和我们本地 vs 部署的开发流程。我发现，在本地开发和部署情况之间切换时，需要对如何避免更改配置文件进行一些研究。

答案就在众多的配置文件中，这些文件可以合并在一起用于本地开发…

我从`_config.yml`发来的`url`指向了我在 blog.kritner.com[的实际域名](https://blog.kritner.com)。这导致了写一篇新文章时的问题，因为我本来打算使用的图像，如“hexo-logo.png”会将其完整的 URL 构建为`[url]/[myPost]/hexo-logo.png`，在我的例子中是`https://blog.kritner.com/...`。我的配置文件的“URL”部分被注入，但是由于这些图像还没有在互联网上存在，我无法确认图像是否真的“工作”。

这导致我将我的`url`更新为“localhost:4000”。现在，我的图像显示出来了(当我拼写图像名称正确时)，但我现在必须处理另一个问题。我需要记得把我的`url`改回我的真实域名“blog.kritner.com”。

# 多个配置文件救驾！

在翻阅 hexo 文档时，我偶然发现:[使用另一种配置](https://hexo.io/docs/configuration.html#Using-an-Alternate-Config)。按照这种方式，我将能够利用“合并”在一起的多个配置文件，配置文件“链”中更下游的值具有优先权。

考虑到这一点，我创建了一个新的配置文件:

_ config . local . yml

```
url: http://localhost:4000 
```

Enter fullscreen mode Exit fullscreen mode

就这样…现在，我们只需要在生成和服务时使用这个文件…如下:

```
hexo clean && hexo generate --config _config.yml,_config.local.yml && hexo server --config _config.yml,_config.local.yml 
```

Enter fullscreen mode Exit fullscreen mode

在上面(我把它放在一个名为“serveLocal.bat”的. bat 文件中)，我们说在构建和服务时使用“_config.yml”，然后是“_config.local.yml”文件。这导致“_config.yml”的`url`属性被覆盖——从“[https://blog . krit ner . com”](https://blog.kritner.com)到“[http://localhost:4000”](http://localhost:4000)。

现在，在本地开发时，我只是运行“serveLocal.bat”文件，而不是运行`hexo generate && hexo server`。当推送到 GitHub 时，GitHub 开始构建 Netlify，普通的原始“_config.yml”文件是该构建的构建步骤`hexo generate`下唯一考虑的文件。

* * *

对于“ *config.local.yml”文件，几乎立即想到的另一个设置是`render_drafts: true`。有了本地配置文件中的这个选项，我可以确保我能够在本地查看我的草稿，同时在我想要将一些草稿签入我的 git repo 的情况下，将它们保留在我部署的博客之外——而不必记得更改配置设置！*

# 总结起来

今天我探索了在我的 hexo 开发过程中使用多个配置文件。我使用的完整的“_config.local.yml”是:

```
url: http://localhost:4000
render_drafts: true 
```

Enter fullscreen mode Exit fullscreen mode

我使用 bat 文件进行本地生成和服务:

```
hexo clean && hexo generate --config _config.yml,_config.local.yml && hexo server --config _config.yml,_config.local.yml 
```

Enter fullscreen mode Exit fullscreen mode

请注意，“_multiconfig.yml”文件是基于您的配置文件的合并自动生成的，以便于(我假设)调试。

Hexo logo 作者:[https://github.com/hexojs/logo](https://github.com/hexojs/logo)

相关:

*   [https://davefollett.io/](https://davefollett.io/)
*   [使用备用配置](https://hexo.io/docs/configuration.html#Using-an-Alternate-Config)