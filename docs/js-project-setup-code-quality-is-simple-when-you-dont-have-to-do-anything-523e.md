# JS 项目设置:当您不需要做任何事情时，代码质量是简单的

> 原文：<https://dev.to/sammyisa/js-project-setup-code-quality-is-simple-when-you-dont-have-to-do-anything-523e>

JavaScript、Web 和 Node 社区最近很幸运，因为我们已经有了易于使用(和不那么易于使用)的工具，以及由社区专门成员为它们编写的文档。

这篇文章是关于用一些众所周知的工具建立一个最简单快捷的管道，比如用于代码质量/可读性的[更漂亮的](https://prettier.io/)，以及用于在我们的代码库中自动执行那些规则的[哈士奇](https://github.com/typicode/husky)。

在后面的一些帖子中，我们将添加构建步骤、测试和一些特定于技术的工具，如 React 和 TypeScript。然而，今天我们保持简单和干净，只有漂亮和沙哑。

此外，请记住，许多现有的工具和框架，如 Create React App、Gatsby 等，已经提供了这里提到的一些工具。然而，我们从零开始，因为我们的目的主要是学习这些工具，并理解为什么使用它们。

一旦我们理解了建立这些代码质量工具是多么容易，那么在您自己的项目中使用这些工具就少了一个障碍。

## 但是萨米，为什么？

> "不能自动化的，就不能强制执行."
> -布莱恩·霍尔特，[@霍尔特布](https://twitter.com/holtbt)

高质量、熟悉、一致的代码更容易阅读和理解。此外，与您自己的代码相似的代码更容易理解。

* * *

## 我该如何开始？

好了，第一步很简单:创建一个新的 npm 项目，创建一个 JS 文件，然后编写你所编写过的最难看、最肮脏、最不可读(但可以工作)的代码。忽略风格规则，最佳实践，忽略人们在 Twitter 上说的话。把那从你的系统里取出来。

现在你面前有一堆乱七八糟的东西，我们的两个工具中的第一个开始发挥作用:

## 漂亮些

因为有时候我们不可信。

我们用的是更漂亮的版本。

Prettier 是一个代码格式化程序——这意味着它会抓取你的代码，并在代码上应用它自己一致的风格规则。它有一定的灵活性，但通常开发人员使用默认规则来保持项目间的风格一致。

更漂亮的将把这个不一致的一行字搞乱:

```
export function funkyFilteredNames(names = [...defaultNames]) { return names.filter((name) => name.toLocaleLowerCase()[0] == "a").map(name => name.toLocaleUpperCase()); } 
```

这段可读的代码片段:

```
export function funkyFilteredNames(names = [...defaultNames]) {
  return names
    .filter(name => name.toLocaleLowerCase()[0] == "a")
    .map(name => name.toLocaleUpperCase());
} 
```

如何做由你决定。您可以将 Prettier 用作 CLI 工具，或者让您选择的文本编辑器来处理它。为了保持简单(并让你使用任何你想要的),我们将使用更漂亮的 CLI。

如果你想知道如何在你的 IDE 上安装得更漂亮，看看他们关于这个问题的文档。

先做最重要的事——装漂亮点:`npm i -D prettier`。

现在帮我一个忙:打开你乱糟糟的代码所在的文件，在你的文件所在的目录下打开命令行。查看您的代码时，输入以下命令:

```
npx prettier *.js --write 
```

然后嘣，不再乱！

恭喜你，你的代码库中有代码风格规则了！为了使这更容易，我们将把上面的代码片段变成一个 npm 脚本，做一些修改就可以找到所有的 JavaScript 文件:

```
// package.json:
{
  // ...
  "scripts": {
    // other scripts
    "pretty": "prettier \"**/*.js\" --write"
  }
} 
```

现在你只需要从你的项目中运行`npm run pretty`来格式化所有的 js 文件！

恭喜你，你让事情变得简单了！✨

你也可以更漂亮地检查代码而不是格式化它，并且在代码没有格式化时报错控制台:

```
"pretty-check": "prettier \"**/*.js\" --list-different" 
```

但是如何提醒自己运行格式化程序呢？你完全确定你每次都会跑得更漂亮吗？这就是我们今天的第二个工具:哈士奇！

## 沙哑

*因为我们太容易忘记*

我们用的是哈士奇版`1.3.1`

🐕Husky 让我们更容易编写 Git 挂钩——当我们调用特定的 Git 操作时运行的命令。我们将使用它来确保 Prettier 在提交到存储库时检查代码。

husky 的步骤很简单，从配置文件开始，它定义了我们的钩子！

在项目的根目录下创建一个名为`.huskyrc`的文件，并在下面的文本中定义一个**钩子**，它将在每次**提交** :
之前运行**漂亮检查**

```
{
  "hooks": {
    "pre-commit": "npm run pretty-check"
  }
} 
```

然后我们安装 Husky: `npm i -D husky`

注意:在定义了`.huskyrc`文件之后安装 Husky *是很重要的。如果有的话，你总是可以重新安装软件包。此外，您应该删除。git/hooks 文件夹，如果 Husky 仍然不工作，然后重新安装软件包。你可以在本期 GitHub 中读到更多关于它的信息*

现在转到你的一个文件，写一些难看的代码。没有必要像上次一样弄得一团糟，一个单引号字符串就足够了！

保存并尝试提交文件，您将在响应的末尾看到:

[![Prompts from Husky, rejecting the commit](img/c486fcf0f00c2d0108fb482d68c56ac5.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--85moLpdK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://i.imgur.com/Mg1jDcb.png)

Husky 拒绝提交，因为 prettle 检查了代码，发现代码不符合 prettle 标准！

如果你愿意，你可以改变预提交钩子来格式化代码，而不是仅仅通过改变``"pre-commit"`的内容来执行`pretty`而不是`pretty-check`来检查代码。

恭喜你，你让它自动运行了！✨

* * *

如今，用格式化程序设置工作空间并不困难，而且它还能极大地提高代码的可读性。

Prettier 是当今使用最多的 JavaScript 格式化程序之一，每周下载量接近 350 万次，开发人员在使其易于使用和理解方面做了大量工作。

Husky 允许我们编写 git 提交钩子，而不需要太多的开销或努力。结合更漂亮，它使得很难提交与更漂亮风格指南不一致的代码。

我已经为您建立了这个小项目，来检验我在文章中描述的步骤:

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[【samyisra】](https://github.com/SammyIsra)/[【jseasy pipeline】](https://github.com/SammyIsra/JsEasyPipeline)

### 带有管道和代码质量工具的 JS 项目示例📝

<article class="markdown-body entry-content container-lg" itemprop="text">

# jseasyppipeline

关于实现 JS 管道/工作空间工具<g-emoji class="g-emoji" alias="memo" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f4dd.png">📝</g-emoji>

* * *

回购将有关于如何实现各种工具的例子，如漂亮，Husky，ESLint，TravisCI，TypeScript，以及类似的风格的工具。

* * *

这个项目展示了如何为 JavaScript 项目实现项目工具。我解释这些工具的文章在这里:

1.  [更漂亮更壮实](https://raw.githubusercontent.com/SammyIsra/JsEasyPipeline/master/)

</article>

[View on GitHub](https://github.com/SammyIsra/JsEasyPipeline)

查看分支`Part1.PrettierAndHusky`中专门针对本文的代码。

* * *

❤️感谢大家的阅读！本系列的更多部分将在后面介绍。我希望在未来的文章中使用 ESLint、TravisCI 和我自己的个人工具。