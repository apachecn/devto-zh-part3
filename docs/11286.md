# React Redux 连接器模式

> 原文：<https://dev.to/pigozzifr/react-redux-connectors-pattern-51oc>

## TL；速度三角形定位法(dead reckoning)

别担心，我不是来讨论第无数个包裹的。

这篇文章更像是对一种简单而强大的方法的反思，这种方法可以避免在所谓的**容器**和我们通常所说的**组件**之间产生依赖。

你们中的许多人会找到解决这个问题的方法；没有的人，请继续阅读。

## 问题

有多少次你发现自己在一个新项目面前，可能是使用`create-react-app`创建的，带着良好的意愿不重复在以前的项目中产生技术债务的相同错误？我已经想象出我们中的许多人在那个时刻:

*“这一次代码库将是结构良好的”*，*“文件夹将只有一层子文件夹，可能有两层”*或*“我不会再将组件绑定到容器上”*。

最后的名言。

## 推理

当我们谈论**容器**时，我们只定义了从特设:**连接**中获得丰富信息的组件。

问题不在于一次又一次地编写 **mapStateToProps** 、**mapdispatctoprops**和 **mergeProps** (是的，还有第三个参数，这太棒了)。问题是，要知道如何处理这些连接到 Redux 商店的组件。

“我在其中包含了表象逻辑吗？但如果我必须移动它或模块化它，我应该重写一切……”

## 天真的解决方法

*“事实上，我看不出**组件**和**容器**之间的区别...现在，我将确保每个组件都可以在它需要的时候以及如何访问 Redux 的状态，毕竟我们正在讨论上下文。*

[![](img/8cfdd2ed1f29eae4518613fb8b9d1caa.png)](https://i.giphy.com/media/vNp0u58iw8FPn8F1jN/giphy.gif)

## 连接器(？)

有一段时间，我喜欢问自己这样一个问题:“我想如何写/使用它？”在 Javascript 或 React 中实现任何实用程序之前。

同理，要解决这个问题，我想这样写:

```
<Layout>
  <Layout.Header>
    <h1>Header</h1>
  </Layout.Header>
  <Layout.Main>
    <PostsConnector>
      {({ posts }) => posts.map(post => (
        <div key={post.id}>
          <h2>{post.title}</h2>
          <p>{post.body}</p>
        </div>
      ))}
    </PostsConnector>
  </Layout.Main>
</Layout> 
```

这种看待它的方式让我想起了很多 Javascript 闭包。

毕竟，React 是一个强大的库，它将自由和活力留给了开发人员，无论是否在 JSX，他们都可以根据自己的喜好创建组件树。

为什么在这一点上，我不能利用一些通用的 Javascript 编程概念来解决这种特定的问题呢？

## 连接器的创建

```
// imports

class PostsConnector extends Component {
  componentDidMount() {
    const { fetchOnMount, fetchPosts } = this.props

    if (fetchOnMount) {
      fetchPosts()
    }
  }

  get childrenProps() {
    const { posts, fetchPosts, createPost } = this.props

    return {
      posts,
      fetchPosts,
      createPost,
    }
  }

  render() {
    const { children } = this.props

    return children(this.childrenProps)
  }
}

// PropTypes and connect's logic 
```

或者使用 React 钩子

```
// imports

const PostsConnector = ({
  children,
  fetchOnMount,
  fetchPosts,
  // Redux
  posts,
  fetchPosts,
  createPost,
}) => {
  useEffect(() => {
    if (fetchOnMount) fetchPosts()
  }, [])

  return children({
    posts, fetchPosts, createPost,
  })
}

// PropTypes and connect's logic 
```

Done ✅

## 奖金:`composeConnectors`

这一切都很好，但是..“如果我在彼此体内有更多的连接器呢？”。

在这种特定情况下，您的问题可能是连接器太多。

然而，在这种情况下，您仍然希望继续并尽量避免连接器——见鬼，并编写类似这样的代码:

```
const ComposedConnector = composeConnectors(
  <PostsConnector />,
  <PostConnector postId="super-secret-id" />,
)

<ComposedConnector>
  {props => console.log(props) || null}
</ComposedConnector> 
```

您可以使用此实用功能:

```
import React from 'react'

// return ({ children }) => (
//   React.cloneElement(connectors[1], null, secondProps => (
//     React.cloneElement(connectors[0], null, firstProps => (
//       children({ ...firstProps, ...secondProps })
//     ))
//   ))
// )

const composeConnectors = (...connectors) => (
  connectors.reverse().reduce((composed, connector) => (
    ({ children, ...childrenProps }) => (
      React.cloneElement(
        connector,
        null,
        connectorProps => composed({ ...childrenProps, ...connectorProps, children }),
      )
    )
  ), ({ children, ...props }) => children(props))
)

export default composeConnectors 
```

## 感谢阅读

如果你喜欢这篇文章，请告诉我；如果没有，无论如何让我知道它。

[![](img/913a466cb191e56e22c194fad16fec94.png)](https://i.giphy.com/media/upg0i1m4DLe5q/giphy.gif)