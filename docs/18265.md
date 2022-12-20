# 通过 Redis 和服务器发送的事件向用户输出实时流程

> 原文：<https://dev.to/jbutz/live-process-output-to-the-user-with-redis-and-server-sent-events-4lb2>

在最近的一个项目中，我需要从服务器上的一个进程中收集输出，并在前端将其输出给用户。理想情况下，这将几乎实时发生。

我需要从中收集输出的进程是一个第三方库，它将所有内容输出到`STDOUT`或`STDERR`。对我来说，最简单的方法是创建一个模块来运行这个库，然后将这个模块作为一个子进程分支，然后通过管道将输出传递给一个流处理程序。除了向用户提供输出，我还需要收集所有的输出以保存到 AWS S3，所以在分叉流程后，我为子流程的`STDOUT`和`STDERR`附加了一个`data`事件监听器，然后将所有的输出写入[文件写流](https://nodejs.org/dist/latest-v10.x/docs/api/fs.html#fs_fs_createwritestream_path_options)。

```
const fs = require('fs');
// ...
const outputFileStream = fs.createWriteStream(tmpFilePath);
// ...
const forkedProcess = fork('path/to/my/module.js', {
    stdio: ['pipe', 'pipe', 'pipe', 'ipc'],
});
// ...
forkedProcess.stdout.on('data', (data) => {
    outputFileStream.write(data.toString());
}); 
```

Enter fullscreen mode Exit fullscreen mode

一旦我有了这个工作，我需要得到输出到前端。鉴于我不想从输出文件中读取，我决定使用 [Redis](https://redis.io) 。在这一点上，我还没怎么用过 Redis 的不同数据类型，但事实证明,[列表](https://redis.io/topics/data-types#lists)类型很适合我。当我收到数据时，我开始调用 [`LPUSH`](https://redis.io/commands/lpush) 将它加载到 Redis 中。

```
const fs = require('fs');
const redis = require('redis').createClient();
// ...
const outputFileStream = fs.createWriteStream(tmpFilePath);
// ...
forkedProcess.stdout.on('data', (data) => {
    outputFileStream.write(data.toString());
    redis.lpush(redisKey, data.toString());
}); 
```

Enter fullscreen mode Exit fullscreen mode

我读过一些关于[服务器发送事件](https://developer.mozilla.org/en-US/docs/Web/API/Server-sent_events/Using_server-sent_events) (SSEs)和[用它们](https://dev.to/2018/08/server-sent-events-with-node)玩的东西，但是我从来没有机会在生产中使用它们。因为我只需要信息单向流动，所以我认为这是完美的解决方案。一旦你有了预期的数据格式，就很容易在 [Express](http://expressjs.com) 中从头开始实现。对我来说，棘手的部分是确保`Last-Event-ID`值在断开连接的情况下在正确的位置重新启动。

```
async function sseHandler(req, res, next) {
    // I was handling normal text responses and SSE responses with the same endpoint
    // this ensures SSE only starts if that is what the request wants
    if (req.header('accept').indexOf('text/event-stream') >= 0) {
        // You need to send an initial response but keep the connection alive
        res.writeHead(200, {
            'Content-Type': 'text/event-stream',
            'Cache-Control': 'no-cache',
            Connection: 'keep-alive',
        });

        // I was using a polyfill that had specific requirements for IE.
        // It requires 2kB of padding for IE
        res.write(`:${Array(2049).join('  ')}\n`);

        // There is an explanation of the flushes later
        res.flush();

        const intervalId = setInterval(() => {
            // Load redis info
            // ...

            res.write('id: LAST_REDIS_LIST_INDEX');
            res.write('data: ....');
            res.write('data: JSON_OBJECT_WITH_DATA');
            res.write('data: ....\n\n');
            res.flush();

        }, 500);

        // When the connection closes stop the interval
        res.on('close', () => {
            clearInterval(intervalId);
        });
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

我从每秒发送随机数据开始，只是为了确保 SSEs 的基础工作正常，然后我返回并添加了从 Redis 提取数据的逻辑。真正的应用程序还有一个心跳事件，它每 15 秒发送一次，以确保 polyfill 保持连接打开。我发回一个带有两个属性的 JSON 字符串。第一个是布尔标志，指示连接是否应该保持打开；第二个是字符串数组。数组中的每一项都是后台进程的单行输出。我包含了布尔标志，因为如果我从服务器端关闭连接，客户端会保持
重新打开连接。这允许我指示客户端在没有新信息通过时关闭连接。

如果你看上面的片段，你会看到几行。我花了很长时间才记住这一点，但是如果您为您的 Express 应用程序启用了压缩中间件，您可能会需要它们。在我的例子中，使用了 [`compression`](https://github.com/expressjs/compression) 包，所以添加了`res.flush()`。使用压缩包，数据被收集在一个缓冲区中，被压缩，然后一次释放(在大多数情况下)。因此，我的 SSE 的输出被捕获在缓冲区中，直到连接结束才被释放，然后它会被立即释放。因此，如果您正在使用 SSEs，并且您的所有数据都是一次到达，而不是像它那样成块到达，那么应该继续在您的应用程序中查找压缩和缓存库。

这就是一般解决方案的全部内容。