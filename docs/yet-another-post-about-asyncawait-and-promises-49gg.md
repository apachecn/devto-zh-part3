# 又一个关于异步/等待和承诺的帖子

> 原文：<https://dev.to/viniciuskneves/yet-another-post-about-asyncawait-and-promises-49gg>

本周有人在懈怠上寻求帮助，同时与`async/await`和承诺混淆在一起。这个人在使用`async/await`并希望在前进之前等待一堆承诺被(并行)解决。我建议他看一下`Promise.all`，但是他一回答我就意识到他对`async/await`和承诺之间的关系有些误解。

这篇文章的目的是一步一步地讲述这两件事是如何联系在一起的，直到我们最终进入 Slack 上的问题。当我展示一些例子时，我假设你至少理解了一些承诺。我们开始吧！

* * *

承诺用法的第一个基本示例:

```
function doSomething() {
  return Promise.resolve('#2');
}

const promise = doSomething().then(console.log);

console.log('#1'); 
```

Enter fullscreen mode Exit fullscreen mode

这里需要强调的是:

*   `doSomething`函数返回一个承诺(已经解决)
*   即使我们先将带有“#2”的`console.log`放在里面，它也会在“#1”之后打印

到目前为止一切顺利。这就是承诺的工作方式，所以这里没有什么特别的。

现在让我们用`async/await`重写它，看看它是如何工作的:

```
async function doSomething() {
  return '#2';
}

const result = await doSomething();
console.log(result);

console.log('#1'); 
```

Enter fullscreen mode Exit fullscreen mode

我刚刚切换到`async/await`，结果不再一样了。现在，我们在“#1”之前打印“#2”，这是我们告诉代码在继续执行之前等待`doSomething`时的预期行为。

但是我们怎么能在承诺中得到同样的行为呢？好吧，我们要做的和上面的代码一样:按正确的顺序等待`doSomething`和**然后是** `console.log`！

```
function doSomething() {
  return Promise.resolve('#2');
}

const promise = doSomething().then(data => {
  console.log(data);

  console.log('#1');
}); 
```

Enter fullscreen mode Exit fullscreen mode

酷，但是现在让我们混合一些东西，看看他们如何表现。我的意思是:一半承诺，一半`async/await`！

```
function doSomething() {
  return Promise.resolve('#2');
}

const result = await doSomething();
console.log(result);

console.log('#1'); 
```

Enter fullscreen mode Exit fullscreen mode

```
async function doSomething() {
  return '#2';
}

const result = doSomething().then(console.log);

console.log('#1'); 
```

Enter fullscreen mode Exit fullscreen mode

嗯，你的东西会被印出来吗？

第一个打印“#2”，然后是“#1”。第二个打印“#1”，然后是“#2”。
您会注意到，它们是上面的相同示例，并且具有相同的结果。但是我们怎样才能把`async/await`和承诺混在一起，并保持“承诺”的行为呢？

我们来看看这个:

```
function doSomethingPromise() {
  return Promise.resolve('#2');
}

async function doSomethingAsync() {
  return '#2';
}

const somePromise = doSomethingPromise();
const someAsync = doSomethingAsync();

console.log(somePromise); // Promise { ... }
console.log(someAsync); // Promise { ... } 
```

Enter fullscreen mode Exit fullscreen mode

