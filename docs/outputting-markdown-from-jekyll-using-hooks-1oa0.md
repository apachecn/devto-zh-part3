# 使用钩子从 Jekyll 输出 Markdown

> 原文：<https://dev.to/nzakas/outputting-markdown-from-jekyll-using-hooks-1oa0>

我最喜欢哲基尔 <sup id="fnref1">[1](#fn1)</sup> 的一件事就是用 Markdown 写博客。我喜欢不用担心 HTML，只是在帖子发布时让 Jekyll 为我生成它。在 Markdown 中直接使用 Liquid 标签也很有帮助，因为我可以定义站点范围或特定于页面的变量，然后在站点生成期间替换它们。这是一个非常有用的功能，我想利用它来输出 Markdown 供其他网站使用。像 Medium 和 dev .这样的地方允许你发布降价文章，所以我认为重新利用我在 Jekyll 中使用的降价会使交叉发布到这些网站更容易。

我假设在`page`变量上有一个属性可以让我访问渲染后的降价，但是我错了。这开始了 Jekyll 插件生态系统中相对未记录的角落的漫长旅程。我在这里分享这段旅程，希望其他人不会经历同样令人沮丧的经历。

## Jekyll 插件简介

在试图弄清楚如何为一篇文章获得渲染后的降价之前，我对 Jekyll 插件系统相对不熟悉。Jekyll 支持许多不同的插件类型 <sup id="fnref2">[2](#fn2)</sup> 。这些插件类型直接影响 Jekyll:

*   **生成器** -创建文件的插件。虽然不要求生成器创建文件，但这是最常见的用例。像`jekyll-archives`这样的插件使用生成器来创建原本不存在的文件。
*   **转换器** -在文本格式之间转换的插件。Jekyll 默认将 Markdown 文件转换成 HTML 是使用转换器实现的。通过创建自己的转换器，您可以添加对要转换为 HTML(或任何其他格式)的其他格式的支持。
*   钩子(Hooks)-插件监听 Jekyll 中的特定事件，然后执行一些与事件相关的动作。

还有两种主要用于液体的插件类型:

*   **标签** -创建一个格式为`{% tagname %}`的新标签，用于您的模板。
*   **过滤器**——创建一个可以用来转换输入的新过滤器，比如`{{ data | filter }}`

还有一个*命令*插件类型，允许你创建新的命令来使用`jekyll`命令行工具。使用这个插件类型实现了`jekyll build`命令。

## 设计解决方案

我的目标是将每个帖子的液态渲染的 Markdown 内容(因此所有数据处理都已完成)放入一个`page`属性中，这样我就可以将该内容输出到服务器上的 JSON 文件中。然后，该 JSON 文件将被 AWS Lambda 函数获取，该函数将内容交叉发布到其他位置。我不需要生成任何额外的文件，也不需要从一种格式转换成另一种格式，所以使用钩子显然是最好的方法。

钩子基本上是事件处理程序，在其中指定一个容器和要监听的事件。Jekyll 向您传递该事件的相关信息，然后您可以执行任何您想要的操作。有四种容器可以与挂钩一起使用:

*   `:site`-`site`物体
*   `:page` -每个非收藏页面的`page`对象
*   `:post` -每篇博客文章的`post`对象
*   `:document` -每个集合(包括博客文章和自定义集合)中每个文档的`document`对象

因为我希望这个解决方案适用于我的站点中的所有集合，所以我选择使用`:document`作为对所有集合类型进行相同更改的简单方法。

有两件事与我的目标直接相关:

*   `:pre_render` -在呈现文档内容之前激发
*   `:post_render` -在呈现文档内容之后、内容写入磁盘之前触发

似乎很明显，获取降价内容需要使用`:pre_render`事件，所以我开始使用这个设置:

```
Jekyll::Hooks.register :documents, :pre_render do |doc, payload|

  # code goes here

end 
```

每个钩子都被传递了它的目标容器对象，在本例中,`doc`是一个文档，还有一个`payload`对象，它包含了所有与文档相关的变量(这些变量是在呈现文档时模板内部可用的变量)。

在呈现每个文档之前会调用`:document, :prerender`钩子，这意味着您不需要担心手动循环集合。

## 问题:渲染并不意味着你认为它意味着什么

我认为一个`:document, :pre_render`钩子中的`content`属性将包含液体渲染的 Markdown，而不是最终的 HTML，但是我只对了一半。`content`属性实际上包含未处理的降价，它仍然包含所有的可变因素。原来“prerender”的意思和我想的不一样。

Jekyll 中给定文档的`content`属性的生命周期如下所示 <sup id="fnref3">[3](#fn3)</sup> :

1.  `content`属性包含移除了前面内容的文件内容(通常是 Markdown)
2.  `:pre_render`钩火
3.  属性被重写，液体标签被渲染(这就是 Jekyll 内部所说的*渲染*)
4.  `content`属性在通过转换器后被重写(这就是 Jekyll 内部称之为*转换*)
5.  `:post_render`钩子开火了

虽然 Jekyll 在内部将渲染(即应用液体)和转换(例如，Markdown 到 HTML 转换)分开，但 exposed hooks 并没有进行这种区分。这意味着，如果我希望用液体变量替换 Markdown 内容，那么我需要获得预先呈现的 Markdown 内容并自己呈现它。

## 解

在这一点上，我的计划是创建一个完成以下任务的`pre_render`钩子:

1.  检索每个文档的原始内容(包含在`doc.content`中)
2.  使用液体再现内容
3.  将结果存储在一个名为`unconverted_content`的新属性中，该属性可以在我的模板中访问

我从事物应该是什么样子的这个基本想法开始:

```
Jekyll::Hooks.register :documents, :pre_render do |doc, payload|

  # get the raw content
  raw_content = doc.content

  # do something to that raw content
  rendered_content = doSomethingTo(raw_content)

  # store it back on the document
  doc.rendered_content = rendered_content
end 
```

当然，我对 Ruby 不是很熟悉，所以这并不像我想的那样工作。

首先，`doc`是一个类的实例，在 Ruby 中不能任意给对象添加新的属性。然而，Jekyll 在文档对象上提供了一个`data`散列，可用于添加模板中可用的新属性。所以最后一行需要重写:

```
Jekyll::Hooks.register :documents, :pre_render do |doc, payload|

  # get the raw content
  raw_content = doc.content

  # do something to that raw content
  rendered_content = doSomethingTo(raw_content)

  # store it back on the document
  doc.data['rendered_content'] = rendered_content
end 
```

最后一行确保`page.rendered_content`稍后在模板中可用(记住，这是在`pre_render`期间发生的，所以模板还没有被使用)。

下一步是使用液体渲染原始内容。为了弄清楚如何做到这一点，我不得不在 Jekyll source <sup id="fnref4">[4](#fn4)</sup> 中四处挖掘，因为没有任何文档。渲染液体的方式与 Jekyll 默认的方式完全相同，需要一点设置，并从几个不同的地方获取数据。下面是最后的代码:

```
Jekyll::Hooks.register :documents, :pre_render do |doc, payload|

  # make some local variables for convenience
  site = doc.site
  liquid_options = site.config["liquid"]

  # create a template object
  template = site.liquid_renderer.file(doc.path).parse(doc.content)

  # the render method expects this information
  info = {
    :registers        => { :site => site, :page => payload['page'] },
    :strict_filters   => liquid_options["strict_filters"],
    :strict_variables => liquid_options["strict_variables"],
  }

  # render the content into a new property
  doc.data['rendered_content'] = template.render!(payload, info)
end 
```

这个钩子的第一步是创建一个液体模板对象。虽然您可以使用`Liquid::Template`直接做到这一点，但 Jekyll 在使用`site.liquid_renderer.file(doc.path)`时会在内部缓存液体模板，因此使用它来保持 Jekyll 构建尽可能快是有意义的。然后，内容被解析成一个模板对象。

`template.render()`方法不仅需要`payload`对象，还需要一些附加信息。`info`散列传入`registers`，这是模板内部可访问的局部变量，以及一些关于 liquid 应该如何表现的选项。准备好所有数据后，内容将呈现到新的属性中。

然后，这个文件需要放在 Jekyll 站点的`_plugins`目录中，以便在每次构建站点时运行。

## 访问`rendered_content`

安装了这个插件后，可以通过`rendered_content`属性获得降价内容，就像这样:

```
{{  page.rendered_content  }} 
```

唯一的问题是将`page.rendered_content`输出到降价页面会导致所有的降价都被转换成 HTML。(记住，Jekyll 首先在内部渲染液体，然后将结果转换成 HTML。)所以为了输出原始的 Markdown，你需要应用一个过滤器来阻止 Markdown 到 HTML 的转换，或者使用一个不能自动转换的文件类型。

在我的例子中，我将 Markdown 内容存储在一个 JSON 结构中，所以我使用了`jsonify`过滤器，就像这样:

```
---
layout: null
---
{
    {%  assign  post  =  site.posts.first  %}
    "id": "{{  post.url  |  absolute_url  |  sha1  }}",
    "title": {{  post.title  |  jsonify  }},
    "date_published": "{{  post.date  |  date_to_xmlschema  }}",
    "date_published_pretty": "{{  post.date  |  date:  "%B %-d, %Y"  }}",
    "summary": {{  post.excerpt  |  strip_html  |  strip_newlines  |  jsonify  }},
    "content_markdown": {{  post.rendered_content  |  jsonify  }},
    "content_html": {{  post.content  |  jsonify  }},
    "tags": {{  post.tags  |  jsonify  }},
    "url": "{{  post.url  |  absolute_url  }}"
} 
```

另一个选择是在`_includes`目录下创建一个`rendered_content.txt`文件，其中只包含这个:

```
{{  page.rendered_content  }} 
```

然后，您可以在需要未转换的减价内容的任何地方包含该文件，就像这样:

```
{%  include  "rendered_content.txt"  %} 
```

## 结论

Jekyll 钩子是一个有用的特性，它让你在 Jekyll 生成你的站点时与它交互，允许你在这个过程中截取、修改和添加数据。虽然没有太多的例子，但是这个概念足够简单，只要几个指针，任何程序员都应该能够让一些东西工作起来。对我来说，最大的绊脚石是缺乏关于如何使用 Jekyll 钩子的文档，所以我希望这篇文章能够帮助那些试图在他们的 Jekyll 站点上完成类似任务的人。

迄今为止，我发现 Jekyll 是非常多才多艺和可定制的。能够获得液体渲染的降价(尽管这需要一些工作)使我的出版工作流程更加灵活，因为我现在可以更容易地将我的作品交叉发布到各种其他网站上。

## 参考文献

*本文原载于[人类编码博客](https://humanwhocodes.com)2019 年[4 月 16 日](https://humanwhocodes.com/blog/2019/04/jekyll-hooks-output-markdown/)。*

* * *

1.  [哲基尔](https://jekyllrb.com/)↩

2.  [哲基尔外挂](https://jekyllrb.com/docs/plugins/) [↩](#fnref2)

3.  [杰基尔解读顺序](https://jekyllrb.com/tutorials/orderofinterpretation/) [↩](#fnref3)

4.  [Jekyll renderer . Rb](https://github.com/jekyll/jekyll/blob/be78b4246c4513738eb4c18b76569182dd4f8578/lib/jekyll/renderer.rb#L70-L93)↩