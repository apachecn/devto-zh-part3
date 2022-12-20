# 是时候抛弃流而支持类型脚本了吗？

> 原文：<https://dev.to/pajasevi/is-it-time-to-ditch-flow-in-favor-of-typescript-2jjc>

最近，我目睹了许多迁移到 Typescript 的项目，不得不问自己同样的问题。是时候停止使用 Flow 而开始使用 Typescript 了吗？

我已经看到很多开发者对使用 Flow 对他们的 Javascript 应用程序进行类型检查的强烈反对。其中一封甚至来自一位前流程开发人员:

> 我放弃 Flow，转而支持 TypeScript...你也应该这样。
> 
> 流量就是不在乎社区...我说我在脸书的心流团队工作过。
> 
> 脸书应该全部降流。在这一点上，即使对他们来说，这也是一项糟糕的投资
> 
> — Jamie 🏳️‍🌈 (@jamiebuilds) [19\. listopadu 2018](https://twitter.com/jamiebuilds/status/1064649666275340288?ref_src=twsrc%5Etfw)

然后又来了一条，是我一个用心流很久的朋友发来的:

> 好吧， [@flowtype](https://twitter.com/flowtype?ref_src=twsrc%5Etfw) ， [@AtomEditor](https://twitter.com/AtomEditor?ref_src=twsrc%5Etfw) ，核素对我来说都死了。 [@code](https://twitter.com/code?ref_src=twsrc%5Etfw) 和 [@typescriptlang](https://twitter.com/typescriptlang?ref_src=twsrc%5Etfw) FTW。
> 
> — este (@estejs) [30\. listopadu 2018](https://twitter.com/estejs/status/1068334989241069568?ref_src=twsrc%5Etfw)

然后我读到了 Apollo (GraphQL 客户端)的评论，声称他们将不再支持 Flow，并转向 Typescript。

当然，Flow 有很多问题，因为它仍然不稳定，所以更新你的应用程序以使用最新版本非常烦人。但是我喜欢将静态类型添加到 Javascript 中的想法，而不是必须使用另一种语言，如 Typescript。

也就是说，Typescript 肯定有它的好处，如抽象/受保护/私有类、方法和属性，并且有一个很好的社区为几乎每个库提供类型定义。

所以，是的，我正在考虑重写我目前的项目，以使用 Typescript。

心流还有希望吗？它是否还有一些您不会迁移到 Typescript 的好处？