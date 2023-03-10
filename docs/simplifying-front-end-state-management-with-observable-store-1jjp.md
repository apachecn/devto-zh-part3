# 使用可观察存储简化前端状态管理

> 原文：<https://dev.to/danwahlin/simplifying-front-end-state-management-with-observable-store-1jjp>

我承认——我认为一些前端 JavaScript 状态管理模式的使用已经失控了。当您花费大量时间编写代码(通常是大量代码)来处理应用程序状态，或者依赖于生成数百行甚至数千行代码的搭建工具时，是时候退一步问自己:“我真的需要所有这些吗？”。当你这么做的时候，你可能还会问，“我能做些什么来简化我的代码并让它恢复正常？”。

我不想大谈我对保持软件简单的看法，为正确的工作选择正确的工具，考虑维护成本的重要性，当涉及承包商或新员工时更复杂的模式带来的挑战，等等，让我开门见山:

> ## **I think front-end state management needs a lot of simplicity!**

在听取了许多人的意见并亲自参与项目后，我对现有的一些状态管理选项感到沮丧，并决定尝试一个简单的解决方案，该方案最终成为我称之为 **Observable Store** 的项目。事实证明，有几个人有类似的想法，这令人耳目一新(Github 和 npm 上有几个类似名称的项目)。

**注:**如果你想知道我对国家管理复杂性更固执的看法，你可以在这篇文章中跳到 **[我对国家管理复杂性的两个观点](#my-two-cents-on-state-management-complexity)** 。

## 可观察商店的诞生

我工作的一个好处是我可以和世界各地公司的许多开发人员一起工作。这表现为[架构工作、培训、指导](https://codewithdan.com/products/productType/training)，在会议、聚会、[网络研讨会](https://www.apexsystems.com/Events/Pages/CompletedEvents.aspx?filter=wahlin)等场合与人交谈。我就各种国家管理方案进行过多次对话，也听过哪些方案有效、哪些方案无效的故事。我不断听到的一个常见评论是，“我希望有一种更简单的方法来处理我的前端应用程序中的状态管理”。

当我与其他架构师和开发人员进行一对一的交谈，帮助人们完成他们的项目，以及自己工作时，我经常问自己，“在状态管理解决方案中，你真正想要的是什么？”。问这个问题的主要目的如下:

1.  真理的单一来源
2.  状态是只读/不可变的
3.  向任何订户提供状态更改通知
4.  跟踪状态更改历史
5.  所需的代码量最少
6.  适用于任何前端库/框架(Angular、React、Vue.js 或任何支持 JavaScript 的东西)

大约一年半前，我开始尝试将这些总的目标/概念添加到一个简单的库中，最终开发了一个我现在称之为 [Observable Store](https://www.npmjs.com/package/@codewithdan/observable-store) 的东西。我将它用于任何需要状态管理解决方案的前端项目(React、Vue.js、Angular 或其他项目)。Observable Store 满足了上面提到的目标，但是是以一种极其简单的方式实现的。这个库的代码总共只有大约 220 行，因为它提供的“能量”来自于使用 [RxJS 主题和可观察对象](https://rxjs.dev/)。事实上，可观察商店只有 1 个依赖关系 RxJS。

那么为什么要考虑可观察商店呢？如果你有兴趣实现前面提到的任何目标，那么 Observable Store 提供了一个非常简单的方法来实现这些目标。您可以立即获得可以在整个应用程序中引用的单个存储、不可变的状态(适用于库/框架中的更改检测)、状态历史跟踪，以及订阅存储更改的方法。另外，Observable Store 可以用于任何 JavaScript 库或框架。除了使用 JavaScript，你不会被任何东西束缚。

那么如何入手可观察商店呢？这里有一个快速概述。

## 可观察商店入门

要开始使用 observable store，您只需将 **npm 安装到您的项目中(Angular、React、Vue.js 或任何 JavaScript 项目):**

```
 npm install @codewithdan/observable-store 
```

从那里，您创建一个扩展了 **ObservableStore** 的服务类。如果使用 TypeScript，可以使用泛型来传递存储在存储区中的数据的形状(传递类或接口)。然而，TypeScript 并不是必需的，它[也可以很好地与 ES2015](https://github.com/DanWahlin/Observable-Store#using-observable-store-with-react) (甚至 ES5)兼容。

```
 // Optionally define what gets stored in the observable store
    export interface StoreState {
        customers: Customer[];
        selectedCustomer: Customer;
        orders: Order[];
        selectedOrder: Order;
    }

    // Extend ObservableStore and optionally pass the store state
    // using TypeScript generics (TypeScript isn't required though)
    export class CustomersService extends ObservableStore<StoreState> {
      constructor() {
        // Pass initial store state (if desired). Want to track all
        // changes to the store? Set trackStateHistory to true.
        super(initialStoreState, { trackStateHistory: true });
      }
    } 
```

现在，向您的类中添加任何函数，以便从数据存储中检索数据并使用这些数据。调用 **setState()** 在存储中设置状态，或者调用 **getState()** 从存储中检索状态。设置状态时，您可以传递一个动作名称，这在跟踪[状态变化和状态历史](https://github.com/DanWahlin/Observable-Store#using-observable-store-with-react)时很有用。

```
 import { Observable, of } from 'rxjs';
    import { ObservableStore } from '@codewithdan/observable-store';

    export class CustomersService extends ObservableStore<StoreState> {
        constructor() { 
            const initialState = {
                customers: [],
                selectedCustomer: null,
                orders: Order[],
                selectedOrder: null
            }
            super(initialState, { trackStateHistory: true });
        }

        get() {
            // Get state from store
            const customers = this.getState().customers;
            if (customers) {
                // Return RxJS Observable
                return of(customers);
            }
            else {
                // call server and get data
                // assume async call here that returns Observable
                return asyncData;
            }
        }

        add(customer: Customer) {
            // Get state from store
            let state = this.getState();
            state.customers.push(customer);
            // Set state in store
            this.setState({ customers: state.customers }, 
                          'add_customer');
        }

        remove() {
            // Get state from store
            let state = this.getState();
            state.customers.splice(state.customers.length - 1, 1);
            // Set state in store
            this.setState({ customers: state.customers } 
                          'remove_customer');
        }

    } 
```

当商店状态改变时，应用程序的任何部分都可以通过订阅商店的 **stateChanged** 事件得到通知。在本例中，将接收到 CustomersService 对商店所做的更改，这提供了一种很好的方式，可以很容易地了解整个商店的“一部分”。

```
 // Subscribe to the changes made to the store by 
    // CustomersService. Note that you'll want to unsubscribe
    // when done.
    this.customersService.stateChanged.subscribe(state => {
      this.customers = state.customers;
    }); 
```

注意，因为存储状态是不可变的， **stateChanged** 订阅者将总是得到一个“新鲜的”对象，这对于检测跨库/框架的状态/数据变化非常有用。因为 RxJS 可观测量是在幕后使用的，所以您也可以使用 RxJS 提供的所有优秀的操作符。

如果您需要收听对商店所做的所有更改，您可以使用**[globalStateChanged](https://github.com/DanWahlin/Observable-Store#store-api)**事件(感谢[米奇·普里](https://github.com/mickeypuri)的贡献):

```
 // Subscribe to all store changes, not just the ones triggered
    // by CustomersService
    this.customersService.globalStateChanged.subscribe(state => {
      // access anything currently in the store here
    }); 
```

您甚至可以通过提供一个**[state liceselector](https://github.com/mickeypuri)**函数来监听商店的特定部分(例如客户和订单)。

为了处理订单，您可以创建另一个扩展 **ObservableStore** 的类，并在其中添加与订单相关的功能。通过将功能分解到单独的类中，您可以实现单一职责(实线中的“S”)，同时仍然只有一个存储支持整个应用程序。

```
 // Extend ObservableStore
    export class OrdersService extends ObservableStore<StoreState> {
      constructor() {
        // Define that we want to track changes that this object
        // makes to the store
        super({ trackStateHistory: true });
      }
    } 
```

**CustomersService** 和 **OrdersService** 共享同一个商店(就像应用程序中扩展 ObservableStore 的所有类一样)。

可观察的商店 [API](https://github.com/DanWahlin/Observable-Store#store-api) 和[设置](https://github.com/DanWahlin/Observable-Store#store-api)很容易学习，你可以立即安装并运行。你可以在 [Github repo](https://github.com/DanWahlin/Observable-Store) 中找到它与 Angular 和 React 应用一起使用的例子(我希望在不久的将来添加一个 Vue.js 例子)。

Observable Store 是前端应用程序中保持状态管理简单的答案吗？这是一个潜在的解决方案，对我的公司和其他几个正在使用它的公司/开发者来说效果很好。我已经私下使用它一年多了，非常喜欢它带来的简单性。如果你试用了它或者对它有疑问，请在下面或者在 [Github repo](https://github.com/DanWahlin/Observable-Store/issues) 中留下你的评论。

## 我对国家管理复杂性的两点看法

我在这篇文章的开头提到过，我不想对状态管理发表“我的”意见，因为我更喜欢关注潜在的解决方案，而不是关注问题。毕竟，我只是一个有些人可能同意，有些人肯定不同意的人。话虽如此，许多人问我对这个特定主题的看法，所以这里有一个我的立场的快速总结。

我认为我们经常陷入开发软件的“群体思维”模式(有时我也会犯这种错误)，这导致伟大的事情和许多不那么伟大的事情像火一样在开发人员社区中蔓延。因为一个概念或模式是“流行的”或“每个人都在使用它”，我们被它吸引，而没有深入研究并考虑它是否是我们特定应用场景的最佳方式，它是否真的有必要，以及它给团队或项目带来的利弊。在某些情况下，这感觉就像“悬崖上的羊”心态。我最近[看到了一个帖子](https://medium.com/@amcdnl/the-future-of-javascript-state-management-is-less-state-management-ba1d97b99308)，它反映了我对前端状态管理复杂性“状态”的许多想法。

这些年来，我与世界各地的各种公司合作，在会议上与开发人员交谈，并与人们在线互动，我一直听到的主要“抱怨”之一可以总结为，“前端状态管理的复杂性正在杀死我们！”。我也听到，“我无法相信我们的应用程序中添加了多少遵循模式 X 的代码”，或者“我们在跨团队的工作中使用技术 X 和 Y，并且不能在他们之间共享我们的状态管理代码！”。

公平地说，一些可用的模式，比如 Redux，提供了很多价值。例如，团队的一致性，对数据流的洞察，在某些情况下更好的调试，等等。我不认为这有什么争议，所以我想澄清一下。许多人非常成功地使用了一些不同的前端状态管理模式，尤其是对于大型团队和大量移动部件。那么问题出在哪里？

首先，如果团队中的每个人都没有很好地理解给定的模式，那么他们就是在复制和粘贴代码或者使用某种类型的搭建工具，而没有真正理解正在发生什么以及他们为什么要这样做。随着应用程序复杂性的增长，他们感到越来越失落。这通常适用于引入承包商、新员工或开发人员的项目，他们可能不仅仅在前端工作。但是，我发现它也适用于纯粹的前端开发人员。

有一种观点认为，任何使用一个模式但没有真正理解它的人都需要花时间更好地学习这个模式，我认为这是一个有道理的观点。但是，当有人没有选择项目中使用的模式，而最后期限又迫在眉睫时，他们没有太多的选择，只能完成它，即使他们并不完全理解发生了什么。另外，我认为还有一个论点，如果一个模式需要那么多的时间和代码去学习，那么也许首先这是一个值得考虑的最好方法？请记住，我在这里只谈论状态管理。我们还需要担心应用程序的其余部分。

除了很好的理解一个模式，你能在不同的前端 JavaScript 技术之间使用相同的代码吗，代码看起来一样吗？比如 React 有 Redux，Angular 有 NgRx (Redux + RxJS)，Vue.js 有 Vuex，等等。这对您来说可能不是问题，但对我工作的几家公司来说却是问题，因为他们不想维护同一个整体模式的不同实现。

对于问题“不同的前端 JavaScript 技术之间可以使用相同的代码吗？”我要说的答案是一个明确的“不！”–在我见过的大多数场景中，共享状态管理代码通常不是一个选项。在某些情况下，使用的模式可能是相似的，但是库/框架之间的实现是完全不同的。如果您的公司没有为前端项目使用一个主库/框架，当您试图使项目尽可能一致(同时也让开发人员使用他们喜欢的技术)时，这可能会带来挑战。

当然，对于更复杂的状态管理选项，我还可以指出更多的挑战(维护挑战、添加的大量代码、包大小、团队知识等)。)不过暂时就这样吧。我认为这真的可以归结为使用正确的工具做正确的工作，并意识到不是所有的东西都是需要复杂锤子的钉子。

> ## I think it really comes down to doing the right job with the right tools, and recognizing that not everything is a nail that requires a complicated hammer.

对于给定的场景，状态管理模式本身(不管它是什么)是否可能过于复杂，是否存在可行的替代方案，这难道不值得考虑吗？一种尺寸永远不会适合所有人，有许多应用程序使用复杂的状态管理模式，根本不需要它。我自己在公司也见过很多次。例如，应用程序可以直接对后端服务执行标准的 CRUD(创建、读取、更新、删除)操作。一旦行动完成，它就完成了。从状态的角度来看，除了向用户显示消息之外，没有其他事情可做。在这个简单的场景和许多其他场景中，通常不需要复杂的状态管理解决方案，这只会增加不必要的复杂性。这让我想到了我最喜欢的三个词:“保持简单”。

> ## **Keep it simple!**

我真的很钦佩那些拥有智慧、知识、专业技能和能力的架构师和开发人员，他们能够在满足用户需求的同时尽可能地简化应用程序代码。构建好的软件是困难的，保持代码简单的能力也同样困难。这是一种艺术和技能，需要随着时间的推移而发展，在某些情况下，我觉得这种技能已经消失了。让事情尽可能简单最终会产生许多积极的结果——尤其是在长期维护方面。

我意识到这绝对是一个非常主观的话题，但是请在评论中告诉我你对它的建设性想法。每种情况都不一样，所以我总是乐于听到不同的意见。你也可以在推特上联系我。

最初发布于[https://blog.codewithdan.com](https://blog.codewithdan.com/simplifying-front-end-state-management-with-observable-store)