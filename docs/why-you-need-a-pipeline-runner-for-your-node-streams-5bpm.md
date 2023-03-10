# 为什么您的节点流需要管道运行器

> 原文：<https://dev.to/youknowfordevs/why-you-need-a-pipeline-runner-for-your-node-streams-5bpm>

# 基本节点流

一个基本的节点流应用程序应该是这样的:

```
const fs = require('fs')
const zlib = require('zlib')

fs.createReadStream(inputPath)
.pipe(zlib.createGzip())
.pipe(fs.createWriteStream(outputPath)) 
```

关键是每个流都提供了一个`pipe()`方法，用于附加流。

然而，尽管这是正确的，但它并不容易操作；特别是，我们必须从一个流开始，然后把其他流连接到它上面，这就破坏了对称性，使得整个过程难以推理。

# 一个更好的模型:一个流的管道

一个更好的比喻是包含多个步骤的管道，每个步骤都是一个流。在这个模型中，第一个或最后一个流没有任何“特殊”之处。从版本 10 开始，Node 提供了模块函数 [stream.pipeline()](https://nodejs.org/api/stream.html#stream_stream_pipeline_streams_callback) 来实现这一点。这个函数可以是任意的，这使得代码更容易阅读。我们之前的管道现在看起来像这样:

```
const stream = require('stream')
const util = require('util')
const pipeline = util.promisify(stream.pipeline)

const fs = require('fs')
const zlib = require('zlib')

await pipeline(
  fs.createReadStream(inputPath),
  zlib.createGzip(),
  fs.createWriteStream(outputPath)
) 
```

现在更清楚的是，我们的流是管道中的“步骤”，它们不再扮演驱动管道的角色。

我们将使用这种模式作为我们的规范管道，因为它更容易扩充。

# 将管道创建与运行分开

既然我们已经将管道作为我们的主要概念，我们就可以开始使用它了。特别是，如果我们能够将*创建*管道的步骤与*运行*管道的步骤分开，我们会发现这非常有用。

例如，我们可以创建一个管道并检查它的错误，甚至在它运行之前；例如，我们可以检查第一步是可读的流，最后一步是可写的流，或者确保在管道开始之前所有必要的资源(如数据库或搜索引擎)都可用。

我们还可以插入不需要在管道中显式指定的额外步骤，例如在每个步骤之间将项目推入队列或从队列中取出项目，或者为特定于步骤的错误添加处理程序。

将管道创建与执行分离的另一个好处是，我们可以在管道运行之前优化管道，也许是通过合并或重新排序步骤。我们甚至可以在一台机器上创建管道，然后将管道发送到另一台机器上运行……或者多台机器上，如果我们有办法分割工作的话。

我们不仅可以用整个管道做有趣的事情，我们甚至可以操纵管道的*部分*；我们可以运行管道的一半，然后将结果具体化，然后使用保存的结果运行管道的其余部分(顺便提一下，允许管道升级，甚至在运行时)，或者我们可以将单独的管道步骤作为无服务器函数来执行，这将带来所有的好处。

为了让我们自己达到这种情况，我们的下一步是将管道本身从其运行者中分离出来。

## 创建跑步者

跑步者的基本形式是先创造一些台阶:

```
const steps = [
  fs.createReadStream(inputPath),
  zlib.createGzip(),
  fs.createWriteStream(outputPath)
] 
```

然后运行这些步骤:

```
await pipeline(...steps) 
```

如果我们将这个功能包装在一个类中，那么我们就有了一个基础来添加我们前面提到的其他功能。因此，让我们创建一个类，允许我们用一种方法*注册*一组步骤，用另一种方法
运行这些步骤

```
class Runner {
  constructor() {
    const stream = require('stream')
    const util = require('util')
    this.pipeline = util.promisify(stream.pipeline)
  }

  register(steps) {
    this.steps = steps
    return this
  }

  run() {
    return this.pipeline(...this.steps)
  }
} 
```

现在我们可以像这样使用我们的跑步者:

```
const runner = new Runner()
const steps = [
  fs.createReadStream(inputPath),
  zlib.createGzip(),
  fs.createWriteStream(outputPath)
]

await runner
.register(steps)
.run() 
```

这与我们之前的管道完全相同，但通过这种结构，我们可以通过继承基类，在很多地方插入我们之前提到的新功能。

例如，我们可以通过`register()`方法添加更多的特性，比如管道验证、步骤优化、额外步骤的插入等等。(管道的操作就像操作数组一样简单。)

我们可以修改`run()`方法，将步骤发送到其他服务器上，或者启动更多的工人，在他们之间分担工作。

在以后的文章中，我们将开始充实如何通过构建这个类来实现这些更高级的特性。

# 结论

节点流非常强大，是高效快速的数据处理和转换应用程序的基本构件。然而，将输入流的输出通过管道传输到另一个流的常用模型很难操作和推理。Node 通过`pipeline()`函数支持一个更好的模型，这篇文章展示了如何使用它来构建包含多个流的管道，然后分别执行这些管道。