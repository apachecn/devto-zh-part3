# BEM in React

> 原文：<https://dev.to/jonmajorc/semantic-class-names-with-react-bem-2e79>

我默认 SCSS 没有用于设计 React 应用程序的模块。因此，我心甘情愿的风格级联。你可能在想，Eek！这意味着未来潜在的特异性战争。尽管从技术上来说可能存在，但是每个组件都使用一个命名约定来命名类名。

类名的传统 BEM 命名约定是`block__element--modifier`。

*   A `block`是 React 组件中包装所有其他元素的元素。每个组件只有一个块。
*   一个`Element`在块内。
*   一个`Modifier`代表一个元素或块的行为。

很简单，对吧？这是一个组件的开始。

```
const Counter = () => {
  return <div className="Counter">Start of an amazing counter component</div>
} 
```

Enter fullscreen mode Exit fullscreen mode

***注意:类名是大小写相同的组件名。*T3】**

让我们扩展一下这个例子，这样我们就可以看到更多 BEM 的应用。

```
const Counter = () => {
  return (
    <div className="Counter">
      <button className="Counter__inc">Click me!</button>
      <span className="Counter__count">0</span>
      <button className="Counter__dec">Click me!</button>
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

***注意:元素的类名以块类名为前缀，后跟双下划线。*T3】**

让我们给这个组件添加一个修改器！

```
const Counter = () => {
  return (
    <div className="Counter">
-      <button className="Counter__inc">Click me!</button> +      <button className="Counter__inc Counter__inc--disabled" disabled>
        up
      </button>
      <span className="Counter__count">0</span>
-      <button className="Counter__dec">Click me!</button> +      <button className="Counter__dec Counter__dec--disabled" disabled>
        down
      </button>
    </div>
  )
} 
```

Enter fullscreen mode Exit fullscreen mode

***注意:元素的原始类名是克隆的，带有一个双破折号，后跟一个修饰符。*T3】**

这些按钮现在被禁用。如果你注意到了，按钮无论如何都缺少点击事件。对于这个演示，我们并不在乎有没有一个工作示例；我们只是想见本。理论上，这个应用程序看起来不错🙃我们应该更有信心，当它挂载时，样式的作用域是`<Counter />`！

邮件变得太大了。为了使它简洁明了，并且仍然易于理解，我从介绍 BEM 开始。我希望继续讲述我如何使用 BEM 和 7-1 sass 架构以及其他一些原则来创建模块化和可维护的样式表！...边界元法只是等式的一部分。

* * *

你好！我是乔恩·康登少校。我是一名资深软件农民，在 [Bendyworks](https://bendyworks.com/) 负责客户代码库。作为一名软件农民，我专注于网络上的任何东西，但我的好奇心通常会让我掉进兔子洞...*“乔恩·梅杰刚刚掉进了另一个兔子洞……敬请期待下一篇博文！👋”*