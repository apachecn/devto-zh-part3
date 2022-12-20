# 如何启动 Node.js 项目

> 原文：<https://dev.to/philnash/how-to-start-a-nodejs-project-4l3j>

有时我写博客来提醒自己我学到了什么，有时我写博客是因为别人分享了一些东西，我想更好地记住它们。本帖就是后者之一。

## 启动 Node.js 项目

通常当我开始一个新的 Node.js 项目时，我会使用`npm`来生成我的初始项目。

```
npm init 
```

`npm`然后问我一些问题，并为我建立一个`package.json`文件。然后我开始构建项目。

后来我不可避免地从 GitHub 有用的`.gitignore`模板报告中复制并粘贴了一个`.gitignore`文件。如果我记得的话，我会用我打算使用的开源许可创建一个`LICENSE`文件。

这样效率不高。

本周，我看到蒂尔尼·西伦在推特上写道:

> ![Tierney Cyren profile image](img/aa014b7002ae3c9132921571b49058c5.png)tier ney Cyren@ bitandbang![twitter logo](img/ad0c7b03deabfe1a161345efb2d537eb.png)如何启动任何新的 Node.js 项目:
> 
> $ npx LICENSE MIT>LICENSE
> $ npx git ignore node
> $ npx covgen YOUR _ EMAIL _ ADDRESS
> $ NPM init-y
> 
> 您已经准备好开始编码了。2019 年 1 月 07 日下午 17:42[![Twitter reply action](img/269095962147c28351274afdd5486a48.png)](https://twitter.com/intent/tweet?in_reply_to=1082331715471925250)[![Twitter retweet action](img/771160ecf06ae3d4d7a7815c29c819c2.png)](https://twitter.com/intent/retweet?tweet_id=1082331715471925250)191[![Twitter like action](img/c077611ab2a5e0b4cd0c826ee7ae1e48.png)](https://twitter.com/intent/like?tweet_id=1082331715471925250)670

这四个命令做了我手动做的所有事情，甚至更多，从一开始就成功地建立了一个项目。

*   `npx license mit`使用[许可包](https://www.npmjs.com/package/license)下载选择的许可，在本例中是[麻省理工学院许可](https://opensource.org/licenses/MIT)
*   `npx gitignore node`使用 [gitignore 包](https://www.npmjs.com/package/gitignore)从 [GitHub 的回购](https://github.com/github/gitignore)中自动下载相关的`.gitignore`文件
*   `npx covgen`使用 [covgen 包](https://www.npmjs.com/package/covgen)生成[贡献者契约](https://www.contributor-covenant.org/)并给你的项目一个行为准则，这将受到所有贡献者的欢迎

*如果你没有看到 [`npx`](https://www.npmjs.com/package/npx) 之前，它看本地是否有命令运行并执行它，如果没有本地命令，它会尝试下载，安装来自`npm`的命令，并运行它。这在生成新项目时非常有用，可以让你不用全局安装一堆只在这种设置模式下使用的`npm`包。*

*   `npm init -y`接受`npm init`询问的所有默认选项

Tierney 还建议定制您的`npm init`默认值，以便`npm init -y`的输出是正确的。

## 定制`npm init`

您可以通过在命令行输入`npm config list`来查看当前的`npm`配置。要查看影响`npm init`的配置，您可以`grep`为“初始化”:

```
npm config list | grep init 
```

您可以设置许多默认值；作者姓名、作者电子邮件、作者 url、许可证和版本。要设置它们，您可以在命令行中输入它们，或者使用`npm config edit`在您的文本编辑器中打开配置文件。命令行非常简单，您可以像这样设置所有五个缺省值:

```
npm set init.author.name "Your name"
npm set init.author.email "your@email.com"
npm set init.author.url "https://your-url.com"
npm set init.license "MIT"
npm set init.version "1.0.0" 
```

一旦你根据自己的喜好进行了定制，`npm init -y`将始终产生正确的设置。

## 构建自己的初始化脚本

我会对 Tierney 的命令做一些改进，尽管我知道他受到了 Twitter 的限制。这里有一个 bash 脚本，是我受他的 tweet 启发想出来的。

```
function node-project {
  git init
  npx license $(npm get init.license) -o "$(npm get init.author.name)" > LICENSE
  npx gitignore node
  npx covgen "$(npm get init.author.email)"
  npm init -y
  git add -A
  git commit -m "Initial commit"
} 
```

在最初的版本中，我添加了从默认的`npm init`中获取许可证类型、作者姓名和电子邮件。我还初始化了一个新的 git 存储库，并将这个脚本的结果提交为“初始提交”。

您可以将此功能添加到您的`~/.bash_profile`中。然后，或者`source ~/.bash_profile`或者打开一个新的命令行窗口并运行`node-project`。随意添加或删除你认为合适的其他位来创建你的完美的初始化脚本。

## 开始一个项目

现在您有了启动 Node.js 项目的完美脚本，为什么不创建一个新的呢？我心中有几个小项目，我计划今年建立，这是一个很好的基础开始。

如果你有任何改进剧本的建议，请在 Twitter @ philnash 上告诉我，或者在下面的评论中告诉我。开源快乐！