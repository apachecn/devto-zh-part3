# 新手学习 VueJS 的收获

> 原文：<https://dev.to/tangweejieleslie/a-newbie-s-takeaway-from-learning-vuejs-5b0l>

## 有些前后关系

*   我最近决定开发我的第一个 web 应用程序。
*   我选择 VueJS 作为前端框架，因为我听说它很简单。
*   我已经创建了普通的 HTML 和 CSS 页面，可以随时改编成 VueJS
*   我写这篇文章是因为有人说教学是最好的学习方式😄

## 我对 VueJS 的了解

在浏览了一些 YouTube 视频并试用了 VueJS 之后，我注意到 VueJS 应用程序有 5 个主要部分。

#### 1。Index.html

这只是一个普通的旧的常规 html 文件。需要注意的主要一点是，每个 Vue 应用程序都呈现在一个 div 容器中，该容器的**必须由元素 ID 指定。**

#### 2。主页. js

我认为这个文件管理应用程序中使用的各种节点包。需要注意的主要一点是，Vue 应用程序在这里与要在其中呈现的应用程序的元素 ID 一起被实例化。

#### 3。应用程序视图

通过`vue create app-name`创建的默认组件被命名为`App.vue`。这是在 index.html 渲染的主要部分。所有其他子组件都应该在这里导入。关于创建 Vue 应用的更多细节可以在 VueJS.org 的[上找到。](https://cli.vuejs.org/guide/creating-a-project.html#vue-create)

#### 4。子组件

就我个人而言，我已经基于主要的 HTML 标签创建了子组件，即和。这是有意义的，因为我不希望导航条和页脚有重复的代码。主标签也是一个子组件，因为每个页面/路径在标签中都有不同的内容。

#### 5。视图-路由器

[Vue-Router](https://www.npmjs.com/package/vue-router) 是一个节点包。这用于根据 URL 路由提供不同的内容，例如`website.com/new`。如果我不使用 Vue-Router，我可能不得不为我想传递给用户的每一页内容创建单独的 html 文件。😩

[![Summary of Relation between the 5 pieces](img/0ce5d054e0b3884166893f1bb3600482.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--DeU2X6Og--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5bhzk5se4cp8mi1dhbx5.JPG) 
*我是如何看待每一件作品与另一件作品的关系的*

## 关键要点

我在实施 VueJS 时的一些要点:

*   记住定义元素#ID 来呈现每个 Vue 应用程序
*   导出每个子组件
*   将子组件导入父组件
*   有许多有用的 Vue 指令让生活变得简单，比如`v-for`和`v-model`，好好利用它们吧！
*   每个组件都有三个部分:模板、脚本和样式
*   记得把`scoped`包含在风格部分，防止意外的设计噩梦！
*   如果启动 VueJS 看起来太难了，你只需要知道你可以用普通的 HTML 增量实现它😄

干杯！

希望这篇文章对那些对学习 VueJS 感兴趣的人有所帮助。

此外，如果任何人发现我对 VueJS 有任何错误/误解，请让我知道或在评论中留下！如果有人被误导了，那就太可怕了😧

## 参考文献

这些是我用来学习 VueJS 的资源列表。

*   [净忍者伏特加酒播放列表](https://www.youtube.com/playlist?list=PL4cUxeGkcC9gQcYgjhBoeQH7wiAyZNrYa)
*   [Traversy Media 的 Vue JS 速成班](https://www.youtube.com/watch?v=Wy9q22isx3U&t=490s)
*   [Flavio Copes 的 Vue 指令备忘单](https://flaviocopes.com/vue-cheat-sheet/)