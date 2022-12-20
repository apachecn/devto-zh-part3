# 关于 Git 提交和拉请求的建议

> 原文：<https://dev.to/mehmetseckin/recommendations-on-git-commits-and-pull-requests-4nld>

# 关于 Git 提交和拉取请求的建议

项目的长期成功与其可维护性紧密相关。一个干净的和精心制作的源代码控制历史是维护项目时最重要和最强大的工具之一。

在这篇文档中，我将尝试提供一些建议来帮助我们建立和保持一个干净的源代码控制历史，并使我们未来的自己和任何可能继承和维护您的代码库的人的生活更容易。

## 提交

源代码管理历史的最简单的构造块是单个提交。随着提交变得更好，您的源代码控制历史变得更健康，项目变得更容易维护和开发，这为项目的成功做出了很大贡献。

一个好的提交是一组*原子*变化，用一个写得很好的*提交消息*来记录。

### 使原子提交

原子提交是一组逻辑分离的变更。每一组变更都包含一个处理单一任务的工作块。

对您的更改进行一些思考，并尝试将它们分成逻辑工作块，每个工作块处理一个操作。

如果可以，让你的承诺*易于理解*；例如，不要为了五个不同的错误写了整整一周的代码，然后提交你所有的修改:

```
Fix issues with single sign-on, header layout and another irrelevant module 
```

试着把你的工作分成*个，每个 bug 至少一个*提交，有写好的提交消息。这将使得检查变更更加容易，并且如果需要的话，在以后提取或者恢复其中一个变更集:

```
commit a023bac42
Author: Mehmet Seckin <mehmetseckin@example.com>
Date: Fri Jan 01 00:00:00 1970 -0000

Enable single sign-on for XYZ context

Fixes: #123
---
commit 0c2cda12
Author: Mehmet Seckin <mehmetseckin@example.com>
Date: Fri Jan 02 00:00:00 1970 -0000

Improve header layout

Moved the navbar towards the top of the page to prevent the logo from
disappearing in responsive mode.

Fixes: #456
---
commit 10dcb332
Author: Mehmet Seckin <mehmetseckin@example.com>
Date: Fri Jan 03 00:00:00 1970 -0000

Add plug-in support for XYZ module

Completes: #789 
```

### 写好提交消息

关于什么是好的提交消息，有一些很好的约定。下面的例子展示了一个好的提交消息的惯用格式和部分:

```
Summarize changes in around 50 characters or less

More detailed explanatory text, if necessary. Wrap it to about 72
characters or so. In some contexts, the first line is treated as the
subject of the commit and the rest of the text as the body. The
blank line separating the summary from the body is critical (unless
you omit the body entirely); various tools like `log`, `shortlog`
and `rebase` can get confused if you run the two together.

Explain the problem that this commit is solving. Focus on why you
are making this change as opposed to how (the code explains that).
Are there side effects or other unintuitive consequences of this
change? Here's the place to explain them.

Further paragraphs come after blank lines.

 - Bullet points are okay, too

 - Typically a hyphen or asterisk is used for the bullet, preceded
by a single space, with blank lines in between, but conventions
vary here

If you use an issue tracker, put references to them at the bottom,
like this:

Resolves: #123
See also: #456, #789 
```

以下是一些经验法则:

#### 将提交消息的第一行视为*主题行*

提交消息的第一行应该像电子邮件的主题行一样对待。

这应该用一个简短的描述性句子来概括这些变化。

##### 以大写字母开头

例如，主题行应该大写；

```
Fix file format and indentation 
```

##### 保持简短——大约 50 个字符或更少

50 个字符不是一个绝对的限制，但它迫使作者思考信息，并试图尽可能清晰地总结它。

> 如果你很难总结，你可能一次做了太多的改变。总是尝试进行原子提交。

大多数 git 平台都知道这种约定，如果主题行比预期的要长，就会显示一个截短的版本。

##### 在主题行中使用祈使语气

Git 本身在创建提交时使用命令式，例如，如果您使用`git merge`，您将会看到如下提交消息:

```
Merge branch 'myfeature' into 'develop' 
```

