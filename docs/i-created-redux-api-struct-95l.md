# 我创建了 redux-api-struct。

> 原文：<https://dev.to/konojunya/i-created-redux-api-struct-95l>

我之前写过这篇文章。

[https://qiita.com/konojunya/items/07fd138144cb410da387](https://qiita.com/konojunya/items/07fd138144cb410da387)

这篇文章把我在一个类似于 dev.to，Qiita Japan 的网站上用日语写的东西翻译成了英语。
因为我是日本人，不擅长英语，所以用翻译机翻译了一下，但是如果英文有错误请告诉我。

* * *

[![](img/e3f198f0a66fdd96d7dd82864979df19.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rajLCgg6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://raw.githubusercontent.com/konojunya/redux-api-struct/master/logo/logo.png)

我发布了一个名为`redux-api-struct`的库。
这是一个库，你可以在其中为 redux 管理的 React 和异步应用编写更简洁、更类型安全的逻辑。

自述文件中描述了该 API，但这里有一个快速使用它的方法。

这些字符是反应，还原，和承诺。以一个 Web 应用程序的常见技术为例，它通常访问 API 的资源。

例如，你正在制作一个博客服务。有文章的详细画面。API 使用 GET 方法访问`/articles/:id`。
假设值由状态条管理。文章的细节被假定为如下结构。

```
interface Article {
  title: string;
  body: string;
} 
```

如果访问 api 并且 statusCode 为 200，文章将如下所示:

```
{
  article: {
    title: "hoge title",
    body: "hoge body"
  }
} 
```

现在考虑一个显示这个内容组件。
定义了以下功能组件:

```
import * as React from "react";

interface Props {
  article: Article;
}

export const ArticleTemplate: React.FunctionComponent<Props> = ({ article }) => (
  <>
    <h1>{article.title}</h1>
    <p>{article.body}</p>
  </>
) 
```

如果它把文章传给道具的话，好像还不错。

让我们试着动起来。然后，错误就出来了，取决于书写。如果文章 initialState 为 null，它将被`null.title`访问并出现错误。让我们定义一个空字符串，而不是放 null。

然后将显示该页面。如果显示一个空字符串和一个空主体，并且 api 访问成功，您可以看到文章。

但是如果 api 出现错误怎么办？
例如，如果您访问文章详情中不存在的文章的 URL，则返回 404。
您必须呈现未找到的页面。有必要知道请求是否成功。
这就是为什么我们要取三篇文章，成功和失败。但是，举例来说，如果你想把一个加载屏幕以外的这些，在目前的情况下，例如，标题是一个空字符串将实现如抓取。
能够轻松处理这些都是 redux-api-struct。:)

api 的访问状态分为初始、获取、成功和失败四种状态。

该组件只需在四种状态之间切换。除非成功，否则访问实际数据不像引用 null 那样是错误的。

首先要做的是用类型`ReduxAPIStruct`绑定减速器的状态。

```
import { ReduxAPIStruct, createDefaultStruct } from "redux-api-struct";

interface InitialState {
  article: ReduxAPIStruct<Article>;
}
const initialState: InitialState = {
  article: createDefaultStruct<Article>(null)
} 
```

传递给`ReduxAPIStruct`的泛型的类型是原始数据类型。

此 ReduxAPIStruct 包装的状态具有以下树结构:

```
interface WrappedArticle {
  state: ReduxAPIState,
  data: Article,
  error: ReduxAPIError
} 
```

状态由上述四种状态管理。

请阅读组件的实现，因为它更容易看到自述文件。

[https://github.com/konojunya/redux-api-struct#usage](https://github.com/konojunya/redux-api-struct#usage)

通过这种方式，您可以轻松地创建异步 web 应用程序，同时保持类型安全。

附言

如果你喜欢，或者你有更多的考虑，我很乐意有一个明星或问题。

谢了。

作者: [@konojunya](https://twitter.com/konojunya)