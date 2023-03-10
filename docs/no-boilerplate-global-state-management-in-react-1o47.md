# React 中的无样板全局状态管理

> 原文：<https://dev.to/charlesstover/no-boilerplate-global-state-management-in-react-1o47>

当您 React 应用程序达到一定的规模和范围时，试图管理组件实例中的状态会增加太多的复杂性、适当的训练和代码味道。开发人员不可避免地求助于*全局*状态管理工具，比如 MobX 或 Redux，来解决这些问题，让他们的生活更简单。我强烈支持 Redux，并在我的个人项目中使用它，但并不是所有的开发人员都赞同我的观点。

我参与过很多大型项目，这些项目都要求 React UI 背后有一个全球性的政府机构。无论是申请规模、团队规模还是成员资历，几乎所有人都对这些全球状态管理包持否定态度。

投诉最多的两个？*样板文件*和*学习曲线*。虽然这些包解决了很多问题，并且解决的很好，但是也不是没有成本的。开发人员对设置或修改他们的全局状态需要多少文件、代码块和复制粘贴感到不满意。更重要的是，初级开发人员很难克服他们需要的学习曲线。创建一个全球商店对一些人来说是一场噩梦，并且用功能扩展它，例如开发人员工具和异步特性，是一项花费太多公司时间并引起太多员工头痛的任务。

我调查了许多开发人员，以评估他们在将全局状态管理集成到他们的 React 应用程序中时的主要抱怨。如果你不想全部读完，你可以跳到列表的末尾。这些摘录仅仅是概述了将全局状态集成到 React 应用程序时的常见困难以及 React 开发者面临的障碍。

*   "新开发人员可能需要更长的启动时间和适当的培训。"
*   “新开发人员对不断变化的体系结构和功能概念有问题……他们本质上应该产生描述应用程序如何变化的事件，而不是强制性地自己去做。这与我们更熟悉的 MVC 模式大相径庭。”
*   “我发现试图在 Redux 中管理复杂的状态树非常具有挑战性，并在我的应用程序早期放弃了它。我真的很难理解除了简单的待办事项应用程序之外还有什么最佳实践。我只是从来没有真正理解如何在具有复杂状态的真实应用程序中使用 Redux。”
*   "做一些琐碎的状态改变通常让人感觉很乏味."
*   “初级开发人员需要一些时间来理解自动运行、反应等的魔力。当你不得不一步一步地通过 MobX 代码来找到你自己的代码时，调试变得更加困难。”
*   “令人恼火的是，Redux 不处理开箱即用的异步操作。你必须花一天的时间来弄清楚这个基本和必要的用例。你必须研究思想和传说。然后，你还得想办法把它们和行动联系起来。要处理的事情太多了，让你渴望美好的承诺。”
*   “对于 Redux 来说，我不喜欢它制造了一个副作用真空，而这个真空必须由一堆中间件来填补。问题在于，没有一个中间件是完美的。”
*   “每当我使用 Redux 时，我都会问自己，‘我到底在想什么？’它把一切都复杂化了。有人会说 Redux 的好处是你可以选择你需要的特性(不可变、重选、sagas 等等。);但是最终，无论如何，你都要将所有这些添加到每个项目中。"
*   “Redux 需要大量的文件来建立一个新的 reducer。实践中的许多优势往往不抵劣势。”
*   " Redux 有太多的样板文件，我必须维护它们."
*   “你真的需要为 MobX 使用 decorators。非修饰语法不太好，而且这是一个很大的依赖性。”MobX 目前的重量为 47kB。
*   “Redux 需要大量繁琐的代码来完成最基本的事情:在动作文件中声明动作名称，创建 saga 文件，将其添加到根 saga，创建动作生成器来调用 saga，将组件连接到 Redux 以便它可以访问存储，编写 mapStateToProps 来调用选择器，编写选择器以从存储中获取用户信息，编写 mapDispatchToProps 以便可以在组件中调度动作， 在组件的 componentDIdMount 中调度一个操作，添加一个操作来处理网络请求的结果，编写一个将用户信息保存到存储区的 reducer，添加另一个操作来处理错误，添加另一个操作来处理加载状态，为错误和加载操作编写选择器和 reducer，在组件的 render 函数中调用选择器来获取和显示数据。 对于一个简单的网络请求来说，这合理吗？在我看来就像一堆热垃圾。”虽然我对 sagas 不太有经验，但我会用 redux thunk 来插入我处理 API 请求的方法。
*   “设置全球薪资包非常麻烦和复杂。他们违反了接吻原则——保持简单，笨蛋。”

