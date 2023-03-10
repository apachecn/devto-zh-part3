# 快乐的小意外——调试 Javascript

> 原文：<https://dev.to/hamatti/happy-little-accidents---debugging-javascript-48ak>

去年，我在赫尔辛基和图尔库❤️前端会议上做了一个题为“快乐的小意外——调试的艺术”的演讲。

这周我花了很多时间调试奇怪的时区问题，这个话题又从我的记忆中出现了。所以我想写一篇关于不同选项的更详细的、侧重于 Javascript 的帖子。

## 打印到控制台

下面所有的例子都是你可以复制粘贴到你的开发人员控制台并开始试验的例子。

### console.log

最被低估但绝对强大的工具之一是`console.log`和它的朋友们。这通常也是检查问题的第一步，也是最容易的一步。

```
console.log(`The value of variable foo is ${foo}`) 
```

Enter fullscreen mode Exit fullscreen mode

最简单的方法就是记录一些带有变量值的额外文本。ES6 和它的模板文字使它变得轻而易举。不需要担心字符串连接。

要打印出多个值，可以做

```
console.log(`name: ${name}, age: ${age}, city: ${city)`) 
```

Enter fullscreen mode Exit fullscreen mode

但是谢天谢地，ES6 给我们带来了对象属性值速记。它允许我们创建对象，在这些对象中，键将成为变量名并对其值进行赋值。

```
console.log({name, age, city}) 
```

Enter fullscreen mode Exit fullscreen mode

你甚至可以用 CSS 给你的输出着色！

```
console.log(`%c This is blue,%c and this is green`, 'color: blue', 'color: green') 
```

Enter fullscreen mode Exit fullscreen mode

### 控制台.表

在很长一段时间里，上面的用法是我如何使用控制台打印的程度。但是最近我学到了很多新工具，可能对你来说也是新的。

```
let dog = {
  name: 'Bolt',
  age: 3,
  isGood: true
}

let cat = {
  name: 'Grumpy',
  age: 5,
  isGood: false
}

console.table([dog, cat]) 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/09c238115cb1ac2b1708016ed4995f02.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--jJzne-qu--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/59f0ddu4gmn0bqxpr3ky.png)

使用`console.table`,您可以很好地输出数据的表格视图。当你有共享相同键的对象时，这是非常好的。

### 控制台. trace

如果你想检查你的函数调用的堆栈跟踪，你可以使用`console.trace()`

```
function foo(bar) {
  console.trace();
  return bar * 2;
}

console.log(foo(21)); 
```

Enter fullscreen mode Exit fullscreen mode

### 控制台.计数

有时你需要记录一行已经执行了多少次。添加一个新的变量，跟踪它，记录它是很麻烦的，最终会在你的代码中留下未使用的变量。

```
function foo(bar) {
  if(bar > 0) {
    console.count('Bar was positive')
    return true;
  } else {
    console.count('Bar was 0 or negative')
    return false;
  }
}

foo(1)
foo(0)
foo(42)
foo(-210) 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/3eda7a61e97d7cdd828b346d6e5c0e25.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--_UF7VzLJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/3jmlv2srtqn541kso5g5.png)

### 控制台

如果您的应用程序打印出大量输出，有时您可能希望更好地对其进行分组。不用插入手动`console.log('Here be dragons')`，可以使用`console.group`

```
function foo(bar) {
  console.group('At function body')
  console.log(`Executing function foo with a list of ${bar.length} items`)
  console.group('Inside loop')
  bar.forEach(baz => {
    console.log(`Value of baz is ${baz}`)
  })
  console.groupEnd()
  console.log(`Function foo has finished`)
  console.groupEnd()
}

foo([1,2,3,4])
foo(['Bolt', 'Lassie', 'Doggo']) 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/a0a033aae47e9cd3df5052eed8556218.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--3srfMG3f--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rnt1dtd262jdcfr0thw1.png)

## 调试器

Javascript 的`debugger`关键字是一种神奇的生物。它可以让您访问所有的位置，并完全访问本地和全局范围。让我们来看一个假设的例子，它有一个 React 组件，该组件传递给它一些道具。

```
const CardComponent = props => {
  debugger;
  return (
    <h1>{props.title}</h1>
    <ul>
      {props.items.map(item => (<li>{item}</li>))}
    </ul>
  );
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/5867d3bb923a429c35a100cc9a37188e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--FeDLn8JV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/1u7c6bqdgsx5ptqthlrb.png)

