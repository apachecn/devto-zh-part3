# React 开发人员的反应式编程第 2 部分——与 React 集成

> 原文：<https://dev.to/petyosi/reactive-programming-for-react-developers-part-2-integrate-with-react-5883>

在系列的第[部分，我们经历了一些反应式编程的基础。今天，我们将使用 React 作为视图，使用**反应引擎**作为后端，来实现一个小(但足够有趣)的任务。](https://dev.to/petyosi/reactive-programming-for-react-developers-the-absolute-beginner-guide-5eeg)

## 先取:计数器

作为热身，我们将做一些类似于 [Redux 计数器示例](https://redux.js.org/introduction/examples)的事情——一个点击并增加一个值的按钮。让我们创建一个函数，它为按钮点击创建一个输入流，为计数器创建一个输出流:

```
import subscribe from 'callbag-subscribe'
import subject from 'callbag-subject'
import pipe from 'callbag-pipe'
import scan from 'callbag-scan'
import startWith from 'callbag-start-with'

export function createEngine() {
  const increments$ = subject()  

  const counter$ = 
    pipe(
      increments$,
      scan( acc => acc + 1, 0),
      startWith(0)
    )

  return {
    counter$,
    increments$
  }
} 
```

我们走吧。如果你看完了这个系列的第一部分，上面的内容看起来应该没那么可怕。我们使用`scan`操作符来捕获和累积点击计数器。我们使用`startWith`指定计数器的初始值。我们来连线一下反应:

```
import React, { useState } from 'react';
import { render } from 'react-dom';

import { useCallbagInput, useCallbagOutput } from './CallbagHooks'
import { createEngine } from './engine'

const App = () => {
  const [ engine ] = useState(createEngine)
  const buttonClick = useCallbagInput(engine.increments$)
  const counter = useCallbagOutput(engine.counter$)

  return <div>
    <button onClick={buttonClick}>Click me</button>

    <span>Button was clicked {counter} times</span>
  </div>
}

render(<App />, document.getElementById('root')); 
```

我们将流放在组件的状态中(保持只读)，并分别使用`useCallbagInput / useCallbagOutput`钩子将它们连接起来以作出反应。让我们看看它的实际效果吧！

[https://stackblitz.com/edit/react-m8retc?embed=1&&](https://stackblitz.com/edit/react-m8retc?embed=1&&)

注意:您可以检查上面例子中的钩子实现——它是流和 React 状态之间的粘合剂。这个`useCallbagInput`甚至不是一个真正的钩子。

上面的方法看起来过于复杂——您可以用更少、更简单的代码行用`useState`或`useReducer`实现同样的功能。然而，它完成了一些重要的事情——它将我们应用程序的逻辑封装在一个构建块中，这个构建块驻留在我们的 React 组件之外。您可以轻松地针对它编写测试，而不涉及任何 React 组件/渲染。

接下来，让我们尝试更复杂的东西！

## 第二步:计算器

我们将建立一个计算器，它将两个或更多的数字相加，并跟踪以前的总和。查看以下原型，了解更好的想法:

[https://stackblitz.com/edit/react-jz4jwh?embed=1&view=preview&](https://stackblitz.com/edit/react-jz4jwh?embed=1&view=preview&)

让我们看看对我们的引擎有什么要求:

我们需要:

*   处理数字按钮点击的东西
*   处理点击“求和”按钮的东西

和

*   更新要求和的数字
*   更新目前为止的计算

从引擎的角度来看，这是两个输入流和两个输出流。输入流将数据推入存储(数字、总和)；输出流将结果输出给消费者(在我们的例子中，是 React UI)。从 Redux 的角度考虑(虽然不是精确的映射)，输入流是动作，而输出流是状态。不过，不要纠结于这种类比。

### 制造发动机

```
import subject from "callbag-subject"
import pipe from "callbag-pipe"
import map from "callbag-map"
import scan from "callbag-scan"
import buffer from "callbag-buffer"
import cut from "callbag-cut"

const numbersToSumString = numbers => numbers.join('+')

const sum = numbers => numbers.reduce((a, b) => a + b)

export const createEngine = () => {
  const numbersToSum$ = subject();
  const calculate$ = subject();

  const solutions$ = pipe(
    numbersToSum$,
    buffer(calculate$),
    map(numbers => `${numbersToSumString(numbers)}=${sum(numbers)}` ),
    scan((solutionsSoFar, solution) => [solution, ...solutionsSoFar], [])
  )

  const pendingNumbers$ = pipe(
    numbersToSum$,
    cut(calculate$),
    map(numbersToSumString),
  )

  return {
    // input
    numbersToSum$,
    calculate$,

    // output
    solutions$,
    pendingNumbers$
  }
} 
```

我们终于到了有趣的部分！我们以不同的方式组合两个输入流(`numbersToSum$`和`calculate$`)，以构建我们的输出流——计算出的解和当前未完成解中的数字。

关于实现，我最欣赏的部分是引擎是有状态的，但是我们不用手动处理。相反，我们使用`scan`、`buffer`和`cut`操作符来完成我们的工作。

下一个示例将引擎连接到我们开始时使用的 React 视图:

[https://stackblitz.com/edit/react-q6xkkv?embed=1&view=editor&](https://stackblitz.com/edit/react-q6xkkv?embed=1&view=editor&)

除了反例中的钩子之外，我们将引擎放在一个上下文中，然后在子组件中访问我们需要的流。请注意，与 Redux 不同，这些流不会随时间而改变。相反，它们的作用类似于**永久管道**,负责接受来自应用程序各个部分的事件输入，并在必要时传递更新的值。

## 为什么 Callbag 而不是 RxJS？

如果我们使用 RxJS，引擎实现看起来几乎是一样的。出于本教程的目的，callbag 感觉更简单(一切都是函数！).

## 我为什么要关心那个？React 已经有 hooks，Redux，MobX 等。？

确实如此——然而，这更像是一个发人深省的练习，关于我们如何在框架的之外编程**。与传统的命令式方法相比，用流编写逻辑感觉像是在更高的层次上编程。注意上面的实现没有任何`if`语句，没有变量重新分配，也没有循环。相反，我们有几个由预制操作符组成的纯函数。**

## 我想了解更多！

让你兴奋的一个极好的资源是[Rx marbles](https://rxmarbles.com/)——没有任何实际代码，它显示了 Rx observables 的威力。如果不是所有的话，大部分 Rx 运营商都在 Callbag 中实现了对方的[。](https://github.com/callbag/callbag/wiki)