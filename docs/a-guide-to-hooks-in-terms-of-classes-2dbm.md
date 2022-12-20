# 钩子的分类指南

> 原文：<https://dev.to/theactualgivens/a-guide-to-hooks-in-terms-of-classes-2dbm>

随着钩子的引入，React 团队似乎正在试图将开发人员从使用实现状态和生命周期的类组件中推开。对于习惯于组件分类的人来说，理解如何使用这些新特性可能有点困难。希望本指南将有助于弥补如何使用钩子替换类之间的差距。

为此，我将简单介绍一下基本的钩子:[使用状态](https://reactjs.org/docs/hooks-reference.html#usestate)和[使用效果](https://reactjs.org/docs/hooks-reference.html#useeffect)。

## 使用状态

所以，让我们从两者中较容易的开始。useState 钩子替换了 State。很明显。唯一的亮点是它允许我们与国家互动。以前，我们的状态是组件的一个类变量。

```
class MyComponent extends React.PureComponent {
  constructor(props) {
    super(props);
    this.state = { count: 0 }
  }
} 
```

然后可以用`this.state`简单地访问它，并用 React 的`this.setState()`方法进行变异。

```
class MyComponent extends React.PureComponent {
  constructor(props) {
    super(props);
    this.state = { count: 0 }
  }

  render() {
    const { count } = this.state;

    return (
      <button type="button" onClick={() => this.setState({ count: count + 1})}>
        Add One
      </button>
    )
  }
} 
```

有了钩子，我们的状态不再是用单一方法访问的单一对象，每一个状态对我们来说都是独立的组件。

```
const MyComponent = () => {
  const [count, setCount] = React.useState(0);
  const [myName, setMyName] = React.useState('Andrew');
} 
```

传递给 useState 的值是 State 将被初始化的值，正如您可能猜到的那样，`setCount(count + 1)`会将我的 count 状态变量增加 1，并触发 MyComponent 的更新。

使用 hook 的状态最酷的部分是它如何与 useEffect 钩子交互。

## 使用效果

useEffect 钩子比 useState 多一点。还记得我们过去使用的那些令人敬畏的生命周期方法吗？

```
class MyComponent extends React.PureComponent {
  constructor(props) {
    this.state = { channelId: 12345, message: '' }
  }

  this.componentDidMount() {
    discordClient.login(token);
  }

  this.componentDidUpdate() {
    { channelId, message } = this.state;
    discordClient.channels.get(channelId).send(message);
  }

  this.componentWillUnmount() {
    discordClient.destroy();
  }
} 
```

```
const MyComponent = () => {
  const isInitialMount = React.useRef(true);
  const [channelId, setChannelId] = React.useState(12345);
  const [message, setMessage] = React.useState('');

  React.useEffect(() => {
    discordClient.login(token);

    return function cleanup() {
      discordClient.destroy();
    }
  }, []);

  React.useEffect(() => {
    if (isInitialMount.current) {
      isInitialMount.current = false;
      discordClient.login(token);
    } else {
      discordClient.channels.get(channelId).send(message);
    }

    return function cleanup() {
      discordClient.destroy();
    };
  }, [message]);

  this.componentDidUpdate() {
    { channelId, message } = this.state;
    discordClient.channels.get(channelId).send(message);
  }
} 
```

现在，让我们把它们都融合到一个函数`useEffect()`中。默认情况下，这充当 componentDidMount 和 componentDidUpdate。因此，类似这样的东西将在装载和每次组件更新时运行。

```
const MyComponent = () => {
  React.useEffect(() => {
    console.log('yeehaw');
  });
} 
```

如果你想让一些东西在 willUnmount 上运行，简单地从你的 useEffect 返回一个函数，如下所示...

```
const MyComponent = () => {
  React.useEffect(() => {
    console.log('yeehaw');

    return function unmount() {
      console.log('componentWillUnmount');
    };
  });
} 
```

每当组件将从 dom 中卸载时，React 将执行 useEffect 返回的函数中的代码。很简单，对吧？

最酷的部分来了，你知道如何在 redux 中订阅你的商店的某些元素。嗯，使用效果你可以对你的状态做同样的事情。

假设我们有两个状态变量，但是我们只希望其中一个更新时会产生这种效果。很简单。

```
const MyComponent = () => {
  const [howdy, setHowdy] = React.useState('partner');
  const [count, setCount] = React.useState(0);

  React.useEffect(() => {
    console.log('yeehaw');
  }, [count]);
} 
```

这个 useEffect 现在只在 count 的值改变时运行。

假设您希望 useEffect 只在 mount 上运行，只需向它传递一个空数组作为条件。

```
const MyComponent = () => {
  const [howdy, setHowdy] = React.useState('partner');
  const [count, setCount] = React.useState(0);

  React.useEffect(() => {
    console.log('yeehaw');
  }, []);
} 
```

现在，让我们尝试用一个 useEffect 来重新实现这个模拟的 discord 机器人。我们希望客户机在装载时登录，在消息更新时发送消息，并在卸载时销毁客户机。为了使客户机只在挂载时登录，我们必须利用 useRef 来确定它是否是初始挂载。这是 React 团队建议的实现。

```
const MyComponent = () => {
  const isInitialMount = React.useRef(true);
  const [channelId, setChannelId] = React.useState(12345);
  const [message, setMessage] = React.useState('yeehaw!');

  React.useEffect(() => {
    if (isInitialMount.current) {
      isInitialMount.current = false;
      discordClient.login(token);
    } else {
      discordClient.channels.get(channelId).send(message);
    }

    return function cleanup() {
      discordClient.destroy();
    };
  }, [message]);
} 
```

现在去勾搭吧！

[![Patrick Star playing Hooky](img/903abd69ba3020e5d425810d95022e12.png)](https://i.giphy.com/media/l1AsOnR2Pjuw4tx4I/giphy.gif)