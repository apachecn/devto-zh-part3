# 使用 npx 和 npm 脚本来减轻开发人员工具的负担

> 原文：<https://dev.to/azure/using-npx-and-npm-scripts-to-reduce-the-burden-of-developer-tools-57f9>

周五，我正在为布鲁克林的[完美黑客](http://flawlesshacks.com/)制作[的](https://github.com/bnb/step-by-step-express)工作坊版本。

在研讨会版本中，我修改了每个步骤，因此有一个`app.js`和一个`app.complete.js`，这样与会者可以从上一步开始，并知道他们在进行的步骤中的目标是什么。

我想 npm 上可能有一个工具可以让我进一步降低门槛，让与会者/用户知道他们需要做什么来完成这个步骤，并在他们的代码没有做他们认为应该做的事情时对照`app.complete.js`检查他们的代码。

经过一番搜索，我找到了 [Pretty Diff](https://www.npmjs.com/package/prettydiff) ，它公开了一个 CLI 工具，允许您比较两个文件，并在控制台中显示差异。

在全局安装 CLI 后，我对其进行了一点测试，并发现由于我对更改进行了结构化(每个文件夹中的`app.js`和`app.complete.js`以及每个文件夹都有自己的`package.json`，我能够对每个步骤使用相同的命令:

```
diff source:"app.js" diff:"app.complete.js" 
```

Enter fullscreen mode Exit fullscreen mode

厉害！我发现了一个工具，有望降低他们将要编写的代码的成功门槛。只有一个问题:他们需要安装 CLI 来运行上面的命令。这听起来像是一个奇妙的方式来增加进入的障碍和浪费时间在工具上，这是为了改善体验，而不是剥夺体验😱

## 输入 npx

幸运的是，每个安装了现代版`npm`的人都已经拥有了这个优秀的工具:`npx`。

如果您不熟悉，`npx`是`npm`团队提供的一个 CLI，它从 npm 注册表上的一个模块自动执行 CLI。理想情况下，大多数模块只会发布一个顶层命令——对于这些模块，您可以简单地运行`npx <module name>`,命令就会被执行。

在我们的例子中，我们希望运行`prettydiff`模块并传递`diff`命令。幸运的是，`npx`让这变得超级简单:

```
npx prettydiff diff source:"app.js" diff:"app.complete.js" 
```

Enter fullscreen mode Exit fullscreen mode

研讨会与会者可以在任何步骤的目录中 100%运行此操作，并且他们将能够看到之前/之后的差异！

[![What a terminal looks like after a participant runs  raw `npx prettydiff diff source:"app.js" diff:"app.complete.js"` endraw  in the step-two directory](img/7a803778311707ded53d1473ec31a1c6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--GX0yiRWV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/hczjz1wyriurmcsjfdhr.png)

厉害！我找到了一个可行的解决方案...这是 58 个字符长，有一些奇怪的语法，可能很难记住每个人。有效果，但不一定理想。幸运的是，我们可以让它变得更简单。

## 使用 npm 脚本

npm 脚本是我们工具箱中一个非常方便的工具，它使重复的任务和长命令变得容易。令人惊叹的是，您可以在 npm 脚本中使用`npx`——这意味着您可以在 npm 上使用**任何** CLI 在您的项目中工作，甚至不需要实际安装它。这对于构建步骤、生产力工具和(在我们的例子中)不同的代码来说是非常棒的。

在我的例子中，我在我的`package.json` :
中添加了一个`diff`命令

```
 "scripts":  {  "lint":  "standard",  "diff":  "npx prettydiff diff source:\"app.js\" diff:\"app.complete.js\""  }, 
```

Enter fullscreen mode Exit fullscreen mode

现在，我的研讨会的与会者不需要写出`npx prettydiff diff source:"app.js" diff:"app.complete.js"`,只需输入`npm run diff`,命令就会执行🤗

## 鳍

我喜欢 npx，并且认为 npx + npm 脚本是一个超级强大的组合，它允许我们的 JavaScript 代码的最终用户减少帮助他们工作流的重复命令的认知负荷。我想分享这个我如何使用它的快速例子，希望能够让其他人更多地关注代码，而不是被工作流✨所困扰

如果你对 npx、npm 脚本或者我在这篇文章中谈到的任何东西有任何问题，请不要犹豫，在评论中提问——我非常乐意回答你的任何问题！