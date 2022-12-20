# 使用 Elixir 和 haveibeenpwned.com 的 API 验证您的密码

> 原文：<https://dev.to/fteem/validate-your-passwords-using-elixir-and-haveibeenpwnedcoms-api-1718>

*本文原载于 2018 年 12 月 23 日[我的博客](https://ieftimov.com)。你可以在这里看到它[。](https://ieftimov.com/haveibeenpwned-password-lookup-elixir)*

除非您过去几年一直生活在岩石下，否则您可能知道什么是双因素身份认证(2FA)。这实际上是一个非常巧妙的技巧——你有一个密码，你必须(显然)正确输入(第一个因素)，但你还必须通过不同的媒体(有时在不同的设备上)接收第二个(随机)代码，你必须输入才能登录(第二个因素)。

现在，显然这增加了相当多的登录开销，但它增加了不成比例的安全价值。如果你在任何类型的组织中工作，当你被要求为你所有的账户打开 2FA 时，可能不会感到惊讶。如果你还没有被要求去做(或者还没有去做)，是时候行动了；)

但是，第一个因素呢？密码。我们放弃他们了吗？

不完全是。但是，在设置密码时，我们必须变得更加警惕和聪明。为什么？请允许我解释。

## 看官，认识一下 haveibeenpwned.com

让我给你介绍一下 haveibeenpwned.com。这是一个免费的资源，任何人都可以快速评估他们是否因其在线帐户在数据泄露中遭到破坏或“入侵”而面临风险。可以想象，为了实现其目的，该服务还包含一个相当长的 pwned 密码列表(更精确地说，大约有 5 亿个)，可以通过 REST API 进行查询。

