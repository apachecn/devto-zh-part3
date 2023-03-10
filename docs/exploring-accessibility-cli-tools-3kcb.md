# 探索可访问性 CLI 工具

> 原文：<https://dev.to/lkopacz/exploring-accessibility-cli-tools-3kcb>

纳税日快乐(至少如果你是美国人)！

我的[上一篇帖子](https://www.a11ywithlindsey.com/blog/web-accessibility-testing-process)是关于我的易访问性测试过程的。然而，这个星期，我想检查自动化测试。我有我工作时做的事情的初始流程，以及我确保手动测试的内容。我还写了我手动测试的所有东西[。](https://www.a11ywithlindsey.com/blog/4-things-always-manually-test)

如今，持续集成对于大型团队来说是必不可少的。令您惊讶的是，我还没有为我的项目配置可访问性 CLI 工具。大部分时间我都是主要的前端，出于习惯，我总是手动测试，所以我觉得没有必要。

是时候把自己叫出来了。我绝对**应该**将这些整合到我的项目测试中。我曾经是唯一的前端开发者，我认为这是理所当然的。我现在所在的团队中**是可访问性专家**，但是团队中还有其他前端开发人员。我回顾了许多 pull 请求，但是如果持续集成能够捕获新的错误，事情会简单得多。这不就是持续集成的重点吗？抓住我们错过的东西？让我们的生活更轻松？

> 抬起头。这将是一篇“跟我学”的帖子。

我将介绍我熟悉的两个 CLI 工具- pa11y 和 aXe-cli。欢迎[发微博给我](https://twitter.com/LittleKope)任何你想了解的工具。

## Pa11y

Pa11y 是我几年前使用的第一个可访问性测试工具。第一次尝试的时候是大口套餐。不可否认，我从未做过任何配置，因为当时我发现这个工具限制太多。

我是新来的，所以我将在这篇博文中介绍如何安装它，看看我是否喜欢它。

`npm install pa11y --save`

现在，我将创建一个 npm 脚本来运行 pa11y。在我的`package.json`文件中:

```
"scripts":  {  "test-a11y":  "pa11y ./index.html"  } 
```

然后我在命令行输入`npm run test-a11y`并得到错误。我得到了一个我记得在 2 年前看到的语法错误，我碰巧记得这是一个节点版本问题。

```
$ npm run test-a11y

> egghead-focus-event@1.0.0 test-a11y /Users/lkopacz/Sites/egghead-focus-event
> pa11y ./index.html

/Users/lkopacz/Sites/egghead-focus-event/node_modules/pa11y/bin/pa11y.js:92
async function runProgram() {
      ^^^^^^^^ 
```

当我检查我正在使用的 node 版本时，我了解到不知何故，我已经从 node v.8 切换到 node v.6。令人尴尬的是，我没有意识到这一点，我使用的是一个旧版本的 node。

`nvm use v8.9.1`

现在脚本看起来可以正常运行了！我运行它，得到一个可访问性错误。

```
$ npm run test-a11y
> egghead-focus-event@1.0.0 test-a11y /Users/lkopacz/Sites/egghead-focus-event
> pa11y ./index.html

Welcome to Pa11y

 > Running Pa11y on URL file:///Users/lkopacz/Sites/egghead-focus-event/index.html

Results for URL: file:///Users/lkopacz/Sites/egghead-focus-event/index.html

 • Error: The html element should have a lang or xml:lang attribute which describes the language of the document.
   ├── WCAG2AA.Principle3.Guideline3_1.3_1_1.H57.2
   ├── html
   └── <html class="no-js"><!--<![endif]--><head> <met...</html>

1 Errors 
```

那太尴尬了。然而，这正是它存在的原因。去解决这个问题，看看会发生什么。

```
<html class="no-js" lang="en">
  <!-- MOAR HTML-->
</html> 
```

现在让我们再次运行该测试，看看会发生什么。

```
$ npm run test-a11y

> egghead-focus-event@1.0.0 test-a11y /Users/lkopacz/Sites/egghead-focus-event
> pa11y ./index.html

Welcome to Pa11y

 > Running Pa11y on URL file:///Users/lkopacz/Sites/egghead-focus-event/index.html

No issues found! 
```

嗯，这很容易设置！到目前为止，我喜欢 pa11y。我很乐意更多地使用它，并研究他们所有的其他工具，比如 pa11y 仪表板。然而，对于这篇博文来说，CLI 似乎是最有益的。

## 轴-cli

领先的无障碍公司 Deque Systems 开发了 aXe-cli 工具。我还没有检查过许多 Deque 工具，这是相当令人震惊的，因为它们是这个领域的领导者。我将使用与上述 pa11y 相同的方式安装该模块。这次我将对照[我的表单标签课程代码](https://github.com/lkopacz/egghead-form-labels)来测试它。这一课有可访问性错误，因为我在视频的[中修复了它们。我知道有表单标签错误。](https://egghead.io/lessons/html-5-creating-accessible-forms-with-associated-form-labels)

`npm install axe-cli --save`

然后，我将创建与 pa11y 任务相同的 npm 脚本。

```
"scripts":  {  "test-a11y":  "axe ./index.html"  } 
```

当我运行该命令时，我收到的消息如下:

```
$ npm run test-a11y

> egghead-30-second@1.0.0 test-a11y /Users/lkopacz/Sites/egghead-form-labels
> axe ./index.html

Running axe-core 3.2.2 in chrome-headless

Testing http://./index.html ... please wait, this may take a minute.
axe-webdriverjs deprecated Error must be handled as the first argument of axe.analyze. See: #83 node_modules/axe-cli/lib/axe-test-urls.js:85:8

  Violation of "document-title" with 1 occurrences!
    Ensures each HTML document contains a non-empty  element. Correct invalid elements at:
     - html
    For details, see: https://dequeuniversity.com/rules/axe/3.2/document-title

  Violation of "html-has-lang" with 1 occurrences!
    Ensures every HTML document has a lang attribute. Correct invalid elements at:
     - html
    For details, see: https://dequeuniversity.com/rules/axe/3.2/html-has-lang

  Violation of "landmark-one-main" with 1 occurrences!
    Ensures the document has only one main landmark and each iframe in the page has at most one main landmark. Correct invalid elements at:
     - html
    For details, see: https://dequeuniversity.com/rules/axe/3.2/landmark-one-main

  Violation of "page-has-heading-one" with 1 occurrences!
    Ensure that the page, or at least one of its frames contains a level-one heading. Correct invalid elements at:
     - html
    For details, see: https://dequeuniversity.com/rules/axe/3.2/page-has-heading-one

4 Accessibility issues detected.

Please note that only 20% to 50% of all accessibility issues can automatically be detected.
Manual testing is always required. For more information see:
https://dequeuniversity.com/curriculum/courses/testingmethods 
```

好的。所以，乍一看，我非常喜欢一些东西。多半是因为最后的免责声明。

> 请注意，只有 20%到 50%的可访问性问题能够被自动检测出来。总是需要手动测试。

## 比较两者

这两个工具我都喜欢。我们先来看看两者的优劣！

### pa11y

优点:

1.  设置它非常简单。
2.  它们会告诉你 HTML 中有问题的代码在哪里

CONS:

1.  我知道如何调试初始错误的唯一原因是因为我记得几年前的错误。它的文档里有它，但是我错过了。Deque 对 README.md 的要求更高
2.  错误消息并不是非常详细。有时候这对新人来说更棘手

### 轴-cli

优点:

1.  正如我上面所说的，我喜欢这个免责声明，你**应该**手动测试以捕捉其他问题。请看我的[上一篇](https://www.a11ywithlindsey.com/blog/web-accessibility-testing-process)关于我的易访问性测试过程的帖子。我也有一张[的清单，我手动测试的是](https://www.a11ywithlindsey.com/blog/4-things-always-manually-test)。
2.  我还喜欢它给你一些失败规则的链接。

CONS:

1.  我更喜欢 pa11y 的一点是，它会告诉您违规元素的确切位置。请注意，我并不是说这是不可能的。我只测试了最基本的命令。

## 结论

嗯，我在自动化可访问性测试方面起步较晚，但我是一个皈依者。如果你注意到了，即使作为一个专家，我也忘记了一些事情。我们并不完美。如果您正在与较新的团队一起工作，那么它是一个强大的工具。它确保其他团队成员不会引入新的问题或错误。我认为这两种工具开箱后都非常坚固。我计划更多地浏览他们的文档，看看如何进一步配置它！

现在，我计划在我的项目中实现 pa11y。对我的团队来说，我更喜欢开箱即用，因为我可以向我的团队解释为什么会出现错误。我想更多地探索 aXe-cli，因为我认为它的冗长更加清晰。我确信有了更多配置，我将能够让 aXe 更好地工作。

你有什么可访问性测试技巧吗？在推特上让我知道[。如果你有兴趣从我这里听到更多内容，](https://twitter.com/LittleKope)[订阅我的简讯](https://pages.convertkit.com/4218bd5fb5/68dc4e412a)(随时退订)。