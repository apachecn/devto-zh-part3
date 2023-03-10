# R 中控制流的异常

> 原文：<https://dev.to/sckott/exceptions-in-control-flow-in-r-28eh>

我正在听一个自行车棚播客[第 189 集，“它会工作的，肯定，没有任何问题”](http://bikeshed.fm/189)，从 27:44 开始有一个关于异常处理的对话。具体来说，它是关于处理 web API 请求时控制流中的异常处理。这个话题立刻激起了我的兴趣，因为我做了很多 API 工作(制作和包装)。

我想讨论的部分是他们的结论，即控制流中的异常是一种反模式。似乎这是编程语言中的一种通用模式，例如 this [SO thread](https://softwareengineering.stackexchange.com/a/189225/329940) 。但是相反，在一些语言中，控制流中的异常被认为是正常行为；如 Python ( [这个](https://softwareengineering.stackexchange.com/a/318542/329940)，[这个](https://softwareengineering.stackexchange.com/a/351121/329940))。

我对此的第一反应是强烈反对，因为根据我的经验，包装 web APIs 会在 400 和 500 系列的 HTTP 状态代码上引发异常是正常的，至少在 R 和 Ruby 中是这样。他们认为有更好的方法来处理这些案件。在我的本能反应之后。

让我们先后退一步，看看一些概念，然后再深入探讨。我想也许这个话题更值得思考，而不是继续下去

## 控制流程

[编程中的控制流程](https://en.wikipedia.org/wiki/Control_flow)是

> 单个语句的顺序...被执行或评估...控制流语句是一种语句，其执行导致选择两条或多条路径中的哪一条。

[R 中的控制流](https://stat.ethz.ch/R-manual/R-devel/library/base/html/Control.html)对 R 中的控制流进行了概述，R 中的控制流构造包括`if/else`、`for`、`while`、`repeat`、`break`、`next`。

## 异常情况

例外情况有

> 需要特殊处理的异常或异常情况——通常会改变程序执行的正常流程(来源: [wikiex](https://en.wikipedia.org/wiki/Exception_handling)

在 R 中，可以用`try`、`tryCatch`、`withCallingHandlers`等进行异常处理。通常`warning()`用于向用户发出发生了什么的信号，但不会停止执行，并且可以用`suppressWarnings()`来抑制。要停止执行，使用`stop()`。

## 返回 web API 异常和控制流 R

请注意上面我们对异常的定义中的单词**exceptual**。BikeShed pod 主机惊讶地看到错误的 API 请求引发的异常，因为他们不认为错误的 API 请求是**异常**，而是给定特定条件下的预期结果(例如，HTTP 400 系列客户端错误意味着客户端做错了什么，可能服务器返回了有用的错误消息来帮助修复请求)。

他们观察到大多数 Ruby API 包装器确实有在 400/500 系列 API 状态上引发异常的行为，但是他们不同意这种方法。

在 R world 中，根据我的经验，大多数 API 包装器也遵循在 400/500 系列 HTTP 错误时引发异常停止代码流的模式。

当 web API 请求返回 400/500 系列错误时，不停止代码执行流程会是什么样子？当前设置需要改变什么？用户会受到怎样的影响？

发出 web API 请求的典型 R 函数如下所示:

```
foo  =  function(path,  query  =  list())  {  conn  =  crul::HttpClient$new("https://httpbin.org")  res  =  conn$get(path  =  path,  query  =  query)  res$raise_for_status()  res$parse("UTF-8")  } 
```

如果请求成功，一切都很好，我们得到了 JSON 有效负载

```
foo(path  =  "get",  query  =  list(apple  =  "pink lady"))  #> [1] "{\n  \"args\": {\n    \"\": \"pink lady\"\n  }, ... 
```

当有 400/500 系列代码时，行`res$raise_for_status()`抛出一个错误，停止执行

```
foo(path  =  "status/400")  #> Error: Bad Request (HTTP 400) 
```

我们可以抛出一个警告并继续下一步，而不是引发一个错误

```
bar  =  function(path,  query  =  list())  {  conn  =  crul::HttpClient$new("https://httpbin.org")  res  =  conn$get(path  =  path,  query  =  query)  if  (res$status_code  >=  400)  {  warning(sprintf("HTTP %s %s",  res$status_code,  res$status_http()$explanation))  }  res$parse("UTF-8")  } 
```

```
bar(path  =  "status/400")  #> [1] ""  #> Warning message:  #> In bar(path = "status/400") :  #>  HTTP 400 Bad request syntax or unsupported method 
```

这很好，但是在一些情况下会有问题:

1.  许多 API**在 400 系列错误上不**返回相同的内容类型，在 500 系列错误上更常见。事实上，JSON APIs 通常会返回一个 HTML 错误页面，其中可能包含也可能不包含有意义的消息，而不是与成功响应相同的内容类型(例如 JSON)。
2.  与简单地解析响应`res$parse("UTF-8")`不同，下游代码可能更复杂(例如，选择特定的字段/键)，并且可能失败(在 R 中，这通常意味着对用户无用的错误消息)。

如果我们接受他们的建议，不在 400/500 系列代码上失败，那会是什么样子？人们可以这样做:

```
hello_world  <-  function(path,  query  =  list())  {  conn  =  crul::HttpClient$new("https://httpbin.org")  res  =  conn$get(path  =  path,  query  =  query)  if  (res$status_code  >=  400)  {  warning(sprintf("HTTP %s %s",  res$status_code,  res$status_http()$explanation))  }  res  } 
```

我们仍然会收到错误警告

```
hello_world(path  =  "status/400")  #> Warning message:  #> In hello_world(path = "status/400") :  #>   HTTP 400 Bad request syntax or unsupported method 
```

但是我们也返回响应对象(在本例中是来自`crul`包的`HttpResponse`):

```
#> <crul response>  #>   url: https://httpbin.org/status/400  #>   request_headers:  #>     User-Agent: libcurl/7.54.0 r-curl/3.3 crul/0.7.0  #>     Accept-Encoding: gzip, deflate  #>     Accept: application/json, text/xml, application/xml, */*  #>   response_headers:  #>     status: HTTP/1.1 400 BAD REQUEST  #>     access-control-allow-credentials: true  #>     access-control-allow-origin: *  #>     content-type: text/html; charset=utf-8  #>     date: Mon, 04 Mar 2019 17:49:39 GMT  #>     server: nginx  #>     content-length: 0  #>     connection: keep-alive  #>   status: 400 
```

现在，用户可以浏览响应体、响应头等。并且自己决定做什么，而不是函数失败并且不返回任何东西。

如果您的用户更高级，这种方法很好，但是大多数包/库可能试图返回用户熟悉的数据对象。在 R 中，这显然是 data.frame。当出现 400/500 系列错误时，一种选择是返回一个空的 data.frame 并抛出一个错误警告，希望有足够的信息供用户修复请求。这对新手用户来说可能是最好的，但是任何软件包都有一些更高级的用户，他们会从更多的信息中受益；更多的信息将帮助一个天真的用户+维护者调试问题。

下一个更复杂的选项是一个无论有无错误都具有相同格式的列表:

```
func  <-  function()  {  res  <-  hello_world(path  =  "status/400")  mssg  <-  sprintf("HTTP %s %s",  res$status_code,  res$status_http()$explanation)  list(data  =  res$parse("UTF-8"),  error  =  mssg)  } 
```

给

```
func()  #> $data  #> [1] ""  #>  #> $error  #> [1] "HTTP 400 Bad request syntax or unsupported method" 
```

或者可能是更复杂的东西，您可以构建访问器来轻松获取用户期望的数据，而且如果需要的话，还可以深入研究 HTTP 响应对象本身:

```
Response  <-  R6::R6Class("Response",  public  =  list(  x  =  NULL,  initialize  =  function(resp)  self$x  <-  resp,  data  =  function()  self$x$parse("UTF-8"),  error  =  function()  {  sprintf("HTTP %s %s",  self$x$status_code,  self$x$status_http()$explanation)  }  )  )  myfunc  <-  function()  {  res  <-  hello_world(path  =  "status/400")  Response$new(res)  } 
```

这给出了:

```
out  <-  myfunc()  # the HTTP message  out$error()  #> [1] "HTTP 400 Bad request syntax or unsupported method"  # the response body, parsed  out$data()  #> [1] ""  # the full HTTP response object  out$x 
```

## 用户在自己这边处理异常怎么办？

如果一个人坚持在 400/500 系列错误的执行流程之外出错，用户仍然可以在他们那端处理它。例如，如果他们在循环/应用类型调用中使用一个函数，他们可以使用`tryCatch`或类似的方法检查错误，并根据错误或成功的请求继续两种或多种方法中的一种。当然，这假设用户知道如何做到这一点。

此外，这意味着每个用户将以不同的方式处理错误，可能会在过程中出错——要求包的开发人员处理异常。

## 太复杂了，干脆失败掉

我喜欢在我的包中忽略 400/500 系列错误的一个原因是，响应中经常有大量的数据篡改。失败让我的生活更轻松，因为我不必担心如何处理失败的 HTTP 响应。在我运行的小型科学/研究 API 的世界中，API 失败行为通常不是很好；它通常是不可预测的，经常变化，失败响应体通常只是它们的 HTML 失败页面，导致解析 HTML 的代码很脆弱，因为 HTML 可能经常变化。例如，如果每个 API 都像 Github 的一样好，那就太好了，但是我们永远不会达到那个程度。

## 性能考虑

在阅读控制流中的异常时，有一个关于性能的共同线索(例如， [c++](https://stackoverflow.com/questions/13835817/are-exceptions-in-c-really-slow) ， [Ruby 1](https://simonecarletti.com/blog/2010/01/how-slow-are-ruby-exceptions/) ， [Ruby 2](https://www.honeybadger.io/blog/benchmarking-exceptions-in-ruby-yep-theyre-slow/) )。也就是说，如果抛出异常是一个缓慢的过程，这是避免它们的一个原因。但是如果异常不慢，那么这就不是避免它们的好理由。

我在 R 中没有看到任何关于性能的异常，尽管我确信那里有一些东西。

即使异常是一个缓慢的过程，有一个论点是，尽早失败也可以节省时间；也就是说，如果您得到一个 400/500 系列错误，那么您就没有花时间在响应的下游处理上。然而，用户拥有的信息较少。一路权衡下来。

## 结论

我不确定我是否会更改我维护的包中的任何内容。我会继续考虑这件事，并四处打听一下，听听其他人对这件事的看法。我有一部分想遵循避免异常的方法，但是我担心两件事。首先，作为开发人员，复杂性增加了。如果我没有失败，那么我必须以某种方式处理每个响应的解析。不是把 HTTP 响应给回去那么简单；理想情况下，我想给用户一个他们熟悉的数据结构，即 data.frame。其次，对于用户来说，如果我返回一个列表或一个`R6`对象，那会增加他们这边的复杂性。对用户来说，更多信息的好处是否值得付出更多复杂性的代价？我不知道。