当你两次打电话时，你会注意到他们都做出了承诺！那就是`async/await`和承诺的联系。当你把`async`放在一个函数前面时，你是在告诉它[将返回一个承诺，其值来自`return`语句解析后的](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function#Return_value)！当你`await`等待承诺实现时，在这种情况下，从返回的承诺中调用`.then`是一样的！
*注意:我们不处理错误，所以不要忘记你还有`.catch`和`try/catch`要处理。*

这些都是非常简单的例子！Slack 上的问题有以下代码:

```
async function myFunction(...params) {
 const value1 = await someFunction1();
 const value2 = await someFunction2(...params);

 // The following code needs to sync
 const whatNow = async () => {
   await value1.forEach(async v1 => {
     someFunction3(v1);
     someFunction4(v1);
     value2.forEach(v2 => {
       someFunction5(v1, v2);
     });
   });
 }

 whatNow();
} 
```

Enter fullscreen mode Exit fullscreen mode

与前面的例子相比，这要复杂一些，但是这里的问题是 Javascript 中的异步代码，因为每个`someFunctionX`都返回一个承诺。让我们看看这个例子能让我们走多远。

前两种说法还可以，但我们可以做得更好。他们很好，因为他们工作，但我们实际上是在等待来自`someFunction1`的第一个承诺解决，然后解决第二个。如果我们用承诺重写它，我们将得到如下:

```
function myFunction(...params) {
  someFunction1().then(value1 => {
    someFunction2(...params).then(value2 => {
      ...
    });
  });
...
} 
```

Enter fullscreen mode Exit fullscreen mode

一个更好的策略是使用 [Promise.all](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all) 来保证这两个承诺在我们继续执行之前被并行解决。我们的代码现在看起来像这样:

```
function myFunction(...params) {
  Promise.all([someFunction1(), someFunction2(...params)]).then(([value1, value2]) => {
    ...
  });
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

酷，这只是一个简单的重写，我们已经得到了一些好处。但是我们怎么用`async/await`来写呢？我更喜欢`async/await`,因为它对我们代码的可读性有很大帮助，毕竟我们写的代码别人必须阅读并弄清楚它是干什么的！

由于`Promise.all`返回一个承诺，我们可以很容易地使用第一个例子中的相同策略，所以:

```
async function myFunction(...params) {
  const [value1, value2] = await Promise.all([someFunction1(), someFunction2(...params)]);
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

哇！现在有一种更好的阅读方式，我们正在利用这种平行方式。

现在我们必须解决`whatNow`函数的问题。他想要的是并行执行所有的承诺，因为等待每个承诺解决后再调用下一个是没有意义的。一步一步来！

第一个问题是将`forEach`与`async/await`一起使用。`forEach`是一个同步调用，对异步代码没有帮助(想知道更多吗？[在这里看这个帖子](https://codeburst.io/javascript-async-await-with-foreach-b6ba62bbf404)。我们必须记住的第一件事是，我们有`Promise.all`来帮助我们。所以我们可以这样开始:

```
async function myFunction(...params) {
  const [value1, value2] = await Promise.all([someFunction1(), someFunction2(...params)]);

  const promises = value1.map(someFunction3); // We have now an array of promises

  await Promise.all(promises);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在情况正在好转。我们映射`value1`的每个值，并从中返回一个承诺，称为`someFunction3`。之后，我们等待，直到所有这些承诺都解决了！

但是我们的问题更深一点，因为对于每个值`value1`，我们有不止一个函数可以调用。让我们看看它是如何工作的:

```
async function myFunction(...params) {
  const [value1, value2] = await Promise.all([someFunction1(), someFunction2(...params)]);

  const promises = value1.map(v1 => [someFunction3(v1), someFunction4(v1)]);

  await Promise.all(promises);
} 
```

Enter fullscreen mode Exit fullscreen mode

它不再工作了。原因是，现在我们正试图解决和阵列在另一个阵列的承诺。貌似[[许诺，许诺]，[许诺，许诺]]。所以一个策略就是扁平化！`Array.prototype.flat`还处于实验模式，所以我将使用[多孔填料](https://stackoverflow.com/questions/10865025/merge-flatten-an-array-of-arrays-in-javascript)。Array.prototype.flatMap 将是一个更好的选择，但它还处于实验模式。

```
async function myFunction(...params) {
  const [value1, value2] = await Promise.all([someFunction1(), someFunction2(...params)]);

  const promises = flatten(value1.map(v1 => [someFunction3(v1), someFunction4(v1)]));

  await Promise.all(promises);
} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们得到了想要的结果！让我们继续前进！

下一个问题是第一个`forEach`里面的`forEach`。让我们尝试应用与之前相同的策略:映射到`value2`并从`someFunction5`返回一个承诺。

```
async function myFunction(...params) {
  const [value1, value2] = await Promise.all([someFunction1(), someFunction2(...params)]);

  const promises = flatten(value1.map(v1 => [
    someFunction3(v1),
    someFunction4(v1),
    value2.map(v2 => someFunction5(v1, v2)),
  ]));

  await Promise.all(promises);
} 
```

Enter fullscreen mode Exit fullscreen mode

它成功了！哇！它之所以有效，是因为我们正在拉平承诺的阵列，否则在阵列的阵列中，它会比以前更令人毛骨悚然...

现在我们可以说我们充分利用了 Javascript 异步代码，因为我们不是一次解决一个承诺来分派下一个，而是将所有承诺一起分派并等待最终结果！

# 结论

我希望你现在能以某种方式理解`async/await`是如何与承诺联系在一起的:它们基本上是一样的东西，但是`async/await`提供了一个更容易阅读的语法(aaand 依赖于`try/catch`，这是处理错误的标准方式)。

上面的例子是一个来自 Slack 的真实案例，它有自己的特点。然而，它深深依赖于异步代码，以尽可能最好的方式工作，这意味着如果您误解了一些概念，它会变得更加棘手。

如果您对此代码有任何其他建议，请告诉我，因为我知道对于同一个问题有不同的解决方案！任何值得添加的建议都可以在下面的评论中提出来，我会在这里添加的！

有例子的 gist:[https://gist . github . com/viniciuskneves/086632 be 8482 e2cd 842669d 202414249](https://gist.github.com/viniciuskneves/086632be8482e2cd842669d202414249)