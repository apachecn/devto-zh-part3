# V8.js -从 Javascript 访问本地 V8 引擎函数

> 原文：<https://dev.to/gmartigny/v8-js-access-native-v8-engine-function-from-javascript-plc>

当谷歌决定创建自己网络浏览器时，他们的工程师建立了一个全新的 Javascript 引擎。Node.js 采用了速度和内存性能，这使它成为最有影响力的引擎。

有些事情在 Javascript 中是不可能完成的。具体讨论与引擎本身，并利用其本地方法。获取堆内存大小的精确测量值([仅在 Chrome](https://developer.mozilla.org/docs/Web/API/Performance#Properties) 上可能)，触发垃圾收集器或获取函数的优化状态对于 Javascripts 开发人员来说是不可访问的。

不再

# [V8.js](https://github.com/GMartigny/v8.js)

在执行中使用标志`--allow-natives-syntax`现在允许你调用所有 V8 的本地方法。由于语法仍然有点不规范，V8.js 将其包装在一个更经典的库接口周围。

## 安装

像 NPM 上的任何其他库一样，您可以用一个命令行安装 V8.js:

```
$ npm install v8.js 
```

Enter fullscreen mode Exit fullscreen mode

或者在你的 HTML 页面中添加一个`<script>`标签:

```
<script src="https://unpkg.com/v8.js"></script>
<!-- Or -->
<script src="https://cdn.jsdelivr.net/npm/v8.js"></script> 
```

Enter fullscreen mode Exit fullscreen mode

## 用法

一旦安装，V8.js 可能是必需的，并公开一个函数列表供您使用。

```
const v8 = require("v8.js"); // Only for node

v8.getHeapUsage(); 
```

Enter fullscreen mode Exit fullscreen mode

## 举例

```
const v8 = require("v8.js");

const previousUsage = v8.getHeapUsage();
v8.collectGarbage();
const afterUsage = v8.getHeapUsage();

console.log(`Just cleared ${previousUsage - afterUsage} bytes of memory.`); 
```

Enter fullscreen mode Exit fullscreen mode

## 我需要你！

我仍然认为它是测试版(因此是 v0.2.0 版)。所以我非常欢迎任何反馈。

v8 引擎的可用功能列表[非常大，我不想盲目地包含所有功能。因此，如果你发现一些有用的 V8.js 丢失了](https://github.com/v8/v8/blob/master/src/runtime/runtime.h)[用你的用例提出一个新的问题](https://github.com/GMartigny/v8.js/issues/new)。

和平✌️