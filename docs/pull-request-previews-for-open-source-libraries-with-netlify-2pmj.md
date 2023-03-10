# 使用 Netlify 的开源库的拉请求预览

> 原文：<https://dev.to/fedekau/pull-request-previews-for-open-source-libraries-with-netlify-2pmj>

*最初发表于[斯特里弗的博客](https://www.streaver.com/blog/posts/pull-request-previews-for-open-source-libraries-with-netlify.html)。*

## 标题到底是什么意思？

我明白了，这个标题很难理解，但是请耐心听我说，我会带你了解这个问题和一个可能的解决方案。

假设您编写了您的第一个开源(或不开源)库，您为它编写了单元测试，并且您有一个示例 web 应用程序，您可以在其中测试库并检查它是否按预期工作，现在，您使用 [npm link](https://docs.npmjs.com/cli/link.html) (或类似的东西)在本地测试它。作为一名独立开发者，这很好。

现在，人们已经开始帮助你为你的库编写新功能，但他们需要一种安全的方法来测试真实应用程序中的变化，并向审查者展示它的工作示例。让审查人员下载示例应用程序、新代码，并为每个 PR 运行 [npm 链接](https://docs.npmjs.com/cli/link.html)并不合适。

让我们用库的拉请求预览应用程序来解决这个问题。

### 什么是拉式请求预览 app？

如果您使用“基于拉式请求”的工作流，预览应用程序就像是为您的每个分支或拉式请求(PR)提供了一个“暂存”或“预生产”服务器。基本上，每个公关/分支机构都有一份整个应用程序(或者几乎整个应用程序)的副本。如果你从来没有使用过这个，并且有机会在工作中或者在一些兼职项目中尝试一下，相信我，这感觉就像天堂一样！

如果您还在这里，您可能想知道为什么不编写端到端(e2e)测试，这样您就完成了。E2E 测试是昂贵的，在某些情况下很难编写，所以，这个解决方案是为那些 e2e 太多的情况，你只需要一些基本的“手工 e2e”测试你的库。此外，您甚至可以部署示例应用程序，作为最终用户使用您的库的示例，这是一个双赢的局面！

我们需要一些简单的东西来实现这一点:

*   为了测试库，我们将使用 [vue-preferences](https://github.com/streaver/vue-preferences)
*   示例应用程序库，这里我们使用[vue-preferences/sample-app](https://github.com/streaver/vue-preferences/tree/master/examples/sample-app)
*   我们使用 [npm-link](https://docs.npmjs.com/cli/link) 将新的库代码链接到示例应用程序
*   一种为 PRs 部署预览应用的方法，为此我们选择了 [Netlify](https://netlify.com)

**警告**

从现在开始，我将提供假设前面列出的项目的步骤，但是您可以根据您的具体需求来替换每个“成分”。一般的想法应该适用。如果你需要帮助，在评论里或者 [@fedekauffman](https://twitter.com/fedekauffman) 告诉我！

## 第一步:带样本 app 的库

第一步是将示例应用程序与您想要启用预览的库放在同一个存储库中，还有其他方法可以实现这一点，但这可能是最方便的方法，并且会“强制”您保持示例应用程序最新。

这里是一个简化的树形视图，显示了 [vue-preferences](https://github.com/streaver/vue-preferences) 库，包括示例应用:

```
...
├── examples
│   └── sample-app <--- This is the sample app
│       ├── package.json
│       ├── public
│       ├── src
│       └── yarn.lock
├── package.json
├── src
│   └── index.js <--- This is the library code
├── tests
│   ...
└── yarn.lock 
```

## 第二步:链接库

接下来，您必须弄清楚如何使用当前分支中存储库中可用的最新代码来创建您的示例应用程序，幸运的是，如果您使用的是`npm`，这非常容易！

1.  进入库储存库并运行`npm link`
2.  构建它，在这种情况下，我们使用`yarn build`。
3.  转到示例应用程序文件夹`cd examples/sample-app`
4.  运行`npm link vue-preferences`
5.  构建它，`yarn build`或者用于实时重新加载(如果你已经设置好的话)，`yarn serve`

**NPM 链接**
关于如何使用`npm-link`的精彩解释，请查看[了解 NPM 链接](https://medium.com/dailyjs/how-to-use-npm-link-7375b6219557)作者[弗兰齐斯卡·辛克尔曼](https://twitter.com/fhinkel)

此时，您应该已经在您的分支中使用最新的代码运行了示例应用程序！

## 步骤 3:使用 Netlify 进行部署

由于这个例子是围绕一个 VueJS 库和一个 web 示例应用程序运行的，所以使用像 [Netlify](https://www.netlify.com) 这样的服务来部署静态资产是有意义的。如果不是这样，并且你的库是一个 CLI(或者任何不是 web 的东西)，你可以，例如，使用 [Heroku](https://www.heroku.com/) 来启动一个 Dyno，在那里你可以运行一些测试命令。

从第 2 步我们知道了如何用库构建示例应用程序，我们现在只需要把它放在`netlify.toml`文件中，我们就完成了:

```
# By default all the branches/PRs build using the latest commit
# available in the branch.

[build]
  base = "."
  publish = "examples/sample-app/dist"
  command = """
    yarn build && \
 npm link && \
 cd examples/sample-app && \
 yarn install && \
 npm link vue-preferences && \
 yarn build --mode development
  """

# The master branch just builds whatever is defined in the package.json
# This is because you probably want the production sample app to be stable
# and not just build whatever is in the repository

[context.master]
  base = "examples/sample-app"
  publish = "examples/sample-app/dist"
  command = "yarn build" 
```

现在，每次您推送一个新的提交，您的示例应用程序都会使用存储库中的最新代码构建，此外，如果您打开一个拉请求，您将获得一个独立的示例应用程序来测试库代码。酷吧🤓？

这种方法可能并不适用于每一个库，您可能需要根据自己的需要进行调整，但是现在您有了一个起点！

这实际上在 [vue-preferences (PR 18)](https://github.com/streaver/vue-preferences/pull/18) 中被合并到 master 中，你可以看到一个测试拉取请求( [PR 19](https://github.com/streaver/vue-preferences/pull/19) )，它将一个`console.log`语句添加到库代码中，然后成功构建到预览应用程序中🎉。