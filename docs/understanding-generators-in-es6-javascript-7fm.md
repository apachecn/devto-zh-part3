# 理解 ES6 Javascript 中的生成器

> 原文：<https://dev.to/tuanphungcz/understanding-generators-in-es6-javascript-7fm>

也许你在 ES6 中听说过这个功能，或者你只是没有时间玩它。

这是我几天前学到的东西，非常酷。

我分几步给你解释一下。

所以**发生器**看起来像一个普通的函数，但是它允许我们**暂停**函数的执行，**稍后继续**它。

下面你可以看到一个生成器的例子，我们将分解它来看看它是如何工作的:

```
function* avengersGenerator() { // Declaring the generator
  yield "Hulk"; // Pausing the execution
  yield "Thor";
  yield "Iron man";
  return "Ultron"; // Example of finishing the generator
  yield "Spiderman";
}

const iterator = avengersGenerator(); // Creating iterator

iterator.next(); // Iterating on the generator 
```

Enter fullscreen mode Exit fullscreen mode

[code sandbox . io 上的源代码](https://codesandbox.io/s/7jkr9y3o81)

## 宣告发电机

生成器看起来与普通函数相似，唯一的区别是我们必须在函数的**后面定义一个`*`(星号)。** 

```
function* avengersGenerator() {
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

## 屈服吧！

我们可以`yield`这个函数，它基本上会在到达第一个`yield`时停止函数的执行。

```
function* avengersGenerator() {
  yield "Hulk" // The execution would pause here.
  yield "Iron man" // When we resume we would start here.
} 
```

Enter fullscreen mode Exit fullscreen mode

## 创建迭代器

在迭代器上，我们可以调用。这样，我们就可以准备好发电机了。

```
const iterator = avengersGenerator(); 
```

Enter fullscreen mode Exit fullscreen mode

## 下一个方法

这使我们能够继续执行该功能。此外，该方法还为我们提供了具有生成值的对象，以及生成器是否已经生成了它的最后一个值，如布尔值。

```
iterator.next(); // [1] Object {value: "Hulk", done: false}
iterator.next(); // [2] Object {value: "Thor", done: false}
iterator.next(); // [3] Object {value: "Iron man", done: false}
iterator.next(); // [4] Object {value: undefined, done: true} 
```

Enter fullscreen mode Exit fullscreen mode

## 返回/退出

一旦调用了 return，它将完成生成器。它基本上将`done`属性设置为`true`。

```
function* avengersGenerator() {
  yield "Hulk";
  return "Ultron"; // Example of finishing the generator
  yield "Thor"; //  Sad Thor and Spiderman wouldn't be called
  yield "Spiderman";
}

iterator.next(); // [1] Object {value: "Hulk", done: false}
iterator.next(); // [2] Object {value: "Ultron", done: true} 
```

Enter fullscreen mode Exit fullscreen mode

在我看来，发电机是一个很酷的玩物，或者至少知道它是做什么的。

在下一篇文章中，我将解释，由于在 ES6 中使用**发电机**可以在需要时**取消承诺**，发电机如何帮助我解决了工作中的一个案例。

* * *

我终于有时间写另一篇关于发电机的博文了，所以开始吧👉[在 ES6 Javascript 中用生成器取消承诺](https://dev.to/phung_cz/canceling-promises-with-generators-in-es6-javascript-d01)

* * *

## 感谢阅读

让我知道你对这个**发电机系列**的看法。如果你爱它，你知道该怎么做！分享给你的朋友和同事。

如果你希望我在下一篇文章中涉及一些话题，请在这里的 [dev.to](https://dev.to/phung_cz) 或 twitter [@phung_cz](https://twitter.com/phung_cz) 上给我发消息，或者如果你有任何建议，欢迎在下面发表评论。

下次见，继续黑✌