在这个列表之后，我觉得有必要重申一下:我是 Redux 的粉丝，我在我的个人项目上使用它。本文的目的不是贬低 Redux 或 MobX，也不是暗示它们是有缺陷的系统。这是为了强调一个真正的问题:将这些包集成到真正的应用程序中是有困难的，而且大部分困难似乎源于学习曲线。这些软件包“太聪明”了，对于初级开发人员来说并不容易获得，而初级开发人员往往是项目的主要贡献者。

我收到的一条反馈明确地指责了软件包的用户:“用户没有投入足够的精力来评估他们的需求；不要明智地或按照建议使用[包装];不要再考虑他们添加的任何依赖；永远不要重新审视他们的设计决策，然后抱怨他们。”我觉得他们有所发现。我不认为 Redux 或 MobX 有天生的缺陷，但我认为将它们集成到企业项目中有真正的困难。它们可能不是最佳解决方案，不是出于功能，而是出于复杂性。

我希望随着 React 16.7 [Hooks](https://reactjs.org/docs/hooks-intro.html) 的发布及其对可读 React 应用程序外观的重新概念化，我们将看到利用有创意的新方法吸引更广泛受众的全局状态解决方案。最终目标是没有样板文件和直观的语法，本文将提供我对如何构建 React 的全局状态管理系统的看法，最后是我对该实现的开源尝试。

你可以通过 NPM 上的 [`reactn`自己使用这个实现，或者贡献、派生或者以其他方式窥探](https://www.npmjs.com/package/reactn)[开源 GitHub 库](https://github.com/CharlesStover/reactn)。

# 保持简单，愚蠢💋

## 一种直观的方法

我个人对此事的看法是，全球状态管理系统似乎是在考虑全球状态管理的情况下设计的，而不是 T2 的反应。它们的设计非常广泛，目的是即使在 React 项目之外也可以使用。这并不是一件坏事，但是对于那些可能已经被学习 React 弄得不知所措的初级开发人员来说，这是不直观的。

React 内置了状态管理— `this.state`、`this.setState`，以及新的`useState`和`useReducer`挂钩。我认为全局状态管理应该和局部状态管理一样简单。迁移到全局状态或从全局状态迁移不需要全新技能。

我们使用下面的语法读写本地组件状态:

```
// Class Component
this.state.name;
this.setState({
  name: 'Charles',
});

// Functional Component
const [ name, setName ] = useState('Default Name'); 
```

Enter fullscreen mode Exit fullscreen mode

我们应该能够以类似的方式利用全球国家的力量:

```
// Class Component
this.global.name;
this.setGlobal({
  name: 'Charles',
});

// Functional Component
const [ name, setName ] = useGlobal('name'); 
```

Enter fullscreen mode Exit fullscreen mode

全局成员变量`this.global`上的每个属性可以利用一个[获取器](https://github.com/CharlesStover/reactn/blob/e56b63d99be8997f959c52b9c0baff872e05ebfb/src/object-get-listener.js)，该获取器将该组件实例订阅到全局存储中的属性更改。每当该属性更改时，访问它的任何实例都会重新呈现。这样，更新全局存储中的属性`name`不会重新呈现只访问属性`this.global.age`的组件，但会重新呈现访问`this.global.name`的组件，这是状态变化的直观行为。

出于技术上的需要，全局*钩子*需要属性名(而不是默认值)来访问特定的属性。在全局钩子上，我会选择不使用默认值。根据定义，一个全局状态属性将被多个组件访问。必须为每个组件设置默认值，理论上应该为该属性的所有实例设置相同的默认值，这不是[的干代码](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)。全局默认值应该在外部管理，比如初始化器。

如果你想把整个全局状态对象放在一个钩子中:

```
const [ global, setGlobal ] = useGlobal(); 
```

Enter fullscreen mode Exit fullscreen mode

尽管是一个功能组件，`global`将类似于类组件中的`this.global`，`setGlobal`将类似于`this.setGlobal`。

# 没有样板文件🔩

## 最小限度的设置或修改

当我们去掉 Redux 或 MobX 的许多开发人员认为不必要、乏味或多余的特性时，就不需要太多样板文件了。尤其是当我们把我们的方案定位于 *React 本身*而不是作为整个互联网的全球国家解决方案时。

如果我们希望`this.global`和`this.setGlobal`出现在类组件中，那么它需要出现在每个组件扩展的类上——T2 和 T3。这个具有全局状态功能的新类将扩展原来的`React.Component`或`React.PureComponent`。有几种不同的方法可以解决这个问题。我选择了我认为对任何开发人员来说都是最简单的方法:一个字节的改变。

这个名为 [ReactN](https://www.npmjs.com/package/reactn) 的包导出了 React 的一个精确副本，除了`Component`和`PureComponent`属性通过添加`global`成员变量和`setGlobal`方法扩展了原来的副本。

```
import React from 'react'; // before
import React from 'reactn'; // after 
```

Enter fullscreen mode Exit fullscreen mode

每当你把这个字节添加到一个文件中，所有对`React.Component`和`React.PureComponent`的引用现在都有内置的全局功能，而所有对其他 React 功能的引用，比如`React.createElement`，都是完全不变的。这是通过将对已经使用的同一个 React 包的引用复制到一个新对象来实现的。因此，ReactN 是轻量级的，与 React 包的复制粘贴克隆相反，它根本不修改原始 React 对象。

但是，如果您不希望导入的 React 对象具有这些新属性，该怎么办呢？我完全理解。 [ReactN](https://www.npmjs.com/package/reactn) 的默认导入也充当装饰器。

```
import React from 'react';
import reactn from 'reactn';

@reactn
export default class MyComponent extends React.Component {
  render() {
    return <div>{this.global.text}</div>;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

你的`create-react-app`中没有装饰器支持？类装饰器很容易在普通的 ES6 中实现。

```
import React from 'react';
import reactn from 'reactn';

class MyComponent extends React.Component {
  render() {
    return <div>{this.global.text}</div>;
  }
}

export default reactn(MyComponent); 
```

Enter fullscreen mode Exit fullscreen mode

这三个解决方案中的一个应该符合您的团队的风格指南，并且这三个选项中的任何一个都只有一行“样板”要实现。

但是开店怎么办？前面提到的初始化器？前面提到的 Redux 噩梦？到目前为止，我最好的解决方案是简单地同步传递一个状态对象，但我觉得这是一个可以从社区反馈中得到一些改进的领域。

```
import { setGlobal } from 'reactn';

setGlobal({
  a: true,
  b: false,
  name: 'Charles',
  age: 'Forever 21'
}); 
```

Enter fullscreen mode Exit fullscreen mode

# 反应钩🎣

“对不起，这是 2018 年 10 月 24 日吗？React *钩子*现在在这里，我再也不用使用类组件了！”

你说得对。React 全局状态管理解决方案应该利用 React 钩子的能力——毕竟，功能组件使用`useState`,所以为了直观地了解 React 开发人员已经知道和使用的东西，应该有一个类似的全局状态钩子。

```
import React, { useState } from 'react';
import { useGlobal } from 'reactn';

const MyComponent = () => {
  const [ localText, setLocalText ] = useState('Hello world!');
  const [ globalText, setGlobalText ] = useGlobal('text');
  return <div>{localText}... {globalText}</div>; }; 
```

Enter fullscreen mode Exit fullscreen mode

我们可以提供一个完全类似的解决方案；而且，正如它应该做的那样，它与类组件演示中使用的 global `text`属性共享全局状态。没有理由为什么函数和类组件不能共享它们的全局状态。使用钩子中的钩子，我们可以强迫一个组件在它所“挂钩”的全局状态属性改变时重新呈现——就像你对局部状态所期望的那样。

更通用一点，我们可以像类组件一样使用`useGlobal`。这对于从类迁移的用户来说可能更容易理解。

```
import React from 'react';
import { useGlobal } from 'reactn';

const MyComponent = () => {
  const [ global, setGlobal ] = useGlobal();
  return (
    <button
      onClick={() => {
        setGlobal({
          x: global.x + 1
        });
      }}
    >
      Click Me {global.x}
    </button>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

`setGlobal`也接受一个函数参数，和`this.setState`一样。

```
setGlobal(oldGlobal => ({
  x: oldGlobal.x + 1
})); 
```

Enter fullscreen mode Exit fullscreen mode

# 减速器:现代国家管理的主食🔉

由于 Redux 对 reducer 的依赖和 React 16.7 对`useReducer`的引入，我不能假装 reducer 不是状态管理的现代实现。没有 reducers 的样板文件，如何管理第三方全局状态？

我实现了两个解决方案。一、对于类语法:

```
import { addReducer } from 'reactn';

// this.dispatch.addCard('Ace of Spades')
addReducer('addCard', (state, dispatch, card) => ({
  cards: state.cards.concat([ card ]),
})); 
```

Enter fullscreen mode Exit fullscreen mode

这介绍了熟悉的 Redux reducers，样板文件更少:函数更小，更容易代码分割，并且没有更高阶的组件来遮蔽 React 组件树。总的来说，对我来说，感觉更容易维护。

第二个解决方案受功能`useReducer`的启发。

```
import { useDispatch } from 'reactn';

const addCardReducer = (cards, card) =>
  cards.concat([ card ]);

const MyComponent = () => {

  // addCard takes a card and concats it
  //   to the global state cards property.
  const addCard = useDispatch(
    addCardReducer, // <-- reducer function
    'cards', // <-- the property being reduced
  );

  // Display a button.
  return (
    <button
      onClick={() => {

        // Add "Ace of Spades" to the global state.
        addCard('Ace of Spades');
      }}
    >
      Click me
    </button>
  );
}; 
```

Enter fullscreen mode Exit fullscreen mode

和`useReducer`一样，可以使用这个返回的调度函数来修改全局状态。因此，您的减速器可以进行代码拆分，甚至导入，如果这比前面提到的`addReducer`更可取的话。如果首选`addReducer`，您仍然可以通过`const addCard = useDispatch('addCard');`访问功能组件中添加的减速器。

# 结论🔚

这不是关于[反应](https://www.npmjs.com/package/reactn)的文档，所以我不会详述细节。我确实想勾勒出一个系统，我相信它对 React 开发人员来说更加*直观*，希望它能激发针对 React 解决方案的创造力。一个全局状态包绝对没有理由需要这么多样板文件或者给项目增加这么多复杂性。所有这些占用了高达 4.3kB 的空间，并且支持开箱即用的异步状态改变(不需要中间件)。

如果你想为这个项目做贡献，它是 GitHub 上的[开源，如果有更多的社区反馈，我绝对会欣喜若狂。如果你想玩这个项目，只需简单的`npm install reactn`或`yarn add reactn`。](https://github.com/CharlesStover/reactn)

如果你喜欢这篇文章，请随意给它一个心形或独角兽。又快又简单，还免费！如果你有任何问题或相关的好建议，请在下面的评论中留下。

要阅读更多我的专栏，你可以在 [LinkedIn](https://www.linkedin.com/in/charles-stover) 、 [Medium](https://medium.com/@Charles_Stover) 和 [Twitter](https://twitter.com/CharlesStover) 上关注我，或者[在 CharlesStover.com](https://charlesstover.com/)上查看我的作品集。