当组件被渲染时，如果我们的浏览器的开发工具是打开的，它将停止执行并让您访问控制台。在这里，您可以利用上一节的知识，检查 props 内部的内容或调试器执行范围内可用的任何其他变量。

我经常使用调试器作为快速原型开发工具:我跳到代码中的特定位置，查看我有什么数据以及什么格式，并在开发人员工具控制台中构建一些小块的内容，然后将这些内容移入代码本身。

这种方法缩短了从“在编辑器中编写代码->刷新浏览器”到“在控制台中编写代码->查看结果”的反馈循环。

一旦进入调试器模式，您还可以继续逐行执行，查看执行失败的地方。

## 浏览器开发工具

如果您正在使用 console.log 和调试器，您可能对浏览器开发工具很熟悉。如果没有，你应该熟悉它们。它们在不同的浏览器之间有所不同，我最熟悉的是谷歌浏览器，所以我的例子将来自于此。如果你使用的是 Safari、Edge 或 Firefox，你可以通过一些探索或互联网搜索很容易地找到答案。

### 条件断点

您也可以在不更改任何代码的情况下访问调试器状态。在 Chrome Dev Tools 的`Sources`标签中，你可以打开一个 Javascript 文件，点击任意行号，使其变成蓝色箭头

[![](img/040d2af7c366c3a45a9643cb84d515ce.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--hlWsDyCR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/zl27bwcg53ncv8sc4nkv.png)

更棒的是，您可以通过右键单击行号并选择**编辑断点**来设置这些断点。

[![](img/c859a88d46f7c99e4a7bd2469ee3b5d3.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--MVeKxddh--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/rer0a0l1zzl5ce9zamdn.png)

虽然它指示您在那里提供一个布尔表达式，但您不一定必须这样做。我已经使用这些断点条件添加了一个`console.log`,当遇到断点条件时运行。它将返回 undefined，这是一个假值——它仍将打印它，但不会停止执行。

### 手表

[![](img/bae08872c5e1b19e2f23f964c35c2085.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--pY0flaIl--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/uczt8xj37hvrz11yyyz0.png)

Chrome 开发工具的右边是按钮和面板的集合。虽然它们都为您提供了有价值的工具，但我只强调一个(您可以自己了解其他工具):观察。

在 watch panel 中，您添加变量，当代码执行时，它将显示它们的当前值。不需要在每个断点之后跳转到控制台(例如在一个循环中)，您可以只查看 watch 中的值，看看会发生什么。

### 网络选项卡

在现代 web 开发中，对 API 端点的 HTTP 调用是传输数据最常见的方式之一。Chrome Dev Tool 的网络标签是一个很好的例子，展示了如何检查什么东西在流出，什么东西在流入。

[![](img/c95c1e870dbc2dbdede825ecabe596e0.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--kZ9KQ7RR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/kpt4vdlwtkoqerwid1bi.png)

在这里，我对[https://api.pokemontcg.io/v1/cards?name=charizard](https://api.pokemontcg.io/v1/cards?name=charizard)进行了一次 API 调用，我可以直接从网络选项卡检查响应。这可以帮助您确定返回的是什么数据，以及它的格式是否适合您的使用。

通过点击不同的选项卡，您可以找到关于标题和响应以及呼叫性能的更多信息。

### 框架特定的扩展

通过扩展对不同框架的支持是惊人的。如果你正在开发 [React](https://chrome.google.com/webstore/detail/react-developer-tools/fmkadmapgofadopljbjfkapdkoienihi) 、 [Redux](https://chrome.google.com/webstore/detail/redux-devtools/lmhkpmbekcpmknklioeibfkpmmfibljd?hl=fi) 、 [Vue](https://chrome.google.com/webstore/detail/vuejs-devtools/nhdogjmejiglipccpnnnanhbledajbpd) 或 [Angular](https://chrome.google.com/webstore/detail/augury/elgalmkoelokbchhkhacckoklkejnhcd) ，查看这些扩展绝对是值得的。

当我在 React+Redux 应用程序中工作时，我几乎总是打开 Redux 选项卡，因为它让我能够最好地了解哪些数据可用。

安装插件(并重启你的浏览器),在 Chrome Dev Tools 中，你将拥有强大的扩展工具。

## 测井

虽然打印到控制台很有帮助，但只有在开发应用程序时才有用。它们不会持续存在，也不会离开客户端的浏览器控制台，因此您无法查看问题的历史记录。

这就是日志服务派上用场的地方。使用像 [Elasticsearch](https://www.elastic.co/) 或 [BugSnag](https://www.bugsnag.com/) 这样的工具，你可以将你的日志保存到服务中，并获得分析和搜索问题。当您的用户报告出现问题时，这尤其有价值，您只需打开日志服务，查看发生了什么。

将日志记录到这些服务很简单。在 BugSnag 中，你使用他们的 SDK，只要有值得记录的事情发生，就运行

```
try {
  something.risky()
} catch (e) {
  bugsnagClient.notify(e)
} 
```

Enter fullscreen mode Exit fullscreen mode

[![](img/074dce83e73562b9b69c8e41e5dd5d85.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YEOTyGmV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/842s9wpv1kifmgwxrl4b.png)

对于小项目，可以看看我一个朋友创建的 [Gentry](https://github.com/akx/gentry) 。

## 非技术手段

[![](img/b3c81739b3f727d2bcfa28818fa007ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--shA1ZLMf--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/f7bnmwg5bj6yhj4qh7go.jpeg)

除了编写 Javascript 代码和与浏览器交互之外，还有一些非技术性的方法来调试软件。

### 橡皮鸭调试

向你的同事解释你正在尝试做什么，遇到了什么问题，以及你已经尝试过什么是一个很好的工具。它迫使你从多个不同的方面思考问题，并以一种通常能帮助你在同事有时间回答之前找到解决方案的方式来表达问题。

[![](img/f4961bf285212406289ff98ba7e3cb1a.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--6f8vWG5M--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/akoesieeg4tc85mmxbwb.jpg)

为了从这种相当独白式的方法中节省你同事的时间，你应该[和鸭子](https://rubberduckdebugging.com/)交谈。我的办公桌上就有一个，虽然有人可能认为和一只橡皮鸭说话是愚蠢或疯狂的，但这种方法真的很有效。我的鸭子是一个特殊的品种，它是一只侦探鸭，就像每个人都知道的那样，侦探鸭对编程错误非常有效。

### 睡觉&走路(不一定按这个顺序)

远离电脑和问题。让你的潜意识去思考它，去想一些完全不同的事情。我经常每天下午散步 20 分钟，尤其是在有严重问题的时候。睡觉是另一个非常好的方法，因为它让你的头脑从过多的想法中清理出来，并明确解决方案。

当我面临一个特别棘手的问题时，我会这样做:

1)大脑倾卸:把一切都记到我的笔记本上。有什么问题，它发生在哪些情况下，我尝试过什么，我有什么假设。

2)出去散步 20 分钟，完全考虑其他事情，享受新鲜空气和大自然。因为我什么都记下来了，所以不用担心忘东西。

3)回到办公桌。如果我在走路的时候对这个问题有了新的想法，我会把它们加到我的笔记里。之后，回到解决问题上来。

休息听起来总是违反直觉，但当你不把头撞向墙壁，而是让大脑放松时，它可以提高你解决问题的技能，你的生产力和你的幸福感。最好的解决方案不是强迫的，而是来自于你给自己时间和空间去思考。

## 总结

那么我们今天学到了什么？

控制台是 Javascript 中的发电站。它最常用于 console.log，但是了解它的不同能力是值得的。

调试器是朋友。无论它是一个进入状态以找出问题所在的工具，还是一个帮助你交互式地构建下一段代码的工具，有效地使用它都会改善你的工作流程。

花时间学习你的浏览器开发工具。你可以开发一个 React+Redux 应用程序，而不需要额外的工具，但是一旦你安装了这两个应用程序的扩展，你就可以更容易地访问你的 Redux 状态、组件状态和 props，以及基本上所有需要控制台、日志和调试的东西。我把 Firefox 作为我的第二浏览器，仅仅是因为他们内置了 CSS 网格工具。

如果你还没有做的话，开始记录你的应用程序中发生的事情。它会让你深入了解它是如何工作的，它是如何被使用的，并且会让你在所有的客户发送愤怒的电子邮件来支持之前解决一些突然出现的问题。

休息一下，和一只鸭子说话，散步，睡觉。开发人员的工作不是(或者至少不应该)用敲键盘的时间或者编写的代码行来衡量的。我花了相当多的时间离开我的键盘:在我的笔记本上写笔记，画草图，设计，计划，散步，与同事讨论问题。