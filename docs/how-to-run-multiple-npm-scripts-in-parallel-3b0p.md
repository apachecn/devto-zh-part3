# 如何并行运行多个 npm 脚本

> 原文：<https://dev.to/therealdanvega/how-to-run-multiple-npm-scripts-in-parallel-3b0p>

我正在为我们的学生做练习，在这个过程中，我学到了一些新东西。当我们的学生完成练习后，他们会被要求进行一些测试来验证他们的解决方案是否有效。我们也用这些测试来给某些练习打分。

问题是我需要使用 [Cypress](http://www.cypress.io) 运行一些端到端的测试，但在此之前，我需要项目运行。在我们的一些普通 JavaScript 示例中，您可以使用 [Live 服务器扩展](https://marketplace.visualstudio.com/items?itemName=ritwickdey.LiveServer)直接从 Visual Studio 代码中运行它们。

这适用于开发，但何时进行测试呢？我想练习的说明可能会说“在运行测试之前，确保你的项目正在运行”，但是这也带来了一些问题。

首先，如果我们这样做，我不确定我们是否可以总是假设项目将在[http://localhost:5500/my-project](http://localhost:5500/my-project)运行。这可能是大多数人的情况，但我从来不喜欢只是假设。我可能会把它放在某个配置中，但我还是觉得不太对劲。

第二，我们想给学生的练习打分，会发生什么？这意味着我们的教师必须开始每个项目，运行测试并记录他们的分数。这是一个非常繁琐的工作流程，也是我们想要避免的。我们也希望这是自动化的，以防我们通过一些持续的集成构建来运行一切。

## 创建新项目

如果你想继续下去，你可以创建自己的项目，但这不是必须的。本文假设您有一些构建现代 web 应用程序的经验。首先创建一个新文件夹，并通过运行命令`npm -y`创建一个新的 package.json。

### 柏树

我们不会深入了解赛普拉斯是什么，但是如果你没有听说过它或者有机会玩它，我强烈建议你去看看。您可以使用以下命令安装 cypress:

```
npm install -D cypress 
```

Enter fullscreen mode Exit fullscreen mode

安装 Cypress 可以让你使用一些命令行工具，比如运行测试的无头版本或者在 chrome 中打开它们。

```
./node_modules/.bin/cypress open 
```

Enter fullscreen mode Exit fullscreen mode

这样我们就可以在 package.json
的脚本部分添加一个新的测试

```
"test:e2e": "./node_modules/.bin/cypress open" 
```

Enter fullscreen mode Exit fullscreen mode

**在 VueJS 中运行 Cypress 测试**

当在像 VueJS 这样的框架中工作时，框架为我们解决了这个问题。您可以使用 Cypress 编写端到端的测试，当您准备好测试时，只需运行命令

```
npm run test:e2e 
```

Enter fullscreen mode Exit fullscreen mode

这将启动应用程序，然后运行 cypress 集成测试。当测试完成后，你得到测试结果，应用程序关闭。这是首选的工作流程，我们将尝试和模仿。

### Http 服务器

对于要求每个人手动运行项目的问题，一个简单的解决方案是安装一个小型 HTTP 服务器。你可以做一些搜索，找到一个适合你的需求，但对我们来说，http-server 又小又快，这是我们一直在寻找的品质。你可以全局安装它，但是对于这个演示，我们将通过运行下面的命令把它作为一个开发依赖项安装:

```
npm install -D http-server 
```

Enter fullscreen mode Exit fullscreen mode

现在已经安装好了，可以添加一个新的脚本来启动 HTTP 服务器。

```
"start": "./node_modules/.bin/http-server" 
```

Enter fullscreen mode Exit fullscreen mode

## 并行运行您的 npm 脚本

现在您已经创建了两个脚本，您需要找到运行它们的方法。在该练习的第一次迭代中，我要求学生运行这两个程序。这意味着在 Visual Studio 代码中，您需要打开一个终端，运行`npm run start`，然后打开一个新的终端实例并运行`npm run test:e2e`。

这没什么大不了的，但当你向某人介绍新概念时，你会想尽可能地消除摩擦。这个练习的重点不是如何运行多个脚本，而是运行测试来确保他们编写的代码是正确的。

我已经意识到我可以使用`&&`操作符一个接一个地运行 2 个脚本。这意味着，如果我有 2 个脚本，下面的脚本将工作。

```
"scripts": {
  "one": "./node_modules/.bin/one",
  "two": "./node_modules/.bin/two",
  "start": "npm run one && npm run two"
} 
```

Enter fullscreen mode Exit fullscreen mode

这是通过按顺序运行脚本来实现的，这也意味着第一个脚本必须在第二个脚本运行之前完成。在 HTTP 服务器的情况下，它保持运行，等待接受新的请求。HTTP 服务器永远不会结束，端到端测试永远不会运行，所以我不得不回到黑板上。

经过一番搜索，我确实找到了几个包，其中一个我将在本文的后面讨论。我还看到一些文档说使用`&&`将顺序运行你的脚本，而`&`将并行运行它们。

这真是让人大吃一惊🤯我的时刻。我立即尝试了这种方法，它奏效了，这促使我发出了下面的推文。

液体错误:内部

我收到了很多回复，其中大部分都和我的反应类似，哇，我不知道它可以做到这一点。我确实有一对夫妇，虽然破坏了我的聚会，并询问有关 Windows。

## windows Dan 呢？

我主要是 mac 用户，但我在 mac 上有 bootcamp，这样我就可以在需要的时候进入 Windows。当人们开始问我这个问题时，我最初的想法是这可能不会在 Windows 上运行。果然，经过快速测试，这实际上不能在 Windows 上工作，因为 cmd.exe 不支持它。

不过不用担心，这个问题仍然有一个跨平台的解决方案。我做了更多的搜索，发现了一些 npm 包，看起来它们可以工作。我最终安装了 [npm-run-all](https://www.npmjs.com/package/npm-run-all) ，效果非常好。

```
npm install -D npm-run-all

"start": "./node_modules/.bin/http-server",
"cypress": "./node_modules/.bin/cypress run",
"test": "npm-run-all -p start cypress" 
```

Enter fullscreen mode Exit fullscreen mode

据一些比我聪明得多的人说，这可能是比使用`&`更安全的路线，我不太明白，但对我来说听起来不错。

## 结论

我觉得自己很幸运，每天都能学到东西。不过，这是我真正兴奋地学习单&运算符的一天。如果你最近学到了什么东西，足以让你脑袋爆炸🤯表情符号请[在推特](https://twitter.com/therealdanvega)上联系我，因为我很想知道。一如既往....

快乐编码
丹

*这篇文章最初是在 https://www.danvega.dev/blog 的[发表在我的博客上的。如果你觉得这篇文章有趣，请考虑](https://www.danvega.dev/blog)[订阅我的时事通讯](https://www.danvega.dev/signup/)或者在 [Twitter](http://twitter.com/therealdanvega) 上关注我。*