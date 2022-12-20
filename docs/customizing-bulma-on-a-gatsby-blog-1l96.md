# 在盖茨比的博客上定制布尔玛

> 原文：<https://dev.to/liltechnomancer/customizing-bulma-on-a-gatsby-blog-1l96>

这是我的个人博客上的一篇文章，位于[这里](https://lvrbrtsn.com/blog/customizing-bulma-on-gatsby/)T2，它们提前一天发表在那里。多漂亮啊。

以下是这篇文章的 TLDR 版本:

1.  首先，我们需要`npm install --save Bulma gatsby-plugin-sass`。
2.  然后我们应该将`gatsby-plugin-sass`放入我们的 dang ole `gatsby-config.js`中。
3.  之后要把`layout.css`改成`layout.scss`。
4.  这允许我们在同一个`layout.scss`文件中`@import '../../node_modules/bulma/bulma.sass';`。
5.  然后，除此之外，我们可以声明您想要覆盖的任何变量。

以下是我写这篇文章时为这个博客设置的变量。

```
$primary: #b084eb;
$blue: #7dc1ff;
$input-shadow: none;
$info: $blue; 
```

Enter fullscreen mode Exit fullscreen mode

尽管我喜欢从头开始编写 CSS，但这并不总是最好的选择，在加入 indy 后，我不得不认真考虑使用 CSS 框架。我决定这样做有以下两个原因。

1.  我希望课程专注于 JavaScript，所以我想使用一个易于使用的 CSS 框架，这样内容就可以专注于 JavaScript。
2.  我想快速构建许多共享一个主题的东西。

选择哪个框架对我来说很容易，因为我真的很喜欢布尔玛，我在 U-Haul 大量使用它，并且真的很喜欢使用它。我喜欢它没有 JS，可以通过 npm 安装，易于定制，有一些很酷的社区主题，它的命名惯例对我来说很有意义。它是迄今为止我最喜欢的 CSS 框架，与 Bootstrap 之类的东西相比，它是一股清新的空气。

这个博客关注的是布尔玛的易于定制。你可以在这里阅读所有关于布尔玛的定制。不过，我会在下面介绍一点。

基本上，布尔玛有几种类型的变量，你可以用你自己的品牌价值来超越它们。有些是从其他的衍生出来的。所以改变一些基本变量会有很大的影响！。

我真的只想做一些事情，改变原色，蓝色的阴影，并删除输入的内部阴影。但首先我需要在我的网站上安装布尔玛。

我还没有对我的 Gatsby 站点做太多的工作，它使用默认的 starter，用 layout.css 文件把你连接起来。布尔玛使用 Sass，所以我们需要改变这一点。幸运的是，gatsby 使这变得非常简单，我们需要做的就是在我们的 Gatsby 站点上安装 gatsby-plugin-sass 并把它添加到我们的 Gatsby 配置中，如下所示。

```
plugins: [
  'gatsby-plugin-react-helmet',
  'gatsby-plugin-sass',
  // ...other plugins.
] 
```

Enter fullscreen mode Exit fullscreen mode

嘣！现在我们开始做饭，我们现在可以将`layout.css`文件更改为`layout.scss`文件并导入布尔玛，因为我们是这样设置的，所以我们也可以覆盖那些变量。把我的`layout.scss`文件弄成这样

```
// BULMA VARIABLE OVER-RIDES
$primary: #b084eb;
$blue: #7dc1ff;
$input-shadow: none;
$info: $blue;
$background: #fafafa;
@import '../../../node_modules/bulma/bulma.sass';
// ...All the Gatsby default stuff. 
```

Enter fullscreen mode Exit fullscreen mode