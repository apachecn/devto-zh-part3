# 用 Ruby 构建一个 30 行的 HTTP 服务器

> 原文：<https://dev.to/appsignal/building-a-30-line-http-server-in-ruby-1p2m>

*原载于 2016 年 11 月 23 日[blog . app signal . com/2016/11/23/ruby-magic-building-a-30-line-http-server-in-ruby . html](https://blog.appsignal.com/2016/11/23/ruby-magic-building-a-30-line-http-server-in-ruby.html)。*

Web 服务器和一般的 HTTP 可能看起来很难理解。浏览器如何格式化请求，以及如何将响应发送给用户？在这一集 Ruby Magic 中，我们将学习如何用 30 行代码构建一个 Ruby HTTP 服务器。完成后，我们的服务器将处理 HTTP GET 请求，我们将使用它来服务一个 Rack 应用程序。

> ## HTTP 和 TCP 如何协同工作
> 
> TCP 是一种传输协议，描述了服务器和客户端如何交换数据。
> 
> HTTP 是一种请求-响应协议，专门描述 web 服务器如何与 HTTP 客户端或 web 浏览器交换数据。HTTP 通常使用 TCP 作为其传输协议。本质上，HTTP 服务器是一个“说”HTTP 的 TCP 服务器。

```
# tcp_server.rb
require 'socket'
server = TCPServer.new 5678

while session = server.accept
  session.puts "Hello world! The time is #{Time.now}"
  session.close
end 
```

在这个 TCP 服务器的例子中，服务器绑定到端口`5678`并等待客户端连接。当这种情况发生时，它会向客户端发送一条消息，然后关闭连接。在它与第一个客户端完成对话后，服务器等待另一个客户端连接以再次向其发送消息。

```
# tcp_client.rb
require 'socket'
server = TCPSocket.new 'localhost', 5678

while line = server.gets
  puts line
end

server.close 
```

要连接到我们的服务器，我们需要一个 TCP 客户端。这个示例客户机连接到同一个端口(`5678`)并使用`server.gets`从服务器接收数据，然后打印出来。当它停止接收数据时，它关闭与服务器的连接，程序将退出。

当你启动服务器时服务器正在运行(`$ ruby tcp_server.rb`)，你可以在一个单独的选项卡中启动客户端来接收服务器的消息。

```
$ ruby tcp_client.rb
Hello world! The time is 2016-11-23 15:17:11 +0100
$ 
```

通过一点想象，我们的 TCP 服务器和客户机有点像 web 服务器和浏览器。客户端发送请求，服务器响应，连接关闭。这就是[请求-响应模式](https://en.wikipedia.org/wiki/Request%E2%80%93response)的工作方式，这正是我们构建 HTTP 服务器所需要的。

在开始精彩部分之前，让我们看看 HTTP 请求和响应是什么样子的。

## 一个基本 HTTP GET 请求

最基本的 [HTTP GET 请求](https://tools.ietf.org/html/rfc2616#section-5)是一个[请求行](https://tools.ietf.org/html/rfc2616#section-5.1)，没有任何额外的头或请求体。

```
GET / HTTP/1.1\r\n 
```

请求行由四部分组成:

*   一个方法标记(本例中为`GET`)
*   请求-URI ( `/`)
*   协议版本(`HTTP/1.1`)
*   一个 CRLF(回车:`\r`，后跟换行:`\n`)来指示行尾

服务器将用一个 [HTTP 响应](https://tools.ietf.org/html/rfc2616#section-6)来响应，可能是这样的:

```
HTTP/1.1 200\r\nContent-Type: text/html\r\n\r\n\Hello world! 
```

这一回应包括:

*   状态行:协议版本(“HTTP/1.1”)，后跟一个空格、响应的状态代码(“200”)，以 CRLF ( `\r\n`)结束
*   可选标题行。在这种情况下，只有一个标题行(“Content-Type: text/html”)，但是可以有多个标题行(用 CRLF: `\r\n`分隔)
*   一个新行(或双 CRLF)将状态行和标题与正文分开:(`\r\n\r\n`)
*   正文:“你好，世界！”

## 一个最小的 Ruby HTTP 服务器

说够了。现在我们知道了如何在 Ruby 中创建 TCP 服务器，以及一些 HTTP 请求和响应是什么样子，我们可以构建一个最小的 HTTP 服务器。您会注意到 web 服务器看起来与我们之前讨论的 TCP 服务器几乎一样。总的想法是一样的，我们只是使用 HTTP 协议来格式化我们的消息。此外，因为我们将使用浏览器发送请求和解析响应，所以这次我们不必实现客户机。

```
# http_server.rb
require 'socket'
server = TCPServer.new 5678

while session = server.accept
  request = session.gets
  puts request

  session.print "HTTP/1.1 200\r\n" # 1
  session.print "Content-Type: text/html\r\n" # 2
  session.print "\r\n" # 3
  session.print "Hello world! The time is #{Time.now}" #4

  session.close
end 
```

在服务器接收到请求后，像以前一样，它使用`session.print`向客户机发回一条消息:不仅仅是我们的消息，它还在响应前面加上一个状态行、一个标题和一个换行符:

1.  状态行(`HTTP 1.1 200\r\n`)告诉浏览器 HTTP 版本是 1.1，响应代码是“200”
2.  指示响应具有文本/html 内容类型(`Content-Type: text/html\r\n`)的标题
3.  换行(`\r\n`)
4.  正文:“你好，世界！…"

像以前一样，它在发送消息后关闭连接。我们还没有读取请求，所以现在它只是将请求打印到控制台。

如果您启动服务器并在浏览器中打开 [http://localhost:5678](http://localhost:5678) ，您应该会看到“Hello world！…”-与当前时间一致，就像我们之前从 TCP 客户端收到的一样。🎉

[![Our minimal Ruby HTTP server returning our "Hello world"-line](img/2fdda54a9d83c7ed4ab90826d3af039b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--YeYRbKJV--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.appsignal.cimg/blog/2016-11/http_server.png)

## 上菜架 app

到目前为止，我们的服务器对每个请求都只返回一个响应。为了让它更有用，我们可以向服务器添加更多的响应。我们将使用一个 [Rack](http://rack.github.io) 应用程序，而不是直接将它们添加到服务器。我们的服务器将解析 HTTP 请求，并将它们传递给 Rack 应用程序，然后 Rack 应用程序将返回一个响应，供服务器发送回客户端。

Rack 是支持 Ruby 和大多数 Ruby web 框架(如 Rails 和 Sinatra)的 web 服务器之间的接口。最简单的形式是，Rack app 是一个响应`call`并返回“tiplet”的对象，tiplet 是一个包含三项的数组:HTTP 响应代码、HTTP 头的散列和主体。

```
app = Proc.new do |env|
  ['200', {'Content-Type' => 'text/html'}, ["Hello world! The time is #{Time.now}"]]
end 
```

在这个例子中，响应代码是“200”，我们通过头传递“text/html”作为内容类型，主体是一个字符串数组。

为了允许我们的服务器提供来自这个应用程序的响应，我们需要将返回的三元组转换成 HTTP 响应字符串。不像以前那样总是返回静态响应，我们现在必须从 Rack 应用程序返回的三元组构建响应。

```
# http_server.rb
require 'socket'

app = Proc.new do
  ['200', {'Content-Type' => 'text/html'}, ["Hello world! The time is #{Time.now}"]]
end

server = TCPServer.new 5678

while session = server.accept
  request = session.gets
  puts request

  # 1
  status, headers, body = app.call({})

  # 2
  session.print "HTTP/1.1 #{status}\r\n"

  # 3
  headers.each do |key, value|
    session.print "#{key}: #{value}\r\n"
  end

  # 4
  session.print "\r\n"

  # 5
  body.each do |part|
    session.print part
  end
  session.close
end 
```

为了回应我们从 Rack 应用程序收到的回复，我们将对服务器进行一些更改:

1.  从`app.call`返回的三元组中获取状态代码、标题和正文。
2.  使用状态代码构建状态行
3.  循环遍历标题，并为散列中的每个键值对添加一个标题行
4.  打印一个新行，将状态行和标题与正文分开
5.  在主体上循环并打印每个部分。因为在我们的 body 数组中只有一个部分，所以它将在关闭会话之前简单地向会话输出我们的“Hello world”消息。

## 阅读请求

直到现在，我们的服务器一直忽略`request`变量。我们不需要这样做，因为我们的 Rack 应用程序总是返回相同的响应。

`Rack::Lobster`是 Rack 附带的一个示例应用程序，它使用请求 URL 参数来运行。从现在开始，我们将不再把 Proc 作为一个应用程序，而是把它作为我们的测试应用程序。

```
# http_server.rb
require 'socket'
require 'rack'
require 'rack/lobster'

app = Rack::Lobster.new
server = TCPServer.new 5678

while session = server.accept
# ... 
```

打开浏览器现在会显示一只龙虾，而不是之前打印的无聊字符串。龙虾！

[![Our minimal Ruby HTTP server running Rack::Lobster](img/afee2cd8ec1137c5508a6ef17767955e.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--A11nVKv_--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://blog.appsignal.cimg/blog/2016-11/http_server_lobster.png)

“翻转！”和“崩溃！”链接分别链接到`/?flip=left`和`/?flip=crash`。然而，当跟随链接时，龙虾不会翻转，也不会崩溃。那是因为我们的服务器现在不处理查询字符串。还记得我们之前忽略的`request`变量吗？如果我们查看服务器的日志，我们会看到每个页面的请求字符串。

```
GET / HTTP/1.1
GET /?flip=left HTTP/1.1
GET /?flip=crash HTTP/1.1 
```

HTTP 请求字符串包括请求方法(“GET”)、请求路径(`/`、`/?flip=left`和`/?flip=crash`)和 HTTP 版本。我们可以使用这些信息来确定我们需要提供什么。

```
# http_server.rb
require 'socket'
require 'rack'
require 'rack/lobster'

app = Rack::Lobster.new
server = TCPServer.new 5678

while session = server.accept
  request = session.gets
  puts request

  # 1
  method, full_path = request.split(' ')
  # 2
  path, query = full_path.split('?')

  # 3
  status, headers, body = app.call({
    'REQUEST_METHOD' => method,
    'PATH_INFO' => path,
    'QUERY_STRING' => query
  })

  session.print "HTTP/1.1 #{status}\r\n"
  headers.each do |key, value|
    session.print "#{key}: #{value}\r\n"
  end
  session.print "\r\n"
  body.each do |part|
    session.print part
  end
  session.close
end 
```

为了解析请求并将请求参数发送到 Rack 应用程序，我们将请求字符串拆分并将其发送到 Rack 应用程序:

1.  将请求字符串拆分为一个方法和一个完整路径
2.  将完整路径拆分为一个路径和一个查询
3.  在[机架环境散列](http://www.rubydoc.info/github/rack/rack/file/SPEC#The_Environment)中将它们传递给我们的应用程序。

比如像`GET /?flip=left HTTP/1.1\r\n`这样的请求会像这样传递给 app:

```
{
  'REQUEST_METHOD' => 'GET',
  'PATH_INFO' => '/',
  'QUERY_STRING' => '?flip=left'
} 
```

重启我们的服务器，访问 http://localhost:5678 ，点击“翻转！”-链接现在将翻转龙虾，并点击“崩溃！”链接会使我们的网络服务器崩溃。

我们刚刚触及了实现 HTTP 服务器的表面，我们的只有 30 行代码，但是它解释了基本的思想。它接受 GET 请求，将请求的属性传递给 Rack 应用程序，并将响应发送回浏览器。虽然它不处理像请求流和 POST 请求这样的事情，但我们的服务器理论上也可以用于服务其他 Rack 应用程序。

这就结束了我们对用 Ruby 构建 HTTP 服务器的快速了解。如果你想玩我们的服务器，这里有[代码](https://gist.github.com/jeffkreeftmeijer/7f08d1f7e381b9c552666750914925eb)。如果你想了解更多，或者有具体的问题，请通过 [@AppSignal](https://twitter.com/appsignal) 告诉我们。

如果你喜欢这篇文章，[订阅 Ruby Magic 时事通讯](https://appsignal.com/ruby-magic):Ruby 的(粗略)月刊。