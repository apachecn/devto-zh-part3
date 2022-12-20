# 鲁比的重试方法

> 原文：<https://dev.to/gadimbaylisahil/ruby-s-retry-method-2f61>

向开发者、社区和热切的红宝石爱好者致敬！这是忙碌的一个月，我将在接下来的几周写几篇挑战和解决方案的文章。

今天的主题是 Ruby 的`retry`方法，我之前并不熟悉，但最近派上了用场。

当我们想要重新执行一个代码块时，使用`retry`。这在我们试图向第三方 API 发送 HTTP 请求时非常有用，如果有任何失败，我们需要重试几次。

因为我以前不知道这个特性，所以当我想重新执行一个代码块时，我会使用循环。

让我们看一个循环的例子:

我们正在发送一个 HTTP 请求，如果由于连接错误而失败，我们将重试 3 次。

```
require 'http'

def send_request(params)
  with_connection_retry { HTTP.post("http://example.com/resource", params: params) }
end

def with_connection_retry
  retries = 0

  loop do
    yield
  rescue HTTP::ConnectionError => e
    retries += 1

    raise e if retries >= 3
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这个代码看起来很难看。我们用重试来看看。

```
def send_request(params)
  with_connection_retry { HTTP.post("http://example.com/resource", params: params) }
end

def with_connection_retry
  retries ||= 0

  begin
    yield
  rescue HTTP::ConnectionError => e
    retries += 1
    raise e if retries >= 3
    retry
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

所以，现在你可以使用 Ruby 的本地 API，而不是依赖循环来重新执行代码块。