# 迁徙的蓝鸟向本土蓝鸟承诺

> 原文：<https://dev.to/mzubairahmed/migrating-bluebird-promises-to-native-ones-4mb7>

我们使用了蓝鸟，因为它是最好的承诺库之一。但承诺在 nodejs 和 browser 上都获得了支持。这很容易捕捉到，所以我们计划将它从我们的代码库中删除。

我们使用的那个库的功能是

*   [蓝鸟承诺](http://bluebirdjs.com/docs/api/promise.promisify.html)
*   [蓝鸟承诺.延迟](http://bluebirdjs.com/docs/api/promise.delay.html)

### 许诺

Node js 附带了`util` api，它提供了与 bluebird 相同的需求。

该方法通过接受常见的错误优先回调样式函数并返回一个承诺来工作。

```
// Before

import Promise from 'bluebird';
const readFile = Promise.promisify(fs.readFile);

// After

import util from 'util';
const readFile = util.promisify(fs.readFile); 
```

### 延迟

此方法的目的是返回一个承诺，该承诺将在给定的毫秒后解决。实现这一点的本地方法是预先定义 setTimeout 函数。

```
// Before

import Promise from 'bluebird';
await Promise.delay(1000);

// After

import util from 'util';
const setTimeoutAsync = util.promisify(setTimeout);
await setTimeoutAsync(null, 1000); 
```