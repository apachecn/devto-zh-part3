# 亲爱的，我检查了孩子们

> 原文：<https://dev.to/laurieontech/honey-i-type-checked-the-children-5ad3>

和我一起继续我们解释和理解样板代码的探索吧！

今天，我们将深入了解 React 中的属性类型。实际上，我被提示这样做是因为当你使用`gatsby new <project-name>`命令时会出现一些模板代码。

首先映入我眼帘的是出现在`layout.js`中的一段代码。

```
Layout.propTypes = {
  children: PropTypes.node.isRequired,
} 
```

Enter fullscreen mode Exit fullscreen mode

这一行代码的有趣之处在于，从技术上来说，负责这里的并不是盖茨比。这实际上是 React 代码(Gatsby 是基于 React 构建的)，但它包含在 Gatsby cli starter 项目中。

# 抓住你的虫子！

`Proptypes`是 React 中的一个特性，有助于验证通过 props 传递的值。

> 如果你不熟悉道具，这篇文章里有一点概述。
> 
> [![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## 这些道具是怎么回事？
> 
> ### 劳丽 5 月 7 日 193 分钟阅读
> 
> #react #webdev #javascript #beginners](/laurieontech/what-s-with-all-the-props-anyway-jfd)

其思想是 PropTypes 可以捕捉从一个组件传递到另一个组件的数据类型的不匹配。例如，如果`msg`是一个`String`，但是它被传递到的组件期望一个`number`。即使没有 Typescript 或其他类型支持的 JavaScript 扩展，React 也提供 PropTypes 作为内置的类型检查功能。

# PropTypes 在行动

PropTypes 在两个层次上工作。它确保将正确类型的数据传递给特定的组件。它还检查以确保组件正在接收正确的数据类型。

> 这篇文章可能会帮助你理解组件这个概念。
> 
> [![laurieontech image](img/ca0667c84fcd9741a29fbc42fbc0816c.png)](/laurieontech) [## 出口、进口和违约，天啊！
> 
> ### 劳丽 Apr 29 ' 19 分钟阅读
> 
> #javascript #react #webdev #beginners](/laurieontech/exports-and-imports-and-defaults-oh-my-fa3)

在我们开始的代码中，它为`Layout`组件设置了 PropTypes。也就是说，为传入`Layout`的道具定义验证。

```
Layout.propTypes = {
     ...stuff goes here...
} 
```

Enter fullscreen mode Exit fullscreen mode

# 孩子是哪里来的？！

这是起始项目附带的`Layout`组件。看一下传递到这个组件中的道具。它传递一个名为`children`的道具，并在渲染函数体中引用它。

```
const Layout = ({ children }) => (
    render={data => (
      <>
        <Header siteTitle={data.site.siteMetadata.title} />
          <main>{children}</main>
          <footer/>
        </div>
      </>
    )}
) 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们看一个如何使用`Layout`组件本身的例子。我们可以在 starter 项目生成的`NotFoundPage`组件中找到这样的例子。

```
const NotFoundPage = () => (
  <Layout>
    <SEO title="404: Not found" />
    <h1>NOT FOUND</h1>
    <p>You just hit a route that doesn&#39;t exist... the sadness.</p>
  </Layout>
) 
```

Enter fullscreen mode Exit fullscreen mode

嗯，有点奇怪。乍一看`Layout`根本不像是在用道具。通常，props 作为出现在组件的 open 标记中的键值对来传递。类似这样的。

```
<Example msg="test" /> 
```

Enter fullscreen mode Exit fullscreen mode

然后在`Example`组件中，我们将传入 props 并访问`msg`内容。但是`Layout`没有这样做。

事实证明，`Layout`还是在用道具，只是方式不同。`children`其实是一个特殊的内置道具。它指的是和`Layout`标签中的所有内容**。** 

```
 <Layout>
//ALL OF THIS
    <SEO title="404: Not found" />
    <h1>NOT FOUND</h1>
    <p>You just hit a route that doesn't exist... the sadness.</p>
//TO THIS
  </Layout>
) 
```

Enter fullscreen mode Exit fullscreen mode

布局开始和结束标签之间的所有内容都被认为是`children`道具。

# 来验证一下吧！

所以回到我们的 PropTypes 验证。现在我们知道了什么是`children`道具，我们可以开始理解我们是如何验证内容的。

```
Layout.propTypes = {
  children: PropTypes.node.isRequired,
} 
```

Enter fullscreen mode Exit fullscreen mode

我们可以使用`Layout`来包装任意数量的东西，但是我们唯一关心的是我们正在包装**的东西**。我们不能渲染一个没有任何信息的页面，或者至少我们不想这样做。一个空的布局不是我们所追求的。这就是我们有`isRequired`的原因。

我们也想给那个东西可能是什么下一个有点模糊的定义。关键字`node`只是说，我已经传入了一些可渲染的东西。不管那是什么？

# 不要停留在样板文件上

请记住，模板的这一部分已经成熟，可以进行更改了。在定义 Gatsby 应用程序时，您可能希望实施不同的标准。例如，您可以将道具限制为只有一个父元素，如下所示。

```
Layout.propTypes = {
  children: PropTypes.element.isRequired
}; 
```

Enter fullscreen mode Exit fullscreen mode

Gatsby 以此为起点，提示您使用类型强制，并向您展示它能做什么。

# 就是这样！

现在我们理解了这个 PropTypes 代码片段是如何对我们的`Layout`组件进行类型检查的。作为一个额外的奖励，我们也更喜欢内置的`children`道具。

和往常一样，我们经常在框架和项目中看到一些我们没有花时间去理解的代码行。我鼓励你好奇地探索你看到的一切！这会让你成为更好的程序员。

期待更多样板基础知识即将推出！