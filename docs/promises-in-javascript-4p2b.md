# Javascript 中的承诺

> 原文：<https://dev.to/full_stackgeek/promises-in-javascript-4p2b>

目录

*   [1。先决条件](https://fullstackgeek.blogspot.com/2019/02/asynchronous-javascript-explained-for-beginners.html#Prerequisites)
*   [2。概述](https://fullstackgeek.blogspot.com/2019/02/asynchronous-javascript-explained-for-beginners.html#Overview)
*   [3。承诺](https://fullstackgeek.blogspot.com/2019/02/asynchronous-javascript-explained-for-beginners.html#Promises)
    *   [承诺的简写语法](https://fullstackgeek.blogspot.com/2019/02/asynchronous-javascript-explained-for-beginners.html#ShortHandSyntaxForPromises)
    *   [承诺链](https://fullstackgeek.blogspot.com/2019/02/asynchronous-javascript-explained-for-beginners.html#PromiseChaining)
    *   [获取 API](https://fullstackgeek.blogspot.com/2019/02/asynchronous-javascript-explained-for-beginners.html#FetchAPI)
    *   [异步等待](https://fullstackgeek.blogspot.com/2019/02/asynchronous-javascript-explained-for-beginners.html#AsyncAwait)

## 理解异步概念的先决条件:

*   正确理解[变量声明](https://fullstackgeek.blogspot.com/2019/02/var-vs-let-javascript.html)
*   [Javascript 中变量的作用域](https://fullstackgeek.blogspot.com/2019/01/variables-scope-in-javascript.html)

## 概述:什么是异步执行任务？

正如谷歌所说,**异步是指不同时存在或发生的事情。**当我们同步执行某项任务时，我们会启动某种任务，比如从某个第三方服务器获取天气预报，然后我们必须等待它完成，然后才能继续下一项任务。当我们异步执行某事时*我们可以开始一些任务，然后我们可以在任务完成之前完成其他工作，*这就是异步执行任务的美妙之处。

## 承诺:

[承诺](https://fullstackgeek.blogspot.com/2019/02/asynchronous-javascript-explained-for-beginners.html)只不过是一种处理和构造异步代码的更即兴的方法，而不是用回调做同样的事情。为了证明这一点，我们将在同一个代码片段中比较承诺和回调。为了模拟延迟，我们将使用 setTimeout()方法，而不是调用第三方 API，这只是为了使事情简单一点。

Promise 在构造函数中接收两个回调: **resolve** 和 **reject。Thes 承诺中的回调为我们提供了对错误处理和成功案例的细粒度控制。当 promise 的执行成功时使用 resolve 回调，而 reject 回调用于处理错误情况。

在调用 promise 时，我们在 promise 对象上定义了 **then** 方法，该方法可以用来相应地接收 promise 的数据。**

考虑下面的代码片段:

```
 //Example of Callback  
 const getDataCallback = (callback) => {  
   setTimeout(() => {  
     const temp = Math.floor(Math.random()*10 + 1);//Generates a random value [1, 10]  
     (temp <= 5)   
       ? callback(undefined, 'This is the Callback data')   
       : callback('This is the Callback error', undefined);    
   }, 10);  
 };  
 getDataCallback((error, data) => {  
   if(error) {  
     console.log(error);  
   } else {  
     console.log(data);  
   }  
 });  
 //Example of Promise  
 const myPromise = new Promise((resolve, reject) => {  
   setTimeout(() => {  
     const temp = Math.floor(Math.random()*10 + 1);//Generates a random value [1, 10]  
     (temp <= 5)   
       ? resolve('This is the Promise data')   
       : reject('This is the Promise error');    
   }, 10);  
 });  
 myPromise.then((data) => {  
   console.log(data);  
 }, (error) => {  
   console.log(error);  
 }); 
```

它产生以下输出:

```
 This is the Callback data  
 This is the Promise error 
```

### <u>承诺的简写语法:</u>

假设我们有一个函数，它接受一些整数，并基于一些复杂的计算返回一个承诺(然而，我们将用 setTimeout()模拟)。实际上，[的简写语法](https://fullstackgeek.blogspot.com/2019/02/asynchronous-javascript-explained-for-beginners.html)没有什么特别的，除了它提供了一种从函数(使用 arrow 语法的函数)返回对象、变量甚至承诺的简洁方式。
考虑下面的代码片段:

```
 //Example of a function returning a Promise  
 const getDataFromPromise = (data) => {  
   return new Promise((resolve, reject) => {  
     setTimeout(() => {  
       (data <= 5)   
         ? resolve('This is the Promise data')   
         : reject('This is the Promise error');    
     }, 1000);  
   });  
 }    
 //Example of a function returning a Promise using Short-Hand syntax  
 const getDataFromPromiseUsingShortHandSyntax = (data) => new Promise((resolve, reject) => {  
   setTimeout(() => {  
     (data <= 5)   
       ? resolve('This is the Promise data using Short-Hand syntax')   
       : reject('This is the Promise error using Short-Hand syntax');    
   }, 1000);  
 });  
 const myPromiseOne = getDataFromPromise(3);  
 myPromiseOne.then((data) => {  
   console.log(data);  
 }, (error) => {  
   console.log(error);  
 });  
 const myPromiseTwo = getDataFromPromiseUsingShortHandSyntax(30);  
 myPromiseTwo.then((data) => {  
   console.log(data);  
 }, (error) => {  
   console.log(error);  
 }); 
```

它产生以下输出:

```
 This is the Promise data  
 This is the Promise error using Short-Hand syntax 
```

### <u>承诺链接:</u>

假设我们有一个函数 funcA()。假设，如果输入类型是 number，funcA()在输入数乘以 2 后返回一个数，否则返回一个错误。基于从 funcA()接收到的输出，我们再次希望通过将从第一次调用接收到的输出作为输入来调用同一个函数(即 functA())。

正如我们之前所做的，在解决上述问题时，我们将对比和比较回拨方法和承诺方法。

考虑下面的代码片段(回调方法):

```
 //Callback Approach  
 const funcA = (num, callback) => {  
   setTimeout(() => {  
     if(typeof num === 'number') {  
       callback(undefined, 2*num);  
     } else {  
       callback('Input type must be number');  
     }  
   }, 2000);  
 }  
 funcA(2, (error, data) => {  
   if(error) {  
     console.log(error);  
   } else {  
     funcA(data, (error, data) => {  
       if(error) {  
         console.log(error);  
       } else {  
         console.log('Final Output(Using Callback Approach): ' + data);  
       }  
     });  
   }  
 }); 
```

正如人们可以很容易看到和分析的那样，随着它的成长，它变得越来越混乱。所以我们在这里看到的通常被称为**回调地狱**。是的，这种混乱的代码实际上有一个名字，很明显，我们需要一个更好的解决方案，既可管理又可理解，T2 承诺链来拯救我们了。

然而，对于承诺，我们会在这里看到两种方法:
考虑下面的代码片段(承诺方法，没有承诺链接):

```
 const funcA = (num) => new Promise((resolve, reject) => {  
   setTimeout(() => {  
     (typeof num === 'number')   
       ? resolve(2*num)   
       : reject('Input type must be number');  
   }, 2000);  
 });  
 funcA(2).then((data) => {  
   funcA(data).then((data) => {  
     console.log(data);  
   }, (error) => {  
     console.log(error);  
   });  
 }, (error) => {  
   console.log(error);  
 }); 
```

即使没有承诺链接，我们的代码看起来也比简单的承诺方法更加精简。现在，我们将寻找一种使用承诺链的更好的方法。在讨论承诺链代码之前，让我们从理论上了解一下:

1.  当我们从另一个承诺处理程序(即 then())返回一个承诺时，这被称为承诺链。
2.  当我们的承诺解决时，我们可以很容易地附加另一个 **then()** 处理程序(我们已经返回的那个),这可以根据我们的需要进行 n 次。
3.  我们可以使用一个名为 **catch()** 的错误处理程序，它的被附加在我们的承诺链的最后。

考虑下面的代码片段(Promise 方法，带有 Promise 链接):

```
 const funcA = (num) => new Promise((resolve, reject) => {  
   setTimeout(() => {  
     (typeof num === 'number')   
       ? resolve(12*num)   
       : reject('Input type must be number');  
   }, 2);  
 });  
 funcA(12).then((data) => {  
   return funcA(data);  
 }).then((data) => {  
   console.log(data);  
 }).catch((error) => {  
   console.log(error);  
 }); 
```

正如所见，由于承诺链接，代码现在更加简洁明了。

### <u>获取 API:</u>

Fetch API 是在相对较新的 JavaScript 版本中引入的，并且内置了对承诺的支持。从技术上讲，这只是另一种处理 HTTP 请求的方法，同时利用了承诺和承诺链的能力和优势。之前，我们已经在本文中看到了如何让[命中 HTTP 请求](https://fullstackgeek.blogspot.com/2019/02/asynchronous-javascript-explained-for-beginners.html#HittingHTTPRequests)。

因此，在 fetch API 中，您按以下顺序传递参数:

*   统一资源定位器
*   {}(这完全是可选的，用于定制我们的请求)

fetch API 只是返回一个承诺，因此我们可以实现像**和**这样的处理程序来处理来自承诺的响应。根据**决定或拒绝的承诺，**我们可以在**处理它。**

考虑下面的代码片段:

```
 fetch('http://puzzle.mead.io/puzzle', {}).then((response) => {  
   if (response.ok) {  
     return response.json();  
     /*  
       Actually, the .json() method takes the response and returns a Promise Object and hence  
       We need to add another then() as we have done in Promise Chaining   
     */  
   } else {  
     throw new Error('Unable to fetch the puzzle');  
   }  
 }).then((data) => {  
   console.log(data.puzzle);  
 }).catch((error) => {  
   console.log(error);  
 }); 
```

<u>建议</u>阅读: [Mozilla-MDN-Fetch](https://developer.mozilla.org/en-US/docs/Web/API/WindowOrWorkerGlobalScope/fetch)

### <u>异步等待:</u>

我们有异步函数和唤醒运算符。当我们一起使用它们时，我们得到了一种新的方式来构建和使用我们的承诺，这使得代码更容易使用。那么，异步和等待会做什么？让我们从异步开始:

考虑下面的代码片段:

```
 const processData = () => {  
   return 12;  
 };  
 const processDataAsycn = async () => {  
   return 12;  
 };  
 console.log('Data from processData() without async: ' + processData() );  
 console.log('Data from processDataAsycn() with async: ' + processDataAsycn() ); 
```

它产生如下输出:

```
 Data from processData() without async: 12  
 Data from processDataAsycn() with async: [object Promise] 
```

正如你所看到的，给一个函数添加 **async** 关键字使它的工作方式有点不同。它开始返回一个**承诺**。我们从一个异步函数中得到的承诺用我们从该函数中返回的任何值来解决。与任何返回承诺的函数一样，我们可以使用像 **catch** 和 **then** 这样的处理程序来处理来自异步函数的响应(即承诺)。
考虑下面的代码片段:

```
 const processDataAsycn = async (num) => {  
   if(typeof num === 'number') {  
     return 2*num;  
   } else {  
     throw new Error('Something went wrong');  
   }  
 };  
 processDataAsycn(21).then((data) => {  
   console.log('Data from processDataAsycn() with async( When promise gets resolved ): ' + data);  
 }).catch((error) => {  
   console.log('Error from processDataAsycn() with async( When promise gets rejected ): ' + error);  
 }); 
```

现在，转到**等待**操作符**，**让我们假设我们有一个**异步**函数 processData()，它调用一个函数 getDataPromise()，该函数又返回一个承诺。现在，我们想要解析/使用返回的数据。让我们看看，如果没有**等待**操作员，我们将如何解决问题。
考虑下面的代码片段:

```
 const getDataPromise = (num) => new Promise((resolve, reject) => {  
   setTimeout(() => {  
     (typeof num === 'number') ? resolve(num * 2) : reject('Input must be an number');  
   }, 2000);  
 });  
 const processDataAsycn = async () => {  
   return getDataPromise(22).then((data) => {  
     return getDataPromise(data);  
   });  
 };  
 processDataAsycn().then((data) => {  
   console.log('Data from processDataAsycn() with async( When promise gets resolved ): ' + data);  
 }).catch((error) => {  
   console.log('Error from processDataAsycn() with async( When promise gets rejected ): ' + error);  
 }); 
```

现在，我们将使用关键字 **await** 实现相同的逻辑，以便我们可以比较和对比这两种方法。实际上，关键字 await 让 JavaScript 一直等到承诺完成并返回结果。因此，看起来代码是同步的，但实际上是异步的。

考虑下面的代码片段:

```
 const getDataPromise = (num) => new Promise((resolve, reject) => {  
   setTimeout(() => {  
     (typeof num === 'number') ? resolve(num * 2) : reject('Input must be an number');  
   }, 2000);  
 });  
 const processDataAsycn = async () => {  
   let data = await getDataPromise(2);  
   data = await getDataPromise(data);  
   return data;  
 };  
 processDataAsycn().then((data) => {  
   console.log('Data from processDataAsycn() with async( When promise gets resolved ): ' + data);  
 }).catch((error) => {  
   console.log('Error from processDataAsycn() with async( When promise gets rejected ): ' + error);  
 }); 
```

所以有了 async 和 awake 操作符，我们可以构建我们的代码，使用 promises 看起来更像常规的旧同步代码。我们可以一个接一个地做手术。在前面的承诺解决或拒绝 return 语句之前，其他代码永远不会运行。现在，在这种情况下，我们只看到了快乐的道路，所有的承诺确实解决了。让我们继续探索当其中一个拒绝

时会发生什么考虑下面的代码片段:

```
 const getDataPromise = (num) => new Promise((resolve, reject) => {  
   setTimeout(() => {  
     (typeof num === 'number') ? resolve(num * 2) : reject('Input must be an number');  
   }, 2000);  
 });  
 const processDataAsycn = async () => {  
   let data = await getDataPromise('2');  
   data = await getDataPromise(data);  
   return data;  
 };  
 processDataAsycn().then((data) => {  
   console.log('Data from processDataAsycn() with async( When promise gets resolved ): ' + data);  
 }).catch((error) => {  
   console.log('Error from processDataAsycn() with async( When promise gets rejected ): ' + error);  
 }); 
```

它产生以下输出:

```
 Error from processDataAsycn() with async( When promise gets rejected ): Input must be an number 
```

如果承诺被拒绝，那么 **awake** 操作符将为我们抛出错误。我们可以看到，承诺链不再是一件令人生畏的事情。

喜欢这个博客吗？点击这里订阅[，不要错过任何未来的博客文章](https://www.specificfeeds.com/fullstackgeek?subParam=followPub)