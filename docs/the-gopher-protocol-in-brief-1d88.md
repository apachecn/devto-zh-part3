# 地鼠协议简介

> 原文：<https://dev.to/dotcomboom/the-gopher-protocol-in-brief-1d88>

Gopher 是一个获取信息的协议，可以追溯到 1993 年。它通常被视为 HTTP 协议的前身，正如我们今天所知，HTTP 协议是 Web 的主干，然而它的简单性和结构赋予了它很多价值。

Gopher 是一个非常简单的协议，一次午休就可以让你基本上了解所有你需要了解的协议。让我简单介绍一下。

在本文中，我将使用`(NL)`来指代换行符。根据 RFC 1436，这相当于`\r\n`，或者一个 Windows/DOS 换行符。注意，Gopher 选择器使用制表符(`\t`)而不是空格来分隔字段，所以在示例中需要用制表符来替换它们。

# 请求

客户端首先连接到远程主机(坦白地说，这是一个非常必要的步骤)。

一旦建立了连接，客户端就向服务器发送文本，其中包含它想要访问的路径(如果适用的话)，一个由制表符分隔的查询字符串，然后是一个换行符，如下所示:
`/fruits pineapple(NL)`

或者，客户机可以只发送一个换行符，服务器会把它当作列表根`/`。

# 响应

这是服务器处理请求所需的所有信息。然后，服务器发送原始数据，有时以换行符后跟句点字符结束(如 RFC 1436 中所述)。然后它关闭连接。

与 HTTP 不同，它使用服务器响应的头，Gopher 把它留给客户机来解释；这就是为什么一个 Gopher URL 应该有一个条目类型，这样客户端就知道它将会收到什么...

# URL 方案

所以，这意味着，代替这个来接收一个文本文档:
`gopher://gopher.floodgap.com/gopher/proxy`

您应该使用这个，明确地告诉客户端它将接收纯文本:
`gopher://gopher.floodgap.com/0/gopher/proxy`

其他(老？)Gopher URL 的实现是这样的，其中条目类型和路径没有用斜线分隔:
`gopher://gopher.floodgap.com/0gopher/proxy`

虽然这就是 RFC 2466“地鼠 URI 计划”中的布局，但我并不赞成。原因在于，使用之前的格式错误的 URL 会告诉客户端它要获取`opher/proxy`并将其视为`g`(恰好是 GIF)项目类型。如果 URL 没有指定条目类型，那么在类型后面加上斜杠会让客户端更容易判断，因为所有类型都是一个字符。

# 目录/菜单

Gopher 服务器可以向客户机发送任何文件和原始数据，但是将它们联系在一起的是菜单。这是一个菜单响应的例子，来自前面的水果例子。

```
1Back to ThingSearch    /   thingsearch.uh  70
iHere are your results for "pineapple". /   error.host  0 
```

Gopher 菜单中的每一行都是一个选择器，它指向特定服务器上的一个资源。这可以是同一台主机和端口，也可以是另一台服务器。除了选择器的显示文本之外，每个字段都由制表符分隔:

*   类型，它告诉客户端如何理解从服务器获取的资源。
*   显示文本，不带任何制表符。一般的经验法则是，它应该被格式化为最多 67 个字符的屏幕。然而，这不一定是因为技术原因。
*   资源所在的路径。在信息选择器中，这可以留空。
*   服务器的主机。
*   服务器的端口，通常为 70。
*   可以使用一个额外的字段来表示该资源是支持 Gopher+的，尽管不支持它的客户端应该可以忽略它。示例中没有显示这一点。

为了正确解析菜单，所有这些字段都必须存在，除了 Gopher+的额外字段，它是可选的。

我已经将菜单中可以使用的类型分成了四个基本类别。

## 项目类型

**正文:**

*   `0`:纯文本

**菜单:**

*   `1`:目录/菜单
*   `7`:搜索服务(接受查询的目录/菜单)

**二进制:**

*   `4` : HQX 档案(旧 Mac 档案)
*   `5` : PC 二进制
*   `9`:通用二进制(您的最佳选择)
*   `g` : GIF 图片
*   `I`:普通图像(解析时要小心，Gopher 项目类型区分大小写)
*   `s` : WAV 声音(对于 MP3 等格式，应使用类型`9`

**信息:**

*   `3`:一个错误
*   `i`:信息文本

`3`和`i`选择器，即使只是信息，仍然应该填写所有上述必填字段。(只要有选项卡，路径就可以为空。)上面的例子使用了一个假的主机和端口。什么都有可能，没什么大不了的。这样，少数不这样解释信息选择器的客户机至少会有一个假的链接。

## 两性畸形

Gophermap 是目录中的一个文件，表示许多现代 Gopher 服务器(Gophernicus、Pygopherd，当然还有引入它的 Bucktooth)使用的 Gopher 菜单。Gophermaps 也有一个简化的语法，其中服务器充当一个预处理器，将根据需要填充字段并处理诸如相对目录之类的事情。此外，没有制表符的行会自动变成信息选择器。

在一个 Gophermap 中，如果你链接到同一个服务器上的另一个资源，你可以有一个这样的选择器:
`1Food food/`
服务器会把它放大成一个完整的 Gopher 选择器，在空白处填充:
`1Food /things/food 127.0.0.1 70`
在这个例子中，Gophermap 在目录`/things`中，服务器把它填充到路径中，这样客户端就可以理解它。服务器从它的配置中知道它正在主机`127.0.0.1`和端口`70`上被通告，所以它填充了这个信息。

下面是另一个带有一些信息的例子:
`The quick brown fox jumps over the lazy dog.`
因为它没有任何制表符，所以它被转换成类似于:
`iThe quick brown fox jumps over the lazy dog. / error.host(T)0`的东西

本质上，当使用 Gophermap 文件时，服务器完成繁重的工作，所以客户端在解析最终的 Gopher 菜单时不需要做任何假设。

# 外部链接

*   [RFC 1436“互联网地鼠协议”](https://tools.ietf.org/html/rfc1436)
*   [RFC 2466“地鼠 URI 方案”](https://tools.ietf.org/html/rfc2466)
*   [SDF 的地鼠教程](https://sdf.org/?tutorials/gopher)
*   [维基百科上的地鼠](https://en.wikipedia.org/wiki/Gopher_(protocol))

使用 Gophermaps 的常见 Gopher 服务器:

*   [<sup>【代理】</sup>](https://gopherlens-py.dotcomboom.repl.co/?url=gopher://gopher.floodgap.com:70/1/buck) (Perl，介绍了 Gophermap 格式)
*   [Pygopherd](https://github.com/jgoerzen/pygopherd) (Python 2.x)
*   [Gophernicus](https://github.com/prologic/gophernicus) (C)

这是我的几个相关项目:

*   [Gophermap 编辑器](https://gmap.dotcomboom.repl.co) (HTML5)
*   [Pituophis](https://github.com/dotcomboom/Pituophis) (Python 3 客户端/服务器库)