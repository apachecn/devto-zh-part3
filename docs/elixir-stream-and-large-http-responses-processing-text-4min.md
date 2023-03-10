# Elixir 流和大型 HTTP 响应:处理文本

> 原文：<https://dev.to/alvisesus/elixir-stream-and-large-http-responses-processing-text-4min>

你对仙丹和凤凰有热情吗？订阅 Poeticoding 时事通讯，加入快乐的普通读者，通过电子邮件接收新帖子。

*   第 1 部分—[即时处理大型 HTTP 响应的 Elixir 流](https://www.poeticoding.com/elixir-streams-to-process-large-http-responses-on-the-fly/)
*   第 2 部分——本文

你可以在[poeticoding/http stream _ articles](https://github.com/poeticoding/httpstream_articles)GitHub repo 上找到这篇文章和上一篇文章的代码。您在这个 repo 中找到的代码并不意味着用于生产，只是这些文章实验的一部分。

[在上一篇文章](https://www.poeticoding.com/elixir-streams-to-process-large-http-responses-on-the-fly/)中，我们将 [HTTP 异步响应](https://www.poeticoding.com/download-large-files-with-httpoison-async-requests/)和[使用 Elixir Streams 处理大文件](https://www.poeticoding.com/processing-large-csv-files-with-elixir-streams/)的概念放在一起，以便能够使用我们的 [HTTPStream 模块](https://github.com/poeticoding/httpstream_articles)轻松下载和保存大文件。

我们围绕 HTTPoison 构建的流能够发出 HTTP 分块响应的块。通过这种方式，我们避免了将整个响应保存到内存中所带来的巨大内存影响。

[在上一篇文章](https://www.poeticoding.com/elixir-streams-to-process-large-http-responses-on-the-fly/#first-ride)的最后一个例子中，每个块都被传输到管道的函数中，保存到我们的本地文件中，然后进行垃圾收集。然后，很容易添加压缩，只需在管道中包含`StreamGzip.gzip`函数。

# 文字回应

以前，我们将响应视为二进制文件，将压缩委托给`StreamGzip`库，并将结果保存到一个文件中。

在这一部分，我们要处理一个大的文本文件的行。为了让它更有趣和更现实，我们将从代码挑战的第一天获得一些灵感。还有[joséValim](https://www.twitch.tv/josevalim)制作的一些很酷的视频，他在视频中使用了 Elixir 来解决代码出现的挑战。

我已经在这个链接
生成并上传了一个 **125Mb** 的文本文件

```
https://poeticoding-data.sfo2.digitaloceanspaces.com/httpstream/numbers.txt 
```

用**3000 万行**。每行有一个从 **-1000** 到 **1000**
的随机整数

```
767
138
-701
98
... 
```

我们希望:

*   **1。**在下载的同时，一行一行地动态处理这个文本文件。
*   **2。** [就像我们在第一部分](https://www.poeticoding.com/elixir-streams-to-process-large-http-responses-on-the-fly)中所做的那样，我们希望避免大的内存峰值(我们无法将完整的响应加载到内存中)
*   **3。**最后但同样重要的是，如果我们只想处理前 30 行文本，我们只想下载所需的前几个块。

为了做一些测试，你还可以找到一个更小的版本 **4.5Mb** 和**100 万行**T4】

```
https://poeticoding-data.sfo2.digitaloceanspaces.com/httpstream/numbers_small.txt 
```

这与您在[poeticoding/http stream _ articles](https://github.com/poeticoding/httpstream_articles)repo 中找到的相同。

# 我们要建造什么

为了逐行处理 HTTP 响应，我们需要类似于
的东西

```
File.stream! "numbers.txt" #, [], :line 
```

它创建一个打开文件的流，默认情况下发出每一行。我们想对 HTTP 异步响应做一些类似的事情。

我们不想改变当前的 HTTPStream 模块实现，而是想充分利用 Elixir 流的可组合性，编写一个函数添加到管道中，将块转换成行。

# 大块去纹

好的，但是为什么我们不能使用我们已经为二进制文件完成的实现呢？

前一个实现的问题是，我们构建的流在不区分行的情况下发出块。我们需要一种能发出线条而不是块状的灵药流。

不幸的是，这并不像看起来那么容易:将块分成行是不够的。这只有在一个简单而具体的情况下才成立:

```
["767\n138\n","-701\n98\n","-504\n22"] 
```

其中列表的每个元素是一个块，每个块以一个新行结束。在这个简单的例子中，我们只需要分割每个块，过滤掉空字符串，输出结果

```
iex> String.split("767\n138\n")
["767","138",""] 
```

但问题是，我们不能保证所有的块都是这种形式。实际上，同一行的多个部分更有可能位于不同的块上

```
["767\n13","8\n-701\n98\n-","504\n22"] 
```

您可以看到，在这种情况下，前两个块没有以换行符`\n`结束，第二个块从上一个块中开始的行的最后一部分开始。

# 转换

我们现在要实现一个函数 [`HTTPStream.lines(chunks_enum)`](https://github.com/poeticoding/httpstream_articles/blob/36bc2167b7024a990b04b28f9447fb9bc0e0310e/lib/http_stream.ex#L58) ，它将一串组块作为输入，并返回一串行。

[![Chunks to Lines](img/0db0f1b7fabf3f14b17a7afeccba569e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--R1M9KfAp--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.poeticoding.com/wp-content/uploads/2019/04/chunks-to-lines-function.png)

然后，我们将使用这个函数作为管道的一部分，方式如下:

```
HTTPStream.get("https://.../numbers.txt")
|> HTTPStream.lines()
|> Stream.map(fn line -> ... end)
... 
```

我们不想使用 [`Enum.reduce`](https://hexdocs.pm/elixir/Enum.html#reduce/3) ，因为这个函数是贪婪的，它会在内存中保存所有的行。相反，我们可以用 [`Stream.transform`](https://hexdocs.pm/elixir/Stream.html#transform/3) 来表示懒惰

```
Stream.transform(enum, initial_accumulator, fn element, acc ->
...
{list_of_elements_to_emit, new_accumulator}
end) 
```

返回的元组的第一个元素是我们将要发出的元素列表，在我们的例子中是行。

[![accumulating final part of the chunk and using it when processing next chunk](img/e9fda3fe0cfe721f6215342ff63b1410.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--v3wFj8Hy--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.poeticoding.com/wp-content/uploads/2019/04/lines_and_accumulator.png)

我们从图中看到，累加器`acc`是第一个块的最后一部分，并被加到第二个块的前面。

我们需要一个名为 [`next_lines`](https://github.com/poeticoding/httpstream_articles/blob/36bc2167b7024a990b04b28f9447fb9bc0e0310e/lib/http_stream.ex#L77) 的函数，它将一个块分割成单独的行，并返回一个包含两个元素的元组:我们想要发出的行和块的最后一部分。

```
Stream.transform(chunks_enum, "", fn chunk, prev ->
  {lines, last_part} = next_lines(chunk, prev)
  {lines, last_part}
end) 
```

我们的初始累加器是一个空字符串。在处理第一个块时，这个空字符串将作为`prev`传递。

# 递归实现

我们现在需要编写 [`next_lines(chunk,prev)`](https://github.com/poeticoding/httpstream_articles/blob/36bc2167b7024a990b04b28f9447fb9bc0e0310e/lib/http_stream.ex#L78) 函数。我们可以使用递归来实现它，遍历每个 UTF-8 字符来寻找换行符。记住，为了表现得像`File.stream!`一样，我们需要保留新行`\n`。

```
# https://github.com/poeticoding/httpstream_articles/blob/36bc2167b7024a990b04b28f9447fb9bc0e0310e/lib/http_stream.ex#L78

def next_lines(chunk, prev), do: next_lines(chunk,prev,[])

def next_lines(<<"\n"::utf8, rest::binary>>, prev, lines) do
next_lines(rest,"",[prev <> "\n" | lines])
end

def next_lines(<<c::utf8, rest::binary>>, prev, lines) do
  next_lines(rest,<<prev::binary, c::utf8>>,lines)
end

def next_lines(<<>>, prev, lines), do: {Enum.reverse(lines), prev} 
```

好吧，这里发生了很多事情。让我们从头开始

*   **`next_lines(chunk,prev\\"")`**
    第一个从句只是一个帮手。我们通过一个`chunk`，和累加器`prev`。该函数调用`next_lines/3`，传递一个空的行列表作为第三个参数。

*   **`next_lines(<<"\n"::utf8, rest::binary>>, prev, lines)`**
    我们正在模式匹配一个 UTF-8 字符序列。只有当我们到达一个换行符时，这个函数才会被调用。然后我们递归地调用`next_lines`，传递我们需要处理的块的`rest`，将累加器设置为空字符串`""`，传递我们已经预先放置了累加行的行列表`prev`。

*   **`next_lines(<<c::utf8, rest::binary>>,prev,lines)`**
    由于每次`c`都是换行符，所以上面的子句是匹配的，在这个子句`c != "\n"`中，所以我们只需要把它追加到`prev`中，并递归调用`next_lines`遍历组块的`rest`。

*   **`next_lines(<<>>,prev,lines)`**
    `<<>>`是一个空二进制数，表示我们已经到达了组块的末尾。出于表演的原因，我们对台词进行了修改。
    `[prev <> "\n" | lines]`比`lines ++ [prev]`快，尤其是`lines`单子大的时候。当我们到达递归末尾时，我们需要反转行列表。

让我们在 iex 上试试这个功能

```
# ["767\n13","8\n-701\n98\n-","504\n22"]
iex> {lines, prev} = HTTPStream.next_lines "767\n13"
{["767\n"], "13"}
iex> {lines, prev} = HTTPStream.next_lines "8\n-701\n98\n-", prev
{["138\n", "-701\n", "98\n"], "-"}
iex> {lines, prev} = HTTPStream.next_lines "504\n22", prev
{["-504\n"], "22"}
iex> prev
"22" 
```

完美，正是我们需要的👍。我们遍历组块列表，将获得的`prev`传递给下一个调用。

# `HTTPStream.lines`

`next_lines`返回传递给`Stream.transform/3`的 reducer 函数中我们需要返回的同一个元组。然后我们可以用一种简洁的方式写`HTTPStream.lines/1`

```
def lines(chunks_enum) do
chunks_enum
|> Stream.transform("",&next_lines/2)
end 
```

让我们在 iex
上试试吧

```
iex> ["767\n13","8\n-701\n98\n-","504\n22"] \
...> |> HTTPStream.lines() \
...> |> Enum.each(&IO.inspect/1)
"767\n"
"138\n"
"-701\n"
"98\n"
"-504\n"
:ok 
```

嗯🤔 ...这里有问题。最后一行`"22"`不见了。

## 放出最后一行

之所以没有发出它，是因为它没有以换行符结束，而是作为一个累加器(`prev`)被卡住。我们必须在流结束时发出它，但是使用`Stream.transform/3`reducer 函数不知道流什么时候结束！(如果你知道有一种方法可以捕捉到流的结尾，请在评论中告诉我)

为了让`next_lines/2`知道流什么时候到达了末尾，我们可以使用的一个变通方法是在块流的末尾添加一个`:end`原子。`next_lines/2`than have to handle the case with a specific 子句

```
def next_lines(:end,prev), do: { [prev], ""} 
```

发出最后一条线。累加器被设置为空字符串，但此时它可以是任何值。

让我们在 iex
上再试一次

```
iex> ["767\n13","8\n-701\n98\n-","504\n22", :end] \
...> |> HTTPStream.lines() \
...> |> Enum.each(&IO.inspect/1)
"767\n"
"138\n"
"-701\n"
"98\n"
"-504\n"
"22"
:ok 
```

太好了，成功了！🎉

但是现在我们如何在 HTTP 分块响应流的末尾添加一个`:end`原子呢？

## 发出:end 在流传输的 HTTP 响应的末尾

> 如果你有其他的方法，请在下面的评论区分享！👩‍💻👨‍💻

我们需要对我们的 [`HTTPStream.get(url)`](https://github.com/poeticoding/httpstream_articles/blob/36bc2167b7024a990b04b28f9447fb9bc0e0310e/lib/http_stream.ex#L31) 函数做一个小而重要的改变。

```
# HTTPStream.get/1
def get(url) do
  Stream.resource(
    start_fun,

    # next_fun
    fn
      #first clause
%HTTPoison.AsyncResponse{id: id}=resp ->
    receive do
...
%HTTPoison.AsyncEnd{id: ^id}->
  # emitting :end
{[:end], {:end, resp}}
end

#second clause
    {:end, resp} -> 
    {:halt, resp}
end,

after_fun
  )
end 
```

*   **1。**当我们收到`%HTTPoison.AsyncEnd{}`消息时，我们知道我们已经到达了 HTTP 响应的末尾。我们不只是暂停流，而是发出`:end`并设置一个新的累加器`{:end, resp}`，其中`resp`是`%HTTPoison.AsyncResponse{}`结构。

*   **2。**发射`:end`后，再次调用`next_fun`。这次累加器是我们刚刚设置的那个，`{:end, resp}`，它的模式与我们的`next_fun`的第二个子句相匹配。

[![AsyncEnd, emits :end and :halt](img/9992edc13f883c4d9a81564939295d5f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s---dH7DZ-e--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.poeticoding.com/wp-content/uploads/2019/04/async_end__emits_end__halt.png)

我不喜欢这个变化的一点是，现在我们总是必须处理最后的`:end`，尤其是在将流保存到文件中的时候。

```
HTTPStream.get("https://.../large_image.tiff")
|> Stream.reject(&match?(:end,&1))
|> Stream.into(File.stream!("image.tiff.gz"))
|> Stream.run() 
```

第二行模式中的函数匹配每个块并过滤掉`:end`原子。

通过传递给 [`HTTPStream.get(url, emit_end)`的选项来启用和禁用最终的`:end`可能更好，就像你在 GitHub](https://github.com/poeticoding/httpstream_articles/blob/36bc2167b7024a990b04b28f9447fb9bc0e0310e/lib/http_stream.ex#L3) 上看到的版本。

## 求和 30M 行远程文件中的数字

让我们用我们实现的东西来处理一个 125MB 的远程文本文件，该文件包含 **30M 的**数字，每个数字由一个新行`\n`分隔。在动态处理这些行时，我们将这些数字相加得到总数。

```
"https://poeticoding-data.sfo2.digitaloceanspaces.com/httpstream/numbers.txt"
|> HTTPStream.get()
|> HTTPStream.lines()
|> Stream.map(fn line-> 
case Integer.parse(line) do
{num, _} -> num
:error -> 0
end
end)
|> Enum.sum()
|> IO.puts()

## OUTPUT
STATUS: : 200
HEADERS: : [
  {"Content-Length", "131732144"},
  {"Accept-Ranges", "bytes"},
  {"Content-Type", "text/plain"},
  ...
]

12468816 
```

太棒了，我们得到了结果: *12468816* ！🎉

使用 Erlang Observer 时，我有时会看到一些内存峰值(仍然低于 100Mb ),有时这条线几乎是平的。我认为这可能与大块有多大有关。

[![memory spike](img/cabcc44ad330e4b82be11272af8fee5c.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--eEovujo7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.poeticoding.com/wp-content/uploads/2019/04/erlang_observer_spike.png)

在 [GitHub repo 中，你可以找到一个`memory_test.exs`](https://github.com/poeticoding/httpstream_articles/commit/cb6953847f49345d86ccf48905dfe777624eff45) 脚本，用来查看不同块大小的`HTTPStream.line`内存分配。即使是一个 4.5Mb 的文件，如果我们夸大块的大小(像`2_000_000`)，我们会有一个巨大的内存峰值。用`2_000`线几乎是平的。

[![2mb vs 2kb chunk](img/57c306a3cba2741acd28876763ee740f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--1qi47CoR--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.poeticoding.com/wp-content/uploads/2019/04/2mb_vs_2kb__chunk.png)

如果能在 HTTPoison 选项中设置一个最大块大小就太好了，不幸的是我没有找到任何选项可以做到这一点。

## 串音

让我们看看 [`HTTPStream.lines(chunks)`](https://github.com/poeticoding/httpstream_articles/blob/master/lib/http_stream.ex#L63) 函数的另一种写法。在前面的实现中，我们使用了递归来遍历块中的每个字符并查找换行符。

如果我们不需要保留换行符，我们可以将 [`String.split/2`](https://hexdocs.pm/elixir/String.html#split/2) 与`Stream.transform/3`一起使用。

```
def lines(enum) do
  enum
  |> Stream.transform("",fn 
    :end, prev -> 
      {[prev],""}
    chunk, prev ->
      [last_line | lines] = 
        String.split(prev <> chunk,"\n")
        |> Enum.reverse()
      {Enum.reverse(lines),last_line}
  end)
end 
```

这个想法和我们之前做的差不多。我们将块分成行，列表的最后一个元素成为累加器，它连接到下一个块。

看看我们如何提取行列表的最后一项。

```
lines = String.split(prev <> chunk, "\n")
[last_line | rev_lines] = Enum.reverse(lines)
{ Enum.reverse(rev_lines), last_line } 
```

*   我们拆分串接的字符串`prev <> chunk`获得一个行列表。我们现在需要得到列表的最后一个元素。
*   我们反转列表，创建一个新元素列表。现在，`Enum.reverse(lines)`的头部是`lines`的最后一个元素。
*   `rev_lines`是我们想要发出的行的列表，但是顺序不对，所以我们发出`Enum.reverse(rev_lines)`并将`last_line`设置为下一个累加器。

[![split and extract last](img/3dad4f162f22c6e1683da33a2a7974ae.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--sA4aMG_2--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://www.poeticoding.com/wp-content/uploads/2019/04/rev_lines.png)

让我们看一个例子

```
iex> chunks = ["767\n138\n-701\n98\n-5", "04\n22\n375"]
iex> [chunk | remaining_chunks] = chunks
iex> chunk
"767\n138\n-701\n98\n-5"

iex> lines = String.split(chunk,"\n")
["767", "138", "-701", "98", "-5"]
iex> [last_line | rev_lines] = Enum.reverse(lines)
["-5", "98", "-701", "138", "767"]
iex> last_line
"-5"
iex> lines_to_emit = Enum.reverse(rev_lines)
["767", "138", "-701", "98"]

#let's process another chunk
iex> [next_chunk | remaining_chunks] = remaining_chunks
iex> next_chunk
"04\n22\n375"
# we need to prepend last_line
iex> chunk = last_line <> next_chunk
"-504\n22\n375"
iex> lines = String.split(chunk,"\n")
["-504", "22", "375"] 
```

事实证明，这个实现比前一个快得多。让我们看看一些基准测试

# 基准`HTTPStream.line`

如果您想在您的计算机上运行这个基准测试，您可以在[poeticoding/http stream _ articles](https://github.com/poeticoding/httpstream_articles)上找到所有内容。

让我们考虑由
创建的流

```
File.stream!("numbers_small.txt") 
```

默认情况下，它发出文件的行。我们想对比一下这个函数和`HTTPStream.line`的速度。

不使用远程文件，我们将使用一个更小的~4Mb 版本，`numbers_small.txt`你可以在 GitHub repo 上找到。

我们现在需要找到一种方法来模拟由`HTTPStream.get`产生的块流。

```
chunk_stream = File.stream!("numbers_small.txt",[],16_000) 
```

将块大小作为第三个参数传递给`File.stream!/3`，流(而不是行)将发出块(在本例中为 16kb)。

在脚本 [bench_lines.exs](https://github.com/poeticoding/httpstream_articles/blob/9bdff69e2a7db09b2af95dbcde75dc16143a2860/bench_lines.exs) 中我们使用了[Benchee](https://github.com/bencheeorg/benchee)T4】

```
# bench_lines.exs
chunk_stream = File.stream!("numbers_small.txt",[],16_000)
lines_stream = File.stream!("numbers_small.txt", [], :line)

stream_sum = fn enum ->
  enum
  |> Stream.map(fn line-> 
case Integer.parse(line) do
{num, _} -> num
:error -> 0
end
end)
|> Enum.sum()
end

Benchee.run(%{
"File.stream! :line" => fn ->
lines_stream 
|> stream_sum.()
end,
"with next_lines" => fn ->
chunk_stream
|> HTTPStream.lines(:next_lines)
|> stream_sum.()
end,
"with String.split" => fn ->
chunk_stream
|> HTTPStream.lines(:string_split)
|> stream_sum.()
end
},
  time: 10
) 
```

```
$ mix run bench_lines.exs

Name                        ips        average
with String.split          3.35      298.30 ms
File.stream! :line          2.08      481.22 ms
with next_lines            1.14      875.01 ms

Comparison:
with String.split          3.35
File.stream! :line          2.08 - 1.61x slower +182.93 ms
with next_lines            1.14 - 2.93x slower +576.71 ms 
```

有趣的是版本`"with String.split"`甚至比`"File.stream! :line"`还要快，而我们做的第一个实现是最慢的。

老实说，我不知道为什么版本`"with String.split"`是最快的一个。也许是对`String.split/2`函数的一些优化？如果你对这些细节感兴趣，我已经在 elixir 论坛上开了一个关于这个的话题:[从一个大块的枚举中流行](https://elixirforum.com/t/streaming-lines-from-an-enum-of-chunks/21244/2)。

将块大小从`16_000`减小到`2_000`，我们可以看到`"with String.split"`和`"with next_lines"`都比
快一点

```
chunk_stream = File.stream!("numbers_small.txt",[],2000)

Name                        ips        average  
with String.split          3.79      263.67 ms
File.stream! :line          2.06      484.98 ms
with next_lines            1.42      706.48 ms

Comparison:
with String.split          3.79
File.stream! :line          2.06 - 1.84x slower +221.31 ms
with next_lines            1.42 - 2.68x slower +442.81 ms 
```

块越小，所有的拆分、反转和连接操作都越快。

# 对前 30 个数字求和

我们构建的行流是惰性的，这意味着我们可以只获取前 30 行并暂停流，而不需要下载整个 HTTP 响应。

取前 30 行我们用 [`Enum.take/2`](https://hexdocs.pm/elixir/Enum.html#take/2) 。

```
"https://poeticoding-data.sfo2.digitaloceanspaces.com/httpstream/numbers.txt"
|> HTTPStream.get()
|> HTTPStream.lines()
|> Stream.map(fn line-> 
case Integer.parse(line) do
{num, _} -> num
:error -> 0
end
end)

|> Enum.take(30)

|> Enum.sum()
|> IO.puts() 
```

你可以在 [sum_30_lines.exs](https://github.com/poeticoding/httpstream_articles/blob/master/sum_30_lines.exs)
中找到这段代码

```
$ mix run sum_30_lines.exs
STATUS: : 200
HEADERS: : [
  {"Content-Length", "131732144"},
  {"Accept-Ranges", "bytes"},
  {"Content-Type", "text/plain"},
  ...
]
END_FUN

1393 
```

应该会很快。一旦使用了 30 行，流就被停止，HTTP 连接被关闭。

你对仙丹和凤凰有热情吗？订阅 Poeticoding 时事通讯，加入快乐的普通读者，通过电子邮件接收新帖子。