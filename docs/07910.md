# 如何避免 Javascript Bugs

> 原文：<https://dev.to/neutrino2211/how-to-avoid-javascript-bugs-31pm>

成为一名 javascript 程序员很棒，但是我们永远无法避免可怕的堆栈跟踪！😡，这些错误中的大多数只是一个谷歌搜索就可以解决，但有时谷歌无法帮助你，你会被这个令人头痛的错误所困扰，但不要担心，因为我有一些技巧可以帮助你避免大多数错误。

### 1。了解你的*来源*和*来源*

[![Sink](img/ccef6a1f62990b4c33878088b4a9575f.png)](https://i.giphy.com/media/3o7TKOuX9nFjq3bR5u/giphy.gif) 
首先，什么是*源*和*汇*，一个**源**是生成一条信息的进程，一个**汇**是这条信息的消费者。
举例:

`function doSomething(randomPercentage){ const percentString = randomPercentage + "%"; console.log(percentString); } const randomPercentage = Math.random()*100; // Math.random is the Source here. doSomething(randomPercentage); // And the doSomething function is the sink.`

这看起来很琐碎，从表面上看，一切都很好，但看起来可能是骗人的，该函数似乎只在数字的末尾添加了一个“%”，并显示结果字符串，但当执行代码时，您将得到类似于此`17.64650669753125%`的内容。这看起来不太好。

这是一个典型的例子，说明如果你不仔细检查你的水源或汇，会出什么问题。

百分比不是整数的原因来自于`Math.random`函数，因为它只生成浮点数，而我们没有 **Math.floor** 。所以让我们来解决这个问题

`function doSomething(randomPercentage){ const percentString = randomPercentage + "%"; console.log(percentString); }` `const randomPercentage = Math.floor(Math.random()*100); doSomething(randomPercentage); // And our sink now receivs correct data.`

要点是找到在我们的函数之前/被我们的函数使用的数据，并沿着流向上工作，直到我们找到产生错误/意外数据的点。

### 2。避免`undefined`

[![Avoid](img/926f31bf49a37ce92ce3b3d73ce6e218.png)](https://i.giphy.com/media/QA88yMhazfDI4/giphy.gif)

有时我们会偷懒，留下一些未定义的变量，并期望当我们使用它们时，它们会被初始化。在某些情况下，这样的代码是必需的，但是当需要使用它们时，最好有一个默认值。

例子

**错误的用法。**

```
 let laterUsed;

doAsyncTask().then(()=>{
    useVariable(laterUsed); // ❌ It could still be undefined
}) 
```

Enter fullscreen mode Exit fullscreen mode

**正确用法。**

```
let laterUsed;

doAsyncTask().then(()=>{
    useVariable(laterUsed || externalAlternativeValue); 
    // ✅ Another value is used when the original value is undefined
}) 
```

Enter fullscreen mode Exit fullscreen mode

**更正确的用法**

```
doAsyncTask()
  .then(result => {
    useVariable(laterUsed)
  })
  .catch(e => {
    // handle whatever happens when doAsyncTask() goes wrong
  }) 
```

Enter fullscreen mode Exit fullscreen mode

还有，避免

*   使用全局变量在异步进程间通信。
*   使用`undefined`指示错误状态。或者没有价值。

### 3。了解你的**面食！**

[![Pasta](img/ccccbdd60bad64fa52e5efea51005681.png)](https://i.giphy.com/media/ToMjGpRhf96j23aTc5i/giphy.gif)

有许多不同的方式来编写代码，这些风格有基于 pasta 的分类，你需要理解以便知道使用哪种风格。

#### 意大利面代码

避免这一点，真正的意大利面是否美味并不重要😋意大利面条式代码的特点是代码杂乱无章，没有任何真正的结构/模式....就像意大利面条一样，这种代码风格很容易出现很多 bug，由于前面提到的混乱的代码库，这些 bug 很难解决。

#### 饺子码

这种编码风格处于一个非常好的中间地带，程序被分解成一些组件，这些组件可以很好地协同工作，但也可以独立运行。你是否写馄饨代码是基于你的程序是如何构造的。

#### 千层面代码

javascript 开发的圣杯，结构良好的组件被分成层，通过定义良好的接口执行特定的任务。尽可能地使用这种方法，因为它降低了错误的风险，如果出现错误，它们将被限制在特定的层。

另一件要注意的事情是**必要时请使用类型检查。**

### 4。不要一次做太多

[![too much](img/b21dd36faade1815f76150ab2bedf3aa.png)](https://i.giphy.com/media/3ohjV6xeE0OrC6QXq8/giphy.gif)

有时我们倾向于写很多代码而不测试每一步，这是非常危险的，因为当一个错误发生时，你不知道是哪个新代码导致了这个问题，这将使你调试你不需要的代码部分，这浪费时间并且可能是有压力的。

因此，每次你添加一个新功能或决定以不同的方式发送你的应用程序数据时，总是要运行代码以确保这部分是正确的。

### 4.5。使用棉绒

请使用棉绒！他们真的很擅长帮助你实施一个好的编码风格，并为你捕捉错误。

## 结论

这些是我给你的一些建议，这样你就可以尽可能地避免错误，其中一些也适用于 javascript 之外的其他语言。

*有趣的事实:我想到了第四点，因为我写了太多未经测试的代码，花了 7 个小时试图调试它*

感谢这些优秀的人给了我很好的建议。

[![jesuszilla_tm image](img/0eab4d389f098a95b3df01774ff96738.png)](/jesuszilla_tm)

## [Jesuszilla 耶稣吉拉](/jesuszilla_tm)

[I got my start in programming with the MUGEN fighting game engine. I am the lead developer of a successor to the engine called Blugen.](/jesuszilla_tm)[![gypsydave5 image](img/ae3081c50d4c953e1125e67f5f086a97.png)](/gypsydave5)

## [大卫·威克斯](/gypsydave5)

[British. Strong opinions held weekly. No, that's not a typo. Teaches when and where and what I can.](/gypsydave5)

## 感谢阅读！！

*考虑在 [Twitter](https://twitter.com/neutrino2211) 上关注我，你可以查看我之前的帖子[这里](https://dev.to/neutrino2211/avoiding-weird-javascript-behaviour-true--true--2-but-true--1-pn9)*