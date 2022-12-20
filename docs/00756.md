# 捕获 Javascript 异步堆栈跟踪

> 原文：<https://dev.to/paxa/capture-javascript-async-stack-traces-epe>

Javascript stacktrace 不包含异步调用，这很糟糕。

示例:

```
var sayHi = () => {
  setTimeout(() => {
    console.log('Hi there');
    thisLineBreaks();
  }, 1000);
};

sayHi(); 
```

Enter fullscreen mode Exit fullscreen mode

输出:

```
$ node try_async.js
Hi there
/my_code/try_async.js:4
    thisLineBreaks();
    ^

ReferenceError: thisLineBreaks is not defined
    at Timeout.setTimeout [as _onTimeout] (/my_code/try_async.js:4:5)
    at listOnTimeout (timers.js:324:15)
    at processTimers (timers.js:268:5) 
```

Enter fullscreen mode Exit fullscreen mode

我们只能看到`sayHi`函数内部的错误细节，我们不知道它是在哪里被调用的。

### 解

为了解决这个问题，我们可以在异步部分之前捕获堆栈跟踪，并将其附加到实际错误中。

```
var sayHi = () => {
  var startStack = new Error().stack;
  setTimeout(() => {
    try {
      console.log('Hi there');
      thisLineBreaks();
    } catch (error) {
      error.stack = error.stack + "\n" +
        startStack.substring(startStack.indexOf("\n") + 1);
      throw error;
    }
  }, 1000);
};

sayHi(); 
```

Enter fullscreen mode Exit fullscreen mode

现在我们得到了！

```
node try_async.js
Hi there
/my_code/try_async.js:9
      throw error;
      ^

ReferenceError: thisLineBreaks is not defined
    at Timeout._onTimeout (/my_code/try_async.js:6:7)
    at listOnTimeout (internal/timers.js:531:17)
    at processTimers (internal/timers.js:475:7)
    at sayHi (/my_code/try_async.js:2:20)
    at Object.<anonymous> (/my_code/try_async.js:14:1)           <<<< HERE
    at Module._compile (internal/modules/cjs/loader.js:774:30)
    at Object.Module._extensions..js (internal/modules/cjs/loader.js:785:10)
    at Module.load (internal/modules/cjs/loader.js:641:32)
    at Function.Module._load (internal/modules/cjs/loader.js:556:12)
    at Function.Module.runMain (internal/modules/cjs/loader.js:837:10)
    at internal/main/run_main_module.js:17:11 
```

Enter fullscreen mode Exit fullscreen mode

它显示了我函数被调用的位置— `/my_code/try_async.js:14`

### 承诺

现在大多数库都使用 promises，我们可以很容易地将它应用于每个返回 promise 对象的函数:

```
var trackPromise = async (func) => {
  var startStack = new Error().stack;
  try {
    return await func();
  } catch (error) {
    error.stack = error.stack + "\n" +
      startStack.substring(startStack.indexOf("\n") + 1);
    throw error;
  }
}

// will make complete stack traces
var rows = trackPromise(() => {
  return db.query('select * from something');
}); 
```

Enter fullscreen mode Exit fullscreen mode

或者修改异步函数:

```
// patch method in object to keep stack traces
var addStackTracker = (proto, methodName) => {
  var originalMethod = proto[methodName];

  proto[methodName] = function(...args) {
    var startStack = new Error().stack;
    return new Promise((resolve, reject) => {
      originalMethod.apply(this, args).then(res => { resolve(res); })
        .catch(error => {
          error.stack = error.stack + "\n" + startStack.substring(startStack.indexOf("\n") + 1);
          reject(error);
        });
    });
  }
}

addStackTracker(DatabaseClient.prototype, 'query')

var db = new DatabaseClient()
db.query('...') // will have full stack 
```

Enter fullscreen mode Exit fullscreen mode

现在我的开发生活变得更容易了。

*注意:如果你的函数在内部调用了多个异步函数，那么这种方法将在进入函数之前只保存堆栈，并为最后一个异步调用保存堆栈*