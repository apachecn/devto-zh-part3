# React 播客第 31 集——瑞安·弗洛伦斯的《钩子是混搭品》

> 原文：<https://dev.to/dance2die/react-podcast-episode-31--hooks-are-mixins-with-ryan-florence-fon>

*照片由[雅各布·卡普斯纳克](https://unsplash.com/photos/4f4YZfDMLeU?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/search/photos/bibimbap?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5 拍摄*

Ryan Florence 出现在 React 播客第 31 集，谈论 React 中的钩子和其他新功能。

播客是在 React Conf 2018 之前录制的，因此涉及一些时间旅行😁。

**免责声明**:我与播客无关，只是一个热心的听众😛。

## 缩略词

我使用了 React 生命周期方法名称的简化名称。

*   CDU–comopendupdate
*   cDM–组件安装
*   cWU – componentWillUnmount

## 📃目录

*   [简介](#intro)
*   [挂钩](#hooks)
*   [90%的清洁剂发生反应](#%E2%99%BB-90-cleaner-react)
*   [暂记+资源(缓存)](#-suspense-resources-cache)
*   [指令？](#directives)
*   [结束语](#closing-comments)
*   [资源](#resources)

## 🏹介绍

Ryan 认为人们抱怨反应过度是“有点不公平的”。

他的解释是，新的 React APIs 将支持更简单、更小的代码。

#### 🔹框架还是库？

Ryan 说 Ruby 一直是一个框架，因为“你把顶层组件交给 ReactDOM.render & React 调用它的所有生命周期”。

他将这个过程与 Ruby on Rails & Ember frameworks 等框架进行了比较(3:47)。

但是在这一集的后面(35:11)，Chantastic 建议 React 仍然不是一个框架，因为 React 没有告诉你使用钩子、悬念或资源。

#### 🔹反应过来，抓住方向盘🎡

Chantastic 提到了 Ryan 的博客文章 [React take the wheel](https://medium.com/@ryanflorence/react-context-and-re-renders-react-take-the-wheel-cd1d20663647) ，其中 Ryan 谈到让 React 处理突变。

在 React 的旧 API(比 Ryan 的“遗留”更好的术语)中， ***React 告诉我们*** 它在做什么。

*   cDM–我将安装
*   cDU–我更新了这个

我们必须想出如何处理它。

但是有了新 React API，

*   ***我们用悬念得到定义*** 我们需要什么&缓存(资源)
*   ***React 告诉我们*** 需要做什么。
    *   `getSnapshotBeforeUpdate` & `getDerivedStateFromProps`告诉我们需要做什么，而不是 React 在做什么。
*   ***我们有一个控制*** 生命周期的方法(`useEffect`)。

我们对我们的代码有更直接的控制，从而产生更简单和可读的代码。

## ⚓钩

#### 🔹什么是钩子？

借用[丹演讲中的一个类比](https://youtu.be/V-QO-KO90iQ?t=3420)在 React Conf 2018 中，

`Atom`曾被认为是物质的最小单位但是电子被发现了，它一直都在。

钩子是 React 中一直存在的功能，分布在整个类中，但现在被发现并命名了。

这是一种跨类处理我们正在做的所有事情的方法，保持我们的状态(`useState`、`useReducer`、`useContext`)并管理功能组件内的生命周期(`useEffect`)，从而实现高度的可组合性。

Ryan 建议不要为了使用新的 API 而迁移所有东西。

*贾里德·帕尔默在 Ep 29 中给出了同样的建议，“* [*不要用贾里德·帕尔默*](https://reactpodcast.simplecast.fm/29)*”*来重写你的应用程序

#### 🔹挂钩的类型

*   [使用状态](https://reactjs.org/docs/hooks-reference.html#usestate)–替换设置状态
*   [使用效果](https://reactjs.org/docs/hooks-reference.html#useeffect)–取代 cDM、cDU、cWU 等生命周期方法。
    *   *`getSnapshotBeforeUpdate`和`componentDidCatch` [尚不支持](https://reactjs.org/docs/hooks-faq.html#do-hooks-cover-all-use-cases-for-classes)。*
*   [使用 memo](https://reactjs.org/docs/hooks-reference.html#usememo)–记忆数值(数据、组件等)。
*   [useReducer](https://reactjs.org/docs/hooks-reference.html#usereducer)–通过调度来管理减速器的状态。
*   [useLayoutEffect](https://reactjs.org/docs/hooks-reference.html#uselayouteffect)–用于一些边缘情况
    *   *`useMutationEffect`也曾提到过[但后来去掉了](https://github.com/facebook/react/pull/14336)。*
*   useRef–替换 [React.createRef](https://reactjs.org/docs/react-api.html#reactcreateref) 。

使用钩子，不需要实例变量&只使用局部变量。

例如)
不用在`cDM`中设置区间并将 ID 保存在`this.ID`中，使用实例变量在`cWU`中清除，而是创建一个简单的局部变量来存储区间 ID，并在同一个钩子`useEffect`中清除。

#### 🔹钩子是(类比)

1.  “混音做对了”
2.  “不吸功能组件的 Mixins”。

*参考 [Mixins](https://reactjs.org/docs/react-without-es6.html#mixins) 文档。*

Hooks 解决了渲染道具的“错误层次”(Ryan 称之为“语法灾难”)。

## ♻ 90%清洁剂起反应

#### 1。钩子让我们在几行代码中共同定位单个关注点

例如)当您有一个文档标题更新代码，以及分布在生命周期方法中的事件订阅代码时，您现在可以将每个不相关的代码(更新文档标题和取消/订阅事件)分离到不同的代码段中(分离`useEffect`或您自己的钩子)。

#### 2。钩子需要更少的代码，并且更易移植

使得重构和提取逻辑在其他地方使用变得容易。

所有的状态和效果都位于彼此旁边，所以很容易剪切和粘贴(重构)。

React 表面上看起来很简单(开发人员只需要处理组件)，但是你为了完成简单的任务(获取/读取数据和取消/订阅事件)而处理的所有事情，代码变得很大。

[*90%清洁工 React 谈*](https://youtu.be/wXLf18DsV-I) *在 React Conf 2018*

## 🌉+💲暂记+资源(缓存)

**悬念&资源允许你像对待同步值一样对待数据。**

有什么合适的处理钩子、悬念、资源的方式？

以前我们只有组件，但大多数应用程序也使用 Redux。

所以用钩子来代替状态和生命周期。

大部分人用 Redux 做客户端缓存，可以用悬念和缓存代替。

*因此，React 感觉更小(如之前在 3:10 指出的)*

## 👉指令？

指令是“你放在一个元素上的属性，并给它添加一个行为”。

Hooks 允许您向元素添加行为，而不必使用渲染道具向组件提供数据。

我需要一些这方面的例子…有点困惑。
*参考[本 Reddit 评论](https://www.reddit.com/r/reactjs/comments/a4p7e0/an_unofficial_show_note_for_react_podcast_episode/ebi8dcd/)关于属性的解释。*

## 🚪结束语

*   瑞安对 React 的未来非常乐观，因为贾里德·帕尔默在第 29 集中。
*   抓紧它(钩子)。
*   钩子可以共享而不用交换。
    *   而且“更容易输入，因为你不用输入一堆道具”，尤其是渲染道具。
    *   对于函数组件，props 只是函数的参数，这也使其易于键入

订阅 Chantastic 的 [React Holiday](https://react.holiday/) ，他将在播客中介绍新的 API。

## 🗻资源

*   React 播客[第 31 集](https://reactpodcast.simplecast.fm/31)。
*   React 播客[第 29 集](https://reactpodcast.simplecast.fm/29)。
*   [官方挂钩文档](https://reactjs.org/hooks)
*   Ryan Florence 的文章

如果你想知道[目录](#table-of-contents)是如何添加的，参考下面的公告。

[![andy image](img/1d3af2881c3a8b9e2c3b908e00ad9727.png)](/andy) [## 降价标题现在被锚定！

### 安迪赵(他/他)11 月 29 日 181 分钟阅读

#meta #changelog](/devteam/markdown-headers-are-now-anchored-18bg)

*帖子 [React 播客第 31 集——瑞安·弗洛伦斯](https://www.slightedgecoder.com/2018/12/09/react-podcast-episode-31-hooks-are-mixins-with-ryan-florence/)的“钩子是 Mixins”首先出现在[宋的技术博客](https://www.slightedgecoder.com)上。*