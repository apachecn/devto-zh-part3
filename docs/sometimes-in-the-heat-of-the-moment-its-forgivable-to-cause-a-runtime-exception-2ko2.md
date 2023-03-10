# 有时，在最激烈的时刻，导致运行时异常是可以原谅的。

> 原文：<https://dev.to/nimmo/sometimes-in-the-heat-of-the-moment-its-forgivable-to-cause-a-runtime-exception-2ko2>

运行时出错*吸*。但是当你在 JS 工作时，它们是很难避免的。幸运的是，我们的整个交易是解决问题；所以我们要避开他们。

对于客户端 JS 来说，这似乎是完全必要的:我们不应该让用户遭受运行时异常；如果出现错误，我们应该给他们适当的反馈。

但是我们总是想不惜一切代价避免运行时异常吗？我不相信。

在一个完美的世界里，我们在每一种语言中都有一个 Elm 编译器的等价物。但是在现实世界中，当事情真的出错时，我们可以节省一些时间。

以此为例:

```
import someModule from 'someModule';

const {
  someObject: {
    someFunction,
  } = {},
} = someModule; 
```

Enter fullscreen mode Exit fullscreen mode

让我们假设我们的代码在部署之前是用 Babel 传输的。在这种情况下，如果`someObject`在`someModule`中不存在，那么这将很好地传输。但是在运行时，`someFunction`应该是`undefined`。

```
Uncaught TypeError: someFunction is not a function. 
```

Enter fullscreen mode Exit fullscreen mode

对于我们的一个用户来说，这段代码似乎注定要失败。

考虑一下，如果我们用这种方法，在我们的析构函数中没有默认值:

```
import someModule from 'someModule';

const {
  someObject: {
    someFunction,
  },
} = someModule; 
```

Enter fullscreen mode Exit fullscreen mode

现在，如果`someObject`在`someModule`中不存在，我们将在尝试传输文件时而不是在部署后得到一个运行时错误。

```
Uncaught TypeError: Cannot destructure property `someFunction` of 'undefined' or 'null'. 
```

Enter fullscreen mode Exit fullscreen mode

这种反馈不仅要快得多，而且在我们的机器上也会失败。这个特殊的例子甚至只能发生在任何给定文件的一个地方，这提高了我们快速定位问题的能力。有了任何种类的自动化构建管道，这个错误现在*甚至不可能再投入生产*。考虑到我们只是去掉了三个角色，这还不错。

当然，这个例子并不代表我们在 JS 中可能遇到的所有问题。但这是我最近看到的一个真实的例子。这是过分热衷于防止运行时异常的直接结果:一些原始代码*甚至没有做的事情*。

**TL；博士**:我们应该花更多的时间去思考我们的代码如何以及在哪里会失败，我们应该非常小心地考虑我们试图保护自己免受错误带来的意想不到的后果。