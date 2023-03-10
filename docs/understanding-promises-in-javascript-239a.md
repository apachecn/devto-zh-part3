# 理解 JavaScript 中的承诺

> 原文：<https://dev.to/nkgokul/understanding-promises-in-javascript-239a>

[![](img/ffcb398d149831002743b0b4452be100.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--a4InINhw--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/2560/1%2AIXaKMoKxyvrZs1prvukJvw.jpeg) 
我向你许下一个小小的承诺，到这篇文章结束时，你会知道
JavaScript 的承诺会更好。

我对 JavaScript 有一种“又爱又恨”的关系。但是 JavaScript 对我来说总是很有吸引力。在过去的 10 年里，我一直在研究 Java 和 PHP，JavaScript 看起来很不一样，但是很有趣。我
没有在 JavaScript 上花足够的时间，最近一直在努力弥补
的不足。

承诺是我遇到的第一个有趣的话题。我一次又一次地听到人们说，承诺让你免于重蹈覆辙。虽然那个
可能是一个令人愉快的副作用，但承诺还有更多的东西，这是我到目前为止能够弄清楚的
。这将是一篇很长的
文章，如果你想突出一些部分，你可以使用我们的扩展【http://bit.ly/highlights-extension】的
T5

**背景**

当你第一次开始使用 JavaScript 时，可能会有点沮丧。你会听到一些人说 JavaScript 是同步的
编程语言，而另一些人声称它是异步的。你会听到
阻塞代码、非阻塞代码、事件驱动设计模式、事件生命周期、
函数栈、事件队列、冒泡、polyfill、babel、angular、reactJS、vue JS
以及一大堆其他工具和库。不要担心。你不是第一个。对此也有一个术语。叫做 **JavaScript 疲劳**。这条推文
很好地抓住了这一点。

如果你想知道更多关于 JavaScript 疲劳的细节，你应该看看下面的文章。这个帖子在 Hackernoon 上获得 42k 的掌声是有原因的:)

JavaScript 是一种同步编程语言。但是多亏了回调函数，我们可以让它像异步编程语言一样工作。

**给门外汉的承诺**

JavaScript 中的承诺与现实生活中的承诺非常相似。首先让我们看看现实生活中的承诺。

字典对承诺的定义如下

承诺:名词:某人将做某事或某件特定的
事情将会发生的保证。

那么当有人向你承诺时会发生什么呢？

1.  承诺给你保证某事会被做。他们(做出承诺的人)是自己做还是让别人做并不重要。他们给你一个保证，在此基础上你可以计划一些事情。
2.  诺言可以遵守，也可以违背。
3.  当一个承诺被遵守的时候，你期望从那个承诺中得到一些东西。你可以将承诺的结果用于你进一步的行动或计划。
4.  当一个承诺被打破时，你会想知道为什么做出承诺的人不能履行他的承诺。一旦你知道了原因，并确认承诺已经被打破，你就可以计划下一步该做什么或如何处理。
5.  在作出承诺的时候，我们所拥有的只是一个保证。我们无法立即采取行动。当**承诺被遵守**(因此我们有预期的结果)或**被破坏**(我们知道原因，因此我们可以计划一个应急方案)时，我们可以决定并制定需要做的事情。
6.  做出承诺的人可能根本不会给你回复。在这种情况下，您更愿意保留一个时间阈值。如果做出承诺的人在 10 天内没有回到我身边，我会认为他有一些问题，不会遵守他的承诺。所以即使这个人 15 天后回到你身边，对你来说也没关系，因为你已经做了另外的计划。

### JavaScript 中的承诺

根据经验，对于 JavaScript，我总是从 MDN Web
Docs 阅读文档。在所有的资源中，我认为它们提供了最简洁的细节。我从 MDSN 网站的[承诺页面
开始阅读
文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise)
，并摆弄代码以掌握其中的窍门。

