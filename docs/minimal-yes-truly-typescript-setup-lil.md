# 最小化(是的，确实如此)的类型脚本设置

> 原文：<https://dev.to/alephnaught2tog/minimal-yes-truly-typescript-setup-lil>

有史以来最令人沮丧的事情之一*是想要尝试一些东西，并且不得不安装一百万个东西和学习 3.14 新工具，仅仅是为了让一些东西在你的浏览器中运行。*

我为一个项目写了一个[最小服务器和存储库](https://github.com/aleph-naught2tog/ts_without_dependencies)来编译你的 TypeScript 以便在浏览器环境中使用，这样人们就可以尝试 TypeScript，而不用担心加载器、webpack 等等。

## ![GitHub logo](img/75095a8afc1e0f207cda715962e75c8d.png)[aleph-naught 2 tog](https://github.com/aleph-naught2tog)/[ts _ 无 _ 从属性](https://github.com/aleph-naught2tog/ts_without_dependencies)

### 具有类型脚本的网站的独立骨架

<article class="markdown-body entry-content container-lg" itemprop="text">

# 独立的类型脚本设置

这是一个真正的*最小化的、**无依赖性的**设置，它将为您提供框架:*

*   用 Typescript 编写代码
*   编译它以供浏览器使用
*   在浏览器环境中使用编译代码

就是这样。这个服务器不会做任何花哨的事情:它不会为你重新加载，它不会缩小或丑化任何东西。

它*会*为你服务文件，就这样。如果您愿意，可以完全忽略服务器；或者，如果你认为编写自己的服务器听起来不错，自述文件的第二部分[值得一读。](https://github.com/aleph-naught2tog/ts_without_dependencies#about-the-server)

这里没有魔法。没有网络包，没有加载器，没有必要的路由库，等等。(如果你不知道这些是什么，不要担心——这个库的*点*是这样的，所以你不必知道。)使用这些工具并没有错，但是它们增加了复杂性，而且当你真正想要的时候，它们会让人非常沮丧

</article>

[View on GitHub](https://github.com/aleph-naught2tog/ts_without_dependencies)

它的*唯一的*依赖是 TypeScript 本身。字面上。(嗯，好吧，Node 也是，但是 TypeScript 本身就用 Node，所以我觉得那是允许的。)

如果你只是想尝试一个基本的 TypeScript 网站，这是完美的。

如果你想试着写你自己的服务器，请以此为基础——我在服务器文件中添加了注释，如果有不清楚的地方，请告诉我！-但是如果你不想与服务器有任何关系，你不应该需要弄乱它的代码。