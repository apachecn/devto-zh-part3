# glitch-local dev–我做了一个 node.js 应用程序！我是未来！

> 原文：<https://dev.to/letmypeoplecode/glitch-localdev-i-made-a-node-js-app-i-am-future-1a4b>

[!["80s retro futurey image"](img/a9c1a563f1b0071ec62f78c479d94e0e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--vyF6RBv5--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bbnpj501oqxejnzokcm9.jpg)

我最近做了一个节点应用，叫做 [glitch-localdev](https://www.npmjs.com/package/glitch-localdev) 。它可以帮助你在 [Glitch](https://glitch.com) 上开发应用，将其克隆到你最喜欢的机器上，使用你最喜欢的编辑器进行开发，并帮助你的机器和 Glitch 保持同步。如果你使用的是 [Visual Studio 代码](https://code.visualstudio.com/)，它甚至会为你设置项目的自动保存。

很容易设置。用`npm install -g glitch-localdev`安装。

然后只需在命令提示符或终端窗口中导航到一个空目录，并键入`glitch-localdev`。它会问你一些配置问题(这些问题在 [glitch-localdev 自述文件](https://www.npmjs.com/package/glitch-localdev)中有解释)，然后砰的一声，它拉下存储库并开始关注它，不时地将你保存的更改提交回 glitch。

它保存您的配置。所以当你回到它的时候，只需要在目录中运行`glitch-localdev`来启动 watcher 函数，在你编码的时候留意变化并定期提交它们。

玩得开心，并随时在 [GitHub repo](https://github.com/YiddishNinja/glitch-localdev) 上提出问题、错误或建议