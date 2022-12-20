# Protip 故障排除

> 原文：<https://dev.to/eroberts/troubleshooting-protip-24g0>

[![](img/7abf81506276dbf5a0a336b861e44b1d.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sStZppi2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://media1.tenor.cimg/55581635532c6f91050ad6558140ffb9/tenor.gif%3Fitemid%3D5213422)

很多次我发现自己试图找出一个问题，无论是故障诊断代码还是配置、操作系统甚至硬件的问题。在几个小时的碰壁困惑之后，我意识到这个问题很简单，我应该从它开始。通常，人们会忽略这个简单的解决方案，因为他们认为这一定是另一个原因。

## 过度自信

[![](img/9154343792e4c08d20520fe46c15bce1.png)](https://i.giphy.com/media/V0fYT9sYUK1J6/giphy.gif)

即使你有多年的经验，你也不能避免同样的问题。如果你是初学者，你不应该假设那些比你更有经验的人已经遵循了每一个基本的故障诊断程序。

永远从头开始。首先从最简单、最容易的解释开始故障诊断。

[![](img/48390f7aa5c388b8917834d5e6c4fb18.png)](https://i.giphy.com/media/Oe4V14aLzv7JC/giphy.gif)

代码不起作用？检查拼写错误、基本语法错误等。电脑坏了？重启。确保所有设备都插上电源并打开。
已经重启了，你还在犹豫吗？重新启动它！

[![](img/4b6cba3d37d6249b9ce2930e75060e79.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--9d7Xl3is--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://images-na.ssl-images-amazon.cimg/I/8166xCVDGnL.jpg)

[橡皮鸭](https://en.wikipedia.org/wiki/Rubber_duck_debugging)技术就是基于这个想法，在这里你解释你的代码应该以最小的增量对某人(或某事)做什么。这迫使你分析你可能忽略的代码的每一部分，因为你假设它按照你想要的方式工作。

这个最基本的建议为我节省了无数时间，同样，不遵循这个建议所浪费的时间比我愿意承认的还要多。

如果你有一个困扰你的被忽视的简单问题的恐怖故事，请在评论中分享。