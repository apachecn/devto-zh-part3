# 你如何处理 JavaScript 错误？

> 原文：<https://dev.to/itachiuchiha/how-do-you-handle-javascript-errors-45do>

你好。你如何处理 JavaScript 错误？

我写了一个这样的示例函数；

### 功能

```
let tried = function(callback) {
    let err = null

    let errorObject = {}

    try {
        callback()
    } catch(ex) {
        err = ex
    }

    return {
        catch: function(data, cb) {
            if(err) {
                if(cb) {
                    switch(err.name) {
                        case 'EvalError':
                            if(data == 'EvalError') cb(err)
                            break;
                        case 'InternalError':
                            if(data == 'InternalError') cb(err)
                            break;
                        case 'RangeError':
                            if(data == 'RangeError') cb(err)
                            break;
                        case 'ReferenceError':
                            if(data == 'ReferenceError') cb(err)
                            break;
                        case 'SyntaxError':
                            if(data == 'SyntaxError') cb(err)
                            break;
                        case 'TypeError':
                            if(data == 'TypeError') cb(err)
                            break;
                        case 'URIError':
                            if(data == 'URIError') cb(err)
                            break;
                        case 'Error':
                            if(data == 'default') cb(err)
                            break;
                        default:
                            cb(err)
                            break;
                    }
                }
            }
            return this
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

### 用法

```
tried(() => {
    thisMethodNotExist()    
}).catch('EvalError', (err) => {
    console.log('Eval error')
}).catch('InternalError', (err) => {
    console.log('Internal error')
}).catch('RangeError', (err) => {
    console.log('Range error')
}).catch('ReferenceError', (err) => {
    console.log('Referans error') // this scope will run.
}).catch('SyntaxError', (err) => {
    console.log('Syntax error')
}).catch('TypeError', (err) => {
    console.log('Tip error')
}).catch('URIError', (err) => {
    console.log('URI error')
}).catch('Error', (err) => {
    console.log('Generic Error')
}).catch('default', (err) => {
    console.log('Custom Error')
}) 
```

Enter fullscreen mode Exit fullscreen mode

这个功能帮了我很多次。你有这样的方法吗？或者，您捕捉错误处理的方法是什么？