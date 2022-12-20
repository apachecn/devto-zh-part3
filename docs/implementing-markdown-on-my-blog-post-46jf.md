# 对博客帖子实施降价

> 原文：<https://dev.to/saral/implementing-markdown-on-my-blog-post-46jf>

在遇到 heroku 部署的问题后，我决定今天就去。我是应该在 heroku 上试着调试应用程序，还是应该在本地开发应用程序，并在其上构建功能。我决定在本地开发这个应用，改天再担心 heroku 的部署。

我从我的待办事项列表中选择了一项任务。今天，它将是-在博客帖子上实现降价。到目前为止，我一直使用简单格式来格式化博客文章。然而，随着 markdown 的实现，我将能够使用 markdown 格式在应用程序上写博客。

第一步是做一个快速的 duckduckgo，并找到一个可行的解决方案。按照教程，这里是我如何能够完成我的任务。

首先，我需要红地毯上的宝石。为此，我通过
`gem 'redcarpet', '~> 3.4'`添加了最新的 redcrapet。在运行 bundle install 之后，redcarpet 被安装。

现在，我不得不修改我的`application_helper.rb`。我在观看这些教程时的一个疑问是“我如何知道在什么文件中做什么改变来实现这些 gem？”我的意思是，当观看教程时，这一切似乎是如此神奇和神秘。现在，我告诉自己要有耐心，继续工作和学习。也继续工作，我的红宝石和细节会慢慢来找我。

`application_helper.rb`中的

```
module ApplicationHelper
    # Setting up markdown
    def markdown(content)
        renderer = Redcarpet::Render::HTML.new(hard_wrap: true, filter_html: true)
        options= {autolink: true,no_intra_emphasis: true,fenced_code_block: true, 
        lax_html_block: true, strikethrough: true, superscript: true, underline: true, quote:true,footnotes:true}

        Redcarpet::Markdown.new(renderer, options).render(content).html_safe 
    end
end 
```

这里我定义了一个方法 markdown，它将内容作为参数。在我的例子中，这些内容就是博客文章的主体。然后按照教程我添加了一个使用 Redcarpet 的 renderer 变量。我确实通读了红地毯文档并添加了我想使用的功能。

