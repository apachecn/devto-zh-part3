# 发行说明:更新 1

> 原文：<https://dev.to/garfbradaz/release-notes-update-1-5co5>

## 发布说明-更新 1

每当我更新这个[博客](https://garfbradaz.github.io)时，我会提供一些关于变化的发布说明，包括可能的话，我做了什么，因为这可能对其他人有帮助。

### 版本注释

我本来打算为网站使用一个合适的语义版本(SemVer)，但是决定只做增量版本(比如 Chrome)。只是，嗯因为！。除了安逸没有别的原因。

### 博文评论

我现在已经开通了评论服务 [Disqus](https://disqus.com/) ，你可以免费注册。因为 Jekyll 没有后端数据存储，这对我来说最有意义(& Disqus 有很好的支持)。

我是怎么做到的？嗯，这是我的网站，所以你可能需要根据你的网站结构将它添加到不同的地方，**但**我遵循了 Github 页面的标准 *Jekyll* 网站创建，结构就是我所说的**香草**。

*   注册 Disqus。

*   记下你为你的站点创建的*简称*。

*   编辑您的`_config.yml`并添加以下内容:

```
disqus:
    shortname: <replace-with-your-short-name> 
```

*   使用下面的代码片段创建一个`comments.html`和，并将其保存到您的`_includes`目录中。这是 Disqus 提供的嵌入式代码:

```
<div class="comments">
        <div id="disqus_thread"></div>
        <script type="text/javascript">
            var disqus_shortname = 'garfbradaz';
            (function() {
            var dsq = document.createElement('script'); dsq.type = 'text/javascript'; dsq.async = true;
            dsq.src = '//' + disqus_shortname + '.disqus.com/embed.js';
            (document.getElementsByTagName('head')[0] || document.getElementsByTagName('body')[0]).appendChild(dsq); })();    
        </script>
    <noscript>Please enable JavaScript to view the <a href="http://disqus.com/?ref_noscript">comments powered by Disqus.</a></noscript>
    </div> 
```

*   将对`comments.html`的引用添加到您的`posts.html`中。将以下内容添加到`posts.html`(或者你的 HTMl 文件的名称)的底部:

```
{% include comments.html %} 
```

*   您需要在每个帖子文件中使用以下 Front Matter 值对发表评论**:**

```
comments: true 
```

*   重建您的帖子:

```
bundle exec jekyll build 
```

*   瞧，你会在页面底部看到一条漂亮的评论:

[![disqus](img/e352765885bf2260a397b3afa381831d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--0wDJJh9k--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://garfbradaz.github.io/assets/img/posts/disqus.png)