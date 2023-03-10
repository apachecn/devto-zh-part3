# laxy:JavaScript 中的惰性初始化

> 原文：<https://dev.to/_bigblind/laxy-lazy-initialization-in-javascript-5ghm>

## ![GitHub logo](img/a73f630113876d78cff79f59c2125b24.png) [詹姆斯塔姆格](https://github.com/jamestalmage) / [莱西](https://github.com/jamestalmage/laxy)

### 惰性加载昂贵对象的代理

<article class="markdown-body entry-content container-lg" itemprop="text">

# laxy[![Build Status](img/3e69fa505f8c5dffc96818a55aba6170.png)](https://travis-ci.org/jamestalmage/laxy)T3![codecov](img/7c30db233c036a5f9508efc6d39340fb.png)T5】

> 延迟加载昂贵对象的代理。懒惰+代理=== Laxy

## 安装

```
$ npm install laxy 
```

## 使用

```
const laxy = require('laxy');
const proxy = laxy(generatorFn)(...argsToPass);
// generatorFn will only be called once, but not until you interact with the proxy in some way:
proxy();
proxy.foo;
Object.keys(proxy); // etc...
// Can be used as a lazy require
const _ = laxy(require)('lodash');

// lodash won't be loaded until you do something with it:
_.isNumber(3);
```

## 基本 API

### laxy(generatorFn)...argsToPass)

#### generatorFn

类型:`fn`

生成被代理的对象时应该调用的函数。这个函数将被延迟调用，并且只调用一次。

#### ...阿根廷肉酱

类型:`anything`

可以提供任意数量的参数，并且将会…

</article>

[View on GitHub](https://github.com/jamestalmage/laxy)

Laxy 导出一个如下所示的函数:

```
laxy(fn, ...args) 
```

我认为解释它如何工作的最简单的方法是用一个例子。假设它运行在节点:

```
const laxy = require("laxy");
const fs = laxy(require, "fs"); 
```

现在，`fs`模块将被加载，只有在它被使用的时候。`require`是用`"fs"`作为自变量调用的，第一次尝试用`fs`。

# 工作原理

当你调用`laxy`时，它立即返回一个对象。这个对象是一个[代理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)。第一次使用代理处理器支持的对象时，`fn`被调用，对象的行为就像由`fn`返回的对象一样。

我用它大大加快了 Firebase 的[谷歌云功能的冷启动时间。每个函数都有自己的“容器”，但是这个容器还包括所有其他函数的代码，这意味着我加载了每个函数的依赖项，而不仅仅是当前容器中的。所以我将所有的依赖项都包装在`laxy`中，这样它们只在第一次使用时才被加载。](https://firebase.google.com/docs/functions/)