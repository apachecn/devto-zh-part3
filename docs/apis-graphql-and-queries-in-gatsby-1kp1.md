# Gatsby 中的 API、graphQL 和查询

> 原文：<https://dev.to/willworth/apis-graphql-and-queries-in-gatsby-1kp1>

这篇文章对 API 进行了简单易懂的介绍，将它们与 GraphQL 进行了对比，然后给出了一个如何在 Gatsby 中运行查询的例子。

Gatsby——react 的静态站点生成器——使用 GraphqQL。

根据[维基百科](https://en.wikipedia.org/wiki/GraphQL)，GraphQL 是:

> ...用于 API 的开源数据查询和操作语言，以及用现有数据完成查询的运行时。

那么这意味着什么，我们为什么要关心呢？

## 传统 API

简单地说， [API](https://en.wikipedia.org/wiki/Application_programming_interface) 是一种连接程序的方式。如果一家公司想让其他人访问它的程序，它会制作一个公共 API 供人们连接。(当然，公司通常也有用于组织内部不同程序的 API 来进行交流。)Goodreads 是一个基于书籍的社交网络，现在归亚马逊所有，它有一个传统的 API。[这里是 API 列表页面](https://www.goodreads.com/api)。

如果你看看这些列表，你会发现它们都是不同的地址——URL。当谈到 API 时，这些被称为“端点”。所以就像访问一个正常的网站一样，不同的地址(端点)提供给你不同的东西:

```
Get the reviews for a book given an ISBN:
https://www.goodreads.com/book/isbn/ISBN?format=FORMAT 
Show an owned book:
https://www.goodreads.com/owned_books/show/OWNED_BOOK_ID?format=xml 
```

Enter fullscreen mode Exit fullscreen mode

关键的区别在于，与普通网页不同，这些*端点*是供程序访问的。这些东西太棒了！你可以访问各种各样的数据，你可以用任何你想要的方式混合。如果你想看到大量免费公开的 api，请查看 [programmableweb](https://www.programmableweb.com/) 或 [any api](https://any-api.com/) 。

如果您查看 goodreads API 的页面(实际上有很好的文档记录),就会发现这些事情会变得很复杂，并且很难知道哪个端点是最好的。这种方法的另一个缺点是查询缺乏灵活性。假设您想要一辆汽车的轮胎尺寸——您可能有一个关于车轮的端点，但是得到的响应包含许多您不感兴趣的其他数据。

## graph QL 的优势

这两点——端点复杂性和冗余数据——是(我的理解)开发人员想要使用 GraphQL 的主要原因:

*   它对所有事情都使用一个统一的端点
*   你有完全的灵活性，所以没有不必要的额外转移。

这对开发人员来说更简单、更有效。它似乎很快变得非常流行。我不是专家(完全不是)，但是如果你想了解更多关于 GraphQL 的知识， [howtographql](https://www.howtographql.com/) 似乎是一个不错的免费资源。

## 盖茨比

所以 gatsby 在编译它的构建时使用 graphql 来获取所有数据。这可能是来自不同地方的各种数据。它来自的一个重要地方是`gatsby-config`，它是 gatsby 项目中的核心文件之一——它拥有所有的插件...*配置*(如你所料)，还包含关于站点的元数据。

例如，与其到处硬编码网站标题，不如通过查询就能访问。通常在 [React](https://reactjs.org/) 中，任何非硬编码的东西都需要作为道具传递，这是一件痛苦的事情，因为你很容易让组件处理他们不感兴趣的数据。我对这一切的理解是，它像[风格的组件](https://www.styled-components.com/)一样工作，因为一切都是独立的。因此，在我的站点名称查询示例中，您将在需要它的组件中查询该数据，而不是向下传递它，无论传递了多少层。

```
import React from "react";  
import { StaticQuery, graphql, Link } from "gatsby";  // remember if it's an internal within gatsby, use Link, not href.

const Header = () => (  //notice we have *not* passed in any props. Instead we use StaticQuery
<StaticQuery // StaticQuery does not accept variables (hence the name “static”), but can be used in any component, including pages.
  query={graphql`
    query HeadingQuery {
      site {
        siteMetadata { 
          title
        }
      }
    }
  `}   
  render={data =>(
  <div className ="header" >
      <h1>
        <Link to="/">
          {data.site.siteMetadata.title}
        </Link>
      </h1>
  </div>
  )}
/> );

export default Header; 
```

Enter fullscreen mode Exit fullscreen mode

## 查询从何而来？

显然，仅仅为了站点标题就这么麻烦，但是同样的技术也适用于其他更有价值的用例。问题是，*查询从何而来？*

当您运行`gatsby develop`时，gatsby cli 会向您显示:

```
View GraphiQL, an in-browser IDE, to explore your site's data and schema

http://localhost:8000/___graphql 
```

Enter fullscreen mode Exit fullscreen mode

它的右边有一个很好的可点击链接列表，供您探索您的模式。我还没有足够的知识来解释它是如何正常工作的(这篇文章足够长)，但是它的伟大之处在于，您可以在交互式 IDE 中检查查询工作，然后按照上面的格式将其复制到您的代码中。

## 警告和结论

我是 GraphQL 的初学者，在这里我可能遗漏或误传了重要的细节。如果你发现任何问题，或者需要任何澄清，请让我知道。我希望这是有用的-祝你好运，继续编码！