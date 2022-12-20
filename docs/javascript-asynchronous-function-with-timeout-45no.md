# 超时的 JavaScript 异步函数

> 原文：<https://dev.to/alialp/javascript-asynchronous-function-with-timeout-45no>

如果你已经到达这里，那么你正在处理 JavaScript 异步开发。本文将解释一种为异步函数或任何从 Promise 对象继承的对象实现超时机制的好方法。

## 一期

在阳光明媚的日子里，一个基本的异步函数是这样的:

```
function run(){
    return new Promise((resolve,reject)=>{
      //the task is going to take 2 seconds to be completed or it doesn’t matter we can wait for it’s completion 
      setTimeout(()=>{
        resolve();
      },2000);  
    });  
}
console.log("begin at " + new Date().getTime()/1000);
run().then(()=>{
  console.log("end at " + new Date().getTime()/1000);  
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
//Output 
begin at 1550329812.264
end at 1550329814.267 
```

Enter fullscreen mode Exit fullscreen mode

但问题是你最终会面临这样的情况:

```
function run(){
    return new Promise(async(resolve,reject)=>{
      //a task which may take n seconds to finish (0<n<?)
      //in another word, we don't know how long it will take
      await delay(100000)
      resolve();
    });
}
async function delay(time) {
  return new Promise(function (resolve) {
    setTimeout(resolve, time);
  });
}
console.log("begin at " + new Date().getTime()/1000);
run().then(()=>{
  console.log("end at " + new Date().getTime()/1000);  
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
//output 
begin at 1550332497.638 
```

Enter fullscreen mode Exit fullscreen mode

从上面的极端例子中可以看出，当我们不知道任务执行的持续时间，或者如果我们没有“永远”等待任务结束的奢侈，我们将开始考虑一种机制，它可以帮助我们定义一个具有超时功能的函数。

## 解

在下面的第一个例子中

```
async function run(timeout){
    let ret= new Promise(async(resolve,reject)=>{
      setTimeout(() => {
            if (!ret.isResolved){
                reject();
            }
        }, timeout);

      //a task which may take n seconds to finish (0<n<?)
      //in another word, we don't know how long it will take
      await delay(2000)
      resolve();
    });
    return ret;
}
async function delay(time) {
  return new Promise(function (resolve) {
    setTimeout(resolve, time);
  });
}
console.log("begin at " + new Date().getTime()/1000);
run(3000).then(function(){
  console.log("end at " + new Date().getTime()/1000);  
}).catch(function(){
  console.log("timeout at " + new Date().getTime()/1000);
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
//output 
begin at 1550339001.759
end at 1550339003.763 
```

Enter fullscreen mode Exit fullscreen mode

持续时间被设置为 2000 毫秒，小于我们的超时值 3000 毫秒，因此任务将在 2000 毫秒后成功完成，但现在让我们看看如果我们将任务的持续时间设置为更大的值会发生什么，以在下面的代码中说明本文的观点

```
async function run(timeout){
    let ret= new Promise(async(resolve,reject)=>{
      setTimeout(() => {
            if (!ret.isResolved){
                reject();
            }
        }, timeout);

      //a task which may take n seconds to finish (0<n<?)
      //in another word, we don't know how long it will take
      await delay(100000)
      resolve();
    });
    return ret;
}
async function delay(time) {
  return new Promise(function (resolve) {
    setTimeout(resolve, time);
  });
}
console.log("begin at " + new Date().getTime()/1000);
run(3000).then(function(){
  console.log("end at " + new Date().getTime()/1000);  
}).catch(function(){
  console.log("timeout at " + new Date().getTime()/1000);
}); 
```

Enter fullscreen mode Exit fullscreen mode

```
//output 
begin at 1550339522.407
timeout at 1550339525.41 
```

Enter fullscreen mode Exit fullscreen mode

可以看出，我们已经成功实现了超时机制，其逻辑简单可靠，如下图所示

```
async function run(timeout){
    let ret= new Promise(async(resolve,reject)=>{
      setTimeout(() => {
            if (!ret.isResolved){
                reject();
            }
        }, timeout);

      await logRunningTask();
      resolve();
    });
    return ret;
}
run(timeoutValue).then(success).catch(timeout); 
```

Enter fullscreen mode Exit fullscreen mode

我希望这个机制对你有用，就像我对我一样，我发现了很多复杂的方法，这些方法促使我想出了一个简单可靠的解决方案:)