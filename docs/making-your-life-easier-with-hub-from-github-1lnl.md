# GitHub 的 Hub 让您的生活更轻松

> 原文：<https://dev.to/konstantin/making-your-life-easier-with-hub-from-github-1lnl>

我发现使用 GitHub 最烦人的部分是必须在终端和 web 界面之间来回切换，要么复制一个 repo 名称，要么查看 pull 请求/问题，要么分叉东西。如果我可以写一些类似于`git repos`的东西并得到列表，这样我就可以马上克隆它，不是更容易吗？

## 输入枢纽

如果你使用 github 已经有一段时间了，你可能知道或者使用过 GitHub 的 [hub](https://github.com/github/hub) 项目。嗯，[最近](https://github.com/github/hub/releases/tag/v2.8.3)他们增加了一些可能会改变你使用 GitHub 的方式的东西: [hub api 命令](https://hub.github.com/hub-api.1.html)允许你使用任何 GitHub API 并进行 [graphql 查询](https://developer.github.com/v4/)。

## 我们来做一些别名

你可以在我之前的文章中找到更多关于 git 别名的信息:

[![konstantin](img/6114b7e6cea0000252b52dea506207c2.png)](/konstantin) [## 化名 git way

### 康斯坦丁 Apr 1 ' 19 分钟读数

#git #alias #gitconfig #shell](/konstantin/aliases-git-way-1kjp)

一般来说，在安装了`hub`之后，您将把它命名为 git，这样它在添加自己的函数的同时包装了所有的基本函数(在您的 shell 中运行`hub alias`进行设置)。现在，让我们来看看上面的问题。

### Git 休息

首先，我们想获得用户的 github 库列表。我找到了一个优雅的解决方案，在一个[中心问题](https://github.com/github/hub/issues/1419#issuecomment-473861104)中列出一个随机的用户回复。我们所需要的是添加一个函数，告诉我们一个登录的用户。为此，我们需要将以下别名添加到`~/.gitconfig` :

```
[alias]
  user = "!f() { hub api --flat user | grep .login | awk '/.login/ {print $2}'; }; f" 
```

Enter fullscreen mode Exit fullscreen mode

现在我们需要将找到的脚本分解成几个别名:

```
[alias]
  # this function allows you to go through hub low level api with or without multiple pages
  paginate = "!paginate() { local output cursor; output=\"$(\"$@\")\"; cursor=\"$(awk '/\\.hasNextPage/ { has_next=$2 } /\\.endCursor/ { if (has_next==\"true\") print $2 }' <<<\"$output\")\"; printf \"%s\\n\"  \"$output\"; [ -z \"$cursor\" ] || paginate \"$@\" -f after=\"$cursor\"; }; paginate "

  repos = "!repos() { local user=\"${1?}\";shift 1; git paginate hub api -t graphql -f user=\"$user\"  \"$@\"  \
 -f query='query($user: String!, $per_page: Int = 100, $after: String) { user(login: $user) { \
 repositories(first: $per_page, after: $after) { nodes { nameWithOwner }, pageInfo { hasNextPage, endCursor } } } }';}; \
 repos \"$(git user)\" | awk '/\\.nameWithOwner\\t/ { print $2 }';" 
```

Enter fullscreen mode Exit fullscreen mode

因此，你只需输入`git repos`，它就会列出你所有的 GitHub 回购:

```
gko/dotfiles
gko/vimio
gko/concat
... 
```

Enter fullscreen mode Exit fullscreen mode

### Git comment

Hub 允许您使用 [hub issue](https://hub.github.com/hub-issue.1.html) 命令从终端查看、创建或关闭任何回购发行。但是没有从终端发布评论的默认方式。但是使用`hub api`我们可以做到这一点:

```
[alias]
  comment = "!f() { hub api \"repos/{owner}/{repo}/issues/$1/comments\" --raw-field \"body=$2\"; }; f" 
```

Enter fullscreen mode Exit fullscreen mode

现在我们只需输入`git comment <ISSUE NUMBER> "Some comment"`，它就会为我们发布。

### 中枢浏览

另一个惊人的枢纽功能是 [`hub browse`](https://hub.github.com/hub-browse.1.html) ，它允许您在浏览器中查看项目的主页，或任何子页(即/问题)，如下所示:

```
[alias]
  issues = browse -- issues
  wiki = browse -- wiki 
```

Enter fullscreen mode Exit fullscreen mode

## 更多的东西

你可以从我的[点文件回购](https://github.com/gko/dotfiles)的 [gitconfig 文件](https://github.com/gko/dotfiles/blob/master/.gitconfig)中看到上面的例子和其他一些很酷的东西。