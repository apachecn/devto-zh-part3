# 如何使用新的 github.dev 个人网站生成器

> 原文：<https://dev.to/orbit/how-to-use-the-new-githubdev-personal-website-generator-1fde>

github.dev 是 github 的一个新社区项目，位于 T2。戴夫·TLD。有了它，你可以派生、定制和部署一个显示你的 GitHub 简历和贡献的个人网站，由 [GitHub API](https://developer.github.com/v3/) 、 [GitHub 页面](https://pages.github.com/)和 [Jekyll](https://jekyllrb.com/) 提供支持。

> 下面是我的页面: [dzello.github.io](https://dzello.github.io/) 。

就我个人而言，我喜欢这个项目，因为它为开发人员提供了一种展示他们的编码天赋和兴趣的替代方式，而不仅仅是他们的 GitHub 个人资料页面，社区中的一些开发人员已经[提出了关于](https://twitter.com/emmawedekind/status/1099235211555074048)的好问题。

如果您已经知道如何派生 repo 并使用命令行，我可以向您展示如何在大约 10 分钟内启动并运行您自己的 github.dev 实例。

[![Ryan Gosling saying ok](img/d34972be0a817f17c65360326fe05a47.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Pb2vr8Ac--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dzello/image/upload/v1551102547/developermode/ryan-gosling-okay.webp)

## 开始使用

将浏览器窗口指向[github/个人网站](https://github.com/github/personal-website)库。

1.  单击 Fork 按钮，在您的帐户中创建存储库的副本
2.  转到 Settings 选项卡，将存储库重命名为`{username}.github.io`，用`{username}`替换您的 GitHub 用户名

名为`{username}.github.io`的库在 GitHub 上做了一些真正独特的事情。它们的内容(特别是 _site 文件夹)将自动部署到 GitHub Pages URL，并成为可浏览的网站，地址:

```
https://{username}.github.io/ 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，这并不在新的 github.dev 域中，但是我希望这个项目的名字暗示了这方面的计划🤓。

然而，在站点出现之前，您需要在重命名之后将至少一个提交推送到 repo。我们将在下面的步骤中做到这一点。

## 本地运行

作为先决条件，[如果你还没有安装 Ruby](https://jekyllrb.com/docs/installation/) 。

首先，克隆您的新存储库。

```
git clone git@github.com:{username}/{username}.github.io.git 
```

Enter fullscreen mode Exit fullscreen mode

接下来，`cd`进入存储库并安装 Jekyll 和依赖项。

```
cd {username}.github.io
gem install jekyll bundler
bundle install 
```

Enter fullscreen mode Exit fullscreen mode

现在，您已经准备好启动网站了。

```
bundle exec jekyll serve 
```

Enter fullscreen mode Exit fullscreen mode

浏览到 [http://localhost:4000](http://localhost:4000) ，你应该会看到一个页面，上面有你的名字、个人照片和公共存储库。多亏了 [github-metadata Jekyll 插件](https://github.com/jekyll/github-metadata#authentication)和 [GitHub pages gem](https://github.com/github/pages-gem) ，这些数据已经存在。

## 定制

不需要进一步定制(请随意跳到 [#deployment](#deployment) ),但是我建议至少改变你的兴趣，这样你的页面才能准确反映你是谁。

### 你的兴趣

默认情况下，github.dev 假设您对 CSS、Web 设计和 Sass 感兴趣。但是如果你更喜欢 PostCSS 或者实际上不是开发者呢？别担心，很容易改变。

[![Red easy button](img/301235b36b07f733bf5bd1222895d7fa.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--M31bORZ1--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/dzello/image/upload/v1550884831/developermode/easy-button.gif)

用您最喜欢的文本编辑器打开`_config.yml`，找到`topics`部分。对 YAML 进行更改，以添加、更新和移除主题。以下是你如何添加[的精彩话题](https://github.com/topics/awesome)例如:

```
topics:
  - name: awesome
    web_url: https://github.com/topics/awesome
    image_url: https://raw.githubusercontent.com/github/explore/c304601f028774885ef27f72e6fe2d331729d8bc/topics/awesome/awesome.png 
```

Enter fullscreen mode Exit fullscreen mode

访问 [GitHub 主题](https://github.com/topics)页面，查看您还可以添加哪些兴趣。

**在您对`_config.yml`进行更改后，您需要停止 Jekyll 并在更改出现前重启它。**然而，对其他文件的更改应该只需要刷新页面。

### 先显示热门回购(可选)

默认情况下，存储库按字母顺序排序。相反，如果你想显示你最受欢迎的回复，你可以修改`_includes/projects.html`文件。

```
<div class="d-sm-flex flex-wrap gutter-condensed mb-4">
  {% assign sorted_repositories = site.github.public_repositories | sort: 'watchers_count' | reverse %}
  {% for repository in sorted_repositories limit:6 %}
    <div class="col-sm-6 col-md-12 col-lg-6 col-xl-4 mb-3">
      {% include repo-card.html %}
    </div>
  {% endfor %}
</div> 
```

Enter fullscreen mode Exit fullscreen mode

重要的部分是`sort: 'watchers_count'`和`reverse`。你也可以显示或排序其他字段，参见 [github-metadata jekyll 插件](https://github.com/jekyll/github-metadata)的文档。你也可以限制显示的库的总数，这是我在上面用`limit: 6`做的。

### 🔦黑暗模式(可选)

在`_config.yml`中，可以设置`style: dark`。这将使你的访问者在晚上阅读你的网站时，脸上不那么发光。

自述文件的[定制部分](https://github.com/github/personal-website#customization)包含了更多让你的网站真正成为你自己的网站的方法。你甚至可以添加一个博客。

[![Mine mine mine GIF](img/fbbe2369533c5ad6ec5bf1a2a320913f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--the6mGbZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://res.cloudinary.com/dzello/image/upload/v1550887348/developermode/mine-mine-mine.gif)

## 部署

每次你推新的提交到 GitHub，你的站点将被构建并部署到`https://{username.github.io}/`。首次部署站点之前，至少需要一个`git push`。

假设您在`_config.yml`文件中对自己的兴趣做了一些更改，下面是您应该做的事情。

添加并提交您的更改:

```
git add _config.yml
git commit -m 'Updated my interests' 
```

Enter fullscreen mode Exit fullscreen mode

然后把它们推上 GitHub:

```
git push 
```

Enter fullscreen mode Exit fullscreen mode

等待几分钟，您的站点应该在`https://{username}.github.io/`开始运行。🎉

您可以在 GitHub repo 的环境选项卡中确认这一点:

[![Text that says deployed to github-pages](img/bda2e78a49806e890716ed5e468b99dc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4dNChzbL--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://res.cloudinary.com/dzello/image/upload/v1551102192/developermode/github-pages-environment-tab.png)

故障排除:如果由于某种原因，几分钟后网站无法加载，请尝试 URL `https://{username}.github.io/index.html`来代替。如果可以的话，可能有缓存问题，你只需要等一会儿就可以在没有`index.html`的情况下访问这个站点了。

## 恭喜恭喜！

你对你的新网站感到兴奋吗？✨在下面留言，让我们一起来看看吧！请告诉我们您所做的、其他人可能也想尝试的定制。

教程到此为止。如果你想看到更多的网站和社区项目在新的。开发领域，检查出[真棒点开发](https://github.com/developermode/awesome-dot-dev) ⭐.