理解承诺有两个部分。**承诺的创建**和
**承诺的处理**。虽然我们的大部分代码通常会迎合其他库创建的承诺的处理，但完整的理解肯定会对我们有所帮助。一旦你跨过初级阶段，理解“创造承诺”也同样重要。

### 创造承诺

让我们看看创造新应许的签名。

构造函数接受一个名为 executor 的函数。这个`executor`函数
接受两个参数`resolve`和`reject`，这两个参数又是函数。
承诺通常用于更容易地处理异步操作或
阻塞代码，例如文件操作、API 调用、DB 调用、IO
调用等。这些异步操作的启动发生在
`executor`函数中。如果异步操作成功，那么
承诺的创建者通过调用`resolve`函数返回
预期结果。类似地，如果有一些意外错误，通过调用`reject`函数将原因传递给
。

现在我们知道如何创造一个承诺。为了我们的理解，让我们创造一个简单的承诺。

```
var keepsHisWord;
keepsHisWord = true;
promise1 = new Promise(function(resolve, reject) {
  if (keepsHisWord) {
    resolve("The man likes to keep his word");
  } else {
    reject("The man doesnt want to keep his word");
  }
});
console.log(promise1); 
```

[![](img/01189d06e32bd74e0a3160a526d0f3a4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--wMfbRwrq--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AByDQ6-tTp2TvHIh4R_P6xw.png) 
每个承诺都有状态和值

因为这个承诺马上得到解决，我们将不能检查承诺的初始状态。因此，让我们创造一个新的承诺，这将需要
一些时间来解决。最简单的方法是使用`setTimeOut`
功能。

```
promise2 = new Promise(function(resolve, reject) {
  setTimeout(function() {
    resolve({
      message: "The man likes to keep his word",
      code: "aManKeepsHisWord"
    });
  }, 10 * 1000);
});
console.log(promise2); 
```

上面的代码只是创建了一个承诺，它将在 10
秒后无条件地解决。因此，我们可以检查承诺的状态，直到它被解决。

[![](img/d4d4cc9e94bba4ce567a53c5620081fc.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rNxu6ZTh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A6GX_rHpGa3HXcYiHm9Vy6g.png) 
承诺直到解决或拒绝的状态

一旦十秒钟过后，承诺就完成了。`PromiseStatus`和
`PromiseValue`都相应更新。正如你所看到的，我们更新了 resolve
函数，这样我们可以传递一个 JSON 对象，而不是一个简单的字符串。这是
，只是为了说明我们也可以在`resolve`函数中传递其他值。

[![](img/8c4a9070ac5b3f9702ada39d203ec089.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--s7Hjdxh8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2An6s4IswZBVUIHc2K3apONA.png) 
一个 10 秒后解析的承诺，返回值为 JSON 对象

现在让我们看看一个拒绝的承诺。让我们为此稍微修改一下承诺。

```
keepsHisWord = false;
promise3 = new Promise(function(resolve, reject) {
  if (keepsHisWord) {
    resolve("The man likes to keep his word");
  } else {
    reject("The man doesn't want to keep his word");
  }
});
console.log(promise3); 
```

由于这将创建一个不可处理的拒绝，chrome 浏览器将显示一个错误。你现在可以忽略它。我们稍后将回到这一点。

承诺中的拒绝

正如我们看到的,`PromiseStatus`可以有三个不同的值。当承诺被创建时，`pending`
`resolved`或`rejected``PromiseStatus`将处于
`pending`状态，并将`PromiseValue`作为`undefined`，直到承诺
处于`resolved`或`rejected.`状态。当承诺处于`resolved`或
`rejected`状态时，承诺被称为`settled.`，因此承诺通常
从待定状态转换到已结算状态。

现在我们知道了承诺是如何产生的，我们可以看看如何使用或
处理承诺。这将与理解`Promise`
对象齐头并进。

### 了解许诺对象

根据 MDN 文档

> `Promise`对象表示一个
> 异步操作的最终完成(或失败)及其结果值。

`Promise`对象有静态方法，
`Promise`对象中的`prototype methods`静态方法可以独立应用，而`prototype methods`
需要应用在`Promise`对象的实例上。记住
普通方法和原型都返回一个`Promise`会让
更容易理解事情。

### 原型方法

让我们首先从`prototype methods`开始，他们有三个人。只是为了
重申一下，记住所有这些方法都可以应用在
`Promise`对象的实例上，并且所有这些方法都依次返回一个承诺。下面所有的
方法为一个承诺的不同状态转换分配处理程序。正如我们前面看到的，当一个`Promise`被创建时，它处于`pending`状态。根据
是`fulfilled`还是`rejected`结算承诺时，将运行以下三种方法中的一种或多种。

`Promise.prototype.catch(onRejected)`

`Promise.prototype.then(onFulfilled, onRejected)`

`Promise.prototype.finally(onFinally)`

下图显示了`.then`和`.catch`方法的流程。由于它们
返回了一个`Promise`，它们可以再次被链接，这也显示在图像中。
如果`.finally`被声明为承诺，那么每当
承诺为`settled`时，它将被执行，而不管它是被履行还是被拒绝。正如
[Konstantin Rouda](https://medium.com/@konrud5) 所指出的，对 finally 的支持是有限的
，所以在使用之前请检查一下。

来自:
【https://mdn.mozillademos.org/files/15911/promises.png】T2

这里有一个小故事。你是一个正在上学的孩子，你向你妈妈要一部
手机。她说“这个月底我会买一部手机。”

让我们看看，如果承诺在本月底
被执行，它在 JavaScript 中会是什么样子。

```
var momsPromise = new Promise(function(resolve, reject) {
  momsSavings = 20000;
  priceOfPhone = 60000;
  if (momsSavings > priceOfPhone) {
    resolve({
      brand: "iphone",
      model: "6s"
    });
  } else {
    reject("We donot have enough savings. Let us save some more money.");
  }
});

momsPromise.then(function(value) {
  console.log("Hurray I got this phone as a gift ", JSON.stringify(value));
});

momsPromise.catch(function(reason) {
  console.log("Mom coudn't buy me the phone because ", reason);
});

momsPromise.finally(function() {
  console.log(
    "Irrespecitve of whether my mom can buy me a phone or not, I still love her"
  );
}); 
```

其输出将是。

[![](img/b01a469798bd278dd14275af244bb5df.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--msXl1RsI--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2AOl16n8YEu5vKgVqh0pBbhg.png) 
妈妈们辜负了诺言。

如果我们将`momsSavings`的值改为 200000，那么妈妈将能够给
一个儿子。在这种情况下，输出将是

[![](img/87f9822b408a7fb2a2a609669f4ab15a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tSR5FoUE--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A9PPksLxayz3373AxoYg4kA.png) 
妈妈信守诺言。

让我们戴上消耗这个图书馆的人的帽子。我们正在嘲笑
输出和自然，以便我们可以看看如何使用然后有效地捕捉。

因为`.then`可以分配两个`onFulfilled, onRejected handlers`，而不是
写分开的`.then`和`.catch`，我们可以用
`.then`做同样的事情，它看起来会像下面这样。

```
momsPromise.then(
  function(value) {
    console.log("Hurray I got this phone as a gift ", JSON.stringify(value));
  },
  function(reason) {
    console.log("Mom coudn't buy me the phone because ", reason);
  }
); 
```

但是为了代码的可读性，我认为最好将它们分开。

为了确保我们可以在一般的浏览器或特定的 chrome
中运行所有这些样本，我要确保我们的
代码样本中没有外部依赖。为了更好地理解进一步的主题，让我们创建一个函数
，它将返回一个将被随机解决或拒绝的承诺，以便
我们可以测试各种场景。为了理解异步
函数的概念，让我们在函数中引入一个随机延迟。因为我们需要随机数，让我们首先创建一个随机函数，返回 x 和 y 之间的随机数。

```
function getRandomNumber(start = 1, end = 10) {
  //works when both start,end are >=1 and end > start
  return parseInt(Math.random() * end) % (end-start+1) + start;
} 
```

让我们创建一个函数，为我们返回一个承诺。让我们调用我们的
函数`promiseTRRARNOSG`，它是
`promiseThatResolvesRandomlyAfterRandomNumnberOfSecondsGenerator`的别名。该功能
将创建一个承诺，该承诺将在 2 到 10 秒之间的随机数
秒后解决或拒绝。为了随机化拒绝和解决，我们将创建一个 1 到 10 之间的随机数
。如果生成的随机数大于 5，我们
将解决承诺，否则我们将拒绝它。

```
function getRandomNumber(start = 1, end = 10) {
  //works when both start and end are >=1
  return (parseInt(Math.random() * end) % (end - start + 1)) + start;
}

var promiseTRRARNOSG = (
 = function() {
  return new Promise(function(resolve, reject) {
    let randomNumberOfSeconds = getRandomNumber(2, 10);
    setTimeout(function() {
      let randomiseResolving = getRandomNumber(1, 10);
      if (randomiseResolving > 5) {
        resolve({
          randomNumberOfSeconds: randomNumberOfSeconds,
          randomiseResolving: randomiseResolving
        });
      } else {
        reject({
          randomNumberOfSeconds: randomNumberOfSeconds,
          randomiseResolving: randomiseResolving
        });
      }
    }, randomNumberOfSeconds * 1000);
  });
});

var testProimse = promiseTRRARNOSG();
testProimse.then(function(value) {
  console.log("Value when promise is resolved : ", value);
});
testProimse.catch(function(reason) {
  console.log("Reason when promise is rejected : ", reason);
});

// Let us loop through and create ten different promises using the function to see some variation. Some will be resolved and some will be rejected. 

for (i=1; i<=10; i++) {
  let promise = promiseTRRARNOSG();
  promise.then(function(value) {
    console.log("Value when promise is resolved : ", value);
  });
  promise.catch(function(reason) {
    console.log("Reason when promise is rejected : ", reason);
  });
} 
```

刷新浏览器页面并在控制台中运行代码，查看`resolve`和`reject`场景的不同
输出。展望未来，我们将看到我们如何
可以创建多个承诺，并检查他们的输出，而不必这样做。

### 静态方法

`Promise`对象中有四个静态方法。

前两个是助手方法或快捷方式。他们帮助你轻松地做出决定或拒绝的承诺。

`Promise.reject(reason)`

帮助您创建被拒绝的承诺。

```
var promise3 = Promise.reject("Not interested");
promise3.then(function(value){
  console.log("This will not run as it is a resolved promise. The resolved value is ", value);
});
promise3.catch(function(reason){
  console.log("This run as it is a rejected promise. The reason is ", reason);
}); 
```

`Promise.resolve(value)`

帮助你创造一个坚定的承诺。

```
var promise4 = Promise.resolve(1);
promise4.then(function(value){
  console.log("This will run as it is a resovled promise. The resolved value is ", value);
});
promise4.catch(function(reason){
  console.log("This will not run as it is a resolved promise", reason);
}); 
```

另外，一个承诺可以有多个处理程序。所以您可以将上面的
代码更新为

```
var promise4 = Promise.resolve(1);
promise4.then(function(value){
  console.log("This will run as it is a resovled promise. The resolved value is ", value);
});
promise4.then(function(value){
  console.log("This will also run as multiple handlers can be added. Printing twice the resolved value which is ", value * 2);
});
promise4.catch(function(reason){
  console.log("This will not run as it is a resolved promise", reason);
}); 
```

输出会是这样的。

接下来的两种方法帮助你处理一系列承诺。当你在处理多个承诺时，最好先创建一系列承诺，然后再对这些承诺采取必要的行动。为了理解这些
方法，我们将无法使用我们方便的`promiseTRRARNOSG`，因为它太
随机了。最好有一些确定性的承诺，这样我们才能
理解行为。让我们创建两个函数。一个将在 n 秒后解决
，一个将在 n 秒后拒绝。

```
var promiseTRSANSG = (promiseThatResolvesAfterNSecondsGenerator = function(
  n = 0
) {
  return new Promise(function(resolve, reject) {
    setTimeout(function() {
      resolve({
        resolvedAfterNSeconds: n
      });
    }, n * 1000);
  });
});
var promiseTRJANSG = (promiseThatRejectsAfterNSecondsGenerator = function(
  n = 0
) {
  return new Promise(function(resolve, reject) {
    setTimeout(function() {
      reject({
        rejectedAfterNSeconds: n
      });
    }, n * 1000);
  });
}); 
```

现在让我们用这些辅助函数来理解`Promise.All`

### 许诺。全部

根据 MDN 文档

> 当`iterable`参数中的
> 所有承诺都已解决或者当
> 可迭代参数不包含承诺时，`Promise.all(iterable)`方法返回单个`Promise`。它以拒绝的第一个
> 承诺的理由拒绝。

**情况 1** :所有承诺都解决的时候。这是最常用的
场景。

```
console.time("Promise.All");
var promisesArray = [];
promisesArray.push(promiseTRSANSG(1));
promisesArray.push(promiseTRSANSG(4));
promisesArray.push(promiseTRSANSG(2));
var handleAllPromises = Promise.all(promisesArray);
handleAllPromises.then(function(values) {
  console.timeEnd("Promise.All");
  console.log("All the promises are resolved", values);
});
handleAllPromises.catch(function(reason) {
  console.log("One of the promises failed with the following reason", reason);
}); 
```

所有的承诺都兑现了。

总的来说，我们需要从输出中得出两个重要的观察结果。

第一个* *:* *耗时 2 秒的第三个承诺在耗时 4 秒的第二个
承诺之前完成。但是正如您在输出中看到的，承诺的顺序在值中保持不变。

第二* *:* *我加了一个控制台定时器，看看`Promise.All`需要多长时间。
如果承诺按顺序执行，总共需要 1+4+2=7 秒
。但是从我们的计时器中我们看到它只需要 4 秒钟。这是一个
证明，所有的承诺都是并行执行的。

* *案例二:* *没有承诺的时候。我觉得这是最不常用的
。

```
console.time("Promise.All");
var promisesArray = [];
promisesArray.push(1);
promisesArray.push(4);
promisesArray.push(2);
var handleAllPromises = Promise.all(promisesArray);
handleAllPromises.then(function(values) {
  console.timeEnd("Promise.All");
  console.log("All the promises are resolved", values);
});
handleAllPromises.catch(function(reason) {
  console.log("One of the promises failed with the following reason", reason);
}); 
```

因为数组中没有承诺，所以返回的承诺被解析。

* *案例 3:* *它以第一个拒绝的承诺的理由拒绝。

```
console.time("Promise.All");
var promisesArray = [];
promisesArray.push(promiseTRSANSG(1));
promisesArray.push(promiseTRSANSG(5));
promisesArray.push(promiseTRSANSG(3));
promisesArray.push(promiseTRSANSG(4));
var handleAllPromises = Promise.all(promisesArray);
handleAllPromises.then(function(values) {
  console.timeEnd("Promise.All");
  console.log("All the promises are resolved", values);
});
handleAllPromises.catch(function(reason) {
  console.timeEnd("Promise.All");
  console.log("One of the promises failed with the following reason ", reason);
}); 
```

第一次拒绝后执行停止

### 无极.竞

根据 mdn 文档

> 一旦 iterable 中的一个承诺解析或拒绝，`Promise.race(iterable)`方法返回一个解析或拒绝
> 的承诺，并带有来自该承诺的
> 值或原因。

**案例一:**其中一个承诺先解决。

```
console.time("Promise.race");
var promisesArray = [];
promisesArray.push(promiseTRSANSG(4));
promisesArray.push(promiseTRSANSG(3));
promisesArray.push(promiseTRJANSG(3));
promisesArray.push(promiseTRSANSG(4));
var promisesRace = Promise.race(promisesArray);
promisesRace.then(function(values) {
  console.timeEnd("Promise.race");
  console.log("The fasted promise resolved", values);
});
promisesRace.catch(function(reason) {
  console.timeEnd("Promise.race");
  console.log("The fastest promise rejected with the following reason ", reason);
}); 
```

[![](img/10410fe8f3230ca0006008c0fb762b1c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--5G_Nc1Tn--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/max/800/1%2A6za2DUGOKIxARTyfP5HihQ.png) 
最快分辨率

所有的承诺都是并行的。第三个承诺在 2 秒内完成。
一旦完成，由`Promise.race`返回的承诺就解决了。

**案例二:**承诺之一先拒绝。

```
console.time("Promise.race");
var promisesArray = [];
promisesArray.push(promiseTRSANSG(4));
promisesArray.push(promiseTRSANSG(6));
promisesArray.push(promiseTRSANSG(5));
promisesArray.push(promiseTRSANSG(4));
var promisesRace = Promise.race(promisesArray);
promisesRace.then(function(values) {
  console.timeEnd("Promise.race");
  console.log("The fasted promise resolved", values);
});
promisesRace.catch(function(reason) {
  console.timeEnd("Promise.race");
  console.log("The fastest promise rejected with the following reason ", reason);
}); 
```

最快拒绝

所有的承诺都是并行的。第四个承诺在 3 秒内被拒绝。
一旦完成，由`Promise.race`返回的承诺将被拒绝。

我写了所有的示例方法，这样我就可以测试各种场景
，测试可以在浏览器中运行。这就是为什么您在示例中看不到任何
API 调用、文件操作或数据库调用。虽然所有这些
都是现实生活中的例子，但您需要额外的努力来设置和测试它们。
而使用延迟功能会给你带来类似的场景，没有额外设置的负担
。您可以轻松地使用这些值来查看和检验不同的场景。你可以结合使用`promiseTRJANSG`、
、`promiseTRSANSG`和`promiseTRRARNOSG`的方法，模拟足够多的场景，让
彻底理解承诺。此外，在相关程序块之前和之后使用`console.time`方法将有助于我们轻松识别承诺是并行运行还是顺序运行
。如果你有其他有趣的场景或者我错过了什么，请告诉我。如果你想把所有的代码样本放在一个地方，看看这个要点。

蓝鸟有一些有趣的功能，如

1.  承诺.原型.超时
2.  承诺。一些
3.  承诺，许诺

我们将在另一篇文章中讨论这些问题。

我还会再写一篇关于我从 async 和 await 中学到的东西的帖子。

在结束之前，我想列出我遵循的所有经验法则，以保持我对承诺的理智。

### 使用承诺的经验法则

1.  每当你使用异步或阻塞代码时，使用承诺。
2.  `resolve`映射到`then`,`reject`映射到`catch`用于所有实际用途。
3.  确保为所有的承诺都编写了`.catch`和`.then`方法。
4.  如果在两种情况下都需要做些什么，使用`.finally`
5.  我们只有一次机会改变每个承诺。
6.  我们可以为一个承诺添加多个处理程序。
7.  `Promise`对象中所有方法的返回类型，不管它们是静态方法还是原型方法，都是一个`Promise`
8.  在`Promise.all`中，承诺的顺序保持在值变量中，而不管哪个承诺首先被解决。