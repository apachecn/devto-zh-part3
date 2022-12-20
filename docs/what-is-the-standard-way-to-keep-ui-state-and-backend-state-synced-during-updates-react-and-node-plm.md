# 在更新期间保持 UI 状态和后端状态同步的标准方法是什么？(反应和节点)

> 原文：<https://dev.to/lennythedev/what-is-the-standard-way-to-keep-ui-state-and-backend-state-synced-during-updates-react-and-node-plm>

假设我有一个带有 REST API 的 Node-Express 后端和一个带有可编辑卡的 React 前端。在应用程序加载时，我获取一个*项目数组*并设置状态

```
componentDidMount() {
    axios.get('/items').then(() => {
        this.setState({ items: res.data })  // res.data is array of items
    })
} 
```

当用户更新卡时，我向后端发送一个 PUT 或 POST 请求，
更新卡的详细信息(有或没有 DB 都没关系，只是假设后端状态被更新)。很好。

我的问题是，更新 UI 的最好方法是什么？
做休息的时候有标准的方式吗？

以下是我能想到的主意:

*   在我的服务器 PUT/POST 路由处理程序中，我可以返回 *items 数组*，包括更新的那个。在 UI 上，我可以做一个简单的`setState({ items: res.data })`。考虑到只有一个条目被更新，返回整个条目数组似乎代价很高。

    *   至少后端是事实的来源，如果更新失败，我可以很容易地告诉 UI 错误发生。
*   在我的服务器 Put/POST 路由处理器上，我可以只返回*更新的项目*。
    然后，我必须在我的状态中找到并替换更新的项目，并执行`setState({ items: res.data })`

    *   很简单，但是感觉我在做额外的工作。但是由于我的数据结构是一个数组，我必须在 UI 状态下替换整个数组。
    *   或者有没有更好的方法在 state 中只更新数组中的一项，或者更好的方法是把我的数据结构改成一个对象，所以 setState 是最优的。
*   在我的服务器 Put/POST 路由处理器上，我可以返回一个*状态代码/消息*来通知 UI 操作成功，现在我可以更新 UI 状态了。

*   在 UI 端，我首先更新，然后将更新发送到后端。

    由于某种原因，如果它在后端失败，那么我回滚 UI 更改(可能很棘手？)

*   在中间使用 localStorage？

还有其他想法吗？

这可能在 GraphQL 中处理得更好，并使用 Flux 等状态管理，但如果我真的使用普通的 Node 和 React，最好的方法是什么？

如果我的问题不够清楚，请告诉我，我可以详细说明

谢谢！