因此，当您在命令中编写提交消息时，您遵循了 Git 自己的内置约定，这增加了整个源代码控制历史中的**一致性**。比如:

```
Refactor XYZ to improve readability
Update README file
Remove deprecated methods
Merge 'feat/abc' into 'develop'
Release v1.0.0 
```

比下面的日志更容易理解:

```
Refactoring XYZ to improve readability
README file has been updated
Removal of deprecated methods
Merge 'feat/abc' into 'develop'
Released v1.0.0 
```

##### 主题行不要以句号结尾

主题行中不需要以句号结尾。此外，期间吃一个珍贵的字符从 50 个字符的限制。

这样做:

```
Configure Kerberos authentication 
```

与其这样:

```
Configure Kerberos authentication. 
```

#### 添加正文，解释你的变化的背景和细节

并非每个提交消息都需要主题和正文。一些提交可以简单到:

```
Correct typo in README file 
```

没必要多说什么。如果读者想知道是什么错别字，他们可以看看修改。

然而，有时您的提交需要一些解释或上下文。在这种情况下，请在主题行后添加更改的详细信息。试着解释变化的背景和原因。例如:

```
Re-authenticate user with admin scope

The X action requires the user to have elevated privileges. This commit
adds functionality to request the necessary privileges and perform the
action subsequently. 
```

##### 用空行将正文与主题行分开

例如，`git revert`生成以下内容:

```
Revert "Add the thing with the stuff"

This reverts commit cc87791524aedd593cff5a74532befe7ab69ce9d. 
```

##### 用身体解释*什么*和*为什么*而不是*如何*

关于*如何进行*变更，代码通常是自我解释的。重点记录问题和解决方案，以及为什么这是解决问题的最佳方式，而不是解决方案的实施细节。

比如这样做:

```
Clear the token cache when a new session starts

The cached tokens were being re-used by the browser which resulted
in token renewal errors between sessions. Clearing the token cache
avoids the futile effort by the browser to renew the previous session. 
```

与其这样:

```
Clear the token cache when a new session starts

Call `OAuth2UserManager.TokenCache.Clear()` to clear the token cache. 
```

##### 在正文末尾添加对任务或 bug 的引用

删除对相关工作项的引用，以便更容易跟踪更改的原因。当 git 平台能够自动关联被引用的工作项时，这一点特别好，因为它也有助于变更日志。

## 拉取请求

拉请求是源代码管理历史的一个重要部分，因为这是记录、解释、审查和提炼特定变更的地方。

### 保持拉取请求尽可能小

拉取请求中的变更越多，理解它就越困难，审查它就需要越长的时间。

将您的工作分解为多个逻辑上分离且独立的拉式请求，以简化审查过程，尽早发现 bug 并减少代码味道。

### 适用单一责任原则

> 单一责任原则是一种计算机编程原则，它规定每个模块或类都应该对软件提供的功能的单一部分负责，并且这种责任应该完全由类来封装。

就像类和模块一样，拉请求应该只做一件事。在提交 PR 进行审核之前，尝试应用单一责任原则。如果这段代码做的不止一件事，把它分解成其他的拉请求。

### 拉取请求的标题应该简短明了

标题应该足以理解正在改变的内容。例如:

*   为 X 添加单点登录支持
*   改进注册错误处理流程

### 描述应包含尽可能多的细节

*   描述一下发生了什么变化。
*   解释**为什么**做出了改变。
*   明确定义**如何实施**变革
*   使用截图、代码片段和其他资源来演示这些变化。

如果拉请求通过*挤压合并*合并到主流中，这将特别有帮助，在这种情况下，你的 PR 标题和描述将自动形成一个好的提交信息。

## 进一步阅读

本文档主要受以下文章的启发:

*   [如何编写 Git 提交消息](https://chris.beams.io/posts/git-commit/)
*   [Git 提交指南](https://www.git-scm.com/book/en/v2/Distributed-Git-Contributing-to-a-Project#_commit_guidelines)
*   [拉动请求的艺术](https://hackernoon.com/the-art-of-pull-requests-6f0f099850f9)
*   [完美拉动的解剖要求](https://medium.com/@hugooodias/the-anatomy-of-a-perfect-pull-request-567382bb6067)