# 使用集群模块 1 利用节点中的机器能力

> 原文：<https://dev.to/ajahso4/utilizing-machine-capability-in-nodejs-using-the-cluster-module-1-3cpj>

人类自然希望充分发挥自己的潜力。我们总是相信我们有未开发的潜力，坚信我们的基因和养育已经足够让我们过上低于标准的生活。在使用 NodeJS 构建应用程序时，我们使用的机器具有不同的功能，要记住机器拥有的内核数量以及单个内核的能力。

NodeJS 的单个实例不会利用所有的机器功能，因为它是单线程的，并且当它具有可用于承担客户端-服务器通信的休眠服务器资源时，您不能让您的应用程序遭受过载。有鉴于此，NodeJS 提供了**集群**模块，该模块有助于派生 NodeJS 应用程序的多个进程。对于 web 服务器，集群模块有助于旋转多个与其父进程共享一个端口的子进程。Ehhh，不像端口可以被 web 服务器共享。从技术上讲，父模块接收请求并有效地将其分发到客户端进程(就像伟大的哈维和伊涅斯塔在他们的全盛时期为梅西所做的那样)。集群模块对于网络应用程序特别有用，但它也可以在其他进程中发挥作用，在这些进程中，我们希望在多个工作人员之间执行 CPU 密集型任务。

根据 NodeJS 官方文档，子进程(工作进程)是使用 child_process.fork()方法产生的，因此它们可以通过 IPC(进程间通信)与父进程通信，并来回传递服务器句柄。

在将传入连接分配给子进程时，集群模块采用了两种方法，它们与平台相关。第一种(除 Windows 之外的所有平台都默认使用)是循环法(无优先级的轮流分配)，其中主进程侦听一个端口，接受新的连接，并以循环方式将它们分配给工作进程，使用一些内置的智能来避免工作进程过载。第二种方法是主进程创建监听套接字，并将其发送给感兴趣的工人。然后，工人直接接受传入的连接。

这些工作进程都是独立的进程，这意味着它们可以根据程序的需要被杀死或再生，而不会影响其他工作进程。只要还有精力充沛的工人，主进程就仍然会接受连接。NodeJS 不负责管理可用工作线程的数量，因此，应用程序负责根据自己的需要管理工作线程池。

现在，让我们深入了解集群模块的一个用例。还记得我们说过集群模块在网络应用中大放异彩吗？我们将使用一个网络应用程序作为例子。我们将支撑一个 http 服务器，并根据我们的操作机器拥有的 CPU 核心的数量将请求分配给子进程。

```
const cluster = require('cluster');
const http = require('http');
const number_of_cpus = require('os').cpus().length;

if(cluster.isMaster){
    console.log(`Master with id ${process.pid} is running`);

    // setting up the workers
    for (let i = 0; i < number_of_cpus; i++){
        cluster.fork();
    }

    cluster.on('exit',(worker, code, signal) => {
        console.log(` Worker with process id ${worker.process.pid} died`);
    });
}
else{
    http.createServer((req,res) => {
        res.writeHead(200);
        res.end(`Hello world from ${process.pid}`)
    }).listen(80);
    console.log(`Worker with ${process.pid} started`)

} 
```

由于 NodeJS 是事件驱动的，主进程和工作进程都监听事件并相应地采取行动(前提是代码中有处理事件的规定。)我们将在关于这个主题的后续文章中讨论与集群模块相关联的两端(工作者和主)的事件、方法和属性。谢谢你的时间。