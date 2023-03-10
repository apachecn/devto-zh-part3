# 使用 React 钩子简化 Apollo 客户端

> 原文：<https://dev.to/jleblanc/using-react-hooks-to-simplify-apollo-client-1kgi>

在过去的几个月里，我一直很喜欢使用 apollo，但我最大的一个痛点是处理应用程序中分散的 graphql SDL、仅针对本地州的小查询，以及编写大量样板文件来完成任何事情。

例如，如果你想为你的组件利用你的局部状态的片断，你需要写这样的东西:

```
const GET_TOKEN = gql`
  {
    token @client
  }
`;

export default () => {
  const { data } = useQuery(GET_TOKEN);
  return <p>{data.token}</p>
} 
```

Enter fullscreen mode Exit fullscreen mode

对于阅读状态来说，这是一大堆样板文件。

我最近一直在使用的是将样板文件的所有部分移动到一个钩子中。

```
// useToken.js
const GET_TOKEN = gql`
  {
    token @client
  }
`;
export default () => {
  const { data } = useQuery(GET_TOKEN);
  return data.token;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，当我想从缓存中读取令牌时，我所要做的就是调用钩子。

```
// Token.jsx
export default () => {
  const token = useToken();
  return <p>{token}</p>
} 
```

Enter fullscreen mode Exit fullscreen mode

这不仅限于查询，当然也不仅限于每个钩子的一个查询/变异。考虑在你的应用中管理一个抽屉的状态。您需要知道它是打开的还是关闭的，并且您希望能够切换这种状态。

```
// useDrawer.js
export const GET_DRAWER_OPEN = gql`
  {
    drawerOpen @client
  }
`;

export const TOGGLE_DRAWER_OPEN = gql`
  mutation ToggleDrawerOpen {
    toggleDrawerOpen @client
  }
`;

export default () => {
  const { data } = useQuery(GET_DRAWER_OPEN);

  return {
    drawerOpen: data.drawerOpen,
    toggleDrawerOpen: useMutation(TOGGLE_DRAWER_OPEN)
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你也可以把这些钩子用在其他钩子上！设想一个场景，您需要根据当前令牌获取一个用户。我们已经有了一个令牌挂钩，现在我们需要的是一个用户挂钩。

```
// useCurrentUser.js
const GET_CURRENT_USER = gql`
  query GetCurrentUser($token: String) {
    currentUser(token: $token) {
      id
      username
      avatar
      email
    }
  }
`;

export default () => {
  const token = useToken();
  return useQuery(GET_CURRENT_USER, {
    variables: {
      token
    }
  });
} 
```

Enter fullscreen mode Exit fullscreen mode

现在，无论何时你想要当前用户，你只需使用`useCurrentUser`钩子，你就可以得到你需要的一切！

使用这种方法，我将所有的 graphql SDL 和逻辑整合到一个钩子中，可以在任何功能组件中调用这个钩子。此外，graphql SDL 可以从钩子中导出，以便在组件之外使用。

我使用[阿波罗反应钩子](https://github.com/trojanowski/react-apollo-hooks)作为`useQuery`和`useMutation`钩子。