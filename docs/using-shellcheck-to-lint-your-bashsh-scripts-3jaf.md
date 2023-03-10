# 使用“shellcheck”来 lint 你的 bash/sh 脚本。

> 原文：<https://dev.to/david_j_eddy/using-shellcheck-to-lint-your-bashsh-scripts-3jaf>

## 开始

由于涉及到技术，特别是 web，我们不必花太多时间来编写 bash(或 shell)脚本。如果您使用服务器端技术，这个事件会在登录到服务器后很快发生。作为一名开发(Sec)运营从业人员，在 a CI/CD 流程中使用林挺代码对我个人来说是一项基本要求。直到最近，林挺 Bash/sh 代码对我来说还是一个痛苦的过程。然而，就像其他许多事情一样，找到一个自动化流程的解决方案只是时间问题。输入' [shellcheck](https://github.com/koalaman/shellcheck) '。

## 装置

对于这个快速演示，我运行的是 Ubuntu 18.04。安装过程非常简单，适合这种工作。

```
sudo apt-get install -y shellcheck

```

<figure>[![](img/4bc9326fb9741051fa0841262b4cc099.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--du1mTWFO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/11/screely-shellcheck_1.png) 

<figcaption>那太容易了。</figcaption>

</figure>

一旦执行，我们应该看到标准的安装输出。为了确保一切都按预期完成，我执行了一个快速版本检查。

```
shellcheck --version
```

<figure>[![](img/c6a5754981b3b805f3eea436df74bdc3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--L1NjqYDG--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/11/screely-shellcheck_3.png) 

<figcaption>果然奏效。</figcaption>

</figure>

并且希望 [shellcheck](https://github.com/koalaman/shellcheck) 确实安装了。

## 选择

进入[外壳检查](https://github.com/koalaman/shellcheck)的选项和安装一样简单。快速的'- help '参数为我们提供了执行选项的列表。

```
shellcheck --help
```

<figure>[![](img/ca6aebea35efe0fde4c1a2ea3ab0f032.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_iKUdkTO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/11/screely-shellcheck_2-1.png) 

<figcaption>整洁、颜色和格式！</figcaption>

</figure>

## 使用

到目前为止一切顺利。现在让我们看看它是如何执行的。我的主目录中有一个 BASH 文件，它提供了一个文件的 SVN 历史，所以我把它用作演示。

```
shellcheck ./svn_file_history.sh
```

<figure>[![](img/9c800a0af4d52fba64d19a6f5df9b2ef.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--KEOczIpO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.davidjeddy.com/wp-content/uploads/2018/11/screely-shellcheck_4.png) 

<figcaption>哎呀，我最不擅长写脚本了。</figcaption>

</figure>

嘿，这真不错。假设它是标准输出，那么将这些消息传送到报告系统或质量检查流程将会很容易。很好。

## 关闭

shellcheck 是使生活变得更容易的工具之一，只要你在试图编写自己的语法检查器怪物之前了解它。易于安装、易于使用、易于集成成为开发过程中的另一个质量和安全保障步骤。

在评论中分享你最喜欢的棉绒，这样我们都可以学习。

## 额外资源

*   [https://en . Wikipedia . org/wiki/Lint _(软件)](https://en.wikipedia.org/wiki/Lint_(software))
*   [https://nickjanetakis . com/blog/here-is-why-you-should-quote-your-variables-in-bash](https://nickjanetakis.com/blog/here-is-why-you-should-quote-your-variables-in-bash)
*   [https://github.com/koalaman/shellcheck](https://github.com/koalaman/shellcheck)
*   标题图片来自[https://www.youtube.com/watch?v=lbMsFXMnqNY](https://www.youtube.com/watch?v=lbMsFXMnqNY)