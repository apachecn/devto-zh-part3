# 新的 redux-帮助减少 redux 样板文件的轻量级库

> 原文：<https://dev.to/doniyor2109/new-redux-lightweight-library-to-help-reduce-redux-boilerplate--4gd5>

我们的工作一直是编写动作、动作类型和 redux 的缩减器。然而，他们服务于一件事，他们不应该被传播。redux-轻量级库允许你在一个类中编写你的 reducers、动作和动作类型。

```
class Counter {
    state = {
        number: 0;
    }
    increment(amount) {
        return this.state.number + amount;
    }
} 
```

如你所见，所有 redux 的东西都在一个类中。

希望，这将有助于你工作 redux 更容易。

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [多尼约尔 2109](https://github.com/doniyor2109)/[redux-轻量级](https://github.com/doniyor2109/redux-lightweight)

### 编写一个业务逻辑，而不是编写动作、动作类型和减少器

<article class="markdown-body entry-content container-lg" itemprop="text">

# redux-轻量级

这个库为您生成动作创建者、动作类型和减少者。它使用 class 作为生成动作和 reducers 的语法糖。

[![](img/ae49152f10974627485798333db60e13.png)](https://www.npmjs.com/package/redux-lightweight)[![Build Status](img/8078e59f9a094ec75d954f8a8e9fe724.png)](https://travis-ci.com/doniyor2109/redux-lightweight)[![codecov](img/d5fb4a1eb020e2e2929a0ea098caec0f.png)](https://codecov.io/gh/doniyor2109/redux-lightweight)[![PRs Welcome](img/6f43bb6ec23adbd1102c103e63a75b84.png)](http://makeapullrequest.com)[![GitHub](img/f8321b47a6dc5fb8b4c6117b7e1d7a2f.png)](https://github.com/doniyor2109/redux-lightweight/blob/master/LICENSE)

[![](img/4182637137f34f5a0aa620c1e2afc983.png)](https://twitter.com/doniyor2109/status/1075425404100468736)

### 目录

*   [简介](https://raw.githubusercontent.com/doniyor2109/redux-lightweight/master/#introduction)
    *   [动机](https://raw.githubusercontent.com/doniyor2109/redux-lightweight/master/#motivation)
*   [入门](https://raw.githubusercontent.com/doniyor2109/redux-lightweight/master/#getting-started)
    *   [安装](https://raw.githubusercontent.com/doniyor2109/redux-lightweight/master/#installation)
    *   [用途](https://raw.githubusercontent.com/doniyor2109/redux-lightweight/master/#usage)
        *   [与 React 挂钩一起使用](https://raw.githubusercontent.com/doniyor2109/redux-lightweight/master/#usage-with-react-hooks)
        *   [与 react-redux 一起使用](https://raw.githubusercontent.com/doniyor2109/redux-lightweight/master/#usage-with-react-redux)
        *   [与 redux-saga 一起使用](https://raw.githubusercontent.com/doniyor2109/redux-lightweight/master/#usage-with-redux-saga)
        *   [仅用于动作](https://raw.githubusercontent.com/doniyor2109/redux-lightweight/master/#usage-only-for-actions)
    *   [高级用法](https://raw.githubusercontent.com/doniyor2109/redux-lightweight/master/#advanced-usage)
*   [工作原理](https://raw.githubusercontent.com/doniyor2109/redux-lightweight/master/#how-it-works)
*   [API 引用](https://raw.githubusercontent.com/doniyor2109/redux-lightweight/master/#api-reference)
    *   [createUpdater(更新者)](https://raw.githubusercontent.com/doniyor2109/redux-lightweight/master/#createupdaterupdater)
    *   [多次更新(更新)](https://raw.githubusercontent.com/doniyor2109/redux-lightweight/master/#useupdaterupdater)
*   [执照](https://raw.githubusercontent.com/doniyor2109/redux-lightweight/master/#licence)

# 介绍

## 动机

Redux 是解决数据管理的伟大库。然而，它引入了一些样板文件。为了添加一个业务逻辑，开发人员必须创建 3 个不同的东西(动作类型、动作创建者、缩减者)，他们一起做一件事。这就是为什么我决定创建允许在一个地方声明它们实用程序一个业务逻辑应该在一个地方声明。

这个库的灵感来自于 [redux-actions](https://github.com/redux-utilities/redux-actions) 和 [mobx](https://mobx.js.org/)

# 入门指南

## 装置

```
$ npm install --save redux-lightweight
```

或者

```
$ yarn add redux-lightweight
```

## 使用

创建属性为……的类

</article>

[View on GitHub](https://github.com/doniyor2109/redux-lightweight)