随着红地毯的设置，降价准备就绪。然而，仍然有一些造型方面需要我的注意。为了处理这个问题，我使用了 [Pygments 宝石](https://rubygems.org/gems/pygments.rb)。我将 as pygments.scss 文件添加到我的样式表文件夹中，这里我使用了 [somebox/gh-like.css](https://gist.github.com/somebox/1082608) 。我把我想要实现的 css 复制粘贴到我的 pygments.scss.

```
/* 
   Some simple Github-like styles, with syntax highlighting CSS via Pygments.
*/
pre{
    background-color: #eee;
    padding: 10px;
    -webkit-border-radius: 5px;
    -moz-border-radius: 5px;
    border-radius: 5px;
    overflow: auto;
}
code{
    background-color: #eee;
    padding: 1px 3px;
    -webkit-border-radius: 2px;
    -moz-border-radius: 2px;
    border-radius: 2px; 
}

h1{
    color: #999;
    font-weight: bold;
}
h2{
    border-bottom: 1px dotted #aaa;
    margin-bottom: 1em;
    color: #333;
}
h3{
    color: #666;
}
.shadow{
    -webkit-box-shadow:0 5px 15px #000;
    -moz-box-shadow:0 5px 15px #000;
    box-shadow:0 5px 15px #000;     
}

.hll { background-color: #ffffcc }
.c { color: #888888 } /* Comment */
.err { color: #a61717; background-color: #e3d2d2 } /* Error */
.k { color: #008800; font-weight: bold } /* Keyword */
.cm { color: #888888 } /* Comment.Multiline */
.cp { color: #cc0000; font-weight: bold } /* Comment.Preproc */
.c1 { color: #888888 } /* Comment.Single */
.cs { color: #cc0000; font-weight: bold; background-color: #fff0f0 } /* Comment.Special */
.gd { color: #000000; background-color: #ffdddd } /* Generic.Deleted */
.ge { font-style: italic } /* Generic.Emph */
.gr { color: #aa0000 } /* Generic.Error */
.gh { color: #303030 } /* Generic.Heading */
.gi { color: #000000; background-color: #ddffdd } /* Generic.Inserted */
.go { color: #888888 } /* Generic.Output */
.gp { color: #555555 } /* Generic.Prompt */
.gs { font-weight: bold } /* Generic.Strong */
.gu { color: #606060 } /* Generic.Subheading */
.gt { color: #aa0000 } /* Generic.Traceback */
.kc { color: #008800; font-weight: bold } /* Keyword.Constant */
.kd { color: #008800; font-weight: bold } /* Keyword.Declaration */
.kn { color: #008800; font-weight: bold } /* Keyword.Namespace */
.kp { color: #008800 } /* Keyword.Pseudo */
.kr { color: #008800; font-weight: bold } /* Keyword.Reserved */
.kt { color: #888888; font-weight: bold } /* Keyword.Type */
.m { color: #0000DD; font-weight: bold } /* Literal.Number */
.s { color: #dd2200; background-color: #fff0f0 } /* Literal.String */
.na { color: #336699 } /* Name.Attribute */
.nb { color: #003388 } /* Name.Builtin */
.nc { color: #bb0066; font-weight: bold } /* Name.Class */
.no { color: #003366; font-weight: bold } /* Name.Constant */
.nd { color: #555555 } /* Name.Decorator */
.ne { color: #bb0066; font-weight: bold } /* Name.Exception */
.nf { color: #0066bb; font-weight: bold } /* Name.Function */
.nl { color: #336699; font-style: italic } /* Name.Label */
.nn { color: #bb0066; font-weight: bold } /* Name.Namespace */
.py { color: #336699; font-weight: bold } /* Name.Property */
.nt { color: #bb0066; font-weight: bold } /* Name.Tag */
.nv { color: #336699 } /* Name.Variable */
.ow { color: #008800 } /* Operator.Word */
.w { color: #bbbbbb } /* Text.Whitespace */
.mf { color: #0000DD; font-weight: bold } /* Literal.Number.Float */
.mh { color: #0000DD; font-weight: bold } /* Literal.Number.Hex */
.mi { color: #0000DD; font-weight: bold } /* Literal.Number.Integer */
.mo { color: #0000DD; font-weight: bold } /* Literal.Number.Oct */
.sb { color: #dd2200; background-color: #fff0f0 } /* Literal.String.Backtick */
.sc { color: #dd2200; background-color: #fff0f0 } /* Literal.String.Char */
.sd { color: #dd2200; background-color: #fff0f0 } /* Literal.String.Doc */
.s2 { color: #dd2200; background-color: #fff0f0 } /* Literal.String.Double */
.se { color: #0044dd; background-color: #fff0f0 } /* Literal.String.Escape */
.sh { color: #dd2200; background-color: #fff0f0 } /* Literal.String.Heredoc */
.si { color: #3333bb; background-color: #fff0f0 } /* Literal.String.Interpol */
.sx { color: #22bb22; background-color: #f0fff0 } /* Literal.String.Other */
.sr { color: #008800; background-color: #fff0ff } /* Literal.String.Regex */
.s1 { color: #dd2200; background-color: #fff0f0 } /* Literal.String.Single */
.ss { color: #aa6600; background-color: #fff0f0 } /* Literal.String.Symbol */
.bp { color: #003388 } /* Name.Builtin.Pseudo */
.vc { color: #336699 } /* Name.Variable.Class */
.vg { color: #dd7700 } /* Name.Variable.Global */
.vi { color: #3333bb } /* Name.Variable.Instance */
.il { color: #0000DD; font-weight: bold } /* Literal.Number.Integer.Long */ 
```

所有这些都准备好了，新的文章页面就可以开始了。

最后一步是修改我的帖子展示页面。在`show.html.erb`文件中，我调用了在`application_helper.rb`中定义的 markdown 函数。我传递了我的 post.body(或者在我的例子中是 post.blob 参数)，markdown 实现完成了。

```
 <div class="post_body">
                <p> <%= markdown(@post.blob) %>  </p>
            </div> 
```

所有这些，在教程的指导下，看起来都很容易完成。但是，脑子里有很多疑问。其中一个是我之前问过的。我仍然不能把事情是如何工作的联系起来，大多数时候我都是跟着有价值的教程走。

再一次，我看着眼前的事物，一句名言“努力工作，保持耐心。”