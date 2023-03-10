# 使用新的 Netlify Addon CLI 命令在 2 分钟内启动 Netlify 上的 Gatsby 站点

> 原文：<https://dev.to/takeshape/how-to-use-netlify-addon-and-dev-with-gatsby-and-takeshape-1n4o>

TLDR:向下滚动到示例，看看如何使用`netlify addons:create takeshape --template shape-portfolio`和`netlify dev`。

用 TakeShape 和 Netlify 推出一个新的盖茨比项目已经很容易了；但是我们不仅仅希望 TakeShape 简单易用，我们还希望 TakeShape 超级简单易用。这就是为什么我们对 Netlify 的 CLI 中的两个最新添加感到非常兴奋:`addons`和`dev`。获得一个运行盖茨比的实时网站，其内容由 TakeShape 提供支持，只需不到两分钟。

这里有一些方法来度过这额外的三分钟:

*   听 3 集 [60 秒科普](https://www.scientificamerican.com/podcast/60-second-science/)🎧
*   向[慈善事业](https://www.classy.org/give/77372/#!/donation/checkout)捐款💰
*   思考 JAMstack 🧘(ommm-JAMstack-ommm 的好处 3 分钟)

我们将分别讨论每个新命令，然后一起看它们的实际操作。

**命令`netlify addon`命令**

附加组件是 Netlify 的一个新特性，允许你为你的 Netlify 托管的站点快速提供外部服务。除了创建插件命令，Netlify 也在建立一个插件市场。我们希望使用 TakeShape 的开发者能够成为这一令人敬畏的新功能的早期采用者。

**The`netlify dev`command**
Netlify 的联合创始人马特·比尔曼(Matt Biilman)在最近的 JAMstack_conf_nyc 上宣布了 dev 特性。他的演示非常棒，我建议大家去看看，尤其是他用 vim 编写代码的那部分:

[https://www.youtube.com/embed/RL_gtVZ_79Q](https://www.youtube.com/embed/RL_gtVZ_79Q)

Dev 允许您在本地 dev 机器上虚拟化 Netlify 栈。这太棒了。这给开发者带来的力量是不可低估的，我不得不感谢 Netlify 不断提升开发者的体验。要使用`netlify dev`命令，您需要安装最新版本的 Netlify CLI 运行`npm install netlify-cli -g`。

以下是您可以用它来做的一些事情:

*   为您正在使用的构建工具启动一个本地开发服务器
*   `netlify dev:exec <command>`在网络开发环境中运行 shell 命令
*   `netlify functions:create`引导一个新功能

还有一件事！
`netlify dev --live`让您在一个实时 url 上与任何人共享您的开发环境！什么！太神奇了！

查看[报告](https://github.com/netlify/netlify-dev-plugin)以获取完整文档。

例-把它们放在一起！使用 TakeShape 的 Netlify 附加组件与盖茨比
一起使用时，TakeShape 附加组件在与盖茨比这样的外部 SSG 一起使用时确实会大放异彩。该插件通过做三件事使您的生活更轻松。

开始了。

1.  **设置网络寿命**
    *   叉[形-投资组合-盖茨比 js](https://github.com/takeshape/shape-portfolio-gatsbyjs) 回购🍴
    *   访问[https://app.netlify.com/start](https://app.netlify.com/start)从分叉回购创建一个新的网络生活网站🆕
    *   克隆分叉的`shape-portfolio-gatsbyjs`回购👯
    *   运行`netlify link`将工作目录链接到网络站点🔗
2.  **采取附加形状**
    *   运行`netlify addons:create takeshape --template shape-portfolio`🎶![TakeShape successful add-on provisioning](img/6729e494e37ba58a3fb844e9d2968942.png)
    *   运行`netlify addons:auth takeshape`🔐
    *   按照说明创建一个 TakeShape 帐户，并申请✅take shape 项目
3.  **改变内容**
    *   改变 takeshape 项目中的一些内容
    *   从 Netlify，观察 Gatsby 在 Netlify 上的构建，访问部署站点，庆祝🕺
4.  **本地开发**
    *   你可能想做一些代码修改，做一些本地开发。这就是新的`dev`命令的用武之地。从您的本地工作目录，运行`netlify dev`。这就解决了在本地环境中配置 TakeShape API 键的困难工作。修改您的代码，提交到 git，然后您的站点将根据您的修改重新部署。

你完了！这让你在短短几分钟内就能和 TakeShape、Gatsby 和 Netlify 一起上手。您可以就此打住，开始修改副本。您现在有了一个可以与全世界共享的 URL！

**下一步是什么**
我们希望开发人员能够更快地完成这项工作！我们正致力于从任何一个 TakeShape 示例项目 Github repos 中直接点击一下，就能启动一个 TakeShape powered 站点。