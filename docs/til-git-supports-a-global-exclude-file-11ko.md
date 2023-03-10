# TIL — Git 支持全局排除文件

> 原文：<https://dev.to/chriis/til-git-supports-a-global-exclude-file-11ko>

### TIL — Git 支持全局排除文件

#### 一个可能对你大有帮助的小建议

我真的不知道为什么直到今天我才认为这是一件事，但我有一些关于 elixir_ls(一个用于 elixir 开发的 VSCode 插件)的问题困扰着我的 git 库和我的项目。gitignore file 似乎忽略了生成的文件。

似乎也有一些 Github 问题在谈论同样的事情，所以我不是一个人在做蠢事。

[![](img/86418f6de07e8550e4e3a6b0d33f559b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zDQk4qN---/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2Aox_aj7StrVzI74o__XeiCA.png)

### 解

您的 git 安装可以有一个全局的。gitignore 文件！我已经编程 5 年了，但毫无头绪——我真的不知道为什么我过去没有考虑过。

#### **添加一个全局**** 。gitignore 文件:**

git config-global core . excludes file ~/。gitignore

#### 编辑一个全局。gitignore 文件:

sudo vim ~/。gitignore(或您选择的编辑器)

以下是我的内容——我冒昧地添加了一些我也想忽略的其他常见文件:

```
.DS\_Store
node\_modules/
.elixir\_ls/ 
```

Enter fullscreen mode Exit fullscreen mode

### 可选额外

你们中的一些人可能会这样做然后发现。gitignore 没有生效——下面的命令将修复这个问题:

```
git add [uncommitted changes you want to keep] && git commit
git rm -r --cached .
git add .
git commit -m "fixed untracked files" 
```

Enter fullscreen mode Exit fullscreen mode

希望这有所帮助！它解决了我的 VScode 问题。

[![](img/fbed11cf1b7368b183fc27b393ac4c5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ue95WxWs--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AVNNeZVL_anR3JxQYZeDfoQ.jpeg)

喜欢我的漫步吗？在 [*twitter*](https://twitter.com/_ChrisGreg) *上关注我，在我的* [*个人网站*](http://chrisgregori.co.uk/) *上了解更多我的开发故事或关注我的附带项目。💻*