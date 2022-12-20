# 名言:等待对象，取消或推迟你的承诺！

> 原文：<https://dev.to/spaciecat/thenables-await-objects-and-cancel-or-defer-your-promises-1f12>

您可能已经使用了`await`来承诺接收值而不创建回调:

```
async function example(){
    const data = await loadSomething()
    // Use data for something...
} 
```

Enter fullscreen mode Exit fullscreen mode

但是你知道除了承诺之外，你还可以用`await`来形容其他物体吗？

`await`语法可用于等待任何`thenable`(包含接受回调的`then`函数的对象)的完成！例如:

```
function wait(time){
    return {then: done => setTimeout(done, time)}
}

async function example(){
    console.log('Hello...') // Logs immediately 
    await wait(1000)
    console.log('World!') // Logs after one second
} 
```

Enter fullscreen mode Exit fullscreen mode

这也意味着任何带有`then`方法的类的实例都可以和`await` :
一起使用

```
class Wait {
    constructor(time){
        this.time = time
    }

    then(callback){
        setTimeout(callback, this.time)
    }
}

async function example(){
    const delay = new Wait(1000)

    console.log("Hello...")
    await delay
    console.log("World!")
} 
```

Enter fullscreen mode Exit fullscreen mode

使用这些标签，实现延期和可取消的承诺变得非常容易。这可以让你*跳到一个承诺的结尾*或者根本阻止它解决！

```
 class Deferred {
    constructor(){
        this.canceled = false
        this.promise = new Promise((resolve, reject) => {
            this.resolver = (value) => {
                if(!this.canceled) resolve(value)
            }
            this.rejecter = (value) => {
                if(!this.canceled) reject(value)
            }
        })
    }

    resolve(value){
        this.resolver(value)
    }

    reject(value){
        this.rejecter(value)
    }

    then(onFulfilled, onRejected){
        this.promise.then(onFulfilled, onRejected)
    }

    cancel(){
        this.canceled = true
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

在你的代码库中，你是否使用了名字而不是承诺？在评论中分享你的使用案例吧！

另外，感谢你阅读我的第一篇文章！