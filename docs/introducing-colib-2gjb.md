# 介绍 Colib🎉

> 原文：<https://dev.to/darcyrayner/introducing-colib-2gjb>

嘿大家好！我写这篇文章是为了宣布 Colib(Command Library 的缩写)的 v1 发布到野外。Colib 是我们多年来用来帮助构建游戏和交互式媒体的一组模式，从一个 [C# Unity 版本](https://github.com/darcy-rayner/colib/blob/master/colib/README.md)开始，现在是一个新的[类型脚本/Web 实现](https://github.com/darcy-rayner/colib-js)。

## 什么是 Colib？

Colib 使得构建复杂的时序逻辑和动画序列变得容易，而不会陷入回调地狱。在其核心，Colib 是一个需要时间的命令库，以及一组安排这些命令的工具。

## 介绍性的例子

在我们准确定义什么是命令之前，让我们看一个使用 Colib 可以做什么的例子。

```
import { globalScheduler, waitForTime, parallel, changeTo, coroutine } from 'colib';

const scheduler = globalScheduler();
const obj = { x: 10, y: 100 };
scheduler.add(
  waitForTime(4.0),
  parallel(
    changeTo(obj, { y: 200 }, 3.0, inOutQuad()),
    sequence(
      waitForTime(1.5), () => {
      console.log('Reached');
    })
  ),
  coroutine(aCoroutine)
);

function *aCoroutine() {
  console.log("Starting Coroutine");
  for (let i = 0; i < 3; ++i) {
    yield waitForTime(0.5);
  }
  console.log("Finished Coroutine");
} 
```

这里我们看到使用了几个命令，[序列](https://github.com/darcy-rayner/colib-js/blob/master/docs/api/README.md#sequence)，[并行](https://github.com/darcy-rayner/colib-js/blob/master/docs/api/README.md#parallel)，[等待时间](https://github.com/darcy-rayner/colib-js/blob/master/docs/api/README.md#waitForTime)，[改变为](https://github.com/darcy-rayner/colib-js/blob/master/docs/api/README.md#changeTO)，以及[协程](https://github.com/darcy-rayner/colib-js/blob/master/docs/api/README.md#coroutine)。让我们一步一步地分解整个事情。

### 击穿

```
scheduler.add(
    //...
) 
```

这里使用的全局调度程序将运行这些命令。命令不会做任何事情，直到有东西运行它们。

```
waitForTime(4) 
```

等待 4 秒钟，然后继续执行下一个命令。

```
parallel(
    //...
) 
```

同时执行它的所有子命令。

```
changeTo(obj, {y: 200}, 3.0, inOutQuad()) 
```

使用`inOutQuad()`缓动，在 3 秒的时间内将`obj.y`的值补间为 200。

```
sequence(
    //...
) 
```

一个接一个地执行所有子命令。因为这个序列嵌套在并行命令中，所以整个序列与 changeTo 命令同时执行。

```
waitForTime(1.5) 
```

等待 1.5 秒，然后继续执行下一个命令。

```
() => { console.log('Reached') } 
```

记录“到达”控制台的回调命令。

```
coroutine(aCoroutine)
//...
function *aCoroutine() {
  console.log("Starting Coroutine");
  for (let i = 0; i < 3; ++i) {
    yield waitForTime(0.5);
  }
  console.log("Finished Coroutine");
} 
```

这将启动一个协程。基本上，协程是 ES6 生成器，您可以在其中生成任何命令。该命令将在功能恢复执行之前完成。在本例中，`aCoroutine`记录“启动的协同程序”，连续三次等待 0.5 秒，然后记录“完成的协同程序”。

## 命令

现在我们已经看到了一些命令，但是它们是什么呢？命令是消耗时间的函数，不管完成与否都会返回。看看下面这个命令的(简化的)函数签名:

```
export type Command = (deltaTime: number) => { deltaTime: number; complete: boolean }; 
```

一个命令占用它被允许前进的最大时间量，`deltaTime`，并返回任何剩余的未使用时间以及它是否完成。通常，命令被实现为闭包函数，因此它们在内部跟踪状态。这意味着大多数命令是不纯的，(它们有副作用)。

因此，命令的定义很简单，但它可以用来构建一些非常酷和复杂的行为。正如我们在例子中看到的，[并行](https://github.com/darcy-rayner/colib-js/blob/master/docs/api/README.md#parallel)和[序列](https://github.com/darcy-rayner/colib-js/blob/master/docs/api/README.md#sequence)允许命令以不同的方式轻松组合在一起。其他值得一试的可用于合成的常见命令包括[重复](https://github.com/darcy-rayner/colib-js/blob/master/docs/api/README.md#repeat)、[重复](https://github.com/darcy-rayner/colib-js//blob/master/docs/api/README.md#repeatForever)和[选择随机](https://github.com/darcy-rayner/colib-js/blob/master/docs/api/README.md#chooseRandom)。

## 其他东西

Colib 是一套超级灵活的工具。查看不同方式的指南，以[调度命令](https://github.com/darcy-rayner/colib-js/blob/master/docs/guides/scheduling.md)，可用的[补间实用程序](https://github.com/darcy-rayner/colib-js/blob/master/docs/guides/tweening.md)，以及[高级协程模式](https://github.com/darcy-rayner/colib-js/blob/master/docs/guides/coroutines.md)。还有很多内置的[命令可用](https://github.com/darcy-rayner/colib-js/blob/master/docs/api/README.md)。

## 欢迎反馈

随意提出 [Github 问题页面](https://github.com/darcy-rayner/colib-js/issues)的 bug，或者建议功能。

快乐测序！