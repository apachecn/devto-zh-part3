# JavaScript 中的测试驱动开发

> 原文：<https://dev.to/tomekbuszewski/test-driven-development-in-javascript-olg>

测试我们正在编写的代码在工作中至关重要。即使有团队根本不做测试，这也是成功交付的最重要的部分之一。

有许多测试软件的方法。我最喜欢的一个是 TDD，测试驱动开发的缩写。它突出的原因是，它颠倒了自然的(或者看起来是这样的)编写流程，首先是逻辑，然后是测试。这种方法首先是测试，然后是逻辑。

## 为什么 TDD 有意义

乍一看，这似乎很奇怪。为什么要测试不工作的东西？为什么要检查明显的东西？换一种方式思考，为你的代码设定需求和假设。当你接到一个任务时，它会迫使你把它分解成尽可能小的部分，并为它写下假设。

以斐波那契序列发生器为例。目标是创建一个接受一个参数并返回一组数字的函数。很简单。我们应该测试什么？

*   它应该为整数以外的参数抛出一个错误；
*   它应该返回一个数组；
*   当参数为 0 时，它会引发错误；
*   当参数为 1 时，应该返回`[0]`；
*   它应该返回一个包含整数的数组；
*   它应该返回参数为 4 的`[0, 1, 1, 2]`。

看看这份名单。六个案子。对，六格，不是六行文字。这些很容易转移到测试中。观察:

```
it ("should return an array", () => {
  expect(Array.isArray(fib(5))).toBeTruthy();
}); 
```

这种符号非常简单，允许提前计划。

## 测试驱动开发的三个周期

TDD 最重要的一点就是给自己创造一个循环。它由三个阶段组成——*红色*、*绿色*和*重构*。

*   红色的阶段是编写测试并运行它。它将失败，IDE 或任何其他运行程序将是红色的；
*   绿色的阶段正在为给定的测试编写代码。得过，但不一定要写得好；
*   *重构*阶段意味着让你的代码符合标准；

在周期结束时，您的代码片段应该按照当前(项目)标准进行测试和编码。请记住，这些周期的长度应该相似，如果不是相同的话。测试驱动开发与[番茄工作法](https://en.wikipedia.org/wiki/Pomodoro_Technique)配合得很好。

这怎么呈现？让我们试着写一个返回数组的案例。

首先，我们创建一个测试(*红色* ):

```
// index.test.js
const fib = require(".");

describe("fib tests", () => {
  it("should return an array", () => {
    expect(Array.isArray(fib(5))).toBeTruthy();
  });
}); 
```

运行它会失败，可能是因为我们甚至没有一个`index.js`文件，或者即使我们有，它也没有任何内容。

让我们开始绿色的阶段。

```
// index.js
const fib = (target) => {
  const collection = [];

  while (collection.length < target) {
    collection.push(null);
  }

  return collection;
}; 
```

这段代码是有效的，现在运行测试将会很好，这意味着它满足了假设。

但是，使用`while`循环似乎有点臭。也许我们应该使用函数范式，进行递归！让我们*重构* :

```
const fib = (target, col = [0, 1]) => {
  if (col.length === target) {
    return col;
  }

  const newCollection = const newCollection = [...col, null];

  return fib(target, newCollection);
}; 
```

结果没有改变，但是这段代码看起来更好。(我知道我应该利用总体拥有成本，但我不想混淆视听)。

我不会在这里写更多的测试，你可以自己做。你可以在我的[代码沙箱](https://codesandbox.io/s/llml7197yq)中检查你的结果或者获得帮助。

## 结论

我在这里展示了测试驱动开发的基本用法。它确实让您对这种技术的工作原理及其带来的好处有所了解。但是要真正欣赏它，你应该和它一起工作一段时间。我强烈建议你这样做！

—

[code sandbox 上的完整代码和测试](https://codesandbox.io/s/llml7197yq)