# 如何使用 React 的上下文挂钩

> 原文：<https://dev.to/codingcardio/learn-how-to-use-react-s-context-hook-28g3>

在本文中，我们将看看如何重构一个组件来使用 React 的上下文挂钩。这让我们可以在很大程度上重构我们的代码，并在使用多个上下文时使内容更具可读性。

如果您已经熟悉 React 的上下文 API，您就会知道它是一个非常强大的工具，可以将数据传递给多个组件。如果你不熟悉，让我们看一个例子。假设我们有一个祖母组件，它像这样返回一个母亲组件:

```
import React, { createContext } from "react";

const FamilyContext = createContext({});
export const FamilyProvider = FamilyContext.Provider;
export const FamilyConsumer = FamilyContext.Consumer;

export class Grandmother extends React.Component {
  state = {
    familyName: { lastName: "Smith" }
  };

  render() {
    return (
      <FamilyProvider value={this.state.familyName}>
        <Mother />
      </FamilyProvider>
    );
  }
} 
```

我们将我们的`<Mother />`组件包装在一个提供者标签中，这将允许我们的孩子使用一些数据。现在，我们从祖母那里呈现的组件将可以访问一些数据。假设我们有:

```
const Mother = () => {
  return <Child />;
};

const Child = () => {
  return (
    <FamilyConsumer>
      {familyContext => (
        <p>Last name: {familyContext.lastName}</p>
      )}
    </FamilyConsumer>
  );
}; 
```

现在，我们可以在`<Child />`中访问由`<Grandmother />`提供的数据

### 注:

我们在 Family Provider 上使用一个状态对象值，而不是传递一个指向值 prop 的对象。如果我们要传递一个新的对象，这将导致我们所有的消费者在每次提供者重新呈现时都要重新呈现。

## 为什么要使用上下文挂钩？

上面的例子很好，但是如果我们有多个想要传递给组件的上下文呢？基于我们的例子，这可能看起来像:

```
 ...

  render() {
    return (
      <FamilyProvider value={this.state.familyName}>
        <UserProvider value={this.state.user}>
          <NotificationsProvider value={this.state.notifications}>
            <Mother />
          </NotificationsProvider>
        </UserProvider>
      </FamilyProvider>
    );
  } 
```

在我们的消费者中，我们必须编写类似于:
的代码

```
 ...
   return (

    <FamilyConsumer>
      {familyContext => (
        <UserConsumer>
          {currentUser => (
            <NotificationsConsumer>
              {notifications => (
                <div>
                  <p>User: {currentUser.email}</p>
                  <p>Last Name: {familyContext.lastName}</p>
                  <p>Notifications: {notifications.count}</p>
                </div>
              )}
            </NotificationsConsumer>
          )}
        </UserConsumer>
      )}
    </FamilyConsumer> 
   ) 
```

这开始变得有点失控，因为我们可以看到，随着我们添加的每个上下文，可读性越来越差。

## 该如何如何

让我们用钩子来清理一下。

首先，让我们确保我们正在导入`useContext`钩子

```
import React, { useContext } from `react`; 
```

我们的 useContext 将接受一个上下文对象。现在让我们重构一下，看看是什么样子！让我们首先从我们的祖母那里移除提供者

```
 // Grandmother
  ...
  render() {
    return <Mother />;
  } 
```

不再嵌套在多个提供者中！这是现在我们在这个组件内部要担心的一件事

现在让我们像这样创建一个上下文对象:

```
const FamilyContext = React.createContext({ lastName: "Smith" }); 
```

理想情况下，您可以将这些上下文保存在它们自己的文件中，并在合适的时候导入它们。根据您试图存储和传递的数据，您可以想象它们会变得非常大。

现在我们可以重构我们的`<Child />`组件，并将其从所有嵌套中释放出来:

```
const Child = () => {
  const familyContext = useContext(FamilyContext);
  const user = useContext(UserContext);
  const notifications = useContext(NotificationsContext);
  return (
         <div>
           <p>User: {currentUser.email}</p>
           <p>Last Name: {familyContext.lastName}</p>
           <p>Notifications: {notifications.count}</p>
         </div>
   );
}; 
```

看看您是否可以根据我们到目前为止所做的工作自己创建通知和用户上下文！

让我们来看看孩子从非挂钩 vs 挂钩的回报差异:

### 旧:

```
 return (

    <FamilyConsumer>
      {familyContext => (
        <UserConsumer>
          {currentUser => (
            <NotificationsConsumer>
              {notifications => (
                <div>
                  <p>User: {currentUser.email}</p>
                  <p>Last Name: {familyContext.lastName}</p>
                  <p>Notifications: {notifications.count}</p>
                </div>
              )}
            </NotificationsConsumer>
          )}
        </UserConsumer>
      )}
    </FamilyConsumer> 
   ) 
```

和...相对

### 新增:

```
 return (
         <div>
           <p>User: {currentUser.email}</p>
           <p>Last Name: {familyContext.lastName}</p>
           <p>Notifications: {notifications.count}</p>
         </div>
   ); 
```

我们可以看到这是多么的可读。这样也更容易理解数据流。我们可以确切地看到每个上下文来自哪里，而不是试图遵循一种嵌套模式。

希望这有帮助！