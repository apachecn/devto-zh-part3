# Easy ruby Web 服务器

> 原文：<https://dev.to/knhr__/easy-ruby-web-server--48al>

我看了`Working With Unix Processes`。这是一本非常有趣的书，因为我不知道 Unix 进程。我在工作中使用过网络服务器，但不知道它是如何工作的。所以我想写这篇文章来记住我学到的东西。在这篇文章中，我试图用 ruby 创建一个简单的 web 服务器。

# 什么是 Web 服务器

它接受来自客户端(用户)的请求，然后返回响应。我现在创建的 Web 服务器非常简单，它可以正确处理 HTTP GET 请求。

# 启动 TCP 服务器

Ruby 提供了`TCPServer`类，为我创建 TCP 连接很容易。
server.rb

```
require 'socket'

server = TCPServer.open('0.0.0.0', 5678)

while connection = server.accept
  connection.write "Hello world!!"
  connection.close 
end 
```

由于不支持 keepalive，我们可以在编写主体后立即关闭客户端连接。

我可以启动一个服务器做`ruby server.rb`，你可以访问`curl http://localhost:5678`，然后它显示`Hello world!!`
`server.rb`处理并关闭你的请求。我准备制作 HTTP 服务器，因为 HTTP 在 TCP 上，它只是添加了一些头信息。

# HTTP 服务器入门

虽然现在这种通信在 TCP 层，我必须创建 HTTP 协议存档我的目标。如我所说，HTTP 需要一些头信息。所以我添加了一些 HTTP 头

```
 head = "HTTP/1.1 200\r\n" \
  "Date: #{Time.now.httpdate}\r\n" \
  "Content-Length: #{body.length.to_s}\r\n" 
```

整个代码

server.rb

```
require 'socket'
require 'time'

server = TCPServer.open('0.0.0.0', 5678)

while connection = server.accept

  body = "Hello world!"

  head = "HTTP/1.1 200\r\n" \
  "Date: #{Time.now.httpdate}\r\n" \
  "Content-Length: #{body.length.to_s}\r\n" 

  # 1
  connection.write head

  # 2
  connection.write "\r\n"

  # 3
  connection.write body

  session.close
  connection.close 
end 
```

1.  它添加了 http 头的信息
2.  它代表标题和正文之间的字符(CRLF)
3.  它添加正文(它将显示在浏览器中。通常是 html、json 和 xml 等)

然后，我们最终可以在您的浏览器中看到响应。如果你访问`localhost:5678`
，它会返回 200 和“Hello world”响应，我可以理解为 HTTP 只是在 TCP 服务器上添加了头信息。

## 真实世界

这总是只能捕获 GET 和 response 200。它看起来不像是一个真正的网络服务器。
然后下一步我想控制我想要的状态、标题和正文。首先，我安装了`Rack`它是一个著名的连接 Web 服务器和应用程序的中间件(像 Rails 一样)。
的[架](http://rack.github.io/)提供了公正的接口

代码将是这样的

```
require 'socket'
require 'time'
require 'rack/utils'

server = TCPServer.open('0.0.0.0', 5678)

# 1
app = Proc.new do |env|
  body = "Hello world!"
  ['200', {'Content-Type' => 'text/html', "Content-Length" => body.length.to_s}, ["Hello world!"]]
end

while connection = server.accept
  # 2
  status, headers, body = app.call({})

  head = "HTTP/1.1 200\r\n" \
  "Date: #{Time.now.httpdate}\r\n" \
  "Status: #{Rack::Utils::HTTP_STATUS_CODES[status]}\r\n" 

  # 3
  headers.each do |k,v|
    head << "#{k}: #{v}\r\n"
  end

  connection.write "#{head}\r\n"

  # 3
  body.each do |part| 
    connection.write part
  end

  body.close if body.respond_to?(:close)

  connection.close 
end 
```

1.  这是机架提供的接口设置
2.  它返回三个值，状态、标题、正文
3.  它写主体和头。因为正文和标题将是数组，所以在这段代码中使用了它们

## 阅读请求

您可以使用`connection.get`获取请求数据。
`connection.get`是这样的`GET / HTTP/1.1`。然后我可以从客户端请求中提取请求路径和方法等。

```
# 1
method, full_path = request.split(' ')
# 2
path = full_path.split('?') 
```

在`Proc.new`中，我可以通过每条路径来路由流程。

```
app = Proc.new do |env|
  req = Rack::Request.new(env)
  case req.path
  when "/"
    body = "Hello world!"
    [200, {'Content-Type' => 'text/html', "Content-Length" => body.length.to_s}, [body]]
  when /^\/name\/(.*)/
    body = "Hello, #{$1}!"
    [200, {'Content-Type' => 'text/html', "Content-Length" => body.length.to_s}, [body]]
  else 
    [404, {"Content-Type" => "text/html"}, ["Ah!!!"]]
  end
end 
```

最后，它可以做路由，方法和查询参数很容易控制如下代码

整个代码

```
require 'socket'
require 'time'
require 'rack'
require 'rack/utils'

# app = Rack::Lobster.new
server = TCPServer.open('0.0.0.0', 5678)

app = Proc.new do |env|
  req = Rack::Request.new(env)
  case req.path
  when "/"
    body = "Hello world!"
    [200, {'Content-Type' => 'text/html', "Content-Length" => body.length.to_s}, [body]]
  when /^\/name\/(.*)/
    body = "Hello, #{$1}!"
    [200, {'Content-Type' => 'text/html', "Content-Length" => body.length.to_s}, [body]]
  else 
    [404, {"Content-Type" => "text/html"}, ["Ah!!!"]]
  end
end

while connection = server.accept
  request = connection.gets
  # 1
  method, full_path = request.split(' ')
  # 2
  path = full_path.split('?')

  # 1
  status, headers, body = app.call({
    'REQUEST_METHOD' => method,
    'PATH_INFO' => path
  })

  head = "HTTP/1.1 200\r\n" \
  "Date: #{Time.now.httpdate}\r\n" \
  "Status: #{Rack::Utils::HTTP_STATUS_CODES[status]}\r\n" 

  # 1
  headers.each do |k,v|
    head << "#{k}: #{v}\r\n"
  end

  connection.write "#{head}\r\n"

  body.each do |part| 
    connection.write part
  end

  body.close if body.respond_to?(:close)

  connection.close 
end 
```

Next => Easy prefork webserver(如果我有空闲时间)