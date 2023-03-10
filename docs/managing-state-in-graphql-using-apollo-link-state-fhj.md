# 使用 Apollo 链接状态管理 GraphQL 中的状态

> 原文：<https://dev.to/progresstelerik/managing-state-in-graphql-using-apollo-link-state-fhj>

您知道使用 GraphQL 可以读写您的状态吗？Apollo 链路状态允许您在单一位置管理本地和远程数据。

使用 GraphQL 不需要 GraphQL API。这似乎是一个疯狂的声明，但是使用 Apollo GraphQL，您可以使用 [apollo-link-state](https://www.apollographql.com/docs/link/links/state.html) 通过查询和变异来读取和写入您的状态。

在本文中，我们将探索如何使用 apollo-link-state 作为状态管理库来取代组件状态、Redux 或 MobX。我们将涉及的主题如下:

*   如何设置链路状态以及定义解析器和默认数据
*   如何执行更新状态的突变
*   如何执行查询来读取状态

如果在任何时候你迷路了，这段代码的最终版本可以在 GitHub 上找到[。](https://github.com/leighhalliday/github-stars-graphql/tree/link-state)

## 阿波罗和数据

阿波罗将你所有的数据保存在缓存中。可以把它想象成 MobX 或 Redux 中的普通数据存储。通常，这个缓存是通过执行 GraphQL 查询来填充的，它将从服务器返回的数据放入缓存中。您可以使用`apollo-link-state`模块自行读取或写入缓存。

你为什么要这么做？它允许您在 React 代码中执行 GraphQL 查询(或突变)来访问您的状态，而不需要 GraphQL API。它还为您提供了一种通用语言来引用所有形式的数据，无论是状态还是来自 GraphQL API 的数据。

在我们的例子中，我们将添加一个简单的控件，让我们选择我们希望从 GitHub GraphQL API 中看到多少星级存储库。让我们看看它是如何工作的！

## 使用链接状态来维护状态

第一步，添加库:`yarn add apollo-link-state`。之后，我们将定义一些突变解析器。这些解析器允许我们寻找特殊的查询或变异，并在本地处理它们，而不是让它们向外部 GraphQL API 发出请求。

下面是`apolloClient.js`文件，我们在其中定义了 Apollo 客户端以及它所包含的所有不同的[链接](https://www.apollographql.com/docs/link/)。如果您不熟悉 Apollo 客户端，请参考本系列的前一篇文章,这篇文章关注的是基本的 Apollo 客户端设置。我已经注释掉了一些链接，以防止代码变得太大。

```
// src/apolloClient.js

import { InMemoryCache } from "apollo-cache-inmemory";
import { ApolloClient } from "apollo-client";
import { ApolloLink } from "apollo-link";
import { HttpLink } from "apollo-link-http";
import { setContext } from "apollo-link-context";
import { onError } from "apollo-link-error";
import { withClientState } from "apollo-link-state";

const cache = new InMemoryCache();
const stateLink = withClientState({
  cache,
  resolvers: {
    Mutation: {
      updateStarredControls: (_, { numRepos }, { cache }) => {
        const data = {
          starredControls: {
            __typename: "StarredControls",
            numRepos
          }
        };
        cache.writeData({ data });
        return null;
      }
    }
  },
  defaults: {
    starredControls: {
      __typename: "StarredControls",
      numRepos: 20
    }
  }
});

// errorLink defined here
// authLink defined here
// httpLink defined here

const client = new ApolloClient({
  link: ApolloLink.from([errorLink, stateLink, authLink, httpLink]),
  cache
});

export default client; 
```

您将传递给`withClientState`函数的主要内容是:

*   `cache`:存储状态的位置。
*   `resolvers`:修改或读取状态所需的任何变化或查询(不过，如果您的查询只是请求与缓存中存储的格式完全相同的数据，则查询不是绝对必要的)。
*   `defaults`:计划状态的默认状态值。

我们定义为`updateStarredControls`的解析器函数看起来是这样的:

```
(_, { numRepos }, { cache }) => {
  const data = {
    starredControls: {
      __typename: "StarredControls",
      numRepos
    }
  };
  cache.writeData({ data });
  return data;
}; 
```

它的主要目的是接收变异变量`{ numRepos }`和`{ cache }`，并使用这两个变量更新缓存中的数据。有关解析器签名的详细信息，请参考[阿波罗文档](https://www.apollographql.com/docs/link/links/state.html#resolver-signature)。

需要注意的是，添加到缓存中的数据必须有一个`__typename`字段，这有助于 Apollo 正常化和正确存储数据。

### 清除解析器&默认值

就像在 Redux 中一样，您可以想象随着状态的增长，您拥有的变异和查询解析器的数量将变得难以容纳在一个文件中。我建议创建一个`resolvers`文件夹，其中包含每个相关的解析器和默认值组的单个文件，并使用一个`index.js`文件将它们合并在一起。比如:

```
// src/resolvers/starredControls.js
const resolvers = {
  Mutation: {
    updateStarredControls: (_, { numRepos }, { cache }) => {
      const data = {
        starredControls: {
          __typename: "StarredControls",
          numRepos
        }
      };
      cache.writeData({ data });
      return null;
    }
  }
};
const defaults = {
  starredControls: {
    __typename: "StarredControls",
    numRepos: 20
  }
};
export default { resolvers, defaults }; 
```

然后是合并它们的代码:

```
// src/resolvers/index.js
import merge from "lodash.merge";
import starredControls from "./starredControls";
export default merge(starredControls); 
```

这将允许我们将`stateLink`的定义更新得更短一些:

```
// src/apolloClient.js
import resolversDefaults from "./resolvers";
const stateLink = withClientState({
  cache,
  resolvers: resolversDefaults.resolvers,
  defaults: resolversDefaults.defaults
}); 
```

## 变异状态

为了调用我们定义的突变解析器，我们将创建一个名为`StarredControls`的组件，它将为用户提供三个选项供选择:一次查看 10、20 或 30 个存储库。在`onClick`事件中，我们将调用突变函数，传递所需的变量(`numRepos`)。在代码示例下面，我们将深入了解它的工作原理。

```
// src/StarredControls.js

import React, { Fragment } from "react";
import { Mutation } from "react-apollo";
import gql from "graphql-tag";
import { Button } from "@progress/kendo-react-buttons";
import styled from "styled-components";

// Just adding some additional styles to our kendo button

const Control = styled(Button)`
  margin-right: 5px;
`;
// Defining the mutation to be executed
const UPDATE_STARRED_CONTROLS = gql`
  mutation UpdateStarredControls($numRepos: Int!) {
    updateStarredControls(numRepos: $numRepos) @client
  }
`;

const StarredControls = ({ numRepos }) => (
  <div>
  View{" "}
  <Mutation mutation={UPDATE_STARRED_CONTROLS}>
  {updateStarredControls => (
    <Fragment>
    {[10, 20, 30].map(num => (
      <Control
        onClick={() => {
          updateStarredControls({ variables: { num } });
        }}
        key={num}
      >
      {numRepos === num ? <strong>{num}</strong> : num}
      </Control>
    ))}
    </Fragment>
  )}
  </Mutation>{" "}
  Repos
  </div>
);

export default StarredControls; 
```

首先，除了使用样式化组件给我们正在使用的来自[剑道 UI 按钮](https://www.telerik.com/kendo-react-ui/components/buttons/button/)库的按钮添加一些空白之外，您将看到变异的定义。

```
const UPDATE_STARRED_CONTROLS = gql`
  mutation UpdateStarredControls($numRepos: Int!) {
    updateStarredControls(numRepos: $numRepos) @client
  }
`; 
```

这看起来像是一个正常的变异，除了一个关键的不同:指令`@client`。这向 Apollo 发出信号，表示它打算使用我们为状态链接定义的解析器。

将`Mutation`组件嵌入 React 代码的实际代码与在服务器 API 上执行 GraphQL 变异和我们正在执行的客户端变异没有什么不同。

## 查询状态

这个查询定义看起来与 Apollo 中的普通查询定义非常相似，主要区别在于`@client`指令(类似于我们对变异所做的)，这是向 Apollo 表明这个查询是要从 state 中读取数据的一种方式。

因为我们的查询与数据的存储方式相匹配，并且没有任何可能修改结果的查询变量，所以我们在定义链路状态时甚至不必定义查询解析器，因为 Apollo 足够聪明，可以从数据缓存中读取它。这被称为[默认解析器](https://www.apollographql.com/docs/link/links/state.html#default)。

```
// within src/StarredRepos.js

const STARRED_CONTROLS_QUERY = gql`
  query StarredControlsQuery {
    starredControls @client {
      numRepos
    }
  }
`; 
```

一旦我们定义了查询，使用它与在 Apollo 中执行任何其他查询是一样的，不管是对 GraphQL 服务器还是对 state。

```
export default class StarredRepos extends React.Component {
  render() {
    return (
      <div>
      <Query query={STARRED_CONTROLS_QUERY}>
      {({ data: { starredControls } }) => (
        <Fragment>
        <Status />
        <StarredControls numRepos={starredControls.numRepos} />
        <Query
          query={STARRED_REPOS_QUERY}
          variables={{ numRepos: starredControls.numRepos }}
        >
        {({ data: repoData, loading }) => {
          if (loading) {
            return <span>Loading...</span>;
          }
          return repoData.viewer.starredRepositories.nodes.map(node => (
            <Repository data={node} key={node.id} />
          ));
        }}
        </Query>
      </Fragment>
      )}
      </Query>
      </div>
    );
  }
} 
```

## 结论

坦白地说，我看到了链接状态的优点和缺点。对我来说，好处是你可以考虑以一种单一的方式查询/改变你的数据，以完全相同的方式处理来自你的状态的数据，来自服务器的数据，以及可能来自其他来源(比如 REST API)的数据。

对我来说，缺点是使用现有的状态管理库，如 Redux、MobX，或者简单地坚持简单情况下的组件状态，似乎可以更简洁明了地管理状态。

* * *

**关于使用 React 构建应用的更多信息:**

*   查看我们的 [All Things React](https://www.telerik.com/blogs/all-things-react) 页面，该页面收集了大量的信息和 React 信息的指针，包括从入门到创建引人注目的 UI 的热门话题和最新信息。
*   您还可以了解更多关于 [KendoReact](https://www.telerik.com/kendo-react-ui) ，我们专门为 React 构建的本地组件库，以及[它能为您做什么](https://dev.to/progresstelerik/kendoreact-what-can-it-do-for-you-3if4)。