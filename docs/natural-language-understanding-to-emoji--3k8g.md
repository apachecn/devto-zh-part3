# 表情符号🗣️的自然语言理解

> 原文：<https://dev.to/alexayala/natural-language-understanding-to-emoji--3k8g>

很长一段时间以来，我一直想把我的工作放在一起，这样我就可以展示自己，吸引新客户。我不可能把我作为一家私人公司开发人员的日常工作所写的代码发布到 Github 上🤷🏾‍♂️.所以，我从一些相对简单有趣的东西开始。我想到了一个小型网络应用的想法，它使用自然语言处理将表情符号与一段文字的情感强度相匹配。我刚刚完成了我的应用程序的初始 MVP，你可以在 nlpmoji.com 查看。欢迎在这里的评论中反馈！

我希望这个项目能帮助我继续锻炼和磨砺我的 Java 肌肉。虽然我是一个全栈开发人员，但我会做团队需要我帮助的任何事情。这些天来，这更倾向于我们特定项目的前端工作。我也想认同[每天写代码](https://johnresig.com/blog/write-code-every-day/)的想法，成为一名更好的开发者。优秀的文学作家不断地写作，不管它们是不是好主意，以保持这些技能的敏锐。

[![sharpened pencil](img/8281acba5ca5103c1fe3d62437d4dad2.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--q4x-c_MS--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images.pexels.com/photos/159731/pencil-education-pencil-sharpener-art-159731.jpeg%3Fauto%3Dcompress%26cs%3Dtinysrgb%26dpr%3D2%26h%3D650%26w%3D940)

# 堆栈

### 打字稿

每当我用几十行 JavaScript 为任何持久的 web 应用程序做前端开发时，这是我必须要做的。许多开发人员仍然对 JS 的静态类型有点担心，但是一旦你尝试了，如果你做得对，你就不会想用其他方法了。

### 做出反应

必须爱我的虚拟世界和 JSX。

### 自举 4

网页开发的视觉设计方面可能是我最不感兴趣的部分。我把核心组件的设计留给真正知道自己在做什么的人；所以 Bootstrap 对我来说是最简单的选择，但我可能会在以后做一些设计上的调整。

### Java +春季开机❤️

和 Spring Boot 一起工作绝对是一种享受。从简单的设置到文档和资源，几乎可以做任何事情。

# 基础设施

由于这是一个小的业余爱好项目，我想尽可能降低成本。有了 below Gitlab、Google App Engine 和 Firebase 的组合，除非我收到大量意外流量，否则我应该能够免费运行我的整个应用程序堆栈。

### GitLab

我还没有准备好开源这个项目，所以在这一点上，我在寻找一个能给我私人回购的解决方案。Gitlab 的问题管理功能和它提供的漂亮的用户界面也很棒。我利用这个机会创建了自己的看板，用来跟踪我希望为项目 MVP 完成的项目。

### 谷歌应用引擎

GAE 资源公司一年 300 美元的优惠信贷，以及比 AWS 更便宜的价格，我决定和谷歌一起部署这个东西。无论如何，我没有和 GAE 结婚，我可以切换到另一个平台服务提供商，而不用太大惊小怪。

### 火基

如果您只是有一些想要免费托管的小静态文件，这是一个很好的选择。

# 服务

*   [IBM Watson 自然语言理解 API](https://www.ibm.com/watson/services/natural-language-understanding/) 用于实际的 NLP 重载。我不太在乎在我的应用程序的这一部分重新发明轮子，他们每个月都免费提供相当数量的交易。
*   [Twitter SDK](https://developer.twitter.com/en/products/tweets) 用于 Twitter 自然语言处理。如果你保持在他们的利率限制之下，他们也有相当数量的交易可以免费进行(我确保通过在应用程序的后端实现 Guava 的利率限制器来做到这一点)。