# 即时处理大型 HTTP 响应的 Elixir 流

> 原文：<https://dev.to/alvisesus/elixir-streams-to-process-large-http-responses-on-the-fly-4i41>

你对仙丹和凤凰有热情吗？订阅 Poeticoding 时事通讯，加入快乐的普通读者，通过电子邮件接收新帖子。

*   第 1 部分——本文
*   第 2 部分-[Elixir 流和大型 HTTP 响应:处理文本](https://www.poeticoding.com/elixir-stream-and-large-http-responses-processing-text/)

这是一篇由两部分组成的文章的第一部分，在这篇文章中，我们看到了一种处理异步 HTTP 响应的简便方法。

*   在第一部分中，我们将看到如何从一个大型 HTTP 响应中生成一个药剂流。
*   在第二部分中，我们将扩展实现，这样我们就可以下载一个大的文本文件，动态地处理行。

# 首当其冲……为什么？

当我们可以简单地使用类似于`HTTPoison.get`的东西来接收带有我们需要的数据的响应时，我们为什么要用流来处理 HTTP 响应呢？

[我们已经看到，默认情况下，HTTPoison 如何将整个 HTTP 响应保存到内存中](https://www.poeticoding.com/download-large-files-with-httpoison-async-requests)。我们还看到，这可以通过使用[异步请求](https://www.poeticoding.com/download-large-files-with-httpoison-async-requests/#async-requests)来避免，但是这迫使使用一切来处理低级别的 HTTPoison 异步流程消息。

能写出这样的东西不是很好吗？

```
HTTPStream.get("https://.../large_file.csv", :line)
|> Stream.filter ...
|> Stream.map ...
|> Enum.take(10) 
```

只需几行代码和一个美妙的管道语法就可以动态处理一个大型文本文件。但是我们也可以使用流来下载和压缩二进制文件，就像这样

```
HTTPStream.get("https://.../large_image.tiff")
|> StreamGzip.gzip
|> Stream.into(File.stream!("image.tiff.gz"))
|> Stream.run 
```

这种方法有很多优点:

*   我们可以利用 Elixir Streams 轻松创建漂亮的管道，获得代码的清晰性和可重用性。
*   一个大的 HTTP 响应被分成几个块，以避免引起内存问题
*   我们可以即时处理任何维度的文件。我们甚至可以处理一个大型 CSV 文件的前几百行，而不需要下载全部内容。

事实证明，由于 native Elixir Stream 的功能，创建我们的 HTTP 流并不困难。

# 第一个例子——一个大的图像文件

在开始编码我们实现之前，我们需要找到一个简单的例子来玩。我们可以从[httposin 异步请求文章](https://www.poeticoding.com/download-large-files-with-httpoison-async-requests/)的例子开始，在那里我们下载了一个大的图像。但是这一次的目标是仅仅使用仙丹流来实现。

[![The Whirlpool Galaxy](img/d557a59ab6687d345fcb737bc3c9ef6c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--yAA1wskW--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://1o6jjh24p1t92lcqci49qg8a-wpengine.netdna-ssl.com/wp-content/uploads/2019/03/heic0506a.jpg)

原始图像是一个 204.9Mb 的 TIFF 文件，这足以对我们的实现进行第一次测试。我们要用的网址是:[https://www . space telescope . org/static/archivimg/original/heic 0506 a . TIF](https://www.spacetelescope.org/static/archivimg/original/heic0506a.tif)

# 实现

## Stream.resource/3

有了[httposin Async Request](https://www.poeticoding.com/download-large-files-with-httpoison-async-requests/),我们有了一个很好的起点:HTTP 响应被分成块，一个接一个地发送到进程的邮箱，而不是用内存中的结构获得整个响应。

[`Stream.resource/3`](https://hexdocs.pm/elixir/Stream.html#resource/3) 正是我们需要包装的 HTTPoison。

```
Stream.resource(
  start_fun,
  next_fun,
  end_fun
) 
```

## 开始 _ 好玩

我们现在定义一个名为`HTTPStream`的模块和一个函数`get(url)`，在其中构建我们的流并返回。传递给`Stream.resource`的第一个函数是`start_fun`，我们在其中描述了如何启动枚举，在我们的例子中是发出一个异步 HTTP 请求。

```
defmodule HTTPStream do

  def get(url) do
    Stream.resource(

      #start_fun
      fn -> 
        HTTPoison.get!(
           url,  %{}, 
           [stream_to: self(), async: :once]
        ) 
      end,

     next_fun,
     end_fun
    )
  end
end 
```

当传递选项`[stream_to: self(), async: once]`时，`HTTPoison.get!`立即返回一个`%HTTPoison.AsyncResponse{id: #Reference<...>}`结构，然后该结构被传递给`next_fun`。

[记住流是懒惰的](https://www.poeticoding.com/processing-large-csv-files-with-elixir-streams#lazy)，传递给`Stream.resource/3`的函数不会立即运行。通过这种方式[我们可以构建一个流管道](https://www.poeticoding.com/processing-large-csv-files-with-elixir-streams#lazy)，只有当一个函数试图枚举它时，它才会发出 HTTP 请求。

# next _ fun–异步状态

在第二个函数`next_fun`中，我们接收并处理来自异步 HTTP 响应的数据。我们现在只编写第一部分来处理状态代码消息`%HTTPoison.AsyncStatus{}`。

```
# next_fun
fn %HTTPoison.AsyncResponse{id: id}=resp ->
  receive do
    %HTTPoison.AsyncStatus{id: ^id, code: code}->
      IO.inspect(code, label: "Status code: ")
      {:halt, resp}
  after
    5_000 -> raise "receive timeout"
  end
end 
```

我们提供的`next_fun`期望由`start_fun`返回的异步响应`%HTTPoison.AsyncResponse{}`结构。我们使用它的 id 有选择地只选择响应的消息。目前，我们只实现了状态代码部分，所以我们可以直接测试它是否工作。

*   `next_fun`必须返回一个 tuple，这可以是当我们想把元素传递给管道时的`{[...], resp}`，或者是当我们想停止枚举时的`{:halt, resp}`。
*   `%HTTPoison.AsyncStatus{}`是我们收到的第一条消息，所以我们打印代码并返回`{:halt, resp}`，因为我们只想测试这一部分并停止枚举。
*   当流暂停时，调用`end_fun`。在这个函数中，我们清理关闭连接的资源。在我们的例子中，我们停止异步响应调用`:hackney.stop_async(resp.id).`

是时候看看 iex 上的第一部分是如何工作的了

```
$ iex -S mix
Erlang/OTP 21 ...
Interactive Elixir (1.8.0)

iex> image_url = "https://www.spacetelescope.org/static/archivimg/original/heic0506a.tif"

iex> image_url
...> |> HTTPStream.get()
...> |> Stream.run
Status code: : 200
:ok 
```

太棒了，成功了！🎉它获取并打印状态代码，然后停止，而不下载整个文件。

## 异步者，AsyncChunk 和 AsyncEnd

```
#next_fun
fn %HTTPoison.AsyncResponse{id: id}=resp->
  receive do
    %HTTPoison.AsyncStatus{id: ^id, code: code}->
      IO.inspect(code, label: "STATUS: ")
      HTTPoison.stream_next(resp)
      {[], resp}
    %HTTPoison.AsyncHeaders{id: ^id, headers: headers}->
      IO.inspect(headers, label: "HEADERS: ")
      HTTPoison.stream_next(resp)
      {[], resp}
    %HTTPoison.AsyncChunk{id: ^id, chunk: chunk}->
      HTTPoison.stream_next(resp)
      {[chunk], resp}
    %HTTPoison.AsyncEnd{id: ^id}->
      {:halt, resp}
  end
end 
```

**`%HTTPoison.AsyncStatus{}`**
我们已经看到了如何处理这个消息。我们现在使用`HTTPoison.stream_next(resp)`请求下一条消息，而不是暂停流，因为在这个阶段我们没有任何数据要发出，所以我们返回一个`{[], resp}`元组。`resp`是累加器，然后在下一次调用时传递给`next_fun`。

**`%HTTPoison.AsyncHeaders{}`**
与我们对状态代码所做的类似，我们打印标题，我们让 HTTPoison 将下一条[消息发送到我们的进程邮箱](https://www.poeticoding.com/hey-process-there-is-a-message-for-you/#sending-message)，我们返回一个带有空列表的元组，因为没有我们需要发出的数据。

**`%HTTPoison.AsyncChunk{}`**
这些是包含实际响应的消息体，分成块。每个小块都有一条消息。像以前一样，我们请求下一条消息，但是这一次我们发出返回`{[chunk], resp}`的块。

**`%HTTPoison.AsyncEnd{}`**
我们收到这条消息时到达了 HTTP 响应的终点。现在是停止枚举的时候了。

# 第一次乘坐的时间🏎

```
iex> HTTPStream.get(large_tiff_image_url)
#Function<55.131689479/2 in Stream.resource/3> 
```

首先，我们看到`HTTPStream.get(url)`返回一个流，此时没有请求运行。

让我们也启动 Erlang observer 来监控分配的内存。

```
iex> :observer.start

iex> large_tiff_image_url \
...> HTTPStream.get() \
...> |> Stream.into(File.stream!("image.tif"))
...> |> Stream.run

STATUS: : 200
HEADERS: : [
  {"Server", "nginx/1.13.7"},
  {"Content-Type", "image/tiff"},
  {"Content-Length", "214777324"},
  ...
]
:ok 
```

这次我们只对写入文件感兴趣，所以我们在最后使用`Stream.run`来运行管道。所有发出的块都被`Stream.into`捕获并写入“image.tif”。

我们还在观察器上看到，下载流分配的内存很少。

# 压缩？只有一行代码

如果我们想在下载的时候压缩文件呢？我们必须改变整个实现吗？不要！

```
large_tiff_image_url
|> HTTPStream.get()
|> StreamGzip.gzip()
|> Stream.into(File.stream!("image.tif.gz"))
|> Stream.run 
```

由于 streams 的高度可组合性，我们只需要在管道中添加一个新的阶段。使用 [StreamGzip 库](https://hex.pm/packages/stream_gzip)，我们压缩来自`HTTPStream.get`的块并保存到“image.tif.gz”。

# 接下来是什么？文字处理！

我们已经看到了用酏剂流包装 HTTPoison 的方法如何带来许多好处。在这一部分，我们刚刚看到了如何下载一个二进制文件，压缩它，并保存在本地。

在下一部分中，我们将看到如何重构我们的实现来处理文本行，而不仅仅是块，这样我们就可以动态地处理巨大的文本文件，避免影响内存。

你对仙丹和凤凰有热情吗？订阅 Poeticoding 时事通讯，加入快乐的普通读者，通过电子邮件接收新帖子。