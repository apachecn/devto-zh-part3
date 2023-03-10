# Harp:静态站点生成器评论

> 原文：<https://dev.to/megazear7/harp-a-static-site-generator-review--38el>

我最近在 [Harp](http://harpjs.com/) 静态站点生成器上做了一系列[博文](https://www.alexlockhart.me/search/label/harpjs)。我想通过回顾 Harp 来结束这个系列。我会给你的优点，缺点，然后这个静态站点生成器的用例。

## 优点

Harp 对于你想使用什么技术很固执己见。对于您的服务器端 html 呈现，该集合严格限于 Jade、Markdown 和 EJS；用于 CSS 预处理的 Sass、Less 和 Stylus 和 CoffeeScript 作为编译成 JavaScript 的选项。当然，你也可以使用普通的 HTML、CSS 和 JavaScript。如果这些技术适合您想要的技术堆栈，这是很好的，因为没有必要进行配置-这些只是开箱即用。它们也很容易学习，所以对于没有经验的开发者来说不应该成为障碍。

此外，Harp 固有的结构化元数据直接与网页内容的结构相关联。这提供了创建上下文相关特征的能力。它还使用 JSON 来存储这些元数据，这当然是用于 web 开发的正确格式。

最后，Harp 灵活支持集成和部署。更改功能或内容就像编辑 JSON 文件和重建站点一样简单，这使得它很容易集成到更大的系统中。此外，build 命令简单灵活，可以轻松地按照您想要的方式进行部署。

## 弊

元数据不能通过 API 访问。如果是的话，这将为创建动态客户端功能带来许多机会。

与页面相关联的元数据可能存在于两个位置之一。它可以作为 _data.json 文件的顶级属性存在，作为它的同级。或者它可以存在于 parent _data.json 文件中子目录名称的属性下的上一级。只要你始终如一，这不会引起大的问题，但这是一个含糊不清的机会。

HTML 模板选项是有限的。EJS 是一项古老的技术，存在许多其他基于 JavaScript 和节点的 html 模板选项。想到的是胡子、车把和灰尘。拥有一个这样的或者类似的 html 模板系统来代替 EJS 将会是一个巨大的进步。这个限制也意味着，随着应用程序复杂性的增加，向模板添加业务逻辑变得不可避免。

最后，没有对元数据和布局继承的内置支持。如果 _data.json 文件的属性可以在子目录中继承，而不必将该逻辑添加到 HTML 模板中，那么您可以以低得多的复杂性成本创建丰富的功能。此外，让 _layout.ejs 文件继承层次结构，以便可以定义单个布局，然后所有子目录都继承该布局，这将再次简化您的模板，并使代码更加简洁。

## 用例及最终想法

我认为 Harp 应该用于那些关注内容多于功能的站点，在这些站点中构建时业务逻辑是最小的。为了保持数据完整性，你必须实现复杂的模板逻辑，这已经超出了 Harp 的最佳用例范围。

查看我的博客,了解更多我对技术和其他各种话题的思考。