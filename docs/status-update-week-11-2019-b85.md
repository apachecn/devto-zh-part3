# 状态更新(2019 年第 11 周)

> 原文：<https://dev.to/fmvilas/status-update-week-11-2019-b85>

朋友们！对我们来说，这是激动人心的一周。 [Solace](https://solace.com) 作为[白金赞助商](https://opencollective.com/asyncapi)加入了 AsyncAPI 计划🎉。谢谢你， [Jonathan Schabowsky](https://twitter.com/JSchabowsky) ，领导这个过程。如果这还不够，我们完成了我们的“[协议映射](https://github.com/asyncapi/asyncapi/milestone/2?closed=1)里程碑。继续读！

## 规范的最新变化

1.  [协议特定信息](https://github.com/asyncapi/asyncapi/issues/42)。这是一个期待已久的功能。它允许您在通道、操作和消息中定义特定于协议的信息。

2.  [将应用报头与协议报头分开](https://github.com/asyncapi/asyncapi/issues/112)。从现在开始，所有与协议相关的头必须包含在一个`protocolInfo`对象中，现有的消息`headers`必须只用于应用程序头，如果有的话。

3.  [将一个通道参数标注为多级](https://github.com/asyncapi/asyncapi/issues/171)。你想告诉你的代码一个特定的参数应该跨越多个级别吗？现在，您可以注释通道参数来表明这一点。很像 AMQP 和 MQTT 中的`#`(磅)符号或者 NATS 的`>`(大于)符号。

4.  [统一实例字段](https://github.com/asyncapi/asyncapi/issues/172)。过去，我们在模式中有`example`字段，在规范的其他地方有`examples`字段。它们现在统一使用复数形式，并允许每个对象有多个示例。然而，我们没有移除模式中对`example`字段的支持，因为我们希望保持与 OpenAPI 模式的兼容性。

<figure>

[![Cumulative flow chart showing the progress of AsyncAPI 2.0.0.](img/a5981e16ec7b26fa8fb0058ef4e52592.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2LUX-Fnv--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/t6vuotpn6a5jup9yc0wp.png)

<figcaption>Cumulative flow chart showing the progress of AsyncAPI 2.0.0.</figcaption>

</figure>

<figure>

[![Release report showing the planned and predicted end dates. We’re 38 days ahead of plan! 🔥](img/c3878016ccac195960274a4fa0bc605c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--IofvrH0x--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/n5y7rvy96ixwaix85g7h.png)

<figcaption>Release report showing the planned and predicted end dates. We’re 38 days ahead of plan! 🔥</figcaption>

</figure>

## 捐赠

最后但同样重要的是，我们已经完成了前三周的赞助活动。我们非常感谢来自 [Solace](https://solace.com) 的支持，并期待着一起工作。在接下来的几周里，你会听到更多激动人心的消息。敬请期待！

记住，我们有不同的层次，这样每个人都可以表达他们的爱！❤️

[捐到这里](https://opencollective.com/asyncapi)。帮助开源项目。

* * *

> “允许自己慢下来。你可以通过减速来加速。”
> 
> —加贝·伯恩斯坦

下周见，伙计们！👋