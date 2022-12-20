# 使用 React 的上下文 API 共享状态

> 原文：<https://dev.to/sunnysingh/sharing-state-using-reacts-context-api-3623>

React 中的全局状态与像 [Redux](https://redux.js.org/) 这样的库是同义词。如果您曾经需要与多个组件共享状态，比如当前的路由或来自 API 的数据，那么您可能已经使用 Redux 了。

较新版本的 React (16.3+)包含了一种共享状态的内置方式，这意味着不必引入外部库。这被称为 [React 上下文 API](https://reactjs.org/docs/context.html) ，学习起来可能有点棘手。我希望提供一个简化的解释和教程，以便您可以快速地将全局状态添加到任何 React 应用程序中。

## 我们试图解决的问题

在深入上下文 API 之前，让我先描述一个问题场景。

假设我们有一个仪表板，用户可以在其中更新他们的用户名。用户名显示在整个仪表板上，这意味着用户名将存储在组件状态中，然后通过 props 传递给其他组件。

如果没有上下文 API，我们将会这样做:

```
class Dashboard extends React.Component {
  state = { username: '' };

  render() {
    return (
      <div>
        <WelcomeMessage username={this.state.username} />
        <SettingsForm
          username={this.state.username}
          updateUsername={newUsername => {
            this.setState({ username: newUsername });
          }}
        />
      </div>
    );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

用户名存储在`Dashboard`组件的状态中，然后通过`username` prop 传递给`<WelcomeMessage>`和`<SettingsForm>`组件。一个额外的属性被传递给表单以更新状态，然后表单将使用新的用户名重新呈现仪表板。

现在很难看出这有什么问题。考虑一下，当我们向仪表板添加更多深度嵌套的组件时，会发生什么。

```
<Dashboard>
  <WelcomeMessage>
    <MessageList>
      <UserMessage>
        <p>Need to show username here...</p> 
```

Enter fullscreen mode Exit fullscreen mode

在这个例子中，我试图说明`<UserMessage>`是仪表板内部的 3 个组件层。为了将用户名传递给它，我们需要做所谓的“道具训练”:

```
<Dashboard>
  <WelcomeMessage username={this.state.username} />
    <MessageList username={this.props.username} />
      <UserMessage>
        <p>Hello {this.props.username}!</p> 
```

Enter fullscreen mode Exit fullscreen mode

随着我们添加更多的状态和嵌套组件，这会变得非常乏味。此外，我们有可能需要在仪表板之外访问用户名。

## 如何使用上下文 API

这个问题的解决方案是使用内置的 [React 上下文 API](https://reactjs.org/docs/context.html) 。

它允许您避免适当的钻取，这意味着在我们前面的例子中,`<UserMessage>`组件将直接访问最初存储在`<Dashboard>`组件中的用户名状态。

> **注意**:一个完整的工作示例是在 CodeSandbox 上提供的[。](https://codesandbox.io/s/kk3myozr5o)

### 创建提供者和消费者组件

让我们首先为您的上下文创建一个文件。我就叫它`user-context.js`。

在该文件中，添加以下内容:

```
import React, { createContext } from 'react';

const UserContext = createContext({
  username: '',
  updateUsername: () => {},
});

export class UserProvider extends React.Component {
  updateUsername = newUsername => {
    this.setState({ username: newUsername });
  };

  state = {
    username: 'user',
    updateUsername: this.updateUsername,
  };

  render() {
    return (
      <UserContext.Provider value={this.state}>
        {this.props.children}
      </UserContext.Provider>
    );
  }
}

export const UserConsumer = UserContext.Consumer; 
```

Enter fullscreen mode Exit fullscreen mode

让我们把这个文件分解一下。

首先，使用`createContext()`创建用户上下文。这里的值将被`UserProvider`覆盖。

接下来，我们创建一个`UserProvider`组件，它将作为父组件来保存和管理共享状态。可以把这看作是我们最早的例子中的`<Dashboard>`组件的等价物。

最后，我们导出一个`UserConsumer`组件，它将允许组件访问共享状态。

### 使用提供者

`<UserProvider>`组件需要包装所有共享状态的组件。最简单的方法是将它添加到您的主应用程序组件中，该组件通常是 React 呈现给 DOM 的组件。

```
import React from 'react';
import ReactDOM from 'react-dom';
import UserMessage from './UserMessage';
import SettingsForm from './SettingsForm';
import { UserProvider } from './user-context';

function App() {
  return (
    <UserProvider>
      <UserMessage />
      <SettingsForm />
    </UserProvider>
  );
}

const rootElement = document.getElementById('root');
ReactDOM.render(<App />, rootElement); 
```

Enter fullscreen mode Exit fullscreen mode

我们还在这里导入了另外两个组件:`UserMessage`和`SettingsForm`。这两个组件将访问共享用户状态。

### 使用消费读取状态

共享状态的一个用例是显示它。在这种情况下，我们将显示当前用户名。创建一个名为`UserMessage.js`的文件，并在其中添加以下内容:

```
import React from 'react';
import { UserConsumer } from './user-context';

export default function UserMessage() {
  return (
    <UserConsumer>
      {({ username }) => <h1>Welcome {username}!</h1>}
    </UserConsumer>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

在这个文件中，我们创建了一个显示“欢迎用户名”消息的`UserMessage`组件。用户名是从从`user-context.js`导出的`UserConsumer`组件中获取的。

在`<UserConsumer>`里面，我们传递了一个叫做[的渲染道具](https://reactjs.org/docs/render-props.html)。在这种情况下，它是一个函数，我们可以获取状态的一部分，并用它来呈现一些东西。

### 使用消费者更新状态

共享状态的另一个用例是更新它。在这种情况下，我们将为用户提供一个表单来更新他们的用户名。创建一个名为`UserSettings.js`的文件，并在其中添加以下内容:

```
import React from 'react';
import { UserConsumer } from './user-context';

export default function UserSettings() {
  return (
    <UserConsumer>
      {({ updateUsername }) => (
        <div>
          <h2>Settings</h2>
          <label htmlFor="username">Username: </label>
          <input
            id="username"
            type="text"
            onChange={event => {
              updateUsername(event.target.value);
            }}
          />
        </div>
      )}
    </UserConsumer>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

这类似于前面的例子，除了我们不是获取用户名，而是获取`updateUsername`函数来更新它。

## 概述

如果你在这一点上感到困惑，我强烈推荐你看一下[工作代码沙箱示例](https://codesandbox.io/s/kk3myozr5o)，它把所有的东西都集合在一起。

此外，这里有一个主要概念的快速概述:

*   提供者组件包装整个应用程序来管理共享状态。
*   消费者组件用于访问或更新共享状态。
*   `user-context.js`文件导出这两个组件，共享状态存储在`<UserProvider>`组件中。
*   通过简单地导入和使用`<UserConsumer>`组件，`<UserMessage>`和`<SettingsForm>`组件读取和更新共享状态。
*   假设`<UserProvider>`正在包装你的整个应用程序，你可以从应用程序的任何地方读取和共享状态。

就是这样。您可以随意使用这个特性来共享导航、模态甚至数据的状态。权力在你手中💪

> **注**:本文原写于[我的个人博客](https://sunnysingh.io/blog/react-context-api)。我在这里为神奇的开发者社区重新发布它。