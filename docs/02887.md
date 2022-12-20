# 写一个 Postgres 代理。第一天。熟悉 API。

> 原文：<https://dev.to/siscia_/write-a-postgres-proxy-day-1-getting-familiar-with-the-api-3006>

# 写一个 Postgres 代理。第一天。

[redisl，Redis 的 SQL 类固醇。](https://redisql.com)是一个非常快速的内存 SQL 引擎。其主要特点是:

1.  速度高达每秒 130，000 次插入
2.  熟悉，它支持标准 SQL，没有奇怪的方言
3.  简单，它很容易操作和使用任何语言的绑定。

github 上的代码: [RedBeardLab/rediSQL](https://github.com/RedBeardLab/rediSQL)

* * *

在这一系列文章中，我们正在编写一个 postgres 代理，它接受使用 postgres (PG)协议建立的连接，并将它们转发给 [RediSQL](https://redisql.com) 。

项目的动机和介绍在这里。

我对这个项目的希望是从这项工作中提取知识，并帮助其他对探索 PG 协议感兴趣的人。

## [T1】简介](#intro)

这篇文章是关于这个项目的第一天，所以主要是介绍在这项工作中使用的参考资料和一点点代码。

在这一天，我们很快到达能够从`psql`(PG 的 CLI 工具)接收查询的阶段。

我们将在文章开始展示最有用的 PG 引用和我们使用的`asyncio`模块的 Python 引用。

然后我们将快速浏览我最后写的几行代码。

最后一部分将探讨我在这一天犯的错误，小错误，但无论如何，这是一个很大的时间下沉。

## 参考文献

在开始这项工作之前，我问自己是否应该为 Postgres 或 MySQL 实现这个代理。

为了做出选择，我查阅了两个项目的文档，它们都很不错。然而，PG 的文档看起来更容易理解，也更线性，所以我决定使用 PG。

该项目的主要文件是 PG 文件的第 50 章。

以下几节尤其令人感兴趣:

*   [消息流](https://www.postgresql.org/docs/9.5/protocol-flow.html)部分解释了 PG 和客户端之间的消息流。它有助于理解我们应该从客户端期待什么消息，以及我们作为服务器需要发送什么消息。
*   [消息数据类型](https://www.postgresql.org/docs/9.5/protocol-message-types.html)部分简单解释了如何阅读和解释“消息格式”部分。
*   [消息格式](https://www.postgresql.org/docs/9.5/protocol-message-formats.html)部分详细介绍并列举每种消息的格式。作为一个例子，我们发现通常每个消息都以一个字母开始，该字母标识消息的类型(如`R`用于认证相关的消息，或者查询以`Q`开始)，然后 4 个字节(Int32)表示整个消息的长度，最后是消息本身的正文。

## Python“ASYNCIO”

虽然我想把这个项目合并到主 [RediSQL](http://redisql.com) rust 代码库中，但我坚信用 Python 启动这个项目是个好主意。我将获得在 Rust 中成功重写软件所必需的知识，同时已经面对了在一种允许非常快速迭代的语言中的大多数实现困难。此外，将 Python 代码库合并到 RediSQL rust 代码库是不可能的，所以我将被迫重写它。

虽然我并不追求性能，但我仍然选择使用`asyncio`，主要是因为我已经很长时间没有在 Python 中做什么大的工作了，我想了解一下可用的工具。此外，我希望它会更接近我会发现与托基奥在铁锈，但在我看来，这两个模型不是很相似。

在 Python 方面，我一直在为 `[asyncio](https://docs.python.org/3.5/library/asyncio-protocol.html#transports-and-protocols-callback-based-api)`引用基于[回调的 API。](https://docs.python.org/3.5/library/asyncio-protocol.html#transports-and-protocols-callback-based-api)

API 非常简单，您只需子类化`asyncio.Protocol`类并实现三个回调:

1.  `connection_made`用于创建到服务器的新连接时。
2.  当一个新的数据包到达服务器时。
3.  当我们与客户失去联系时。

正如你所想象的，所有的逻辑都在`data_received`回调中，它将比标准的 web 服务器更复杂。事实上，HTTP 是一个无状态协议，如果协议是无状态的，那么一切都简单了，每个请求都不依赖于前一个。

PG 协议是有状态的，这意味着我们需要存储和使用来自以前消息的信息。例如，在发送查询之前，客户端需要发送握手并进行身份验证。这意味着我们的服务器至少有两种状态，一种是“初始”状态，在这种状态下每个连接开始，另一种是“就绪”状态，在这种状态下，只有在完成握手和身份验证后，连接才会结束。

## 代码

最后[这里的代码这第一天的代码。](https://github.com/RedBeardLab/pg-redis-proxy/blob/fe3408b02f7f1cd12061cae75de3a309a5c7d935/main.py)代码大部分是从 Python 文档复制来的样板文件，但已经足以接受来自`psql`的连接并接收第一个查询。

为了测试我们的进展，我们启动了 Python 服务器，同时我们执行了`psql`,给出了一个文件作为输入[,其中包含一些要执行的 SQL 语句](https://github.com/RedBeardLab/pg-redis-proxy/blob/70d1c2de917648900289a36e142720237aed29b2/goal.sql)。

`psql -f goal.sql -h localhost -p 8888`

### 一天的工作流程

除了样板代码，代码中有趣的部分是标识消息的幻数的定义:

```
SSLRequestCode = b'\x04\xd2\x16\x2f' # == hex(80877103)
StartupMessageCode = b'\x00\x03\x00\x00' # == hex(196608)

NoSSL = b'\x4E' # == 'N'

AuthenticationOk = b'\x52\x00\x00\x00\x08\x00\x00\x00\x00'
AuthenticationCleartextPassword = b'\x52\x00\x00\x00\x08\x00\x00\x00\x03'

ReadyForQuery = b'\x5A\x00\x00\x00\x05\x49' # == Z0005I , the last I stand for Idle 
```

以及回复客户端的逻辑:

```
 def _reply(self, data):
        if self.state == "initial" and data[4:8] == SSLRequestCode:
            self.transport.write(NoSSL)
        elif self.state == "initial" and data[4:8] == StartupMessageCode:
            # we don't require a password
            self.transport.write(AuthenticationOk)
            # good to go for the first query!
            self.transport.write(ReadyForQuery)
        return 
```

让我们探索一下我们是如何得到这几行代码的。

## 我的发现过程

快速浏览文档，看起来第一个消息是`StartupMessage`，然而，`psql`发送的第一个消息是`SSLRequest`消息，这花了很长时间才弄清楚。

`SSLRequest`消息被识别是因为它包含了幻数 80877103，我们在 python 代码中将其编码为`b'\x04\xd2\x16\x2f'`

因为我们还不支持 SSL，所以我们简单地用`N`(编码为`b'\x4E'`)响应`SSLRequest`，让客户端知道我们不打算使用 SSL。此时，客户端可以放弃连接，或者决定接受一个不加密的连接，并以纯文本的形式发送`StartupMessage`。

对于`StartupMessage`还有一个幻数(196608)，我们将其编码为`b'\x00\x03\x00\x00'`。

除了这个神奇的数字之外，`StartupMessage`还包含了开始连接的用户、用户试图连接的数据库以及其他信息。目前我们忽略所有这些信息。

在`StartupMessage`之后，服务器要求认证，在我们的例子中，我们并不关心认证，我们只是发送`AuthenticationOk`消息。

下一步有点棘手。

我们只是向客户端发送一条消息，即`AuthenticationOk`消息，所以我希望客户端向服务器发回一些信息。

不对！

现在，服务器需要主动告诉客户机可以开始发送查询了。我们需要向客户端一个接一个地发送两条消息。

事实上，您可以在代码中看到，我们立即发送了`ReadyForQuery`消息。

至此，我们今天的时间结束了，但是我们可以从日志中清楚地看到，服务器收到的下一条消息是对我们的文件的第一次查询！

成功！

## 这一天犯的错误

在这个编码部分，我浪费了很多时间，因为我没有足够仔细地阅读文档。

事实上，我期待第一条消息是`StartupMessage`，而不是`SSLRequest`。我花了很多时间试图将`StartupMessage`放入`SSLRequest`，也许我用错误的字节顺序阅读了信息？也许是协议层的“垃圾”？

不要。我只是读错了信息。

另一个时间陷阱是我试图使用十进制而不是十六进制编码读取 ASCII 表中的错误列。所有的消息都以字母开头，在我们的例子中，我们需要用`N`拒绝 SSL，用`R`拒绝`AuthenticationOk`消息，最后用`Z`拒绝`ReadyForQuery`消息。例如，`N`是十进制的 78，十六进制的 4E。我试图将`N`编码成`b'\x78'`而不是`b'\x4E'`。

## 结论

我希望你喜欢这个职位。

我会继续在这个博客上发表关于这个话题的文章，所以如果你感兴趣的话，请在 twitter 上关注我或者订阅下面的邮件列表。

本系列所有文章:[编写 Postgres 代理。](https://redbeardlab.com/category/pg-redis-proxy/)