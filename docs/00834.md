# 用钩子和 Husky 自动化 Git

> 原文：<https://dev.to/satansdeer/automate-git-with-hooks-and-husky-4amo>

Git 提供了一个接口来运行一些代码，作为对特定触发器的反应。那些触发器被称为 git hooks(git 使用的钩子)。您可以在每次提交、推送、拉取或其他操作时运行代码。

> 钩子是可以放在钩子目录中的程序，可以在 git 执行的某些时候触发动作。

你可以在 [git 文档页面](https://git-scm.com/docs/githooks)查看整个列表。

管理 githooks 并让它们对整个团队可用可能会很麻烦。

幸运的是有一个叫做 [Husky](https://github.com/typicode/husky) 的工具，它提供了一个很好的界面来管理 githooks。

它是一个 **npm** 包，所以通常用于 Javascript 项目。

## 安装哈士奇

要开始使用 [Husky](https://github.com/typicode/husky) 你需要先把它安装成 def 依赖:

```
npm install husky --save-dev 
```

Enter fullscreen mode Exit fullscreen mode

然后需要在`package.json` :
中添加 husky 配置段

```
//  package.json  {  "husky":  {  "hooks":  {  "pre-push":  "npm test",  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

在此配置中，指定我们希望在每个`push`之前运行`npm test`以进行远程回购。

或者，您可以在`.huskyrc`、`.huskyrc.json`或`.huskyrc.js`文件
中指定您的设置

```
//  .huskyrc  {  "hooks":  {  "pre-commit":  "npm test"  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 支持挂钩

Husky 支持除服务器端挂钩(`pre-receive`、`update`和`post-receive`)之外的所有 Git 挂钩。

`applypatch-msg` -该钩子由`git-am`调用。它接受一个参数，即保存提交日志消息的文件的名称。以非零状态退出会导致 git am 在应用补丁之前中止。

`pre-applypatch` -该钩子由`git-am`调用。它不带任何参数，在应用补丁之后、提交之前调用。

`post-applypatch` -该钩子由`git-am`调用。它不带参数，在应用补丁并提交之后调用。

`pre-commit` -该钩子由`git-commit`调用，可以通过`--no-verify`选项绕过。它不带参数，在获取建议的提交日志消息和提交之前被调用。

`prepare-commit-msg` -这个钩子由`git-commit`在准备默认日志消息之后，编辑器启动之前调用。

`commit-msg` -该钩子由`git-commit`和`git-merge`调用，可以通过`--no-verify`选项绕过。它接受一个参数，即保存提交日志消息的文件的名称。

`post-commit` -该钩子由`git-commit`调用。它不带参数，在提交后调用。

`pre-rebase` -这个钩子由`git-rebase`调用，可以用来防止一个分支变得不稳定。

`post-checkout` -当更新工作树后运行`git-checkout`时，调用该钩子。

这个钩子由 git-merge 调用，当 git pull 在本地存储库上完成时就会发生。

`pre-push` -该挂钩由`git-push`调用，可用于防止推压发生。

`pre-auto-gc`该钩子由`git gc --auto`调用。

`git-gc`在当前存储库中运行许多内务处理任务，例如压缩文件修订版(以减少磁盘空间并提高性能)，删除可能在之前调用 git add 时创建的不可达对象，打包引用，修剪引用日志，重新引用元数据或过时的工作树。

`post-rewrite` -这个钩子由重写提交的命令调用(当用`--amend`和`git-rebase`调用时，调用`git-commit`)。

还有其他不太流行的钩子，你可以在 [git 文档](https://git-scm.com/docs/githooks)中查看它们

## 访问 Git 参数和标准输入

Git 挂钩可以通过命令行参数和 stdin 访问参数。

Husky 通过`HUSKY_GIT_PARAMS`和`HUSKY_GIT_STDIN`环境变量来访问它们。

```
{  "husky":  {  "hooks":  {  "commit-msg":  "echo $HUSKY_GIT_PARAMS"  }  }  } 
```

Enter fullscreen mode Exit fullscreen mode

## 在 Monorepos 中使用

如果你有一个多包存储库，建议使用像[lerna]这样的工具，并让 husky 在根`package.json`中只安装了**的**来作为事实的来源。

## 运行多个命令

根据设计，husky 将把钩子脚本作为一个命令来运行。就像运行`package.json`中定义的脚本一样。

```
{  "pre-commit":  "cmd && cmd && cmd"  } 
```

Enter fullscreen mode Exit fullscreen mode