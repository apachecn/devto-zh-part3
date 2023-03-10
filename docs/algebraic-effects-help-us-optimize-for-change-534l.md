# 代数效应帮助我们优化变化

> 原文：<https://dev.to/yurkaninryan/algebraic-effects-help-us-optimize-for-change-534l>

当一个功能必须与除了它自己之外的任何东西交互时，它就会产生副作用。

```
function double(x) {
  if (!Number.isFinite(x)) {
    return console.error(
      `double(x): ${x} is not a number`
    )
  }

  return x * 2
} 
```

`double`正在通过用`console`记录日志来处理这里的错误。结果的原因和处理程序位于同一位置。

我们的一些用户喜欢这个；有些*讨厌。*如果他们不希望`double`登录生产，或者他们购买了他们想连接的错误报告软件，该怎么办？

我们需要提升对这种效果的处理。但是，我们如何发出一个效果应该被处理的信号？

我们*可以*使用回调和类似`onError`的参数来做这件事。

```
function double(x, opts) {
  if (!Number.isFinite(x)) {
    const error = `double(x): ${x} is not a number`;
    if (opts.onError) {
      return opts.onError(error)
    }

    return console.error(error)
  }

  return x * 2
} 
```

这个解决方案在规模上有一些严重的问题。它导致重构时的阻力，并使新开发人员更难落入成功的[陷阱。](https://blog.codinghorror.com/falling-into-the-pit-of-success/)

### 好的架构有助于代码库和开发者扩展

想象一下，必须在位于`double`和其余代码之间的层中编写代码。

```
import double from "./double";

// How were we supposed to know we get a second argument
// that has an onError function?
function doublePlusOne(x) {

  // if double isn't a number, it will return a string!
  return double(x) + 1;
} 
```

**如果你忘记通过管道`onError`或者如果你不知道`double`在出错时提前返回，你将会破坏代码**。

这是处理那些边缘情况的版本。

```
import double from "./double";

function doublePlusOne(x) {
  return double(x) + 1;
} 
```

如果我们不必处理这些情况，而是能够神奇地将结果的原因与处理者联系起来，那么每个人的生活都会变得更容易。

```
function double(x) {
  if (!Number.isFinite(x)) {
    throw `double(x): ${x} is not a number`;
  }

  return x * 2
}

// We don't have to be aware anymore!
function doublePlusOne(x) {
  return double(x) + 1;
}

// anyone consuming our code can wrap at the top level!
try {
  doublePlusOne("😵")
} catch (error) {
  ErrorService.log(error);

  if (!env.PROD) {
    console.error(error)
  }
} 
```

这是产生代数效应的主要原因。可以存在而不被意识到副作用。

在这之间添加的任何代码都不会产生这种精神负担。

### 向`Try/Catch`学习

如果它不是 Javascript 语言的默认部分，你如何在 Javascript 中重新创建`try/catch`?

1.  `throw`在遇到第一个`catch`块时停止，允许可以被覆盖的顶级默认值。
2.  当我们`throw`调用栈被丢弃。这让我们打破任何主要使用`return`的语言的规则。

像 Koka 和 Eff 这样的编程语言使得代数效果成为头等公民，然而 Javascript 却不是。你可以用这些语言来构建`try/catch`。

Javascript 没有公开我们创建代数效果所需的工具，但是它公开了像`try/catch`和`async/await`这样的抽象。

虽然工程、变更和迭代可能是快速的。我们协作和重构的速度是获得高质量最终结果的关键。

**想象一下，如果我们能够提取`try/catch`的精髓，并将其应用于其他编程概念，如获取数据。**

在这个虚构的世界中，当 Javascript 找到一个`fetch.throw`时，它会开始寻找一个处理程序。

```
const fetch = new Effect();

function double() {
  const x = fetch.throw("number");
  return x * 2
}

try {
  double()
} fetch.catch(resume, ...args) {
  // resume lets double() continue running with the value!
  if (property === "number") {
    API.get("/number").then(
      response => resume(response.data.number)
    )
  }
} 
```

您可能已经注意到`resume`被传递给了处理程序。在暴露代数效应的语言中，它们通常有办法返回到发生`throw`的地方。

### 这如何帮助优化变化？

在本文的前面，我谈到了我们如何“提起处理程序”，以便暴露行为。

我喜欢把“重构时间”看作任何 API 的一个非常重要的部分。如果当你上下移动处理程序时，一个效果通过管道通过代码，你必须清理/通过相关的点。

代数效应最大限度地减少了您必须接触的代码数量，这意味着:

1.  PR 将变得更小，使得代码审查更快，更少威胁。
2.  迭代将花费更少的时间和更平滑，因为你不必接触效果和处理程序之间的每个文件。
3.  犯错误和悄悄地破坏代码库的其他部分的机会更少了。

### 包装完毕

代数效果使我们的代码库的两个部分意识到副作用:效果在哪里激发，在哪里处理。

通过加快重构周期，我们可以更快地迭代。你很少需要接触一个效果被激发的地方，或者代码库的中间代码。通常你只需上下移动处理程序。

它也给那些使用你的代码库的人提供了免费的集成入口。只要记录并公开了这一点，您就可以免费获得很大的灵活性。

* * *

如果您有任何问题或正在寻找一对一的 React 导师，请随时发推特给我**[@ yurkaniryan](https://dev.to/yurkaninryan)**！

祝你好运，编码快乐！！😄