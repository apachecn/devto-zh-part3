# 用 Git 在 NearlyFreeSpeech.NET 部署 Hugo

> 原文：<https://dev.to/euantorano/deploying-hugo-on-nearlyfreespeech-net-with-git-5elp>

我和[NearlyFreeSpeech.NET](https://www.nearlyfreespeech.net)一起管理这个网站已经有两年多了，使用 Hugo 生成这个网站的时间也稍微长一点。我觉得把我的设置放在某个地方可能是个好主意，这样我就可以在以后引用它，既然我这么做了，我也可以把细节作为帖子公开。

## 在 NearlyFreeSpeech 上安装 Hugo

第一步是在 NearlyFreeSpeech 上安装 Hugo 二进制文件。我个人使用了一个简单的 bash 脚本来完成这项工作，它允许我在需要的时候删除所使用的版本，并简单地重新运行它来升级:

```
#!/usr/bin/env sh

set -ex
VERSION=0.74.3
FILENAME="hugo_${VERSION}_FreeBSD-64bit.tar.gz"
BIN_DIR=$HOME/bin
mkdir -p "${BIN_DIR}"
fetch -q -o hugo.tar.gz "https://github.com/gohugoio/hugo/releases/download/v${VERSION}/${FILENAME}"
tar -C "${BIN_DIR}" -xvzf hugo.tar.gz hugo
rm hugo.tar.gz 
```

Enter fullscreen mode Exit fullscreen mode

这将从 GitHub 版本下载 Hugo 的 FreeBSD x64 版本`0.74.3`,从归档中抓取`hugo`二进制文件并将其放入`$HOME/bin/hugo`中，然后清理下载。

就我个人而言，我不会把 Hugo 添加到我的`$PATH`中，因为我唯一一次真正与它交互是通过 Git 挂钩——下面会有更多介绍…

## 在 NearlyFreeSpeech 上建立 Git 存储库

我使用 Git 将站点部署到服务器上——我简单地将存储库作为 Git remote ( `nfsn`)添加到我的近 FreeSpeech 服务器上，每当我想向站点推送更新时，就像使用`git push nfsn master`一样简单。

### 在服务器上创建资源库

第一步是在服务器上创建一个空的 Git 存储库。我将这个存储库命名为`euantorano.co.uk.git`，但是很明显，如果你的站点不叫这个名字的话，这个名字应该会改变

```
cd $HOME
mkdir euantorano.co.uk.git
cd euantorano.co.uk.git
git init --bare 
```

Enter fullscreen mode Exit fullscreen mode

### 创建一个签出目录，供提交挂钩使用

接下来，我们需要在服务器上创建一个目录，其中包含 Hugo 将要处理的库的签出:

```
cd $HOME
mkdir euantorano.co.uk.checkout
cd euantorano.co.uk.checkout
git clone $HOME/euantorano.co.uk.git 
```

Enter fullscreen mode Exit fullscreen mode

### 创建 Git `post-receive`钩子

服务器上的最后一步是向服务器上的 Git 存储库添加一个`post-receive`钩子。这个钩子将在一个`git push`发生后运行，并将 Git 库签出到上面的签出目录，然后运行 Hugo 来发布更新后的站点。

首先，创建钩子文件并使其可执行:

```
cd $HOME/euantorano.co.uk.git
touch hooks/post-receive
chmod ug+x hooks/post-receive 
```

Enter fullscreen mode Exit fullscreen mode

接下来是时候实际编写钩子脚本了:

```
#!/bin/bash

set -ex
SITE_CHECKOUT=$HOME/euantorano.co.uk.checkout
GIT_DIR=$SITE_CHECKOUT/.git
PUBLIC_WWW=/home/public
cd $SITE_CHECKOUT
git --git-dir=$GIT_DIR pull -f
$HOME/bin/hugo --cleanDestinationDir --minify -d $PUBLIC_WWW
exit 
```

Enter fullscreen mode Exit fullscreen mode

这个脚本首先导航到站点签出目录(`$HOME/euantorano.co.uk.checkout`)，然后提取站点源代码的最新副本。最后，它使用 Hugo 构建站点，将生成的内容输出到公共网站目录。我个人也启用 Hugo 内置的 minifier 从生成的 HTML 中去除无关紧要的空白。

### 将新遥控器添加到您的本地存储库中

为了能够推送到服务器，您需要在包含您的站点的本地存储库中添加一个新的 Git remote:

```
git remote add nfsn user@server:/home/private/euantorano.co.uk.git 
```

Enter fullscreen mode Exit fullscreen mode

显然，`user`和`server`取决于你的用户名和服务器名——这两个都可以在`SSH/SFTP Information`的`sites`标签下的 NearlyFreeSpeech 会员界面中找到。

## 部署更新

现在 Hugo 已经安装好了，Git 钩子也配置好了，更新站点就像下面这样简单:

```
git commit -m "..."
git push nfsn master 
```

Enter fullscreen mode Exit fullscreen mode

Git 推送照常进行，然后运行`post-receive`钩子，该钩子回显它所做的一切(感谢我们包含的`set -x`标志):

```
Enumerating objects: 16, done.
Counting objects: 100% (16/16), done.
Delta compression using up to 8 threads
Compressing objects: 100% (10/10), done.
Writing objects: 100% (10/10), 3.77 KiB | 3.77 MiB/s, done.
Total 10 (delta 5), reused 0 (delta 0)
remote: + SITE_CHECKOUT=/home/private/euantorano.co.uk.checkout
remote: + GIT_DIR=/home/private/euantorano.co.uk.checkout/.git
remote: + PUBLIC_WWW=/home/public
remote: + cd /home/private/euantorano.co.uk.checkout
remote: + git --git-dir=/home/private/euantorano.co.uk.checkout/.git pull -f
remote: From /home/private/euantorano.co.uk
remote: ef9d151..7afb7dd master -> origin/master
remote: Updating ef9d151..7afb7dd
remote: + /home/private/bin/hugo --cleanDestinationDir --minify -d /home/public
remote: Building sites …
remote: | EN
remote: +------------------+----+
remote: Pages | 37
remote: Paginator pages | 0
remote: Non-page files | 0
remote: Static files | 6
remote: Processed images | 0
remote: Aliases | 11
remote: Sitemaps | 1
remote: Cleaned | 0
remote:
remote: Total in 681 ms
remote: + exit 
```

Enter fullscreen mode Exit fullscreen mode

神奇！