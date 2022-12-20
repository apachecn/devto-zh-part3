# 使用 Gatsby 和 GraphQL 重写静态网站-第 1 部分

> 原文：<https://dev.to/laurieontech/rewriting-a-static-website-using-gatsby-and-graphql---part-1-53o0>

**最初发布在[十里广场的博客上。](https://tenmilesquare.com/blog/)T3】**

我的[个人网站](https://www.laurieontech.com)是用我喜欢的 Jekyll 实现的，但它有一个不幸的初始加载行为，看起来不太好，也没有提供我想要的用户体验。所以，我最近决定重新实现这个网站，并在这个过程中学习 [GraphQL](https://graphql.org/) 。

大约在同一时间，一个朋友推荐了[盖茨比](https://www.gatsbyjs.org/)。由于 Gatsby 意味着可以开箱即用地使用 GraphQL，所以这是一个完美的选择。我的目标是，如果我正在开发一个我已经熟悉的最终产品，那么学习这两种技术会更容易。

### 下一代

Jekyll 混合使用 Markdown(或者在我的情况下，Yaml)、Html/CSS 和 Liquid templating engine 来生成一个静态站点。在我的站点中，我使用 Liquid templating 创建静态页面，这些页面可以循环显示我在 Yaml 文件中定义的内容。这些内容可以是博客文章(如本文)、演讲活动的细节等。我还可以为页面的一个部分定义一个模板，比如标题，并使用 Yaml 为每个页面定义该部分的内容。所有这些都使用“Jekyll build”构建，并使用 Firebase 部署，尽管这是另一篇文章的主题。

另一方面，Gatsby 基于 React，所以大部分是 Javascript。这看起来很不一样，而且有一些重大的变化，但是 GraphQL 和 Gatsby 插件使这些变化变得更加简单。首先，Gatsby 附带了一些入门模板。我最初只是以默认的“新盖茨比”开始。然而，当我发现一个预先设计好的 [HTML5UP](https://html5up.net/) 启动包时，我用它做了一个版本。对我来说，看到一个现有的预样式页面并开始从这些模式中学习是有益的。一般来说，我喜欢从有用的东西开始，然后迭代到我想要的功能或外观。

### 深入研究

使 Gatsby 功能如此丰富的部分原因是添加插件的能力。虽然大多数框架都允许插件/库，但那些为 Gatsby 而存在的插件/库是特定于该框架的，而且粒度相当细。

我的第一个目标是限制我需要对我的站点进行的更改。具体来说，我希望将数据定义为静态 yaml，这样就不必全部重新格式化。我可以通过添加一些插件来做到这一点。第一个是

```
{    
  resolve: `gatsby-source-filesystem`,
  options: {  
      path: `./src/data/`,
  } 
} 
```

Enter fullscreen mode Exit fullscreen mode

这允许我的代码查看 `src/data` 目录中的文件。现在，我所要做的就是把我的文件从 Jekyll 项目的` _data` 文件夹中取出，放到 Gatsby 项目的 `src/data` 文件夹中。

此时，我能够找到文件本身，但我无法深入了解其中的内容。为此，我需要添加插件` Gatsby-transformer-YAML`。有了它，我的 GraphQL 查询可以查看我的项目中任何 yaml 文件的内容来查询结果。我应该在这里指出，如果你喜欢这种语法，markdown 文件也有类似的插件。

现在我可以访问数据了，我需要创建一个查询来从中提取我想要的信息。典型的 GraphQL 查询如下所示:

```
type  Query  {  me:  User  } 
```

Enter fullscreen mode Exit fullscreen mode

在我的例子中，使用 yaml 插件，我需要的查询如下所示:

```
{  allSpeakingYaml  {  edges  {  node  {  conference  year  url  date  location  image  talks  {  title  video  }  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，查询从 `allSpeakingYaml` 开始，以指示 GraphQL 在 speaking.yaml 文件中查找该数据。例如，如果我想查询 blog.yaml 文件，我可以用 allBlogYaml 开始查询。

### 连接起来

我发现的一个陷阱是错误`类型查询`上的未知字段 allSpeakingYaml。我多次看到这个错误，触发它的代码类型似乎总是不同的。我花了一点时间来确定它试图传达什么。这个错误的真正原因是 GraphQL 无法找到匹配的结果。这可能意味着它找不到与第一行匹配的文件，正如我在我的文件说话时偶然发现的。yaml 文件意味着搜索失败。也可以通过查找文件而不是查询所要求的相关内容结构来触发。当我忘记将 allSpeakingYaml 更改为 allBlogYaml，但已经更改了我要查找的数据的结构和关键字时，我再次发现了这个错误。发现这个错误的另一种方法是省去系统文件插件；没有它，保存我的数据的文件夹对 GraphQL 是不可见的，它将抛出同样的错误。

既然我现在没有错误，是时候看看查询的响应了。Gatsby 的一个伟大之处在于它提供了一个浏览器视图来测试您的 GraphQL 查询，类似于 Postman。我可以使用这个端点来测试我的查询并检查响应的结构。这很重要，因为为了在 React 中使用 JSX 并在 web 上显示数据，需要适当地引用它。我将从查询我的博客文章开始，因为这是最简单的结构。这是回应:

```
{  "data":  {  "allPostsYaml":  {  "edges":  [  {  "node":  {  "title":  "Optimistic UI vs Intuitive UX",  "url":  "https://tenmilesquare.com/optimistic-intuitive/"  }  },  {  "node":  {  "title":  "Technology for the Non-Technical",  "url":  "https://tenmilesquare.com/for-the-non-technical/"  }  }  ]  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

### 映射数组对象

我想把我的文章的所有标题和它们的 url 链接放在我的博客文章页面上。在 Angular 以前的项目中，我临时引用了整个混乱的对象，并用括号渲染，以确保一切正常。有趣的是，即使我想这样做，我也不能在这里这样做，JSX 不允许这样做，并给出了错误`对象作为反应子对象无效`。这意味着我需要引用响应中可用的单个字符串。

看上面的响应对象，首先要注意的是数据和 allPostsYaml 是对象。然而，在这些对象中有边，它指的是一个数组。这意味着 JSX 需要使用地图。

```
{data.allPostsYaml.edges.map(({ node }, index) => ({node.title}))} 
```

Enter fullscreen mode Exit fullscreen mode

在地图中，我可以引用上面例子中的 node.title ，或者 node.url 。这意味着我的站点将遍历数组中的所有条目，并显示我想要的博客文章标题。

但是并不是所有的 yaml 文件都有这样简单的结构。这是对我的说话对象查询的部分响应:

```
{  "data":  {  "allSpeakingYaml":  {  "edges":  [  {  "node":  {  "conference":  "Indy Code",  "url":  "https://indycode.amegala.com/",  "date":  "April 24-26, 2019",  "image":  "headshot.jpg",  "talks":  [  {  "title":  "How to Talk Like an Engineer",  "video":  null  },  {  "title":  "A Software Engineer's Guide to DevOps",  "video":  null  }  ]  }  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

在顶层，响应看起来与博客帖子的响应相同。但是，在响应中，关键字 `talks` 有一个数组值。我最初的直觉是在另一个地图函数中添加一个地图函数，然后完成它。大概是这样的:

```
{data.allSpeakingYaml.edges.map(({ node }, index) =>
    ({node.talks.map(({ talk }) =>( {talk.title} )) 
))} 
```

Enter fullscreen mode Exit fullscreen mode

但是那没有用。它一直在说那个谈话是无效的。为什么？嗯，我必须非常仔细地观察响应对象。在我们之前的例子中 `edges` 是一个引用数组对象的键，就像这个例子中的 `talks` 一样。在这两种情况下，数组中都有没有键引用的对象。然而，在 `edges` 示例中，这些对象内部有另一个对象， `node` 。所以我们引用一个对象并查看它的属性。在 `talks` 的情况下，只有键控属性，所以我们能够直接引用它们，就像这样:

```
{data.allSpeakingYaml.edges.map(({ node }, index) =>
    ({node.talks.map(({ title, video }) => ( {title} )) 
))} 
```

Enter fullscreen mode Exit fullscreen mode

我承认，我仍然期望给 `talks` 中的每个对象分配一个引用键，并访问 `title` 和 `video` 数据作为属性。然而，我们没有对 `edges` 数组那样做，我们直接引用了 `node` 。所以这是一样的。

### 还会有更多

此时，我已经移动了我的站点数据，并使它在站点的页面上可以访问和查看。这是良好的第一步，但仍有许多工作要做。在我的下一篇文章中，我将讨论移除我的 Jekyll 实现中留下的一些液体模板指令。