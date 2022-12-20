# 如何在 Web 组件之间传递数据？

> 原文：<https://dev.to/lamplightdev/how-to-pass-data-between-web-components-21c8>

对于第一次尝试 Web 组件的人来说，一个常见的问题是如何在同一页面上的不同组件之间进行通信。乍一看，Web 组件的设计初衷——在离散元素中封装 DOM、CSS 和行为——意味着它们不适合在边界之外与其他元素进行通信。但是传递数据只是使用您已经熟悉的标准 JavaScript 事件模型。

部分困惑可能是由于 Web 组件介绍中经常忽略这个主题。可以理解的是，文章关注的是 web 组件提供的所有新功能，但它们的另一个巨大优势是它们只是现有本地 web 平台的扩展——在构建组件时，您可以使用 Web 的所有技术和 API(新旧的)。

### Web 组件中的 JavaScript 事件模型

web 的本地事件模型简单地由使用`dispatchEvent`和`addEventListener`的事件调度和监听组成，Web 组件也使用相同的方法。

`CustomEvent`是一个具有给定名称的用户定义的事件，可以使用`dispatchEvent`从组件发送到包含它的 DOM:

```
// ...

onClick() {
  const myEvent = new CustomEvent('x-increment', {
    bubbles: true, // bubble event to containing elements
    composed: true, // let the event pass through the shadowDOM boundary
    detail: { // an object to hold any custom data that's attached to this event
      amount: 1,
    }
  });

  this.dispatchEvent(myEvent);
}

// ... 
```

请注意需要设置的`composed`属性，以使事件能够“逃离”ShadowDOM 的封装。如果没有这个，事件仍然会在阴影世界中传播，但不会更远。

然后，`x-increment`事件可以被任何使用`addEventListener`的包含元素捕获，并且任何数据被直接提取和使用，或者被传递给子组件:

```
// ...

constructor() {
  super();

  // attach shadowDOM here

  this.addEventListener('x-increment', (event) => {
    // extract data
    const { amount } = event.detail;

    console.log(amount); // 1

    // pass down to a child
    this.shadowRoot.querySelector('x-target').amount = amount;
  });
}

// ... 
```

### 用容器组件管理状态

要遵循的一个有用模式是拥有一个包含组件，该组件通过捕获子事件并将数据向下传递回层次结构来管理应用程序状态。通过这种方式，状态总是以一种单向的方式(也称为单向数据绑定)沿着组件树向下传递，这种方式可能与其他 UI 框架类似。

这里有一个基本的例子，展示了上述技术在提供一个简单计数器中的作用。它由一个保存输入的`x-controls`组件、一个显示输出的`x-counter`组件和一个管理应用程序状态并将其作为属性传递给其他组件的`x-container`组件组成。

[https://glitch.com/embed/#!/embed/morning-yam?path=index.html](https://glitch.com/embed/#!/embed/morning-yam?path=index.html)

在接下来的文章中，我们将更仔细地看看如何改进上面的应用程序，方法是概括属性的处理，使用模板库来声明性地更新 DOM 并附加事件处理程序，管理 web 组件中的样式，等等。

如果你有任何反馈或问题，我很乐意倾听。

[订阅邮件列表以获得关于 Web 组件和构建高性能网站的新帖子的通知](http://eepurl.com/gvjFwv)