# LWC——实验装饰者的错误

> 原文：<https://dev.to/brettmn/lwc-experimental-decorators-error-i51>

[https://www.youtube.com/embed/aDTibiYKKBo](https://www.youtube.com/embed/aDTibiYKKBo)

你好，我是布雷特和 WIPDeveloper.com。

有一天，在使用 Lightning Web Component 时，我注意到在 Visual Studio 代码中，当我使用 wire decorator 时出现了一个错误，该错误说“对 decorator 的实验性支持是一个在未来版本中可能会发生变化的特性，请设置实验性 decorator 选项以删除该警告。”

现在，看着示例应用程序和 Salesforce GitHub repo，我没有改变任何东西。

没有解释这是从哪里来的或者发生了什么变化。我想可能是因为我用的是 Visual Studio Code Insiders Edition。其中一个更新开始使用 TypeScript，因为如果你看消息的结尾，它说 TS，然后是括号 1219。所以我认为它在使用类型脚本检查是否使用了实验性的装饰器。

因此，因为我可以，所以我仍然可以部署它。所以我需要工作，以便我可以摆脱红色的曲线，因为我不喜欢红色的曲线。

所以我做的是创建一个 Ts 配置文件。

它有编译器选项，这些选项是实验性的装饰器，允许 JS 为真。

#### tsconfig.json

```
{
    "compilerOptions": {
        "experimentalDecorators": true,
        "allowJs": true
    }
} 
```

你是否需要 allow JS，我不知道。但这是我在互联网上找到的例子，它对我很有效，它去掉了红色的曲线。

这主要是为了方便起见，因为我能够进行部署，但是如果您想通过查看 prop problems 选项卡标题来了解您是否有任何问题，这将有助于澄清这一点，因为您可以看到我们没有 TS 配置。

我们总是会发现我们有一个问题。

如果你有多个衬里 web 组件，它们有多个装饰器和用途，你可能很容易忘记这是不是一个问题，或者是不是编辑器没有正确地识别出这是一个装饰器的正确使用。

## 现在就这样。

记得报名参加 **[每周一次的单口相声！你可以得到我们关于 WIPDeveloper.com 的任何最新信息。](https://wipdeveloper.com/newsletter/)**

帖子[WIPDeveloper.com](https://wipdeveloper.com/lwc-experimental-decorators-error/)最先出现在[上【LWC——实验装饰师的错误](https://wipdeveloper.com)。