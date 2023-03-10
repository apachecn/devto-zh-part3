# API 设计:错误

> 原文：<https://dev.to/samwho/api-design-errors-40fj>

创建 API 时，错误是最容易被忽略的事情之一。你的用户会不时地遇到问题，当他们遇到问题时，第一个看到的就是错误。值得花时间在它们上面，让使用你的 API 成为更愉快的体验。

# 指导原则

一个好的错误消息应该做到以下几点:

1.  解释哪里出了问题。
2.  解释一下你能做些什么。
3.  如果是可恢复的错误，要易于隔离和处理。

# 案例分析

我们将重用来自[上一篇文章](https://dev.to/blog/2019/03/26/api-design-optional-parameters/)的 HTTP 客户端案例研究，它的 API 表面看起来像这样:

```
package http

import (
  "io"
)

type Response struct {
  StatusCode int
  Headers map[string]string
  Body io.ReadCloser
}

type options struct {}
type Option = func(*options)

var (
  FollowRedirects = func(o *options) {}
  Header = func(key, value string) func(*options) {}
)

func Get(url string, options ...Option) (Response, error) {} 
```

这里有一个现实的例子来说明如何调用它:

```
package main

import (
  "fmt"
  "http"
  "io"
  "os"
)

func main() {
  res, err := http.Get("https://example.com", http.FollowRedirects, http.Header("Accept-Encoding", "gzip"))
  if err != nil {
    fmt.Fprintln(os.Stderr, err.Error())
    os.Exit(1)
  }

  if res.StatusCode != 200 {
    fmt.Fprintf(os.Stderr, "non-200 status code: %v\n", res.StatusCode)
    os.Exit(1)
  }

  _, err := io.Copy(os.Stdout, res.Body)
  if err != nil {
    fmt.Fprintln(os.Stderr, err.Error())
    os.Exit(1)
  }

  if err := res.Body.Close(); err != nil {
    fmt.Fprintln(os.Stderr, err.Error())
    os.Exit(1)
    }
} 
```

我想说明的是，选择 Go 与此无关。我们将要讨论的原则适用于大多数语言。

# 有用的错误信息

当返回一个错误时，你需要做的第一个区分是调用者是否能做任何事情。

以网络错误为例。以下是网络连接程序正常操作的一部分:

1.  目标进程已崩溃，正在开始备份。
2.  您和目的地之间的一个节点出现故障，没有转发任何数据包。
3.  目标进程过载，并限制客户端的速率以帮助恢复。

当上述情况发生时，我希望看到以下情况:

> 对 https://example.com 的[的 HTTP GET 请求失败，错误为:连接被拒绝，经济被拒绝。运行`man 2 connect`获取更多信息。你可以选择通过`http.NumRetries(int)`,但是请记住，重试次数过多会导致你的费率受限或被某些网站列入黑名单。](https://example.com)

这让我省去了一些网上的挖掘，一趟文档之旅，以及愤怒的网站管理员或自动限速器的警告。它非常符合我们指导原则的第 1 点和第 2 点。

然而，这并不是您想要展示给最终用户的。我们需要给 API 用户工具来处理错误(比如给他们提供重试的选项)，或者向最终用户显示一个漂亮的错误消息。

# 不同类型的错误

不同的语言有不同的区分错误类型的最佳实践。我们将研究 Go、Java、Ruby 和 Python。

## 去吧

在 Go 中实现这一点的惯用方法是在 API 中导出函数，这些函数可以检查抛出的错误的属性。

```
package http

type httpError struct {
  retryable bool
}

func IsRetryable(err error) bool {
  httpError, ok := err.(httpError)
  return ok && httpError.retryable
} 
```

并使用它:

```
package main

func main() {
  res, err := http.Get("https://example.com")
  if err != nil {
    if http.IsRetryable(err) {
      // retry
    } else {
      // bail
    }
  } 
```

这个想法延伸到错误可能具有的任何属性。您认为 API 用户可能想要做出决定的任何事情都应该以这种方式公开。

## Java

在 Java 中实现这一点的机制更清楚一些:自定义异常类型。

```
public class HttpException extends Exception {
  private final boolean retryable;

  private HttpException(String msg, Throwable cause, boolean retryable) {
    super(msg, cause);
    this.retryable = retryable;
  }

  public boolean isRetryable() {
    return this.retryable;
  }
} 
```

并使用它:

```
public final class Main {
  public static void main(String... args) {
    Response res;
    try {
      res = Http.get("https://example.com");
    } catch (HttpException e) {
      if (e.isRetryable()) {
        // retry
      } else {
        // bail
      }
    }
  }
} 
```

## 巨蟒

Python 中的情况类似。

```
class Error(Exception):
  pass

class HttpError(Error):
  def __init__ (self, message, retryable):
    self.message = message
    self.retryable = retryable

  def is_retryable(self):
    return self.retryable 
```

并使用它:

```
try:
  res = Http.get("https://example.com")
except HttpError as err:
  if err.is_retryable():
    # retry
  else:
    # bail 
```

编写一个从`Exception`扩展而来的通用`Error`类是编写 Python 库时的常见做法。它允许您的 API 的用户根据自己的意愿编写全面的错误处理。

## 红宝石

再一次用 Ruby。

```
class HttpError < StandardError
  def initialize message, retryable
    @retryable = retryable
    super(message)
  end

  def is_retryable
    @retryable
  end
end 
```

并使用它:

```
begin
  res = Http.get("https://example.com")
rescue HttpError => e
  if e.is_retryable
    # retry
  else
    # bail 
```

与 Python 非常相似。

# 结论

不要忽视你的错误信息。他们通常是用户第一次接触你的作品，如果你的作品很糟糕，他们会感到沮丧。

您希望让代码的用户能够灵活地以他们认为最有意义的方式处理错误。使用上面的方法，尽可能多的给他们关于这种情况的信息。

我想讨论 Rust 和函数式语言，但是没有。他们的错误处理方法与上面的有很大不同。如果你知道其他语言中的好模式，我很乐意在评论中听到。