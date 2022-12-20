# 在 heroku 上运行 R 脚本

> 原文：<https://dev.to/rmhogervorst/running-an-r-script-on-heroku-6l7>

在这篇文章中，我将向你展示如何每天在 heroku 上运行一个 R 脚本。这是我之前在推特上发布的维基数据死亡事件的延续。

# 为什么我要在 heroku 上运行脚本？

这非常简单，你不需要在 AWS、Google、Azure 或 Nerdalize 的云中启动一台机器。你只需运行脚本，它就能工作。你甚至可以让它每天运行。Heroku 不支持 R 开箱即用。所以你必须告诉 heroku 安装 R(他们称之为 buildpack)。

# 需要什么？

我综合了戴夫·夸泰的这篇中型文章和戴夫在那篇文章中提到的构建包的[描述。](https://github.com/virtualstaticvoid/heroku-buildpack-r/tree/heroku-16)

为此，您需要两个脚本:

1.  初始化。安装软件包和设置机器的脚本
2.  您想要运行的脚本

Heroku 的工作方式有点像 github。您下载并安装 heroku 命令行界面(CLI ),然后您可以告诉 heroku 做什么，以及哪些脚本需要移动到 heroku。

# 设置您的项目

我假设你从一个新文件夹开始，里面只有你的脚本。

1.  创建一个 heroku 帐户。
2.  下载并安装[heroku CLI](https://devcenter.heroku.com/articles/heroku-cli)

“heroku 登录”时，你会往返于网站，以验证这真的是你吗

拿起你的脚本，看看它需要什么包，把这些包写下来。

创建一个名为 init 的脚本。r 和 modifyit 安装软件包:

```
my_packages <- c("glue","rtweet","WikidataQueryServiceR")
 install_if_missing <- function(p) {
 if(p %in% rownames(installed.packages())==FALSE){
 install.packages(p)}
 }

invisible(sapply(my_packages, install_if_missing)) 
```

Enter fullscreen mode Exit fullscreen mode

在这个文件夹中，你现在有 2 个脚本:`init.R`和你的原始脚本。

1.  建立 heroku 项目

*   `heroku create`

这将创建一个随机名称的应用程序，如果你想控制名称类型`heroku create NAME`

1.  将 heroku 堆栈设置为“heroku-16”(因为这是构建包的用途，我也不知道这些堆栈是什么…)

*   `heroku stack:set 'heroku-16`

1.  在 heroku 项目中安装 R 环境(buildpack)

*   `heroku buildpacks:set https://github.com/virtualstaticvoid/heroku-buildpack-r.git#heroku-16`

1.  将这两个脚本添加到 git 并推送到 heroku

```
git init # if you haven't already
git add init.R YOUROTHERSCRIPT
git commit 
git push heroku master 
```

Enter fullscreen mode Exit fullscreen mode

1.  而且现在一切正常(大概)！

[![](img/6f00146d6e2a6a29dfe581bbeb107d9a.png)](https://i.giphy.com/media/COvkHUsABRWwM/giphy.gif)

1.  让它每天运行(*)

要让这个 heroku 应用程序每天运行，你需要一个调度程序。前往 [heroku 网站](https://devcenter.heroku.com/articles/scheduler#installing-the-add-on)并将其安装在您的应用程序中，或者使用命令行。

`heroku addons:create scheduler:standard`

在你这么做之前，你必须给 heroku 添加一张信用卡，如果你经常使用 heroku 的话，那就要花钱了。

1.  配置调度程序(*):

它显示类似 run 的内容，或者它后面有一个“$”符号和一个空格。这是我用的(我的脚本叫 runtask。R): `Rscript app/runtask.R`。

我花了一段时间才找到脚本在应用程序中的位置，但显然它在应用程序目录中。

这就是它的作用:

[![screenshot of past few tweets](img/6a51286d5161bb76ab7ceb1bca2fabce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--dhcYFsbr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.rmhogervorst.nl/post/2018-12-06-running-an-r-script-on-heroku_files/Screenshot_2018-12-06%2520wikidata_people_bot%2520%28%2520WikidataB%29%2520Twitter.png)

过去几条推文的截图