如果你想进一步了解这个项目，或者它的[作者](https://www.troyhunt.com/)，我建议查看一下这个项目的[关于](https://haveibeenpwned.com/About)的页面。

## 使用 pwned 密码 API

这个 API 允许我们检查 haveibeenpwned 数据库中是否存在任何密码。这意味着，如果你发送一个已经密码，它会告诉你，这个密码已经密码，并建议选择另一个。

想象一下，你有一个网站，人们可以在那里设置他们的密码，一旦用户输入完他们的新密码，你就可以 ping 这个服务，并检查他们选择的密码以前是否被 pwn 过。

现在，如果您在思考“您是在告诉我通过网络向某个随机 API 发送一个纯文本密码吗？”那你就领先一步了，干得好！

很抱歉让你失望了，但是不，实际上我不是那个意思。这个 API 只需要实际密码的 SHA-1 散列的 5 个字符，而不是以纯文本形式发送整个密码。

用长生不老药来说，看起来应该是:

```
:sha
|> :crypto.hash("password")
|> Base.encode16
|> String.slice(0..4) 
```

Enter fullscreen mode Exit fullscreen mode

有趣的是，它发回的是与您发送的 5 个字符相匹配的哈希密码的剩余部分。基本上，这意味着如果我们把“密码”的阿沙-1:

```
iex(1)> :crypto.hash(:sha, "password") |> Base.encode16
"5BAA61E4C9B93F3F0682250B6CF8331B7EE68FD8" 
```

Enter fullscreen mode Exit fullscreen mode

我们将只向 API 发送`5BAA6`，而在响应体中，我们将收到一个表示 SHA-1 其余部分的字符串的大列表，或者在我们的示例中是`1E4C9B93F3F0682250B6CF8331B7EE68FD8`。

《haveibeenpwned》的作者 Troy Hunt 写了一篇相当详尽的文章解释这是如何工作的——你可以在这里阅读。

## ping API

出于本练习的目的，我们将创建一个小型 Mix 包来封装所有的行为。如果您不熟悉如何使用 Mix 创建新的包，我建议您阅读我的文章[编写并发布您的第一个药剂库](https://dev.to/writing-elixir-library)。

我们将把我们的包裹命名为`Pwnex`,因为不知何故，我的大脑总是认为我必须把主词(pwned)和 Elixir 混在一起才能想出一个名字。无论如何，让我们创建它:

```
› mix new pwnex
* creating README.md
* creating .formatter.exs
* creating .gitignore
* creating mix.exs
* creating config
* creating config/config.exs
* creating lib
* creating lib/pwnex.ex
* creating test
* creating test/test_helper.exs
* creating test/pwnex_test.exs

Your Mix project was created successfully.
You can use "mix" to compile it, test it, and more:

    cd pwnex
    mix test Run "mix help" for more commands. 
```

Enter fullscreen mode Exit fullscreen mode

现在我们已经在本地启动了这个包，让我们打开`lib/pwnex.ex`并
添加一些文档:

```
defmodule Pwnex do
  @moduledoc """
  Consults haveibeenpwned.com's API for pwned passwords.
  """

  @doc """
  Checks if a given password is already pwned.

  ## Examples

      iex> Pwnex.pwned?("password")
      {:pwned, 3_000_000}

      iex> Pwnex.pwned?("m4Z2fJJ]r3fxQ*o27")
      {:ok, 0}

  """
  def pwned?(password) do
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

`moduledoc`简要解释了这个包的目的，而`doc`解释了`pwned?/1`函数的目的，并且有两个我们
可以在文档测试中使用的例子。

## 我们的小算法

让我们看看实现`Pwnex.pwned?/1`函数的步骤是什么:

```
def pwned?(password) do
  {hash_head, hash_tail} =
    password
    |> sanitize
    |> hash
    |> split_password

  hash_head
    |> fetch_pwns
    |> handle_response
    |> find_pwns(hash_tail)
    |> return_result
end 
```

Enter fullscreen mode Exit fullscreen mode

再一次 Elixir 中的管道操作符让这个函数变得如此清晰和程序化，以至于解释起来有点多余。不过，这就是:

*   `sanitize` -我们想删除密码中所有的前导和尾随空格
*   我们想将密码转换成 SHA1 散列，并返回它的前 5 个字符
*   我们想要分割头部——我们将发送给 API 的前 5 个字符和尾部——SHA-1 散列的剩余部分
*   `fetch_pwns` -我们将向 haveibeenpwned 发送一个 API 请求，以获取密码的所有 pwn(如果有)
*   根据响应，我们要么得到主体，要么返回失败的原因
*   我们将获取响应体，因为 haveibeenpwned 使用 k-匿名模型，我们需要自己找到实际的匹配(如果存在的话)
*   `return_result` -将返回包含结果原子和 pwns 计数的元组

让我们一步一步来实现这些功能。

## 操纵密码

让我们从简单的开始。在 sanitize 中，我们希望修剪前导和尾随空格，而在`hash`中，我们希望将密码改为 SHA1 并返回它的前五个字符。

```
def sanitize(password), do: String.trim(password) 
```

Enter fullscreen mode Exit fullscreen mode

这里真的没什么好解释的。我们可以不使用`sanitize`而是使用`String.trim/1`，但是我更喜欢有一个独立的函数，我们可以扩展并测试任何边缘情况。

```
defp hash(password) do
  :crypto.hash(:sha, password)
  |> Base.encode16
end 
```

Enter fullscreen mode Exit fullscreen mode

`:crypto`是一个 [Erlang 模块](http://erlang.org/doc/man/crypto.html)，它提供了一组加密函数。有趣的是，它不是标准库的一部分，但它包含在发行版中。正如您在上面的代码中看到的，其中一个函数是`hash/2`，它将哈希算法作为第一个参数，将待哈希的实际字符串作为第二个参数。它返回二进制散列，我们可以使用`Base.encode16`将其转换为十六进制。

## 向 API 发送请求

我打赌你在想[httposin](https://hex.pm/packages/httpoison)。你不是吗？当我写这篇文章的时候，我也在想，我们是否必须把一个简单的 GET 请求包含到一个完整的包中。你猜对了-我们没有。

虽然 Elixir 没有提供 HTTP 客户端，但是 Erlang 提供了。就像使用`:crypto`一样，您可以使用`:httpc`从 Elixir 使用 Erlang 的 HTTP 客户端。该模块向 HTTP/1.1 兼容的客户端提供 API。我建议在我们继续之前给[它的文档](http://erlang.org/doc/man/httpc.html)一个快速浏览。

让我们打开 IEx，给`:httpc`一个旋转:

```
iex(1)> :httpc.request('https://api.pwnedpasswords.com/range/21FCB')
{:ok,
 \{\{'HTTP/1.1', 200, 'OK'},
  [
    {'cache-control', 'public, max-age=2678400'},
    {'connection', 'keep-alive'},
    {'date', 'Sat, 22 Dec 2018 11:09:46 GMT'},
    {'server', 'cloudflare'},
    {'vary', 'Accept-Encoding'},
    {'content-length', '19951'},
    {'content-type', 'text/plain'},
    {'expires', 'Tue, 22 Jan 2019 11:09:46 GMT'},
    {'last-modified', 'Thu, 12 Jul 2018 01:32:06 GMT'},
    {'set-cookie',
     '__cfduid=d51115381191fd7bd0a003d466916efc41545476986; expires=Sun, 22-Dec-19 11:09:46 GMT; path=/; domain=.pwnedpasswords.com; HttpOnly; Secure'},
    {'cf-cache-status', 'HIT'},
    {'access-control-allow-origin', '*'},
    {'arr-disable-session-affinity', 'True'},
    {'cf-ray', '48d2235cbe93bf5c-AMS'},
    {'expect-ct',
     'max-age=604800, report-uri="https://report-uri.cloudflare.com/cdn-cgi/beacon/expect-ct"'},
    {'strict-transport-security',
     'max-age=31536000; includeSubDomains; preload'},
    {'x-content-type-options', 'nosniff'},
    {'x-powered-by', 'ASP.NET'}
  ],
  'THEBODYISHERE\r\nOMGSOMUCHSTUFFHEREWHATISTHISEVEN' ++ ...}} 
```

Enter fullscreen mode Exit fullscreen mode

您看，虽然如果您曾经通过 cURL 发送过 HTTP 请求或者打开过浏览器的调试工具，输出会非常冗长，但是这里应该不会有什么意外。`request/1`函数将向 pwnedpasswords API 发送一个请求，它将返回大量嵌套元组，其中大部分是响应头和响应的正文。

为了我们的目的，我们可以保持简单。我们只对函数是否将返回`:ok` atom 作为元组中的第一项或者`:error`感兴趣。我们可以使用模式匹配来做到这一点:

```
iex(1)> {:ok, {_status, _headers, body }} =
  :httpc.request('https://api.pwnedpasswords.com/range/21FCB') 
```

Enter fullscreen mode Exit fullscreen mode

所以，让我们回到我们的`Pwnex.fetch_pwns/1`函数。该函数将接收散列密码的前 5 个字符，它将把它发送给 API，并将返回响应的`body`:

```
def fetch_pwns(head) do
  :httpc.request('https://api.pwnedpasswords.com/range/#{head}')
end 
```

Enter fullscreen mode Exit fullscreen mode

## 处理响应

`handle_response`实际上将是一个具有三个主体的函数:

```
 def handle_response({:ok, {_status, _headers, body}}), do: body
  def handle_response({:error, {reason, _meta}}), do: reason
  def handle_response(_), do: nil 
```

Enter fullscreen mode Exit fullscreen mode

通过使用模式匹配，我们可以有三种类型的函数体。当响应状态为 HTTP 200 OK 时，将调用第一个，当出现错误时调用第二个，对于任何其他情况，将调用第三个。

正如你所想象的，我们可以在这里使用条件逻辑，但是拥有模式匹配的能力允许我们有三个非常容易阅读、理解和测试的小函数。

## 解析响应

下面是响应的正文:

```
003D68EB55068C33ACE09247EE4C639306B:3\r\n012C192B2F16F82EA0EB9EF18D9D539B0DD:1\r\n01330C689E5D64F660D6947A93AD634EF8F:1\r\n0198748F3315F40B1A102BF18EEA0194CD9:1\r\n01F9033B3C00C65DBFD6D1DC4D22918F5E9:2\r\n0424DB98C7A0846D2C6C75E697092A0CC3E:5\r\n047F229A81EE2747253F9897DA38946E241:1\r\n04A37A676E312CC7C4D236C93FBD992AA3C:5\r\n04AE045B134BDC43043B216AEF66100EE00:2\r\n0502EA98ED7A1000D932B10F7707D37FFB4:5\r\n0539F86F519AACC7030B728CD47803E5B22:5\r\n054A0BD53E2BC83A87EFDC236E2D0498C08:3\r\n05AA835DC9423327DAEC1CBD38FA99B8834:1\r\n05E0182DEAE22D02F6ED35280BCAC370179:4 
```

Enter fullscreen mode Exit fullscreen mode

如果你仔细观察，你会注意到它实际上是由`\r\n`分隔的部分 SHA-1 散列的列表。仔细观察第一个:

```
003D68EB55068C33ACE09247EE4C639306B:3 
```

Enter fullscreen mode Exit fullscreen mode

您会注意到它实际上是散列的一部分，一个冒号`:`和一个数字(上例中的`3`)。这实际上是没有前 5 个字符和特定密码被 pwn 的次数的散列。

据 haveibeenpwned.com 称，这意味着 SHA-1 散列值为
`5BAA6003D68EB55068C33ACE09247EE4C639306B`的密码已经被修改了 3 次。

我们需要对这个响应体进行分割，将它转换成一个列表
，我们将在其中迭代并找到匹配的散列。让我们这样做:

```
def find_pwns(response, hash_tail) do
  response
  |> to_string
  |> String.split
  |> String.split()
  |> Enum.find(&(String.starts_with?(&1, hash_tail)))
end 
```

Enter fullscreen mode Exit fullscreen mode

虽然看起来可能有点多，但我向你保证这不是。让我们看看每一行在这里做了什么:

1.  `to_string`将转换我们从`fetch_pwns`收到的字符列表，并将它转换成一个字符串，这样我们就可以在接下来的步骤中解析它
2.  `String.split`将拆分`\r\n`字符上的字符串，并将创建一个字符串列表，看起来像:`["003D68EB55068C33ACE09247EE4C639306B:3", ...]`
3.  我们将调用`Enum.find`，它将列表和函数作为参数。列表是哈希尾及其 pwns 计数的解析列表，而函数是`String.starts_with?/2`，当一行以`hash_tail`的值开始时，它将返回`true`。

仅此而已。最后，`find_pwns/2`函数将返回包含匹配散列尾部的行
，或者返回`nil`。

## 返回有意义的结果

既然我们已经找到了散列的 pwn 的计数(或者只是一个`nil`)，我们需要处理它并向模块的用户返回一个有意义的元组。

当`find_pwns`函数找到一个计数时，我们希望返回一个类似于`{:pwned, count}`的元组
。否则，当`find_pwns`没有找到一个计数时，它将返回`nil`，我们在`return_result`函数的第二个定义中处理它:

```
def return_result(line) when is_binary(line) do
  [_, count] = String.split(line, ":")
  {:pwned, count}
end
def return_result(_), do: {:ok, 0} 
```

Enter fullscreen mode Exit fullscreen mode

在第一个函数体中，我们将获取`line`，它应该是一个二进制的
(字符串)，在`:`字符处将其拆分，然后返回带有计数的元组。
在第二个函数体中，我们接受任何参数(在我们的例子中是`nil`),然后
返回一个以`0`为计数的元组。

## 使用 Pwnex

现在，让我们在 IEx 中加载 Pwnex，并让它旋转一下。要在 IEx 中加载它，需要打开模块的根目录并运行`iex -S mix`。这将打开一个 IEx 会话，并在其中执行`mix`，实际上，这将加载和编译模块，并使其可直接从 IEx:
调用

```
› iex -S mix
Erlang/OTP 21 [erts-10.2] [source] [64-bit] [smp:4:4] [ds:4:4:10] [async-threads:1] [hipe] [dtrace]

Interactive Elixir (1.7.4) - press Ctrl+C to exit (type h() ENTER for help)
iex(1)> h Pwnex

                          Pwnex

Consults haveibeenpwned.com's API for pwned passwords.
iex(2)> Pwnex.pwned?("password")
{:pwned, 3533661}
iex(3)> Pwnex.pwned?("123!@#asd*&(*123SAkjhda")
{:ok, 0} 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，`password`被 pwn 了大约 350 万次，而`123!@#asd*&(*123SAkjhda`从未被 pwn 过。

基本就是这样。我们让 Pwnex 工作——它将我们的输入作为函数参数，通过 Erlang HTTP 客户机与 API 对话，解析它的响应体，构建散列图，并为给定的密码找到任何 pwn。

如您所见，这整个包只用了 65 行代码就完成了相当多的工作。

在本文中，我们看到了如何创建一个新的包，使用它通过 HTTP 与 API 通信，我们了解了为什么不总是需要 HTTPoison，如何解析请求体以及如何与一些数据混合。

如果你想看我们在这篇文章中写的实际代码，请前往 Github 上的[repo。](https://github.com/fteem/pwnex)