# alex 的包容性文档

> 原文：<https://dev.to/kennethlarsen/inclusive-documentation-with-alex-2gcp>

[![Inclusive Documentation With alex](img/fc3c5795370d01a42213cc7c7c4ca730.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3Sehc0tN--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.kennethlarsen.org/conteimg/2019/02/Sk-rmbillede-2019-02-19-kl.-15.00.54.png)

有很多有用的开源工具可以帮助我们改进文档。一般来说，自动化工具可以帮助我们消除维护文档的开销，让我们把宝贵的时间花在编写内容上。遗憾的是，这些工具并不完美，您必须注意一些事情。在这篇文章中，我将向你展示如何使用 alex 重构我们当前的文档。

当谈到包容性和体贴时，开发社区并没有最好的名声。尽管已经做了很多努力来改善这一点，我们仍然离目标很远。然而，有一件事我们可以做，以帮助促进这一点-这一切都始于文件。

alex 是一个自动化的工具，它让我们能够捕捉文档甚至代码中不体贴的和攻击性的语言。

## 入门

要开始，我们必须`cd`进入包含一个或多个 markdown 文件的目录并运行`npx alex`。(或`npm install -g alex; alex`)。现在，终端中的输出应该会告诉您我们的文档当前是否有任何问题。

如果我们想让它在我们的编辑器中作为一个 linter，有几个集成可用:

*   Atom: [atom-linter-alex](https://github.com/get-alex/atom-linter-alex)
*   崇高:[SublimeLinter-contrib-Alex](https://github.com/sindresorhus/SublimeLinter-contrib-alex)(作者 [@sindresorhus](https://github.com/sindresorhus) )
*   Visual Studio 代码: [vscode-alex](https://github.com/shinnn/vscode-alex) (作者 [@shinnn](https://github.com/shinnn) )
*   Gulp: [gulp-alex](https://github.com/dustinspecker/gulp-alex) (作者[@ Dustin pecker](https://github.com/dustinspecker))
*   Slack: [alex-slack](https://github.com/keoghpe/alex-slack) (作者 [@keoghpe](https://github.com/keoghpe) )
*   Probot: [linter-alex](https://github.com/swinton/linter-alex) (作者 [@swinton](https://github.com/swinton) )
*   我知道" T0 "和" T1 "是" T2 "和" T3 "

如果我们使用 Ember.js，甚至有一个 Ember 插件将在我们的测试套件中运行 alex，还可以捕捉代码中的攻击性命名:

*   Ember: [ember-cli-alex](https://github.com/yohanmishkin/ember-cli-alex) (作者 [@yohanmishkin](https://github.com/yohanmishkin) )

## 重构我们的文档

因此，我们有亚历克斯运行，它列出了许多问题，我们如何从这里继续下去？

亚历克斯可能会听到一些根本不为他人着想或冒犯他人的话，这没什么。是棉绒，不是人。这是我们作为公司或维护者定义规则的地方。假设我们写了下面的句子:

> 要试用新的 React 挂钩，请访问指南。

亚历克斯将返回以下内容:

> 小心，这在某些情况下是亵渎神明的

现在，虽然在某些情况下`hooks`是亵渎的，但在某些情况下却不是。如果我们想忽略这个警告，因为它在这个上下文中不是亵渎，我们在句子上方添加一个 ignore 语句，如下所示:

```
<!-- alex ignore hooks -->
<p>To try out the new React hooks visit the guides.</p> 
```

Enter fullscreen mode Exit fullscreen mode

如果我们再次运行 alex，它将不会返回任何错误或警告。

但是如果我们写的都是反应钩子呢？每当我们提到 React 钩子时，添加 ignore 语句是非常麻烦的。谢天谢地，alex 支持忽略文件。

在 Ember.js 网站的[库中，我们有一个类似于
的忽略文件](https://github.com/emberjs/website)

```
{
  "allow": ["color", "dad-mom", "her-him", "he-she", "hook", "hooks", "harder"] 
} 
```

Enter fullscreen mode Exit fullscreen mode

只要这个文件被命名为`.alexrc`并放在项目的根目录中，它就会自动忽略这些单词。

忽略了非冒犯性的话之后剩下的东西，都是我们应该修复的东西。假设我们有一个 README.md，其中包含以下部分:

```
It writes all changes to the **master server** thus, the slaves were read-only copies of master. 

If any of you guys would like to contribute please do so 
```

Enter fullscreen mode Exit fullscreen mode

亚历克斯会提出关于这篇文章的问题。首先，`master/slave`的使用被认为是不敏感的。相反，我们应该使用像`primary/replica`这样的术语——除了更具包容性之外——这是一个更精确的术语。

我们的另一个问题是`guys`的使用。alex 告诉我们“`guys`可能不敏感，用`people`、`persons`、`folks`代替”。在这种情况下，我同意`guys`是不敏感的，并暗示男性是被鼓励做出贡献的人。

经过一些变化，我们现在有:

```
It writes all changes to the **primary server** thus, the replicas were read-only copies of primary. 

If any of you folks would like to contribute please do so 
```

Enter fullscreen mode Exit fullscreen mode

好多了。

## 保持清洁

除了在我们的 IDE 中使用 linter 并在我们的本地机器上运行 alex 之外，我们还可以做一些事情来捕捉后续的错误。

如果我们再次查看 [Ember.js 网站存储库](https://github.com/emberjs/website)，我们会发现 CI 正在运行一个脚本。

```
git remote add base https://github.com/emberjs/website.git
git fetch base
BLOG_HAS_CHANGES=$(git diff --name-status base/master source/blog/2*)
if [[$BLOG_HAS_CHANGES]]
  then
    alex $(git diff --name-status base/master source/blog/2* | sed s/^..//)
fi 
```

Enter fullscreen mode Exit fullscreen mode

这个脚本检查 blog 是否有任何变化——因为 blog 是当前运行 alex 的唯一地方——如果有，那么 alex 只在 diff 中运行。当我们已经有很多文档时，这一点尤其重要。一次改变一切通常是不可能的，但是抓住它向前发展是可行的。

因此，每次提出拉取请求时，alex 都会确保我们考虑自己的语言。

* * *

一开始可能会让人不知所措，但我确实认为值得投资改进我们的文档，使其更受欢迎和更具包容性。