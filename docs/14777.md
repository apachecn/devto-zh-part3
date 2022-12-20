# 告别生命周期方法，专注于高效的代码

> 原文：<https://dev.to/hybrids/how-to-say-goodbye-to-lifecycle-methods-and-focus-on-productive-code-175>

这是关于 [hybrids](https://github.com/hybridsjs/hybrids) 核心概念系列文章的第二篇——这是一个用简单实用的 API 创建 Web 组件的库。

基于组件的 UI 库最根深蒂固的特性之一是复杂的生命周期。它是一组方法，提供对可能随时间变化的组件状态的完全控制。通常，库使用自我解释的命名约定，在事情发生后调用 did*方法，在改变前调用 will*。在研究图书馆文件时，我们经常会发现一系列的可能性，这可能会导致困惑甚至沮丧。毕竟你需要有深入的理解，才能创建正确高效的代码。例如，组件状态可能依赖于事件在时间上的特定顺序，这使得代码难以测试并最终维护或扩展。

## 有那么糟糕吗？

让我们面对生命周期方法的两个模糊事实。首先，他们把国家管理的负担从图书馆转移到我们身上。这看起来很合理，但通常意味着我们必须手动编写更多冗余代码:

```
class MyComponent extends Component {
  componentDidUpdate(prevProps) {
    if (this.props.name !== prevProps.name) {
      // do something...
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，库提供了以前属性的映射，但是它没有通知它们中的哪一个有新值。我们必须显式地创建条件，以确保只有在`name`属性发生变化时才调用我们的代码。

另一方面，如果一个组件需要异步数据，生命周期结构可能会强制提取数据两次——第一次在类似于`componentDidMount()`的方法中，然后每次在`componentDidUpdate()`中当依赖关系改变时:

```
import { getUser } from './api';

class MyComponent extends Component {
  componentDidMount() {
    this.fetch();
  }

  componentDidUpdate(prevProps) {
    if (this.props.userId !== prevProps.userId) {
      this.fetch();
    }
  }

  fetch() {
    getUser(this.props.userId)
      .then((data) => this.setState({ data }));
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

即使我们已经将冗余逻辑提取到了`fetch()`方法中，它也必须在两个独立的生命周期方法中被调用两次。

这两个代码示例对您来说可能都很熟悉。事实上，它们代表了`React.Component`类所提供的内容。React 当然不是一个 web 组件库，但是 [LitElement](https://lit-element.polymer-project.org/guide/lifecycle) 、 [Omi](https://github.com/Tencent/omi#lifecycle) 、 [Slim.js](http://slimjs.com/#/component-lifecycle) 、 [Stencil](https://stenciljs.com/docs/component-lifecycle) 和许多其他的跟随潮流，他们实现了非常相似的概念(使用链接去库文档的生命周期部分)。

在本系列的第一篇文章中，我们已经了解了如何将组件定义从类语法转换为独立属性描述符的映射。如果你还没有读过，现在是时候了:

[![smalluban image](img/6861718a2fd849252ddb6888604c411f.png)](/smalluban) [## 从类到简单的对象和纯函数

### 张秀坤·卢巴斯基 1919 年 1 月 10 日 7 分钟阅读

#webdev #webcomponents #javascript #functional](/hybrids/from-classes-to-plain-objects-and-pure-functions-2gip)

这次我们将更深入地了解属性描述符的定义，并了解更多关于缓存机制、变更检测及其`connect`方法的信息。

## 不同的做法

生命周期方法促使我们更多地考虑事情发生的时间，而不是定义 T2 如何得到 T4 需要的东西。如果您可以专注于价值计算，而将其他工作留给库，您会说什么？

混合属性描述符概念引入的不仅仅是一个用于保存属性值的中间件。该库提供了完整的缓存和更改检测机制。

需要异步获取数据的组件可以像下面这样用混合定义:

```
import { html } from 'hybrids';
import { getUser } from './api';

const AsyncUser = {
  userId: 1,
  data: ({ userId }) => getUser(userId),
  render: ({ data }) => html`
    <div> ${html.resolve(
        data.then(user => html`
          <span>${user.firstName}</span>
        `),
      )} </div>
  `,
}; 
```

Enter fullscreen mode Exit fullscreen mode

[点击这里玩一个⚡️StackBlitz 的真实例子](https://stackblitz.com/edit/hybrids-async-user?file=async-user.js)

上述定义包括`userId`、`data`和`render`描述符。`data`属性依赖于`userId`，并返回一个包含用户详细信息的承诺。现在不要太在意`render`属性。现在您应该知道它使用了`render`工厂(使用属性转换)，该工厂使用`html`函数来创建和更新定制元素的内容。在模板的主体中，我们使用动态值，它将`data` promise 解析为一个包含用户名字的元素。

## 缓存机制

缓存机制附加到由库定义的每个属性的 getter 和 setter 上。对于`set`方法，如果计算返回新值，它会自动更新缓存。For `get`方法缓存确保仅在需要时才计算值，例如，当一个属性依赖关系发生变化时。在我们的例子中，这意味着只有当`userId`改变时，才会调用`getUser()`来设置初始值。它是如何工作的？

缓存控制着`data`以及`userId`属性。当在`data` getter 内部调用`userId`时，缓存可以将其保存为`data`依赖项。下一次，当我们调用`data`时，缓存从缓存中检查`userId`，并且仅当`userId`已经改变时才调用`getUser(userId)`。否则，它返回最后一个缓存的值并省略 getter。缓存对于库定义的所有元素都是全局的，所以我们也可以依赖其他元素中定义的属性！

缓存概念利用了这样一个事实:如果属性没有被调用(即使依赖关系已经改变)，它们就永远不会被计算。您可以尝试手动获取值`data`,您会看到，它始终返回相同的承诺。然而，如果你改变`userId`属性，`data`将返回一个新的承诺，名为下次。

## 简化的生命周期

在第一篇文章中，我们了解到属性描述符可能有`get`和`set`方法。实际上，您可以为资产生命周期控制定义两个以上的方法- `connect`和`observe`。`connect`方法可以返回一个函数，当一个元素断开时调用这个函数。而当属性值改变时，异步调用`observe`方法。

```
{
  get: (host, lastValue) => {...},
  set: (host, value, lastValue) => {...},
  connect: (host, key, invalidate) => {
    // ...
    return () => {...}; // disconnect
  },
  observe: (host, value, lastValue) => {...},
}; 
```

Enter fullscreen mode Exit fullscreen mode

然而，在上面的`AsyncUser`例子中，我们不需要显式地使用它。我们甚至根本不需要创建属性描述符！如果我们将所有的概念放在一起，我们可能会开始看到一个更大的画面。原始描述符提供了创建有状态属性所需的所有特性。然后，该库在缓存机制的基础上进行添加。然而，定义属性的首选方法是使用内置或定制的工厂(函数，产生描述符)。由于属性定义是独立的，所以您可以在任何需要的地方重用工厂。因此，您不必自己定义`connect`方法，并且可以通过声明的方式专注于生产性编码！

## 失效

您可能已经注意到了`connect`方法的第三个参数——`invalidate`回调。如果一个属性只有一个 getter，但是它依赖于第三方工具，`invalidate`是一个通知缓存的聪明方法，下次应该计算那个值。由于功能结构，创建连接到外部状态管理器的属性非常容易，如 redux:

```
import store from './store';

function connect(store, mapState) {
  return {
    get: (host) => mapState(store.getState(), host),
    connect: (host, key, invalidate) => store.subscribe(invalidate),
  };
}; 
```

Enter fullscreen mode Exit fullscreen mode

Redux `subscribe`方法在我们可以传递`invalidate`的地方接受回调。它返回 unsubscribe 函数，所以我们可以在定义为 arrow 函数的 connect 方法中调用它。我们可以在组件定义中使用工厂，就像下面的例子:

```
import store from './store';
import connect from './connectFactory';

const MyElement = {
  userId: 1,
  userDetails: connect(store, ({ users }, { userId }) => users[userId]),
}; 
```

Enter fullscreen mode Exit fullscreen mode

## 改变检测机制

在文章的最后一部分，让我们回到`render`属性。如果库不为我们调用 getters，我们的组件怎么可能工作？尽管`render`看起来很特别，但它和其他的属性描述符是一样的吗？区别在于`render`工厂如何使用`connect`和`observe`的方法。

理解`render`如何工作的最好方法是构建一个简化版本:

```
function render(fn) {
  return {
    get: (host) => fn(host),
    connect: (host, key) => {
      if (!host.shadowRoot) host.attachShadow({ mode: 'open' });
    },
    observe: (host, fn) {
      fn(host, host.shadowRoot);
    },
  };
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的`render`工厂用`get`、`connect`和`observe`方法返回描述符。我们利用了缓存机制，所以我们的 getter 调用`fn`并保存它的依赖项。仅当`fn`中使用的属性之一改变时，属性值才会重新计算。

如果不存在的话，`connect`创建`shadowRoot`。然后，每当依赖关系改变时，我们想调用`fn`。这正是`observe`方法所提供的。来自其他库的`componentDidUpdate()`回调可能看起来很熟悉。最终，当变化发生时，我们想要做些什么。然而，`observe`方法背后的思想要深刻得多。只有当属性值更改时，库才会调用它。由于使用`requestAnimationFrame` API 调度的内部队列，该方法在当前事件循环中只被调用一次。我们不必费心检查哪个属性有新值，因为我们用缓存机制覆盖了它。

## 总结

可能会有很多新东西需要处理。当然，混合动力车并没有放弃生命周期方法。它们只是被重新设计并以与其他库中的模式相反的方向实现。在所解释的组件示例中，因果链从呈现属性到数据(在其他库中，它将从获取数据到呈现新状态)。一个创建模板的函数需要用户的详细信息，正因为如此，才会获取这些信息，并最终触发模板的更新。如果在某些情况下模板不需要这些数据，那么就根本不会获取它们。

我们可以称之为**简化的生命周期**。如果我们再加上智能缓存机制和所有已知的基于属性的概念，一切都会改变。我们可以将大部分与状态相关的责任转移给库，并专注于组件的业务逻辑。通常，组件需要一个属性列表来保存简单值或计算值，并需要呈现方法来创建元素结构。如果我们需要库中没有的东西，我们可以很容易地创建可重用的工厂，并且仍然不直接使用生命周期方法。

## 接下来是什么？

今天，我们已经了解了`render`工厂的表面。在本系列的下一篇文章中，我们将了解更多关于该库提供的 render factory，以及构建在标记模板文字之上的丰富模板引擎。

同时，你可以在[项目文档](https://hybrids.js.org)中阅读更多关于混合库的内容。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png)[hybrids js](https://github.com/hybridsjs)/[hybrids](https://github.com/hybridsjs/hybrids)

### 从普通对象和纯函数创建 web 组件的最简单方法！💯

<article class="markdown-body entry-content container-lg" itemprop="text">

# [![hybrids - the web components](img/7835334dab31a0355c19ba1881e91e6e.png)](https://raw.githubusercontent.com/hybridsjs/hybrids/master/docs/assets/hybrids-full-logo.svg?sanitize=true)T3】

[![npm version](img/e38aed3fd42d3a246ff244f63d653e08.png)](https://www.npmjs.com/package/hybrids)[![bundle size](img/e7096235243d88623d28c0853db0d596.png)](https://bundlephobia.com/result?p=hybrids)[![types](img/a28e08b57c6b70b0919ab8465c56c835.png)](https://github.com/hybridsjs/hybrids/blob/master/types/index.d.ts)[![build status](img/6acbe72ed8add64a16839f48caf1b2c4.png)](https://travis-ci.com/hybridsjs/hybrids)[![coverage status](img/efe2f85a0edbb1ade946f6fee616132a.png)](https://coveralls.io/github/hybridsjs/hybrids?branch=master)[![npm](img/9668c23a207d986b16e5fc71f7b421ae.png)](https://www.npmjs.com/package/hybrids)[![gitter](img/c4b1678de1d3017fd92d1ceb3704f144.png)](https://gitter.im/hybridsjs/hybrids)[![twitter](img/0bd5d9f1184388e52b4a074597fa5b12.png)](https://twitter.com/hybridsjs)[![Conventional Commits](img/5c9ec1154f801f65b71e877883f3927c.png)](https://conventionalcommits.org)[![code style: prettier](img/76245b01cc15290741bd336634799095.png)](https://github.com/prettier/prettier)[![GitHub](img/742d640928cb2cc526e6b43c4de7a98f.png)](https://raw.githubusercontent.com/hybridsjs/hybrids/master/LICENSE)

> <g-emoji class="g-emoji" alias="medal_sports" fallback-src="https://github.githubassets.cimg/icons/emoji/unicode/1f3c5.png">🏅</g-emoji>2019 年[开源奖](https://osawards.com/javascript/)的**“年度突破”**类四个提名项目之一

**hybrids** 是一个 UI 库，用于创建 [web 组件](https://www.webcomponents.org/)，它具有基于简单对象和纯函数的独特声明性和函数性方法。

*   **最简单的定义**——只是简单的对象和纯粹的函数——没有`class`和`this`语法
*   **无全球生命周期** —独立的资产，拥有自己简化的生命周期方法
*   **继承之上的组合** —易于重用、合并或拆分属性描述符
*   **超快速重新计算** —智能缓存和变更检测机制
*   **全局状态管理** -支持外部存储的模型定义
*   **没有外部工具的模板** —基于标记模板文字的模板引擎
*   **开发人员工具包括** —开箱即用的 HMR 支持，实现快速愉快的开发

## 快速查找

将 hybrids [npm 包](https://www.npmjs.com/package/hybrids)添加到您的应用程序中，导入所需的特性，并定义您的自定义元素:

```
import { html
```

…Enter fullscreen mode Exit fullscreen mode</article>

[View on GitHub](https://github.com/hybridsjs/hybrids)

* * *

🙏你如何支持这个项目？给 GitHub 库一个⭐️，在⬇️下面评论，把混血儿的消息传播到全世界📢！

* * *

👋欢迎开发者加入社区！我的名字是 Dominik，这是我写的第三篇博客——欢迎任何反馈，❤️.

<small>保罗·斯科鲁普斯卡斯在[Unsplash](https://unsplash.com)T5 拍摄的封面照片</small>