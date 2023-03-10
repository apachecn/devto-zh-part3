# 利用 MobX 中内置的反应能力

> 原文：<https://dev.to/olpeh/utilizing-the-built-in-reactivity-in-mobx-5ca2>

这是我在 dev.to 的第一篇帖子！耶！

这篇博文最初发表在[我的个人博客](https://olavihaapala.fi/)上，但我认为这也是开始在 dev.to 上发表的绝佳机会。

## 背景

在我目前工作的客户项目中，在过去的两年中，我们已经在 4 个不同的 web 项目中使用了(P)react + MobX。这篇博客文章不是对这一技术堆栈的介绍，而是展示我用来解决问题的一个巧妙技巧。

## 问题

我们目前正在构建一个由多个小部件组成的 web 项目，这些小部件可以独立嵌入到 iframe 中。这个需求引导我们设计了这样一个页面，当组件被挂载时，页面上的每个小部件获取它们自己的数据。我们有多个小型 MobX 存储，以保持文件大小较小，存储易于维护和推理。大多数可嵌入的小部件都有自己的 MobX 商店。如果需要，一些公共存储被注入到组件中。到目前为止，这看起来工作得很好。

然而，我们现在需要在页面顶部包含一个刷新按钮，它将刷新页面上所有小部件中的数据。然而，这些小部件是独立的，不知道同一页面上的其他小部件。这是一个有趣的问题，我们决定尝试提出最好的解决方案。

### 可能的解决方案

最简单的解决方案是让 refresh 按钮刷新整个浏览器选项卡，这样最终也会刷新数据。从性能或用户体验的角度来看，这不是最佳的。

```
<button onClick={() => location.reload(true)}>Refresh</button> 
```

Enter fullscreen mode Exit fullscreen mode

我们想到的另一个解决方案是有一个公共存储，它有一个刷新方法，可以通过单击刷新按钮来触发。这并不是最优的，因为全局存储无法知道页面上实际上有哪些小部件，并且最终会刷新所有的部件，而不仅仅是那些需要刷新的小部件。

代码看起来应该是这样的:

```
export class GlobalSharedStore {
    // Imaginary array that contains all the stores that
    // need to be refreshed when refresh button is clicked
    stores = [];

    refreshAll() {
        this.stores.forEach(store => store.refresh());
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这不是最佳的，也肯定不是被动的。

## MobX 方式

利用 MobX 的力量怎么样？

MobX 自带反应性，除非需要深入了解 MobX，否则不需要考虑太多。Mobx 有多种方式对可观察到的事物做出反应，其中之一就是反应。

来自 MobX 文档:

> “请务必注意，副作用只会对数据表达式中访问的数据起作用，这些数据可能少于效果中实际使用的数据。此外，只有当表达式返回的数据发生变化时，才会触发副作用。换句话说:反应需要你在你的副作用中产生你需要的东西。”
> 
> –来源: [MobX 文档](https://mobx.js.org/refguide/reaction.html)

简而言之，反应是一种定义函数的方式，每当反应中定义的可观察属性发生变化时，该函数就会被触发。为了解决在独立小部件中刷新数据的问题，反应似乎正是我们所需要的。我们创建了一个名为`refreshTrigger`的可观察属性，它是一个`number`，目的是通过简单地增加数字来容易地触发变化。这个标志需要在一个共享的商店，在我们的情况下`flagStore`。

稍微简化版的`flagStore` :

```
export class FlagStore {
  @observable
  refreshTrigger: number = 0;

  @action
  triggerRefresh() {
    this.refreshTrigger++;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们将通过创建一个名为`Refreshable`的抽象类来利用这一点，它看起来像这样:

```
import { FlagStore } from './FlagStore';
import { reaction, IReactionPublic } from 'mobx';

export abstract class RefreshableStore {
  private _reaction: IReactionPublic;

  constructor(flagStore: FlagStore) {
    reaction(
      // First param is a function that returns the data that
      // we want to react to when it changes
      () => flagStore.refreshTrigger,
      // Second param is a function that receives the data and
      // a reaction that can be used to dispose the reaction
      // In our case we don't care about the value of the refreshTrigger
      (_, r) => {
        this.fetchData();
        this._reaction = r;
      }
    );
  }

  // Abstract method that needs to be implemented
  // by classes that extend RefreshableStore
  abstract fetchData(): Promise<unknown>;

  // Dispose the reaction, in order to not react anymore
  // Called in componentWillUnmount in order to not fetch data
  // for components that are not mounted anymore
  dispose() {
    if (this._reaction) {
      this._reaction.dispose();
    }
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

在我们的例子中，尽管我们不关心`refreshTrigger`值，我们只关心它已经改变的事实，并且应该触发刷新。

利用这个特性的组件和存储看起来像什么？

组件代码是这样的:

```
import { h, Component } from 'preact';
import { Store } from './store';
import { observer, inject } from 'mobx-preact';
import { FlagStore } from 'stores/FlagStore';

export interface Props {
  flagStore?: FlagStore;
}

@inject('flagStore')
@observer
export class ExampleComponent extends Component<Props> {
  store: Store;

  constructor(props: Props) {
    super(props);
    this.store = new Store(props.flagStore);
  }

  componentDidMount() {
    this.store.fetchData();
  }

  componentWillUnmount() {
    this.store.dispose();
  }

  render() {
    return <div>Irrelevant</div>;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

而`Store`看起来是这样的:

```
import { RefreshableStore } from 'stores/RefreshableStore';
import { action } from 'mobx';

export class Store extends RefreshableStore {
  @action
  fetchData() {
    // Implementation for fetchdata here
  }

  // Nothing more needed here, the store will react to the refreshTrigger
  // and refetch the data if the component is mounted
} 
```

Enter fullscreen mode Exit fullscreen mode

这种方法确保不会发生不必要的刷新，因为只有页面上的小部件才会对触发器做出反应。

## 未来的工作

现在，使用这种方法，我们需要确保所有拥有数据的商店都应该利用`RefreshableStore`来刷新刷新按钮。这可能会使维护变得繁琐，并且可能会导致某些数据在将来不总是被刷新。

## 鸣谢

*   感谢我的同事 Markus 和 Farzad 帮助我实现并审阅了这篇博文