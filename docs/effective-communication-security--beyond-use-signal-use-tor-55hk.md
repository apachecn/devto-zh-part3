# 有效通信安全/超越'使用信号使用 Tor '

> 原文：<https://dev.to/ondrejs/effective-communication-security--beyond-use-signal-use-tor-55hk>

[![alt text](img/c75fe8b672682c5892a91f3f5299fd8d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--2TtOP4Z9--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/fdd6a79n0nzoxbsfatuz.jpg)

### 有效的通信安全/超越‘使用信号使用 Tor’。

献给生活在暴虐政权下的人们。

“工具优先”团队喜欢推进“使用${this}”，好像不管${this}是什么，它都会为您实现流程的所有序列。那么，任何未能解决实际威胁或提供适当保护的工具都会因未能解决任意的威胁模型而受到指责。这整个方法是倒退的。” Grugq

关键点:

与糟糕的通信实践相关的真正问题通常出现在端点上，而不是真正出现在软件/应用/协议上。请记住，技术的不良实施是人的问题。

笔记本电脑——尽可能多地使用短暂的信息应用，这些应用无法追踪到你的物理身份(例如**Ricochet-[https://Ricochet . im](https://ricochet.im)**)。另一个有好 UX 的选择可能是**CoyIM**([https://coy . im](https://coy.im))。

移动设备——一定要使用**信号/电线**，优先选择加密语音通话而不是短信，或者确保在所有敏感对话中使用自毁式短信。不要使用他们的基于电子的桌面版本(XSS 攻击媒介的可能性，但操作系统级访问是一个甜蜜的奖励)。还要意识到，这些应用程序与你的物理身份绑定在一起(与你的电话号码、电子邮件或简单的 IP 地址绑定在一起)，所以你不是真正的假名/匿名。

经验法则-没有记录，就没有犯罪。**避免日志**，即使在客户端。这也是避免 IRC 分享敏感信息/组织活动的原因之一。

避免使用专有的消息应用程序(如 Telegram、Messenger)。尽量总是使用经过专业人员审核的开源软件。

总的来说，笔记本电脑远不如 iOS 设备安全。即使是 Pixel Android 设备(一直打着补丁)也比笔记本电脑更安全。如果可能，使用 iPhone，总是更新到最新的 iOS 版本。不要越狱。如果使用 Android，不要 root 你的设备&不要启用开发者模式。对于两种平台，禁用云备份。需要密码才能解锁。如果可能，用预付费 SIM 卡注册信号/电线。

不惜一切代价避免通过电子邮件进行私人交流，即使是加密的电子邮件。请使用上面列出的沟通渠道。

**启用双因素认证(YubiKey，Google Auth。、Duo、Authy)。**

如果你想在任何社交媒体/事件分享平台(如博客、Twitter feed)上展示你的想法&未来计划，一定要使用 Tor(注意:Tor 并不是指极不安全的 Tor 浏览器)。

启用全硬盘加密。如果你的机器被查封，只加密/home/ folder 通常是不够的。使用后请务必关闭 PC/Mac。切勿将解密密钥存储在未加密的驱动器上。

考虑使用 **VeraCrypt** 容器来存放敏感物品(除了全硬盘加密之外)，因为你可能会被法院**强迫向当局交出你的钥匙(这尤其与澳大利亚、加拿大、法国、挪威、俄罗斯和英国的公民相关)**。

永远不要用你的真实身份污染你的在线活动家身份。学会划分。

斯诺登简单的‘使用信号使用 Tor’声明有什么问题？

嗯，你最好首先使用你的端点(即你的大脑，我的朋友)的端点。考虑您的威胁模型，并采取适当的行动。不要仅仅依靠技术。**大多数严重的通信安全问题都是在终端上产生的(即用户的不良操作安全实践)。**

安全是一个整体的、永无止境的过程，而不是最终产品(引用 Bruce Schneier 的话)。

注意安全，我的朋友。