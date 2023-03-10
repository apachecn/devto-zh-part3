# ngrok 的快乐世界

> 原文：<https://dev.to/lssweatherhead/the-happy-world-of-ngrok-45km>

当前最喜欢的工具: **ngrok**

*   你有没有想过与产品经理、项目经理甚至其他开发人员分享一些仍在开发中的东西，以便他们可以快速点击一下，也许给你一些提示，但不要太激烈？
*   你是否曾经使用过第三方服务(Mailchimp、SendGrid、SparkPost、Twilio、Authy，等等等等)并希望使用 webhook 在开发网站上传输数据？
*   你是否曾经在一次开发演讲中想到“如果人们可以访问我在本地运行的这个站点就太好了”，但是你不想部署任何东西？

Ngrok 就是答案。这是一个命令行工具，使用一个安全的 URL 和一个优秀的调试/检查面板来访问您的本地服务器。

这是最好的事情。不，真的。我很容易在技术前沿偶尔发飙(漫无边际地谈论如何找到完美的显示器配置可以等同于找到真爱)，但在这一点上相信我。

你应该像我一样热爱 ngrok 的理由:

1.  免费的！(我的意思是，如果你想要自定义子域等，你可以付费，但对于大多数开发/测试需求，免费版本是它所在的地方)
2.  就像 cmd `ngrok http 80`一样简单，然后砰的一声，你就可以开始运行了
3.  但是如果你运行在 IIS express 上呢？那也超级简单:`ngrok http -host-header="localhost:[port-number]" [port-number]`
4.  检查面板出乎意料地有用，我从来没有感觉到被工具如此支持。我的意思是，我喜欢 Fiddler，但是它的功能太多了，我经常觉得我少给了它一点...(就像有人给我建了一座豪宅，我已经搬进了储藏室)

澄清一下，我和 ngrok 或者它的开发者没有任何关系，我只是对加速我自己的开发过程感到兴奋！

在这里下载:[https://ngrok.com/download](https://ngrok.com/download)