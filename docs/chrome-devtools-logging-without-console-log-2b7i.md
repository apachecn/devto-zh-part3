# Chrome DevTools:没有 console.log 的日志记录

> 原文：<https://dev.to/apvarun/chrome-devtools-logging-without-console-log-2b7i>

如果你曾经编写过 JavaScript 代码，你很有可能遇到过`console.log`语句。他们是识别代码中基于逻辑的问题的救星。

但是这里有一个限制。您需要修改代码来添加控制台语句，以便验证值(另一个选项是添加断点)。

但是，那不再是真的了。

Chrome 现在允许你添加日志记录语句，而不需要实际添加到你的代码中。他们称之为**日志点**。

[![](img/35618e88ee5fd1bd94439914f544e67f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--l3YGup-8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/l5ajhkt8i1ird5dzc5u0.png)

为了添加一个登录点，打开需要登录的代码，右键单击行号。选择“添加日志点”选项，并输入要记录的变量/值列表(用逗号分隔)。

一旦设置，Chrome 将在每次执行该行时将这些值记录到控制台。🥳

*预览(不记录控制台日志):*
[https://www.youtube.com/embed/6X_SKJS_s-w](https://www.youtube.com/embed/6X_SKJS_s-w)

**在** [亲身体验课程:条件断点](https://learndevtools.now.sh/docs/guides/sources/1.-logging/)(演示页面)

您也可以复制下面的 repo 并在您的本地机器上运行。

## [apvarun](https://github.com/apvarun) / [学习工具](https://github.com/apvarun/learndevtools)

### 使用 DevTools 更好地调试

<article class="markdown-body entry-content container-lg" itemprop="text">

# 学习开发工具

用于学习在浏览器中使用开发人员工具的演练集合。在这里阅读更多

## 入门指南

访问[https://learndevtools.now.sh/](https://learndevtools.now.sh/)了解并尝试 devtools 的各种功能，帮助您提高工作效率。

## 贡献的

欢迎拉取请求。对于重大更改，请首先打开一个问题，讨论您希望更改的内容。

## 许可证

麻省理工学院

</article>

[View on GitHub](https://github.com/apvarun/learndevtools)

Previous Post: [Conditional Breakpoints](https://dev.to/apvarun/chrome-devtools-conditional-breakpoints-444j)