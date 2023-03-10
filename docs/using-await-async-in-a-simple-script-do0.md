# 在简单的脚本中使用 await/async

> 原文：<https://dev.to/henryjw/using-await-async-in-a-simple-script-do0>

**问题:**

您希望在简单脚本的顶层使用`await/async`，如下所示:

```
async function asyncFunction() {
    return new Promise(resolve => {
        //  simulate wait
        setTimeout(resolve, 1000);
    })
}

try {
    // This won't work!
    await asyncFunction()
} catch(err) {
    console.error('Something bad')
} 
```

Enter fullscreen mode Exit fullscreen mode

没那么快！`await`只能在`async`函数中使用！

```
await asyncFunction()
^^^^^

SyntaxError: await is only valid in async function
    at createScript (vm.js:80:10)
    at Object.runInThisContext (vm.js:139:10)
    at Module._compile (module.js:617:28)
    at Object.Module._extensions..js (module.js:664:10)
    at Module.load (module.js:566:32)
    at tryModuleLoad (module.js:506:12)
    at Function.Module._load (module.js:498:3)
    at Function.Module.runMain (module.js:694:10)
    at startup (bootstrap_node.js:204:16)
    at bootstrap_node.js:625:3 
```

Enter fullscreen mode Exit fullscreen mode

* * *

**解决方案:**

将您的代码包含在一个[立即调用的函数表达式(life)](https://en.wikipedia.org/wiki/Immediately_invoked_function_expression)中。

```
async function asyncFunction() {
    return new Promise(resolve => {
        //  Simulate wait
        setTimeout(resolve, 1000);
    })
}

// This works
(async () => {
    try {
        await asyncFunction()
    } catch(err) {
        console.error('Something bad')
    }
})() 
```

Enter fullscreen mode Exit fullscreen mode