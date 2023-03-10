# 课场来了。这就是 React 的含义

> 原文：<https://dev.to/ascorbic/class-fields-are-coming-heres-what-that-means-for-react--3a87>

如果你曾经在 React 中编写过一个类组件，你可能有一个类似这样的构造器:

```
import React, { Component } from "react";

export class Incrementor extends Component {
  constructor() {
    super();
    this.state = {
      count: 0,
    };
    this.increment = this.increment.bind(this);
  }

  increment() {
    this.setState({ count: this.state.count + 1 });
  }

  render() {
    return (
      <button onClick={this.increment}>Increment: {this.state.count}</button>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这是 React 文档中使用的模式，也是我在野外看到的最常见的方法。如果您和我一样，您会忘记绑定事件处理程序，直到出现无处不在的`this is undefined; can't access its "setState" property`错误。

TypeScript 用户可能会看到这些，并想知道为什么需要这些歌曲和舞蹈。最惯用的打字方式大概是这样:

```
import * as React from "react";

interface State {
  count: number;
}

export class Incrementor extends React.Component<{}, State> {
  state = { count: 0 };

  increment = () => this.setState({ count: this.state.count + 1 });

  render() {
    return (
      <button onClick={this.increment}>Increment: {this.state.count}</button>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

它不是在构造函数中初始化状态，而是类的一个属性。`increment`方法已改为箭头函数。这意味着不需要绑定它:它已经可以从组件访问`this`。我们实际上也可以将`render()`方法改为一个箭头函数。就范围而言，这并没有给我们带来任何好处，但对我来说看起来更清晰了:

```
import * as React from "react";

interface State {
  count: number;
}

export class Incrementor extends React.Component<{}, State> {
  state = { count: 0 };

  increment = () => this.setState({ count: this.state.count + 1 });

  render = () => (
    <button onClick={this.increment}>Increment: {this.state.count}</button>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

[试试看](https://stackblitz.com/edit/react-ts-exb8ll?file=Incrementor.tsx)

现在，很多阅读这篇文章的人可能会想“嗯，咄:我们已经能够做这个 JavaScript 很久了”，这是真的，但前提是你有正确的巴别塔插件。这不是标准的 ECMAScript，默认情况下不会启用。然而它在 create-react-app 中是默认使用的，所以相当多的人认为它是标准的。我知道我做到了。如果您启用了插件，您可以编写以下代码，它实际上与类型脚本相同:

```
import React from "react";

export class Incrementor extends React.Component {
  state = { count: 0 };

  increment = () => this.setState({ count: this.state.count + 1 });

  render = () => (
    <button onClick={this.increment}>Increment: {this.state.count}</button>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

[试试看](https://stackblitz.com/edit/react-p4hhyy?file=Incrementor.js)

整洁多了，对吧？

然而，类字段函数并不是所有问题的解决方案。首先，你不能在子类中覆盖它们，也不能用它们来覆盖超类方法。因此，您不能将它们用于生命周期方法。如果您正在创建组件的大量副本，也有潜在的性能问题。当在原型上创建一个类方法时，类字段是在每个对象上创建的:每个组件都有自己的每个函数的副本。但是，如果您正在创建数百个实例，这很可能是一个问题。

### 为什么是现在？

类属性插件已经推出好几年了，为什么我现在写这篇文章？嗯，最近发生了一些变化。[类字段提案](https://github.com/tc39/proposal-class-fields)已经通过 TC39 ECMAScript 流程文鼎多年，现在处于第 3 阶段，这是批准前的最后阶段。然而，这是一个颇有争议的提议，自 2017 年 7 月以来一直处于第三阶段。这主要是由于对私有字段语法和实现的分歧。然而，标准化过程似乎就要开始了，并且在过去的一个月里有了一个重要的发展:浏览器支持已经登陆。Chrome 72(和 V8 v7.2)将默认[启用公共字段](https://developers.google.com/web/updates/2018/12/class-fields)，私有字段在一个标志后面可用。这将于 2019 年 1 月 29 日发布。同时，在 [Firefox](https://bugzilla.mozilla.org/show_bug.cgi?id=1499448) 和 [Safari](https://bugs.webkit.org/show_bug.cgi?id=174212) 中应该很快会有支持。最近的 TC39 更新是他们的目标是一旦有两个实现就转移到阶段 4(完成)[。看起来这将是](https://github.com/tc39/tc39-notes/blob/master/es9/2018-09/sept-26.md#class-fields-and-private-methods-stage-3-update)[迫在眉睫](https://hg.mozilla.org/integration/autoland/rev/d0b577458d53)。

当然，我们都知道钩子是前进的方向，但是脸书已经明确表示类组件不会有任何发展。我想提出一个请求，现在是采取行动的时候了，采取行动，走向职业领域。放逐构造器(大部分时间)！