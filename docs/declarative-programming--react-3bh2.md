# 声明式编程和反应

> 原文：<https://dev.to/itsjzt/declarative-programming--react-3bh2>

*封面照片由[米米·蒂安](https://unsplash.com/photos/s2DbUV-yx2A?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)在[Unsplash](https://unsplash.com/search/photos/coding?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)T5 拍摄*

声明式编程是由 React 在 JavaScript 社区中推广的。这不是一个新事物，但最近几天才得到普及。

根据维基百科:

> **声明式编程**是一种编程范式——一种构建计算机程序的结构和元素的风格——它表达计算的逻辑，而不描述其控制流。

只有当你知道声明式编程时，这个定义才是好的，但是不要担心我们会理解它。

## 声明式编程

声明式编程就像描述一幅画，命令式编程是描绘这幅画的指令。声明式编程产生代码:

*   可读性更强:程序更容易阅读，因为它隐藏了底层细节。在声明式编程中，我们甚至不知道系统的底层细节。
*   更容易推理:我们编写的代码更容易推理，因为它非常抽象，我们描述的是解决方案而不是过程。

正如我所说的，声明式编程并不是一个新事物，所以业界广泛使用的许多语言都是声明式的。让我告诉你其中的几个

### SQL

SQL 是一种特定于领域的语言，用于编程和管理关系数据库中的数据。如果你正在读这篇文章，我想我不需要给你任何关于 SQL 的介绍，它只是管理关系数据库的事实上的标准。

请看这个简单的 SQL 查询:

```
SELECT * FROM Employees 
```

Enter fullscreen mode Exit fullscreen mode

这里我们定义了我们需要每一个**员工**的细节，而不是如何得到**员工**。我们不关心数据库用来存储数据的任何复杂的数据结构。

### CSS

CSS 是声明式编程的一个很好的例子，在 CSS 中，我们实际上定义了元素看起来应该是什么样子，浏览器负责为你实现。你只需要说*这个 div 应该是蓝色的，文本应该看起来是亮黄色的*，浏览器就会帮你完成。

想象一下，如果将`body`设置为

```
body {
  width: 500px;
  height: 500px;
  background: palevioletred;
  color: white;
} 
```

Enter fullscreen mode Exit fullscreen mode

现在浏览器根据你的 CSS 制作主体。这就是声明式编程的概念，你定义结构，编译器/宿主替你做。

## React 中的声明式编程

在 react 中，通过改变组件的状态来创建交互式 ui，React 负责根据它更新 DOM。

以这段 react 代码为例:

```
import React from "react";

class App extends React.Component {
  state = {
    message: "Hello react"
  }
  render() {
    return (
    <div className="App">
      <h1>{this.state.message}</h1>
      <button 
        onClick={e => this.setState({message: "Hello World"})}>
          Change Message</button>
    </div>
  );
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

这创建了一个“hello React”消息和一个按钮，[，你可以看到这里的](https://codesandbox.io/s/j3v2p856py)像

[![react-hello](img/98d21f864894d2fad89d6b1f8327c9b9.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--mkMjyojf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/u5utlb7v3h9a4dmtvd3q.PNG)

当你点击这个按钮时，它会把信息变成“你好，世界”。

在 react 中，DOM 是声明性的。这意味着我们从不与 DOM 交互，当我们改变状态时，UI 会更新。这使得用户界面的设计和调试变得更加容易，你可以改变程序的状态，看看用户界面在那个特定的时刻会是什么样子。

## TL；速度三角形定位法(dead reckoning)

在声明式编程中，你描述程序/UI/图片，别人 React/Browser/OS 实现。