# Javascript 代理实用指南

> 原文：<https://dev.to/tombarr/a-practical-guide-to-javascript-proxy-4cpa>

# 代理简介

用[代理](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)，[你得到一个伪装成猫对象的老虎对象](https://blog.bitsrc.io/a-practical-guide-to-es6-proxy-229079c3c2f0)。这里有六个例子，我希望这些例子能让你相信代理在 Javascript 中提供了强大的元编程。

虽然它不如 ES2015 的其他功能那样广为人知，但代理有许多用途，包括[操作符重载](https://hackernoon.com/introducing-javascript-es6-proxies-1327419ab413)、[对象模仿](https://en.wikipedia.org/wiki/Mock_object)、[简洁而灵活的 API 创建](https://medium.com/dailyjs/how-to-use-javascript-proxies-for-fun-and-profit-365579d4a9f8)、[对象变化事件](https://github.com/sindresorhus/on-change)，甚至为 Vue.js 3 背后的[内部反应系统提供动力。](https://medium.com/@gustojs/vuejs-3-and-other-top-news-from-q-a-event-with-core-vue-devs-c9834946ae7b)

> `Proxy`对象用于定义基本操作的自定义行为(如属性查找、赋值、枚举、函数调用等)。–MDN

代理是一个占位符对象，它“捕获”对其目标对象的调用和操作，然后可以传递、无操作或更优雅地处理这些调用和操作。它在**目标对象**周围创建了一个不可检测的屏障，将所有操作重定向到**处理程序对象**。

使用接受两个必需参数的`new Proxy`构造函数创建代理:目标对象和处理程序对象。