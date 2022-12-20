# 面向“高效”开发人员的十个 VS 代码插件

> 原文：<https://dev.to/wrrnwng/ten-vs-code-plugins-for-a-productive-developer-4380>

*最初发表于[warrenwong.org](https://warrenwong.org/ten-vs-code-plugins-for-a-productive-developer)。*

这个列表有一些明显的警告。这是我的工作流列表。我主要使用 TypeScript 和 React，但也使用 Python 处理一些数据。YMMV。

### [阿波罗](https://marketplace.visualstudio.com/items?itemName=apollographql.vscode-apollo)或[阿波罗](https://marketplace.visualstudio.com/items?itemName=Prisma.vscode-graphql)

我可以用 Prisma 插件让 GraphQL 高亮显示在 TypeScript 上工作，但在某一点上不能用 Apollo 插件。我不太确定现在是否一切都好，因为我懒得拿掉一个去检查。我认为它们现在都工作了，所以这些插件可能是多余的。哦好吧。

### [自动完成标记](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-complete-tag)

这个插件据说结合了[自动关闭标签](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-close-tag)和[自动重命名标签](https://marketplace.visualstudio.com/items?itemName=formulahendry.auto-rename-tag)的功能，但是我还没有让它和 JSX 一起工作。如果你把这三个插件一起安装，或者只是把自动重命名和自动完成一起安装，看起来确实可以。使用 TypeScript 的一个烦恼是，插件不知道 JSX 和 TypeScript [类型断言](https://www.typescriptlang.org/docs/handbook/basic-types.html#type-assertions)之间的区别。VS 代码自带了自动完成关闭标签，用于`html`和其他一些模板文件，但是这些插件对于 JSX 文件仍然是必要的。

**你可以把这个算作一个插件或者三个插件。*

### [钴 2 主题官方](https://marketplace.visualstudio.com/items?itemName=wesbos.theme-cobalt2)

这个主题就像日晒黑暗，但更好。我听说材料 UI 主题非常受欢迎，如果我厌倦了这个主题，我可能会尝试一下，但我已经摇了一段时间了，它对我的眼睛来说很容易。

### [Chrome 的调试器](https://marketplace.visualstudio.com/items?itemName=msjsdiag.debugger-for-chrome)

老实说，我用得不多。我只是和其他堕落者一样。这是一个更有抱负的进入这个名单。

### [GitLens](https://marketplace.visualstudio.com/items?itemName=eamodio.gitlens)

这需要一点时间来适应，但我现在发现这个插件非常有价值。我通常不会注意到提交信息，除非我需要它。当我需要它的时候，它就在那里。

### [集所有功能于一身](https://marketplace.visualstudio.com/items?itemName=yzhang.markdown-all-in-one)

我用 Markdown 写博客，但这并不是这个插件如此有用的原因。虽然我过去常常在 Emacs 的 Org 模式下写笔记，但 VS Code 已经成为我的日常驱动程序，我也几乎已经切换到 Markdown 中记笔记了。这个插件有助于保持`md`文件的美观和格式化。

### [降价预览](https://marketplace.visualstudio.com/items?itemName=shd101wyy.markdown-preview-enhanced)

我能说什么呢？打字的时候并排看一个渲染过的 Markdown 文件是很不错的。这在编写一些 [LaTeX](https://www.latex-project.org/) 时非常有用。

### [漂亮码格式化程序](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode)

在过去，我在预提交钩子中更好地格式化了我的代码，但是那并不完全令人满意。我确保在保存时格式化 VS 代码。如果你在一个更大的团队中工作，确保每个人都使用更漂亮的代码来保持代码格式的一致性是非常重要的。它还节省了大量关于代码格式化的浪费时间的代码审查。

### [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python)

Python 对 VS 代码的支持变得非常好。

### [彩虹 CSV](https://marketplace.visualstudio.com/items?itemName=mechatroner.rainbow-csv)

如果你看了很多 CSV，这个插件真的很有帮助。每一列都是不同的颜色。还有一个插件,如果你需要处理 Excel 文件，我可以用它来渲染。