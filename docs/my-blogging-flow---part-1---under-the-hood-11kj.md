# 我的博客流-第一部分-引擎盖下

> 原文：<https://dev.to/dmerejkowsky/my-blogging-flow---part-1---under-the-hood-11kj>

*最初发布于 [dmerej.info](https://dmerej.info/blog/post/my-blogging-flow-part-1-under-the-hood/) 。如果你继续阅读 dev.to 上的这篇文章，请记住这一点:*

几个人问我我的博客是如何工作的，它是如何写的，所以这里有一篇关于我如何写博客的博文(如此元)。

让我们从头开始。

# dmerej.info/blog 的历史

## 购买 DNS 域名

第一步是购买域名。我选择了 gandi.net 的 T2，因为我只听说过他们的注册服务。我喜欢`.info`的“复古”感，而`dmerej`前缀是我用来缩短我名字的前缀。不管这是好是坏，现在改变已经太晚了，所以就这样吧。

## 获得自己的机器

我总是想控制关于我的博客托管的所有方面，所以我开始寻找一台专用的机器。

如果你不熟悉这个概念，拥有一台专用机器意味着你要为硬件和对 soneone else 拥有的服务器的 ssh 访问付费。作为交换，你可以手动安装和配置一切。如果这听起来对你来说是一个很糟糕的交易，请记住[我重视控制胜过便利](https://dmerej.info/blog/post/a-definition-of-the-linux-desktop/):)

反正我是在一家叫 dedibox 的公司买的服务器。(为了完整起见，我必须说我现在是由[数字海洋](https://www.digitalocean.com)的好心人主持的)。

关于主机服务已经说得够多了，让我们来谈谈博客引擎吧！

## 静态或动态

博客引擎有两种类型:*静态*和*动态*。它与当访问者请求文章的 URL 时会发生什么有关。

在动态博客引擎中，每篇文章的内容通常存储在数据库中，HTML 由引擎为每个访问者生成。这意味着服务器必须为每次访问运行一些代码。

另一方面，一个“静态”引擎通常会预先从用标记语言(通常是 Markdown)编写的源代码中生成一堆 HTML 文件。然后，当访问者想要查看页面时，服务器所要做的就是显示正确的文件。

两者各有利弊:

*   动态博客引擎可以处理评论、计算统计数据(比如浏览量)等等。但是因为它们需要数据库和一些编程语言的支持，所以很难部署。找到正确备份的方法也会变得棘手。

*   静态博客不能处理评论或类似的事情，但是它们非常容易部署(T4 ):你可以简单地直接使用现有的网络服务器(比如 T0 或 T1)。另外，备份很容易——你只需要备份源文件，一个`git`库就足够了。

## 寻找博客引擎

我必须说，我花了太多的时间来寻找我的博客引擎。

我的直觉告诉我我想要一个静态引擎，所以我开始摆弄许多静态博客生成器。唉，那里有成千上万的静态引擎，我几乎放弃了寻找我会使用的那一个——选择实在太多了。

这时候我意识到我没有正确处理这个问题:我担心如何发布内容，但我说内容还没有写完。

所以我看了看动态博客引擎(那里选择较少)，几乎是偶然找到 Dotclear，然后开始写。

最后，我的文章在网上公开，世界上任何人都可以阅读。多开心啊！

给我亲爱的读者的建议:如果你想在网上发表一些东西，不要犯我的错误:在担心它的出版之前，确保你有一些内容。

## 网络时代

为 Dotclear 配置`php`有点乏味，但是一旦完成，我必须承认它使用起来非常愉快。管理视图丰富而有特色，编辑表单符合人体工程学。评论是支持开箱即用的，而且很容易调节。

但是我仍然有一个问题:我收到了很多关于评论的通知，但是几乎所有的都是垃圾邮件

这令人沮丧，所以我取消了评论服务，开始寻找替代服务。

## 雨果时期

我回去找静态发动机，在 StaticGen 发动机对比网站上[找到了 Hugo。](https://www.staticgen.com/)

我立刻喜欢上了它:

*   有许多美丽的主题可用。(Dotclear 主题也不错，但在我看来有点过时)。
*   Hugo 易于安装(只需一个二进制文件)
*   它的文档是完整的，易于理解
*   我不再需要在我的服务器上设置`php`。仅仅几行`nginx`的配置就足够了。
*   再加上它快得离谱*。一秒钟不到一百多页。*

 *为了工作，雨果需要一个主题。Hugo 主题由各种*模板*和一些*静态文件*(如 CSS 或图片)组成，模板控制 HTML 如何生成，静态文件需要随生成的文件一起复制。

雨果没有预设主题，所以试了几次后，我选定了布莱克本。

### 编写自己的短代码

Hugo 的好处是你可以随心所欲地定制你的博客的外观和感觉。我仍然天真地记得我在 FOSDEM 2017 的[所见所闻中写的简短场景实现“电影脚本外观”的那一天。](https://dmerej.info/blog/post/heard-and-seen-at-fosdem-2017/#scenes)

它是这样工作的。

首先，我在主题
的`layouts/shortcode`目录中定制了`scene`短代码

```
<div class="scene">
<h1>
  {{ .Get "title" }}
</h1>
<p>
{{ .Inner | markdownify }}
</p>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

这让我可以在 Markdown source:

```
{{< scene title="Resurrecting Dinosaurs" >}}
SPEAKER: I did *not* expect to have a win32 architecture slide here at FOSDEM
{{< /scene >}} 
```

Enter fullscreen mode Exit fullscreen mode

当 Hugo 看到`scene`指令时，它将生成包含以下内容的 HTML 代码:

*   `<div>`及其特殊的`scene`类
*   `h1`标签内的`title`
*   和`scene`指令中的文本，也被解释为 Markdown:

```
<div class="scene">
<h1>
  Resurecting Dinosaurs
</h1>
<p>
SPEAKER: I did <em>not</em> expect to have a win32 architecture slide
here at FOSDEM.
</p>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

最后，我有一点 CSS 来正确地渲染`div`:

```
.scene {
  font-family: monospace;
  line-height: 1.2em;

  h1 {
    line-height: 1.5em;
    font-size: 1.2em;
    font-style: bold;
    text-align: center;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

仅此而已:)

### 切换到新主题

过了一段时间，我开始厌倦了`blackburn`。它的 CSS 文件不容易编辑，它也有很大的依赖性，如`font-awesome`或雅虎的`Pure CSS`。

所以我换成了 [minio](https://themes.gohugo.io/minimo/) ，这是我今天还在用的主题。它用的是`Sass`而不是纯粹的`CSS`，来源组织的很整齐。此外，它还附带了一个配置良好的工具链，允许即时获得对主题更改的反馈。

## 来源组织

如果你看一下博客的[来源，你会发现帖子是按时间顺序编号的:](https://github.com/dmerejkowsky/blog/tree/master/content/post) 

```
0001-hello-world.md
0002-i-use-vim-and-so-should-you.md
0003-finding-a-good-project-name.md
... 
```

Enter fullscreen mode Exit fullscreen mode

我喜欢这个方案有几个原因:

*   帖子很自然地出现在各个地方(即使有`ls`)
*   当我写第 100 篇文章时，我会有这种成就感
*   我可以很容易地从我的编辑器自动完成文件名(我只需要记住文章编号)

由于我不是很想让内部号被外面看到，所以所有的文章在“前面的事”里都有一个`slug`:

```
# In 0042-foo.md
-------- slug: "foo"
date: "2016-03-31T13:00:19+00:00"
draft: false
title: "..."
-------- 
```

Enter fullscreen mode Exit fullscreen mode

这意味着`0042-foo.md`文件将被写成`foo/index.html`。

我还告诉`nginx``blog/post/foo`对应`blog/post/foo/index.html` :

```
location /blog {
  try_files $uri $uri/index.html =404;
} 
```

Enter fullscreen mode Exit fullscreen mode

这在 Hugo 的文档中被称为*漂亮的 URL*。

## 注释

最后一个难题是评论系统。

我使用 [isso](https://posativ.org/isso/) ，一个自托管的评论服务。

首先，我修补了 Hugo 模板，在每篇文章的底部添加了一点 JavaScript:

```
<!-- in layouts/entry.html -->
<div>
  <script data-isso="//dmerej.info/isso/"
            src="//dmerej.info/isso/js/embed.min.js"
  >
  </script>
  <section id="isso-thread">
  </section>
</div> 
```

Enter fullscreen mode Exit fullscreen mode

然后，在`nginx` :
的帮助下，我确保`isso`服务在`https://dmerej.info/isso`时可以到达

```
# In /etc/nginx/conf.d/blog.conf
location /isso {
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Script-Name /isso;
    proxy_set_header Host $host;
    proxy_set_header X-Forwarded-Proto $scheme;
    proxy_pass http://localhost:1234;
} 
```

Enter fullscreen mode Exit fullscreen mode

和`systemd` :

```
# In isso.service [Unit]
Description=isso comments service
Wants=network-online.target
After=network-online.target

[Service]
Type=simple
ExecStart=/srv/isso/.local/bin/isso -c /srv/isso/isso.conf run
User=isso

[Install]
WantedBy=multi-user.target 
```

Enter fullscreen mode Exit fullscreen mode

一些注意事项:

*   评论表单不会显示访问者是否禁用了 JavaScript。我觉得这有点可悲，但另一方面，它阻止了机器人发布垃圾邮件(至少到目前为止)。
*   所有的评论都存储在 SQLite 数据库中。我有一个系统定时器每天备份它。
*   人们可以选择在表单中留下他们的电子邮件。Isso 对此不做任何处理，只是将它们存储在数据库中。我有时用它们给他们发私人信息。

这就是博客在幕后的运作方式！请继续关注第 2 部分，我将解释新文章是如何撰写和发表的。

* * *

我很想听听你有什么要说的，所以请随时在下面留下你的评论，或者查看我的联系页面了解更多与我联系的方式。*