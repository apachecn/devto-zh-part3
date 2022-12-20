# 用常规提交增强您的 git 日志

> 原文：<https://dev.to/maxpou/enhance-your-git-log-with-conventional-commits-3ea4>

Git 是安装在我们大多数机器上的一个非常强大的工具。作为开发人员，我们每天都在使用它。但是，不幸的是，乍一看，这个工具对开发人员并不友好。这就是为什么很多人用图形用户界面(GUI)绕过命令行界面(CLI)的原因。

这就像在不了解语言本身的情况下使用框架一样。开始的时候还可以，但是，你迟早会有问题的。

我们来举个例子:

```
$ git log --oneline ./src/components/button/

daccff1f test should pass
3fff19f6 test should pass
5b998d9a add disabled property for button
06faab4d fix lint
186cce90 refactor button
4b99d91a fix spinner component
5b998d9a fix css
263288a5 test should pass
c3fb85af Create Button component 
```

Enter fullscreen mode Exit fullscreen mode

这个日志对你来说可能没什么问题。让我向您展示我在这个日志中发现的问题:

*   很难理解组件的历史。我们可能会重复已经修复的错误。
*   有一些不必要的提交会污染日志并使历史难以阅读。此外，像`git blame`这样的功能变得无关紧要。
*   看起来一个特性是通过几次提交添加的。如果我想恢复此操作，应该包括哪个提交？
*   讲述一个不同的组件。这是不想要的改变吗？同样，如果我们需要恢复呢？
*   ...

我认为我们不应该混淆`git commit`和`ctrl` + `s`。一个`git log`应该就像**在读一个故事**。通过阅读日志，我应该能在 10 秒钟内了解整个文件历史。

如果我们有这样的东西会怎么样:

```
$ git log --oneline ./src/components/button/

06faab4d revert: feat: add disabled property
186cce90 feat: add disabled property
5b998d9a test: add scenario for readonly property
263288a5 fix: fix css when hover
c3fb85af feat: add button component 
```

Enter fullscreen mode Exit fullscreen mode

干净多了，不是吗？
那个叫做**约定俗成的东西犯了**。

## 常规提交

常规提交是由 [Angular 团队](https://github.com/angular/angular/blob/master/CONTRIBUTING.md#commit)制定的 Git 提交约定。基本上，每个拉请求都应该以一个提交和一个标准化的提交消息结束。

消息应该遵循这个正则表达式:

```
/^(revert: )?(feat|fix|docs|style|refactor|perf|test|chore)(\(.+\))?: .{1,50}/ 
```

Enter fullscreen mode Exit fullscreen mode

提交类型:

*   **专长**:增加一个新特性(相当于[语义版本](https://semver.org)中的一个`MINOR`)。
*   **修复**:修复一个 bug(相当于[语义版本化](https://semver.org)中的一个`PATCH`)。
*   **文档**:文档变更。
*   **样式**:代码样式改变(分号，缩进...).
*   **重构**:重构代码，不改变公共 API。
*   **性能**:更新代码性能。
*   **测试**:将测试添加到现有特征中。
*   **苦差事**:在不影响用户的情况下更新一些东西(例如:在`package.json`中增加一个依赖)。

使用这个约定的项目: [Angular](https://github.com/angular/angular) ， [Vue.js](https://github.com/vuejs/vue) ， [Gatsby(差点)](https://github.com/gatsbyjs/gatsby)， [Lerna(差点)](https://github.com/lerna/lerna)， [jest(差点)](https://github.com/facebook/jest)。

## 好处

**项目/代码可理解性**

提交更具描述性，更容易理解项目的历史。做贡献也变得更容易了。

比如，我从来没有贡献过 Angular 的 http 包。但是，[阅读回购的 git 日志](https://github.com/angular/angular/commits/master/packages/http)有助于我更好地理解这个包的历史。

可用性

如果每次提交都有一个动作，那么恢复变更就变得更容易了。如果有 git 冲突，也是如此...

**掌握你的 Git 技能**

因为不是所有的 Git-repository 管理器都有“压缩并合并”选项，所以有时候你不得不自己做这个操作。因此，您将学习如何“挤压”您的提交，如何“修复”提交，如何删除特定的提交...
了解幕后发生的事情总是很重要的！

## 亦见

*   [conventionalcommits.org](https://www.conventionalcommits.org/en/v1.0.0-beta.2/)
*   [Vue.js 提交约定](https://github.com/vuejs/vue/blob/dev/.github/COMMIT_CONVENTION.md)
*   github.com/conventional-changelog/conventional-changelog——一个基于 git 消息生成 changelog 的好工具。
*   github.com/conventional-changelog/commitlint——一个饭桶犯。配合哈士奇使用。

最初发布于 [maxpou.fr](https://www.maxpou.fr/git-conventional-commits) 。