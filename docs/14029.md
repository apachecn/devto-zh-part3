# Webpack 5 中的新功能

> 原文：<https://dev.to/bnevilleoneill/new-features-in-webpack-5-49a>

[![](img/c0f30acc42a2ac0c33c73df2544d4c6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--brOaXoIu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/400/1%2A92BokALSOmNQ40kn57LeDg.png)

如果您正在捆绑前端资产，那么很可能您正在使用一个工具来做这件事。最有可能的工具是 Webpack。在本文中，我将分享 Webpack 5 的一些新特性，以及在日常工作中继续使用它时应该注意的事项。

这个新版本包含了大量的新东西，虽然我不会详细介绍每一个新特性，但我会分享核心团队概述的几个重要特性。

### 有什么期待

在撰写本文时，v5 版本仍处于早期阶段，可能仍会被破坏，然而，主要版本有突破性的变化，一些插件可能不再像预期的那样工作。Webpack 试图在可能的情况下提供兼容层，尽管一些更改使这变得困难(特别是关于额外运行时代码的注入)。如果一个插件不工作，你应该在这里报告它[(你也可以在这里](https://github.com/webpack/webpack/issues/8537)阅读完整的变更日志[)。**你也应该知道，对于 Webpack 5，最低支持的 Node.js 版本已经从 6 增加到 8。**](https://github.com/webpack/changelog-v5/blob/master/README.md)

一般来说，v5 版本关注几个关键组件:

*   使用持久缓存提高构建性能
*   用更好的算法和默认值改进长期缓存
*   清理内部结构而不引入任何破坏性变化，
*   通过现在引入突破性的变化为将来的特性做准备，并允许团队尽可能长时间地停留在 v5 上

再一次，这里是完整的[变更日志](https://github.com/webpack/changelog-v5/blob/master/README.md)，但是一定要确保阅读直到配置改变以保持最新。

为了测试 v5 的工作，您可以使用下面的命令安装它:

```
npm install —save-dev webpack@next 
```

这个命令引用了最新的 alpha 版本，但是您也可以使用它们的标签通过 Webpack 的存储库使用下面的命令安装 progress with v5 的不同版本:

```
npm install —save-dev webpack@v5.0.0-alpha.9 
```

如果你使用的是 Webpack v4 或更高版本，你还需要安装 [CLI](https://webpack.js.org/api/cli/) :

```
npm install --save-dev webpack-cli 
```

### 弃用项目

v4 中不赞成使用的所有项目在 v5 中都已删除。当迁移到 v5 时，确保您的 Webpack 4 版本不打印弃用警告。如果你遇到关于错误的问题，试着忽略统计选项或者不要使用预设。也就是说，事情仍然处于预发布阶段，所以最好通过 [GitHub](https://github.com/webpack/webpack/issues/8537) 询问 Webpack 团队。

有一些东西也被删除了，但在 v4 中没有反对警告，如 IgnorePlugin 和 BannerPlugin，现在必须向它们传递一个 options 对象。以下是一个可用于 IgnorePlugin 的示例，因为当前文档似乎没有对此进行概述:

```
new webpack.IgnorePlugin({ resourceRegExp: regex }) 
```

**参考:**

*   [https://web pack . js . org/plugins/banner 插件/#options](https://webpack.js.org/plugins/banner-plugin/#options)
*   [https://webpack.js.org/plugins/ignore-plugin](https://webpack.js.org/plugins/ignore-plugin/)

### **自动 Node.js 多填充移除**

过去，Webpack 的目标是允许在浏览器中运行大多数 node.js 模块，但是模块的前景发生了变化，现在许多模块的使用是专门为前端目的编写的。对于大多数 Node.js 核心模块，版本< = 4 附带了 polyfills，一旦模块使用任何核心模块，就会自动应用这些 polyfills。

这反过来将这些大的多孔填料添加到最终的管束中，但通常是不必要的。v5 中的尝试是自动停止聚合填充这些核心模块，并专注于前端兼容模块。

迁移到 v5 时，最好尽可能使用前端兼容模块，并尽可能为核心模块手动添加聚合填充(错误消息可以帮助指导您)。感谢/鼓励核心团队的反馈，因为这一变化可能会也可能不会出现在最终的 v5 版本中。

### **确定性组块和模块 id**

添加了新算法，以帮助长期缓存，并在生产模式下通过以下配置行启用:

```
chunkIds: "deterministic”,
moduleIds: “deterministic" 
```

这些算法以确定的方式给模块和块分配非常短(3 或 4 个字符)的数字 id。这是包大小和长期缓存之间的权衡。从 v4 迁移时，最好使用 chunkIds 和 moduleIds 的默认值。您也可以选择使用配置文件中的旧默认值:

```
chunkIds: "size”,
moduleIds: “size" 
```

这些行将生成更小的包，但为了缓存，它们会更频繁地失效。

**参考**

*   [https://web pack . js . org/configuration/optimization/# optimization-moduleids](https://webpack.js.org/configuration/optimization/#optimization-moduleids)
*   [https://web pack . js . org/configuration/optimization/# optimization-chunkids](https://webpack.js.org/configuration/optimization/#optimization-chunkids)

### **命名组块 id**

一个新命名的块 id 算法现在在开发模式下默认启用，它给出块(和文件名)人类可读的引用。模块 ID 由其相对于上下文的路径决定。块 ID 由块的内容决定，因此您不再需要使用:

```
import(/\* webpackChunkName: "name" \*/ "module") 
```

上面一行可以用于调试，但是如果您想要控制生产环境的文件名，它也是有意义的。在产品中使用 chunk ids:“named”是可能的，只是要确保不要意外暴露关于模块名称的敏感信息。

```
optimization: { chunkIds: 'named' } 
```

从 v4 迁移时，您可能会发现不喜欢文件名在开发模式下被更改。记住这一点，您可以传递下面的代码行，以便使用配置文件中的旧数值模式。

```
chunkIds: “natural” 
```

**参考**

*   [https://web pack . js . org/configuration/optimization/# optimization-chunkids](https://webpack.js.org/configuration/optimization/#optimization-chunkids)

### **编纂者**

编译器将被要求在使用后关闭，因为它们现在进入和离开空闲状态，并拥有这些状态的钩子。插件可能使用这些钩子来做不重要的工作(例如，永久缓存缓慢地将缓存存储到磁盘)。当编译器关闭时，所有剩余的工作应该尽快完成。随后，回调将发出成交已经完成的信号。

插件和它们各自的作者应该预料到一些用户可能会忘记关闭编译器，所以所有的工作最终应该在空闲时完成。当工作正在进行时，还应该防止进程退出。当传递回调时，webpack() facade 自动调用 close。更新到 v5 时，确保在完成工作后使用 Node.js API 调用 Compiler.close。

**参考**

*   [https://webpack.js.org/api/compiler-hooks](https://webpack.js.org/api/compiler-hooks/)

### **分割块和模块大小**

模块现在能够以更好的方式表达大小，而不是显示单一的数字，并且有不同类型的大小。SplitChunksPlugin 现在知道如何处理这些不同的大小，并将它们用于 minSize 和 maxSize。默认情况下，只处理 javascript 大小，但是现在可以传递多个值来管理它们:

```
minSize: {
  javascript: 30000,
  style: 50000,
} 
```

迁移到 v5 时，请确保检查您的版本中使用了哪些类型的尺寸。这可以用 splitChunks.minSize 配置，也可以在 splitChunks.maxSize 中配置。

**参考**

*   [https://webpack.js.org/plugins/split-chunks-plugin](https://webpack.js.org/plugins/split-chunks-plugin/)

### **持久缓存**

在 v5 中，您会发现一个实验性的文件系统缓存，它是一个选择加入的特性，使用 Webpack 配置文件中的下面一行代码来启用:

```
cache: { type: "filesystem” } 
```

现在，只有核心特性集准备好了。但是在使用它的时候，你必须意识到它的局限性，以避免意想不到的错误。如果你不能完全理解这些限制，你最好完全避免这个特性，直到你真正适应为止。

在解析模块源代码和文件系统结构时，你也可以使用自动缓存失效，但是对于配置和加载器/插件/核心的改变，没有自动缓存失效。如果你想手动缓存失效，有一个选项可以在你的配置中使用 cache.version。它现在还没有完全准备好，但是当你升级你的工具依赖(webpack，loader，plugin)或者当你改变你的配置时，你可以通过更新你的 cache.version 使一切顺利运行。

如果您想自动执行此操作，最好散列 webpack.config.js 和 node_modules/。yarn-integrity，并将它们传递给 cache.version，这很可能是 Webpack 团队内部将如何做的。

*使用永久缓存时，不再需要* *缓存加载器。巴别塔* *cacheDirectory 也是如此。*

**参考**

*   [https://webpack.js.org/configuration/other-options/#cache](https://webpack.js.org/configuration/other-options/#cache)

### **配置变更**

由于有太多的配置更新要列出来，你可以通过 v5 [changelog](https://github.com/webpack/changelog-v5/blob/master/README.md#changes-to-the-configuration) 来阅读所有关于配置变化的内容。

### **内部变化**

有一些内部变化与插件作者密切相关。如果你需要通过 [changelog](https://github.com/webpack/changelog-v5/blob/master/README.md#major-internal-changes) 引用它们，你可以进一步阅读这些内部变化。

### **离别的思念**

如果您发现一个令人困惑的错误或需要进一步的帮助，请确保在这里提出您的问题[或浏览其他开发人员发布的评论，因为您可能会在发布之前找到问题的答案。](https://github.com/webpack/webpack/issues/8537)

如果你在变更日志中发现了什么缺失，一定要帮助团队，并在这里报告它[。目前，每个 Webpack 贡献者都有写权限，那些没有权限的人被鼓励发送一个拉请求。](https://github.com/webpack/webpack/issues/8537)

最后，在报告您的问题之前，请确保尝试升级到最新的 alpha 版本，因为该问题可能已经得到解决。捆绑愉快！

**有用链接&资源**

*   [https://github.com/webpack/webpack](https://github.com/webpack/webpack)
*   [https://webpack.js.org/configuration](https://webpack.js.org/configuration/)
*   [https://webpack.js.org/concepts](https://webpack.js.org/concepts/)

### Plug: [LogRocket](https://logrocket.com/signup/) ，一款适用于网络应用的 DVR

[![](img/d56be9e9e36d8fa98c6959f7097b7787.png)](https://logrocket.com/signup/)

<figcaption>[https://logrocket.com/signup/](https://logrocket.com/signup/)</figcaption>

LogRocket 是一个前端日志工具，可以让你回放问题，就像它们发生在你自己的浏览器中一样。LogRocket 不需要猜测错误发生的原因，也不需要向用户询问截图和日志转储，而是让您重放会话以快速了解哪里出错了。它可以与任何应用程序完美配合，不管是什么框架，并且有插件可以记录来自 Redux、Vuex 和@ngrx/store 的额外上下文。

除了记录 Redux 操作和状态，LogRocket 还记录控制台日志、JavaScript 错误、堆栈跟踪、带有头+正文的网络请求/响应、浏览器元数据和自定义日志。它还使用 DOM 来记录页面上的 HTML 和 CSS，甚至为最复杂的单页面应用程序重新创建像素级完美视频。

免费试用。

* * *