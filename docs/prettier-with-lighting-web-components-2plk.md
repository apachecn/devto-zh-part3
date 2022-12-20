# 使用灯光网络组件更美观

> 原文：<https://dev.to/brettmn/prettier-with-lighting-web-components-2plk>

[https://www.youtube.com/embed/OdLXYjHuzqc](https://www.youtube.com/embed/OdLXYjHuzqc)

你好，我是 WIPDeveloper.com 的布雷特。如果您一直在 Visual Studio 代码中使用 Lightning Web 组件，并且安装了 Prettier 扩展，您可能会遇到这样的问题:他试图将一个属性分配给 HTML 上的一个特性。

## 一个比较漂亮的问题

如果我试图将标题从 web developer calm 字符串更改为属性标签，它将重新格式化并导致问题。我要点击“保存”,现在我有一个问题,“它更漂亮”自动在我的财产周围插入了引号。

Lightning Web Component linter 说，您不应该在属性名周围加上引号，以防止 beauty 自动在您的 lightning lab 组件的 HTML 模板上的属性周围插入双引号。

我们将不得不告诉更漂亮的忽略 HTML 文件。我们可以在我们的项目中这样做，这样我们就不必卸载扩展。

## 更漂亮的方案

我们创建一个名为`.prettierignore`的新文件

现在我们要做的不仅仅是忽略 HTML 文件，实际上这是从 René Winkelmeyer 那里得到的，我希望他不介意我说出他的名字，他在 Salesforce 工作，是一名开发宣传员。这是 Trailhead 示例应用程序中的一个漂亮或忽略文件。

我将要做的是，它告诉 prettle 忽略 CSS、HTML、SVG 文件、XML 文件以及`.sfdx`文件夹中的任何内容，这样 prettle 就不会检查这些内容的格式。

我们所做的就是将它添加到我们的`.prettierignore`文件中。现在当我们去掉双引号时，

我们按保存。它不起作用。我拼错了什么？

我在哪里创建了`.prettierignore`文件

你必须在你的项目的基础上创建它，我没有给予足够的关注。

现在我们可以删除双引号了。

按保存它漂亮没有重新插入双引号。贷方对我们的语法没有问题，我们可以继续使用

属性，我将提供一个例子`.prettierignore`的链接，我也将有下面的全部内容。

#### `.prettierignore`

```
**/lwc/** /*.css
**/lwc/** /*.html
**/lwc/** /*.svg
**/lwc/** /*.xml
.sfdx 
```

## 链接

*   [。prettierignore 示例](https://github.com/trailheadapps/purealoe-lwc/blob/master/.prettierignore)
*   勒内·温克尔梅尔

## 现在就这样。

记得报名参加 **[每周一次的单口相声！你可以得到我们关于 WIPDeveloper.com 的任何最新信息。](https://wipdeveloper.com/newsletter/)**

帖子[beautiful with Lighting Web Components](https://wipdeveloper.com/prettier-with-lighting-web-components/)首先出现在 WIPDeveloper.com[的](https://wipdeveloper.com)上。