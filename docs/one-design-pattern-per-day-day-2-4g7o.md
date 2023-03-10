# 每天一个设计图案(第二天)

> 原文：<https://dev.to/danielmwakanema/one-design-pattern-per-day-day-2-4g7o>

## 一点脉络

回到几年前，一位客户“发现了一种方法”，可以赚到比某个传销组织所推销的更多的钱。本质上，欺骗骗子。

该计划有一个网站，你可以付费创建账户，如果达到一定的标准，你就可以获得奖励。正如你所料，整个过程建造了一个金字塔。

我接受了，主要是因为对实现解决方案感兴趣，)。

## NB:

任何需要进一步解释的东西都附有一个链接。

## 方法？

1.  网站没有类似 API 的实体，所以我选择了使用[nightman . js](http://www.nightmarejs.org/)的[无头浏览](https://en.wikipedia.org/wiki/Headless_browser)。
2.  使用[邻接表模型](http://www.mysqltutorial.org/mysql-adjacency-list-tree/)与 [MongoDB](https://en.wikipedia.org/wiki/MongoDB) 保持一致。
3.  [REST API](https://restfulapi.net/) 在 [Node.js](https://nodejs.org/) 中，因为还有一个客户端需要的 [React](https://reactjs.org/) 应用。

## 问题为天？

这个有点琐碎。需求指定了许多构建金字塔的方法，在某些条件下，应用程序需要在这些方法之间切换。本质上，改变它的行为。所以之前看过之后，我想我会使用[策略模式](https://refactoring.guru/design-patterns/strategy)。

对于这个应用程序，我使用的是动态类型的 JavaScript，根据我的少量知识，它没有像接口这样的结构，所以在给出的策略没有实现我们期望的情况下，我们将遵循 T2 让它失败的思想。

## 我将如何实现它？

1.  使用[揭示模块模式](https://gist.github.com/zcaceres/bb0eec99c02dda6aac0e041d0d4d7bf2)创建策略。你可以使用一个[函数构造器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Function)来实现，也可以通过将可访问成员附加到`this`来实现。
2.  创建一个上下文函数，该函数使用简单的[依赖注入](https://en.wikipedia.org/wiki/Dependency_injection)来接收策略，并随着策略的变化改变其行为。我假设的上下文提供了一个公认的访问我们的 1..n 策略。

然后，应用程序选择策略并通过上下文访问它们。同样，我没有运行这段代码，不知道为什么。

## 攻略:

```
const StrategyB = _ => {
  //more private members here

  function execute () {
    // do stuff in here
  }

  return {
    execute: execute
  }
}

const StrategyA = _ => {
  // more private members here

  function execute () {
    // do more stuff here
  }

  return {
    execute: execute
  }
} 
```

## 上下文:

```
 function Context (Strategy) {
    this.strategy = Strategy

    this.execute = _ => { this.strategy.execute() }
    this.setStrategy = strategy => { this.strategy = strategy} 
  } 
```

## 用法:

```
 const context = new Context(A_DEFAULT_STRATEGY_OF_YOUR_CHOICE)
  if (SOMETHING) context.execute()
  else if (SOMETHING_ELSE) {
    context.setStrategy(StrategyA())
    context.execute()
  }
  // ... and the ladder goes on 
```

我在这一点上有一点麻烦，但是对我来说，使用这种模式允许我独立地实现每一个策略，同时不太担心编写混乱的代码。随着时间的推移，我可能会对它有更多的了解。