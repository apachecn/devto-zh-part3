# 用 HTMR 赋予《盖茨比》中的林克斯以生命

> 原文：<https://dev.to/arcath/using-htmr-to-bring-life-to-links-in-gatsby-1968>

关于 Gatsby 的一个令人惊讶的事情是，你的网站是一个成熟的 React 应用程序。正因为如此，虽然在*应用*中的导航速度非常快，但我从 markdown 链接到的任何东西都会触发对页面的完整请求，这一直困扰着我。

这是因为 markdown 中的链接只是从`dangerouslySetInnerHTML`放在页面上的一个`a`标签。这意味着没有事件侦听器或任何东西，只是一个普通的老式链接。解决方案是解析 HTML 字符串，并为正确的 React 组件交换链接。

有一些 HTML 反应转换器，但我发现 [HTMR](https://github.com/pveyes/htmr) 很容易使用我的需求。我只希望用一个`Link`组件替换掉`a`标签，而不是绑定事件或任何类似的东西。

我已经有了一个使用`html`道具的`Content`组件。我创建它就是为了这样一种情况，我想改变整个网站的内容呈现方式。

```
import React, {ReactHTMLElement} from 'react'
import convert from 'htmr'
import {HtmrOptions} from 'htmr/lib/types'
import {OutboundLink} from 'gatsby-plugin-google-gtag'
import {Link} from 'gatsby'

export const Content: React.FunctionComponent<{html: string}> = ({html}) => {
  const transform: HtmrOptions["transform"] = {
    a: (node: Partial<ReactHTMLElement<HTMLAnchorElement>["props"]>) => {
      const {href} = node

      if(href!.substr(0, 4) === 'http'){
        return <OutboundLink href={href!}>{node.children}</OutboundLink>
      }

      return <Link to={href!}>{node.children}</Link>
    }
  }

  return <div>{convert(html, { transform })}</div>
} 
```

那么这是在做什么？

`convert`来自 HTMR 并执行实际转换。我把它放在这里的一个`div`标签中，以处理`html`是一个没有标签的字符串的边缘情况。为了给链接带来活力，我使用了可选的`transform`选项，你可以将它传递给`convert`函数。从这里您可以将标签映射到其他标签，例如`b: 'strong'`对所有`b`标签使用`strong`而不是`b`。或者你可以提供一个替换组件，`button: Button`来使用一个样式化的按钮来代替。第三种使用方法是提供一个函数。

向该函数传递一个参数，这个参数是 HTMR 将用于原始标记的`props`。

例如:

```
// HTML
<a href="/something">a link</a>
// Props
{href: '/something', children: 'a link'} 
```

在上面的代码中，我检查了`href`属性，看看这是内部链接还是外部链接。如果是一个外部链接，它会被替换为一个`OutboundLink`,以确保谷歌分析正确处理该链接。否则，它一定是一个被替换为`Link`的内部链接。

这一切的结果是更快的内部链接。

带这个链接到[关于我](https://dev.to/about)这是一个内部链接，现在是一个正确的`Link`。试一试，注意它有多快。这个链接到我的[使用页面](https://arcath.net/uses)另一方面，这是一个成熟的链接，像 HTMR 之前的内部链接一样工作。

有一个插件[Gatsby-plugin-catch-links](https://www.gatsbyjs.org/packages/gatsby-plugin-catch-links/)提供了一个类似的结果，但是用了一种非常笨拙的方式。它将自己绑定到浏览器中的所有链接，并在每个链接的基础上决定它是否是一个内部链接以及如何导航到它的目标。我用 HTMR *的方法在网站建设期间修复了*链接，并且在客户端不需要额外的绑定。另外，它只适用于 markdown，而不适用于网站上已经是 React 组件的其他链接。