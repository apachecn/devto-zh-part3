# Node.js fork 不是你想的那样！

> 原文：<https://dev.to/pi0/nodejs-fork-is-not-what-you-think-of-37ko>

今天我意识到，在 Node.js 中，`cluster.fork`和`child_process.fork`的行为都不像你在 C 环境中所期望的那样。实际上，在[文件](https://nodejs.org/api/child_process.html#child_process_child_process_fork_modulepath_args_options)中有简短提及:

> 与 [fork(2)](http://man7.org/linux/man-pages/man2/fork.2.htm) POSIX 系统调用不同，`child_process.fork()`不克隆当前进程。
> 
> `child_process.fork()`方法是`child_process.spawn()`的特例，专门用于产生新的 Node.js 进程。像`child_process.spawn()`一样，返回一个`ChildProcess`对象。返回的`ChildProcess`将有一个额外的内置通信通道，允许消息在父节点和子节点之间来回传递。

## 那么是什么意思呢？

以一个简单的 C 代码为例，它分支 5 个进程:

```
#include <stdio.h>
#include <sys/types.h>
#include <unistd.h> 
int main()
{
   printf("Shared Code\n");

   for (int i = 0; i < 5; i++)
   {
      int pid = fork();
      if (!pid)
      {
       return printf("Worker %d\n", i+1);
      }
   }

   return 0;
} 
```

编译并运行这段代码会得到如下结果:

```
Shared
Worker 1
Worker 2
Worker 3
Worker 4
Worker 5 
```

操作系统在幕后做的是，当我们调用 [fork()](http://man7.org/linux/man-pages/man2/fork.2.htm) 时，它**将**整个进程状态复制到一个具有新 PID 的新状态。工作者进程中的返回值总是`0`，所以我们有一种方法来查明**代码的剩余部分**是否在分叉进程或主进程中运行。(*感谢@小狐狸 comment🧡* )

重要的一点是分叉的进程**从调用`fork()`的地方继续**。不是从头开始，所以`Shared`只打印一次。

在 Node.js 中运行类似的代码:

```
 const { fork, isMaster } = require('cluster')

console.log('Shared')

if (isMaster) {
  // Fork workers.
  for (let i = 0; i < 5; i++) {
    fork();
  }
} else {
  // Worker
  console.log(`Worker`);
} 
```

输出惊人地不同:

```
Shared
Shared
Worker
Shared
Worker
Shared
Worker
Shared
Worker
Shared
Worker 
```

关键是每次工人分叉时，它都从一个新的 V8 实例开始。这不是一个顾名思义的行为。Node.js 中的 Fork 实际上是在执行 exec/spawn，这导致每次都运行共享代码。

## 还好。所以让我们把`console.log('Shared')`移到`if (isMaster)` :P

好吧。是的。你是对的。这就是解决办法。但是就这个例子来说！

在一个需要集群的真实应用程序中，我们不会立即分叉工人。我们可能想要建立我们的 web 框架，解析 CLI 参数，并需要一些库和文件。所有这些步骤都必须在**每个**工作器上重复，这可能会引入大量不必要的开销。

## 最终解决

现在我们知道了`cluster.fork`到底在做什么，我们可以将我们的工人逻辑分割到一个单独的`worker.js`文件中，并将`exec`的默认值`process.argv[1]`更改为`worker.js`:)这可以通过在主进程上调用 [`cluster.setupMaster()`](https://nodejs.org/api/cluster.html#cluster_cluster_setupmaster_settings) 来实现。