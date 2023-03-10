# 孔雀 v1 发布

> 原文：<https://dev.to/azure/peacock-v1-released-4kce>

你有没有发现自己在 VS 代码的多个实例之间切换，同时试图找到你要找的那个？我经常打开多个实例进行编码、写作(比如本文)，以及我对文本做的几乎所有事情。能够快速识别每个实例非常有帮助。

我曾经手动切换 VS 代码的几个关键方面的颜色，以便区分它们。我在会议上使用了这种技术，我在会上做了演示，发现它对观众很有帮助，这样他们也能识别我的代码。我最终决定自动化这一点。那就是[孔雀](https://marketplace.visualstudio.com/items?itemName=johnpapa.vscode-peacock&wt.mc_id=devto-blog-jopapa)的由来。

[https://www.youtube.com/embed/0ZXjGBxK_es](https://www.youtube.com/embed/0ZXjGBxK_es)

> 在发布 v1 之前，我想要的一个关键方面是准备好大量的单元测试，并将其与 CI 结合起来。我决定使用 Azure DevOps 和他们的 YAML 选项。我对结果很满意。一个很酷的方面是，我正在 Windows、Linux 和 macOS 下对 Node 8 和 Node 10 进行测试。我会在以后的文章中写更多关于如何在 Azure DevOps 上工作的内容。

## v1 到了！

周末刚发布了[孔雀 v1.1.0](https://marketplace.visualstudio.com/items?itemName=johnpapa.vscode-peacock&wt.mc_id=devto-blog-jopapa) ！

自从我发布并宣布了孔雀的预览版，很多人似乎都很喜欢它，并且有几个人对 OSS 的扩展做出了贡献。

## 它能做什么？

很棒的问题！你可以点击查看完整的[文档。但是这里有一个主要特征的快速浏览。](https://github.com/johnpapa/vscode-peacock/blob/master/README.md)

*   更改标题栏、状态栏和/或活动栏的颜色
    *   输入您自己的颜色(十六进制、rgb、hsl 等)
    *   用一种随机的颜色感到惊讶
    *   选择角度、vue 或反应的主要颜色
    *   从收藏夹中选择用户定义的颜色
*   在收藏夹中保存一种颜色
*   通过使受影响的元素稍微变暗或变亮来调整它们的颜色，以在它们之间提供微妙的视觉对比

你可以在你的用户设置中选择哪些元素应该被着色。

[![settings](img/f90150cbfeee313f128bf440ee7a3063.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Y4pxnjPw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/8mktbarrrkgtos12fibg.jpg)

## 得到孔雀

如果你有孔雀，想更新到 v1.1.0，VS 代码会很快提示你。

如果你有兴趣试用孔雀，你可以在这里的市场找到它。

## 投稿

贡献给 GitHub 库[这里](https://github.com/johnpapa/vscode-peacock?wt.mc_id=devto-blog-jopapa)

谢谢！