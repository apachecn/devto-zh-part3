# Ant Design Pro v4 在这里

> 原文：<https://dev.to/chenshuai2144/ant-design-pro-v4-is-here-1jga>

[![image.png](img/cb92f5c3089301b0b9e9ef5bbbf4e1c7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ttC3dAnc--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://user-gold-cdn.xitu.io/2019/5/31/16b0bc0373304c04%3Fw%3D1440%26h%3D290%26f%3Dpng%26s%3D224412)

距离上一次发布 Pro 已经过去了 8 个月。我们一直在更新，但大多数都集中在 bug 修复上。这段时间我们使用 Pro 遇到了很多痛点，也在思考如何解决这些问题。同时，整个前端行业也在发生变化。随着业务的复杂化，微前端应运而生。像阿里云的控制台这样的几百个页面，会有越来越多的合作项目。无服务器化方兴未艾，前端的云每天还在探索新的挑战。

V4 是我们迎接挑战的最新尝试。我们将 TypeScript、布局组件、块和组件从 Pro 转移到 Ant Design。同时，我们也带来了创建项目的新方法，大大减少了冗余代码。

### 🚀快速启动

创建一个新的空文件夹作为项目目录，并在其中执行如下命令:

```
yarn create umi // or npm create umi 
```

选择 ant-design-pro:

选择样板类型(使用箭头键)

```
❯ ant-design-pro  - Create project with an layout-only ant-design-pro boilerplate, use together with umi block.
  app             - Create project with a simple boilerplate, support typescript.
  block           - Create a umi block.
  library         - Create a library with umi.
  plugin          - Create a umi plugin.
The Ant Design Pro scaffolding will be installed automatically. 
```

### ✨升级到 v4

蚂蚁设计 Pro 4.0 兼容 2.0 的所有功能，从 2.0 升级到 4.0 不需要做任何改动。在 Ant Design Pro 4.0 中，我们将布局分成单独的组件。可以选择用最新的替换。

```
npm i @ant-design/pro-layout --save 
```

在 src/layouts 目录下，删除 BasicLayout.js 和 BasicLayout.less，替换为新版本的 BasicLayout.tsx。

如果您修改了原始的 BasicLayout，请记住将更改的逻辑应用到替换的文件。

### 🏆以打字打的文件

在 v4 中，脚手架和块都用 TypeScript 重构。TypeScript 对开发非常有效，良好的生态可以大大减少查询 API 的时间。它还可以消除错误的参数或拼写错误的变量名。更好的是，由于其强大的表达能力，您可以更容易地阅读旧项目。因为该类型在某些情况下可以用作文档。

TypeScript 更大的优势是简化了重构的难度。当您接手一个不太健壮的遗留代码时，TypeScript 的静态类型检查将使您更有效率。重构会导致类型错误，这些错误可以在开发时被发现。比如把 antd@2 升级到 antd@4，如果你没有 TypeScript，很难想象你上线要承受多大的心理压力。

我们希望有一天当你接手项目的时候，你会发现它用的是 Pro 的脚手架，让你觉得很安心。可以快速入门，快速开发，使用同样的抽象模型和代码风格。要更好地做到这一点，TypeScript 是必不可少的一部分。

对于非 Typescript 用户，我们还提供了额外的 JavaScript 版本。在初始化项目时，可以选择 JavaScript 搭建。

> 👎JavaScript 失去了很多特性，对编辑不够友好。我们强烈建议您优先使用 TypeScript。

### 📦布局

在 v2 版本中，启动新项目的过程通常是:

*   克隆项目
*   删除未使用的页面
*   开始开发

大多数参考页面不一定会用到，但布局肯定是保留的😁。

Pro 的布局有很多很棒的功能。比如自动生成的菜单，面包屑和各种漂亮的布局。即使是一个新的前端开发人员也可以快速地为他的项目建立一个框架。

但是动力也很笨重。更糟糕的是，我们向每个用户公开了所有的代码细节。虽然 Layout 看起来只是一个 UI，但是逻辑却分布在多个文件中。对于开发人员来说，可能只关注显示效果，但为了修改一个小地方可能需要跳转几个文件，还需要仔细观察数据的流向。这对开发者来说是个大问题。更不用说繁琐的升级方式，几乎没有用户会在性能正常的情况下升级布局。

为了解决这些问题，我们提取了 v4 中的布局。现在，它作为一个包重新亮相，保留了几乎所有的原始功能！只用几行代码:

```
import ProLayout from '@ant-design/pro-layout';
render(
  <ProLayout
    logo={<img src="https://demo.com/logo.png" />}
    layout="sidemenu"
    navTheme="dark"
    fixSiderbar
    locale="zh-CN"
  />
, document.getElementById('root')); 
```

也许你的代码已经成型很久了，但是没关系，你也可以快速访问。并且它只与 antd 耦合。这意味着它可以支持所有脚手架。

在可定制性方面，我们提供了非常多的渲染方法。你几乎可以自定义渲染任何块菜单，页眉，页脚，甚至标题。api 方面，接近 antd 的成熟解决方案。如果你熟悉 antd，它会像一个强大的 antd 组件一样使用，没有入门成本。

如果你想马上尝试，看这里 [@ant-design/pro-layout](https://github.com/ant-design/ant-design-pro-layout) 。欢迎点赞、吐槽或发起 PR。

### 🐱‍组件

v2 中提供了一系列组件。但在 v4 中，我们将它们移除，并逐渐迁移到 antd。在 antd 中，他们得到了更好的支持、更好的社区和更健壮的 api。

有些组件你现在就可以开始使用，比如描述、页眉、排版。这些组件可以满足大量的页面显示需求。对于那些高度面向业务的组件，我们倾向于使用块进行安装

### 🎯街区

在 v4 中，我们将 Pro 的资源分为两部分，布局和页面。所有的界面都由这两部分组成。我们将布局组件化以提高开发效率，而块是我们的页面解决方案。每个块都是一个页面，可以拥有自己的状态，本地化，甚至模拟数据和服务器。基于海的功能使它们能够轻松快速地集成到脚手架中。

由于砌块的特性。你的新项目将会非常简洁，没有额外的依赖，也没有一堆你不能使用的页面。看起来是这样的:

如果您需要 Pro 中的页面，您可以通过块快速添加它。在 Pro 的演示站点中，我们添加了一个按钮。当您想要在您的项目中使用这个页面时，您可以通过单击并复制下面的代码行来将这个块下载到您的项目中。就像去商场买一件产品一样简单:

如果你想要全部(哇！)，您可以在您的项目中运行，我们的脚本会将所有 Pro 块下载到您的演示项目中。npm 运行 fetch:blocks

区块在带来好处的同时也带来了问题。最重要的是冗余代码的数量，这将是我们未来工作的重点。

🧩蚂蚁设计 4.0
我们预计在今年第四季度发布蚂蚁设计 4.0 版本🍻。主要目标是:

*   面向未来
*   最大向后兼容性
*   更快更小
*   这里可以看到详细的扩展说明[。](https://medium.com/ant-design/ant-design-pro-v4-is-here-6f23098ae9d9)

### 🥇展望未来

自 2017 年 10 月 29 日第一版 [@afc163](https://dev.to/afc163) 发布以来，2019 年 Pro 有了很多变化。

底层框架修改为 umi，代码组织改为 block，完全拥抱了 TypeScript。

可以说 Pro 发生了翻天覆地的变化。

蚂蚁设计还发布了 4.0 计划，作为一次巨大的版本升级，4.0 将解决一些一直被诟病的问题。包括重写、更多新组件、更强大的性能等等。

蚂蚁设计 Pro 和蚂蚁设计未来还会继续迭代。但无论代码如何变化，我们的初心和愿景都不会改变。那就是为每个用户带来最极致的开发体验，提高开发效率。

### 🌚还有一点

大多数浏览器已经支持黑暗模式。蚂蚁设计 Pro 和蚂蚁设计也在[黑暗主题](https://github.com/ant-design/ant-design-dark-theme)上做一些工作。目前的解决方案可能达不到预期，但你仍然可以尝试。

### 🤝特别感谢

感谢所有发现 bug，发起 PR，回复问题，写文档的人！特别感谢两个社区合作伙伴，他们承担了 v4 开发工作的大部分。[`@xiaohuoni`](https://github.com/xiaohuoni)[`@imhele`T5】](https://github.com/imhele)

如果您在使用 Ant Design Pro 时遇到任何问题，请随时在 GitHub 上提交问题。

感谢您的阅读，请安装并尝试。🎉