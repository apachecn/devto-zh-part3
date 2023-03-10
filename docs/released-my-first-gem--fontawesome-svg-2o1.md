# 发布了我的第一个宝石:FontAwesome SVG

> 原文：<https://dev.to/sylvainmetayer/released-my-first-gem--fontawesome-svg-2o1>

> 声明:这是我的第一篇英文帖子，它不是我的母语。如果我犯了语法错误，请告诉我如何改进，这样我下次会做得更好！
> 
> 你可以在我的网站上找到这篇文章的法文版:[https://sylvainmetayer . fr/projet/Jekyll-gem-SVG-font awesome/](https://sylvainmetayer.fr/projet/jekyll-gem-svg-fontawesome/)

## 需要

我想在我的[个人网站](https://sylvainmetayer.fr/)上使用 [FontAwesome](http://fontawesome.com/) ，而不需要整合整个库，也不会降低性能。另外，我不想用 Javascript 来显示图标。

我一直在寻找一种方法，只包括使用过的图标，而不是一个一个地放置它们，但没有找到任何适合我需要的东西。这就是我如何创建我的第一个 jekyll 插件

[文档](https://jekyllrb.com/docs/plugins/your-first-plugin/)足够清晰，对我帮助很大。

## 实现

我只想显示图标，所以我创建了 2 个液体标签，带参数。

我的第一个标签获取图标类型(常规、纯色或品牌)和我想使用的图标名称。这样，我就生成了相应的 svg 标记。

然后，我必须生成页面内图标的 svg 定义。

为此，我在一个简单的数组中将图标名称和类型添加到 jekyll 页面元数据中。

用我的第二个标记，我得到了每页使用的所有图标，并生成了 SVG 定义。这意味着如果一个页面不使用图标，什么都不会生成:)

我首先在`_plugins`目录中这样做，因为这样更容易调试。当它工作的时候，我决定把它打包成一个宝石，这样每个人都可以使用它，如果它对某些人有用的话:)

制作一颗宝石很容易，Bundler 指南非常有用。

我试图遵循 Ruby 的惯例，但是由于我不熟悉这种语言，我愿意接受任何改进它的建议。

我已经发布了第一个版本，现在，我必须全面测试它并处理潜在的错误(丢失图标，...)，那都是暂时的....以乐观的方式处理:(

## 用法

你可以这样使用宝石。

当你想打印一个图标时，使用下面的标签(这个标签生成了 [Twitter 图标](https://fontawesome.com/icons/twitter?style=brands) )

```
{% fa_svg fab.fa-twitter %} 
```

Enter fullscreen mode Exit fullscreen mode

现在，您必须在每一页中为您使用的所有图标生成 SVG 定义。

它必须只在所有页面的公共部分定义一次(页脚布局是一个好的选择)

```
{% fa_svg_generate %} 
```

Enter fullscreen mode Exit fullscreen mode

然后，享受你的图标:)

> 因为这个液体标签不在 dev.to 上，所以这里不呈现任何内容。你可以在这里查看它的外观

## 链接

如果你想检查代码，它在 Github [这里](https://github.com/sylvainmetayer/jekyll-fontawesome-svg)。

下载宝石，[就在这里](https://rubygems.org/gems/jekyll-fontawesome-svg)！

非常感谢你的阅读！