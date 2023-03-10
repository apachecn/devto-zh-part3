# 构建您的第一个 WebApp —与前端集成

> 原文：<https://dev.to/bhavaniravi/building-your-1st-webapp-integrating-with-frontend-51oo>

### 构建您的第一个 WebApp —与前端集成

*如何将后端 API 集成到 ReactJS 前端。*

这是*用 Python 构建你的第一个 WebApp 系列的第三篇博客。* [在第一篇博客中，我们看到了如何编排和设计后端系统](https://medium.com/@bhavaniravi/learn-reactjs-by-building-a-chat-frontend-2d8fe664276e?source=your_stories_page---------------------------)，编写 Python Flask 代码来公开 REST APIs，然后是[如何拆分前端设计并获得 react UI。](https://dev.to/bhavaniravi/learn-reactjs-by-building-a-chat-frontend-24oi)

在本次会议/博客中，我们将把它集成到一个前端 ReactJS 应用程序中。让它离你的用户更近一步。

现在，我不是前端开发人员。我可能不是教授最佳实践的最佳人选。所以我做的是我已经研究了多年(只是做了谷歌搜索)并找到了最好的教程(第一个 Github repo)让你开始。

在这一点上，你可能会被催促逃跑。但是记住，我刚刚把我的黑客技术给了你。如果你做不好某件事，比如说 CSS，不要从头开始。从 Github 中搜索一个干净的、有文档记录的代码库，并试用它。

为了这个练习的简单，我分叉了代码并删除了复杂的部分。

我们要寻找的最终输出看起来像这样。

[![](img/0321f6a7bdf8ede9d838fa69afdc0387.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--NINIg3Re--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/600/1%2AgcUr59uDGbVmxjxFPQ9_wA.png)

### 前行之前

虽然这个博客旨在对初学者友好，但你需要在继续之前做一些工作。

1.  你知道 python 吗？
2.  你有没有尝试并实现以前博客中的 flask backend？
3.  你知道反应吗？—至少 setState 是这样做的？

*警告！！！—不要让自己因为没有先决条件而无法理解任何事情而感到沮丧。*

### 我们开始吧

在这一点上，我假设并向所有的神祈祷，你已经阅读并尝试了第一个博客，因为从这里开始，它变得有点陡峭。

1.  分叉和[克隆回购](https://github.com/kabirbaidhya/react-todo-app)
2.  cd 反应-待办事项
3.  npm 安装
4.  npm 开始

它将启动您的开发服务器。在你的浏览器上，打开 localhost:3000 和 ta-da 你就会看到一个功能齐全的待办事项应用。但是不要太兴奋了。添加几个项目并刷新页面，它就消失了，不是吗？这就是为什么你需要一个与数据库耦合的后端系统。来保存我们输入的数据。

### 代码里有什么？

```
react-todo-app
    |\_\_\_ node\_modules # virtualenv for JS apps
    |\_\_\_ public # rendered react items are in public
    |\_\_\_ src # Actual code you write
         |\_\_ assests # all the images, icons required
         |\_\_ components # Common items like textbox, buttons are 
                         # designed and maintatined for reuse

         |\_\_ service **# business logic handler**
    |\_\_ index.js # Entry point of the app 
```

虽然看起来很多，但我们将只关注单个文件夹 src 和 index.js. Index.js 是应用程序的起点。该文件除了链接到“让我们看看 App.js 得到了什么”之外，没有任何其他内容。

```
class App extends Component {
     render() {
         return (
              <StateProvider>
                    <TodoList/>
              </StateProvider>
          );
}} 
```

深入挖掘 StateProvider、TodoList 中的每一个元素，您将看到这些组件是如何组合在一起形成这个漂亮的 UI 的。

[![](img/b7c40352dd95a40eb93fe1bf6d47ae4b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--x2jO67GT--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/1024/1%2AduTbxpeQhZRLLeOn4-uvNA.png)

正如您所见，主要组件 StateProvider 不在图片中。它是一个包装器，向所有其他组件提供数据状态。在 React 术语中，这被称为上下文。要了解更多关于上下文的信息，请点击这里查看官方博客。

### 整合

我们刚刚看到了 UI 如何被编排到不同的 react 组件中，以及 StateProvider 如何用所需的数据(`state`)包装它们

在我们继续看代码之前，让我们列出我们需要的 API，并编写一个实用程序来使用它们。

1.  第一次打开应用程序时，我们会调用/todo GET API 来列出所有创建的待办事项。

```
export function getAll() {
    return service.get(BASE\_URL + `/todo`, { data: {} })
}) 
```

1.  在输入一个 todo 项并按 enter 键后，它应该调用我们的 flask API — /todo POST，并带有参数{"title": }

```
export function addToList(list, data) {
     return service.post(BASE\_URL + `/todo`, 
                         { body: JSON.stringify(data)})
} 
```

1.  单击复选框后，我们将使用/todo/todo_id PUT API 更新 todo 项目的{is_done:true}

```
export function updateStatus(itemId, completed) {
    return service.put(BASE\_URL + `/todo/` + itemId, 
            {body: JSON.stringify({"\_is\_done":completed})})
} 
```

1.  在单击删除时，我们将使用/todo/ DELETE API 来删除该项

```
export function deleteItemService(itemId) {
     return service.delete(BASE\_URL + `/todo/` + itemId, {})
} 
```

因此，我们重新编写了 4 个主要函数，它们构成了 todo 的数据部分。现在，每当有人输入一个待办事项，它就会被存储在一个数据库中。

这是我与后端分支和集成的回购协议，继续克隆它，并将其插入我们上次编写的后端。

[*喜欢刚刚看的？给我在推特上喊出来(@*](http://twitter.com/geeky_bhavani) *【极客 _ 巴瓦尼】* [*)。*T9】](http://twitter.com/geeky_bhavani)