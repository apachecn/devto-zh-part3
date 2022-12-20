# 用 GatsbyJS 挑战无头 WordPress

> 原文：<https://dev.to/geocine/challenges-in-headless-wordpress-with-gatsbyjs-4a0b>

在你最近的一个项目中使用了带有 GatsbyJS 的 Headless WordPress 的开发者们，你们遇到的主要挑战是什么，你们是如何解决的？

在这篇文章中，我将尝试分享我在最近的一个项目中使用这个堆栈的经验。

# 古腾堡编辑

Gutenberg 编辑器是由 WordPress 团队开发的新编辑器，发布于 WordPress 的第 5 版。你可以在由*汤姆·J·诺埃尔*建立的[这个网站](https://frontenberg.tomjn.com/)和官方古腾堡项目[这里](https://github.com/WordPress/gutenberg)上试用一下这个编辑器。我意识到这正是我所需要的，来满足我的用户的需求

## 渲染生成盖茨比上的内容

呈现来自 WordPress 的内容的最基本方式是这样的

```
return (<div
   dangerouslySetInnerHTML={{
      __html: data.WordPressPage.content
   }}
/>) 
```

Enter fullscreen mode Exit fullscreen mode

如果 WordPress 生成的标记是这样的，当你点击其中一个图片时，你将如何呈现一个打开模态的图片库？

```
<div class="gallery">
  <img src="https://placekitten.com/200/300" />
  <img src="https://placekitten.com/g/200/300" />
</div> 
```

Enter fullscreen mode Exit fullscreen mode

我已经使用了`html-react-parser`并将这个 HTML 字符串转换成一个组件。

检查下面的代码。请注意，我在这里实现的自定义图库组件没有我前面提到的那种模式。但是因为它已经是一个 React 组件，所以我们可以做任何事情。

[https://stackblitz.com/edit/html-to-react-component?embed=1&&](https://stackblitz.com/edit/html-to-react-component?embed=1&&)

## 预览的样式应该与呈现的盖茨比内容相匹配

在这里，我们至少应该将预览的样式与呈现的内容同步。

我们要么改变现有块的用途，要么创建我们自己的自定义块，以便它在编辑器和网站上看起来完全一样。仅供参考，块是用 React 编写的。

我在主题级别创建了块样式的覆盖，并确保在 Gatsby 上显示相同的样式。如果我们不这样做，那么编辑器就不再是所见即所得了。

这里的主要挑战是，用户可以安装一些新的模块，然后我们必须编写支持代码，并确保它不会破坏其他模块。

## ACF 在古腾堡编辑页面上

起初，如果您将字段放在标题下面，您会认为您的[高级自定义字段](https://www.advancedcustomfields.com/)被破坏了。古腾堡激活，ACF 将只显示，如果你把它放在下面的内容。

我还没有尝试把它放在边栏上，但我猜它应该也能工作。

# 发布内容

我已经选择 Netlify 来托管 Gatsby 生成的内容。又快又可靠。

## 瞬间反馈

我知道用户需要一种方法来立即验证他们刚刚发布的内容。

我探索了[这个插件](https://github.com/justinwhall/littlebot-netlify)来解决这个问题。在保存期间，这将调用 Netlify 上的一个构建挂钩来触发部署。

> 确保你已经为`Production`和`Staging`设置了一些东西，否则插件将无法工作。

仍然需要一些时间来反映实际站点的变化。

## 盖茨比缓存

在某些情况下，我不得不清空缓存来查看我所做的更改。

我不确定这是什么原因造成的。不过，这种情况并不经常发生。在盖茨比已经有一个未解决的问题。

我可以使用 Next.js 探索服务器端呈现，但是我真的很喜欢 Gatsby 工作流。

# 全局设置

有一些东西，我希望用户能够配置如下:页脚文本，公司地址，电子邮件等。

我还没有尝试寻找解决方案。我对你们会如何处理这件事很感兴趣。

# 表格

我使用了 Netlify 提供的表单提交 API。这不是一个简单的旅程。

我按照[这个文档](https://www.netlify.com/blog/2017/07/20/how-to-integrate-netlifys-form-handling-in-a-react-app/)正确地设置了一切，但是我没有收到任何提交。

你应该记住这一点。不要试图用你通常的测试本能来测试表单。Netlify 会在你不知情的情况下自动阻止看起来像垃圾邮件的表单提交。你应该像填写真实表格一样测试它，而不是肌肉记忆中的乱码测试字符串。

我希望在 Netlify 表单上有一种方法，至少可以查看有多少邮件被阻止，甚至可以在开发模式下禁用过滤。

我的用户希望将消息直接发送到他们的电子邮件中，所以我集成了 Zapier。

# 盖茨比上页

我使用`gatsby-node.js`在 Gatsby 中创建了页面，但是我想显式地创建一些特定的页面，因为它们有特定的样式和内容。

对于这些页面，我通过页面 id 查询 WordPress API。在`gatsby-node.js`的自动创建中，我也排除了这些页面 id。这样做是为了确保页面即使在标题或段落改变时也能正常工作。

但是，如果更改了一个 slug，在 Gatsby 上将没有处理它的路由。也许，我试图在这里创造一个非问题，但这是值得一提。

# 结论

如果你是一名前端开发人员，过去没有做过 react 或 WordPress 开发。这是我认为你至少应该尝试的事情。

这个工作流程真的是我想要“完善”的东西，它允许我们用 WordPress API 创建任何东西。

我希望这些挑战能帮助你决定这是否适合你。我确实希望从社区中得到一些反馈，了解您在使用这个堆栈时遇到的挑战，以及您是如何解决或计划解决这个问题的。