# 我们如何提高 npm 生态系统的安全性？

> 原文：<https://dev.to/nepeckman/how-do-we-improve-security-in-the-npm-ecosystem-3hmj>

对于那些没有在其他地方看到这种趋势的人来说，[一个流行的 npm 库在受害者的计算机上执行恶意代码](https://github.com/dominictarr/event-stream/issues/116)。总结一下思路(尽管值得一读),库的维护者将控制权交给了一个声称想要维护它的未知个人。这个人添加了一个旨在执行某种恶意代码的依赖项，人们仍在试图弄清楚有效载荷是做什么的。虽然很多人都在玩指责游戏，但我对讨论可以采取哪些实际步骤来限制这种攻击媒介很感兴趣。我们是否应该为放弃 npm 模块的控制权建立更严格的流程？我们唯一的希望是更好的审计工具吗？我对任何解决这个安全问题的想法都感兴趣。