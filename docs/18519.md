# 嘿谷歌，哟 Alexa...为我做这件事

> 原文：<https://dev.to/joehobot/hey-google--yo-alexa--do-this-for-me-388f>

几个月前，我接受了一个关于将谷歌家庭助手与 GKE(谷歌 Kubernetes 引擎)集成的业余爱好任务。我到了能够说嘿谷歌构建集群的地步。它开始尝试构建一个集群，但是在 API 查询方面做得并不好。

昨晚我在实验室用 Lambda 函数工作，它应该可以执行几乎所有的 kubectl 命令，甚至可以构建一个集群。这篇文章帮助我完成了一半的战斗。

我的观点是:

从 DevOps 的角度来看，我真的希望 2019 年的一些低端任务能够利用语音命令来提高工作效率。

我想举几个例子:

**我:**有多少豆荚形状不好？
**Alexa/Google:** Goes 查询任何不在“运行”状态的东西。
**Alexa/Google:** 嗨 Joe，有 16 个 pod 没有处于运行状态，你想要这个列表吗？
**我:**是请

或者作为开发人员...因为有人问我我们在开发和 QA 中是什么版本

**我:** (Google/Alexa)我在 Dev 和 Qa 中运行的 AppName 是什么版本？
**Alexa/Google:**Goes do a describe on a pod/container in Dev and QA
**Alexa/Google:**AppName 版本:1.15.2 in Dev 和版本:1.15.3 in QA
**ME:** 谢谢

或者作为 Ops...

**我:** (Google/Alexa)有多少节点形状不好？
**Alexa/Google:** 查询节点状态，很少有未就绪或类似的..
**Alexa/Google:** 节点 222.333.111.222 处于未就绪状态，最新事件:OutOfDiskSpace
**ME:**:(Google/Alexa)耗尽节点 222.333.111.222 并将集群扩展到 45

因此，这些是一些简单的任务，在 2018 年实际上应该可以自我修复，但如果您的管道和集群以及稳定性没有达到生命的关键点，至少您可以在晚上 11 点停止访问您的 PC，连接到 VPN，使用 K8s 集群进行身份验证，执行几个命令，现在是凌晨 12:30，因为您在晚上 11:25 修复了原始问题，但却忙于阅读其他内容:)..

你用过类似的东西吗？也许是聊天机器人？