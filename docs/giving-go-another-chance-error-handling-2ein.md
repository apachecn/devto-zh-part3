# 再给一次机会:错误处理

> 原文：<https://dev.to/detunized/giving-go-another-chance-error-handling-2ein>

自从上一集以来，我设法在这里和那里添加了一些功能:删除条目的能力，最小限度的编辑，对文件存储的一些改进。不幸的是，很难找到时间去做任何有意义的事情。生活发生了。这是相当忙乱和繁忙的。不过，我有时间思考。我意识到我没有选择最好的项目来学习围棋。

首先，我试图重新创建一个我最满意的软件。我觉得是 99%。我意识到在越来越多地使用它的同时。我应该对原始项目提出一些拉取请求，而不是试图重写它。但那样我就不会学围棋了，对吧？

第二，围棋似乎不太适合这类项目。文本处理，无并发，无网络接入。用 Go 编写某种服务器或网络爬虫会更合适。接下来我会尝试这样的东西。

到目前为止，我可以说事情进展顺利。在几天内取得成效并不困难。编写 Go 感觉大部分时间都像是在编写 C 语言，没有一些 C 语言的麻烦。不过，我总是先输入类型，然后想知道为什么它不能编译。习惯死得慢。

一直困扰我的是错误处理。大多数人经常抱怨在每次函数调用后都需要键入`if err != nil ...`。我得说我并不觉得这有多困扰。与类似异常的隐式辅助控制流相比，它通过清晰的控制流路径使错误处理显式化。

我认为对我来说最困难的部分是没有处理错误的惯用方法。错误本身太普通了:

```
type  error  interface  {  Error()  string  } 
```

就是这样。只是返回一个字符串。所以任何事都可能是个错误。大多数时候它只是一个字符串。比如`os.IsNotExist`。它检查从任何`file.*`函数返回的错误是否意味着没有找到文件:

```
func  IsNotExist(err  error)  bool  {  return  isNotExist(err)  } 
```

据我所知，在大多数系统中，错误被编码为枚举、类型或两者的组合。编译器实际上可以处理的东西。在 Go 中，用`fmt.Errorf`创建一个字符串就足够了。处理这样的错误很困难，感觉有点脏，因为错误消息*是*错误值及其编码。这是标准`os`封装中的处理方式:

```
func  isNotExist(err  error)  bool  {  return  checkErrMessageContent(err,  "does not exist",  "not found",  "has been removed",  "no parent")  }  func  checkErrMessageContent(err  error,  msgs  ...string)  bool  {  if  err  ==  nil  {  return  false  }  err  =  underlyingError(err)  for  _,  msg  :=  range  msgs  {  if  contains(err.Error(),  msg)  {  return  true  }  }  return  false  } 
```

因此，要查看文件是否因为不存在而没有打开，我必须检查“不存在”或“未找到”的错误消息。如果是“不存在”或者是其他没有被发现的东西呢？幸运的是，为此提供了方便的方法:`os.IsExist`和`os.IsNotExist`。不错！

这意味着处理我自己的错误，如果我不想在整个程序中比较字符串，我必须创建类似的方法。那有点乏味。

另一种方法是创建我自己的错误类型。这也是在标准库中的许多地方完成的。至少可以有一个[类型断言](https://tour.golang.org/methods/15)来查看我得到了什么类型的错误:

```
func  underlyingError(err  error)  error  {  switch  err  :=  err.(type)  {  case  *PathError:  return  err.Err  case  *LinkError:  return  err.Err  case  *SyscallError:  return  err.Err  }  return  err  } 
```

通常，当错误被处理时，通过简单地将错误消息粘在一起，它被包装到另一个错误中。从围棋程序中得到这个是很正常的事情:

```
Error: Error building site: failed to render pages: render of "page" failed: execute of template failed: template: _internal/opengraph.html:31:19: executing "_internal/opengraph.html" at <.>: range can't iterate over Making a time tracking tool in Go 
```

注意错误消息中有多少个冒号(`:`)。实际上每个`:`都是一个调用栈条目，因为典型的模式是处理一个错误并再次包装它:

```
_,  err  =  blah()  if  err  !=  nil  {  return  fmt.Errorf("blah failed: %v",  err)  } 
```

有一个库 [pkg/errors](https://github.com/pkg/errors) 以更好的方式处理这个问题:

```
_,  err  =  blah()  if  err  !=  nil  {  return  errors.Wrap(err,  "blah failed")  } 
```

这是一个进步。很小的一个，但向前迈出了一步。错误仍然是字符串，但是它们有一些结构概念。处理这样的错误仍然是痛苦的。到目前为止，我选择不正确地处理它们，只是把它们交给可怜的用户去处理。

总结一下:我对如何在围棋中返回和处理错误还没有很好的感觉。我会做更多的实验，看看什么更适合我。但愿 Rust 里有类似 [`std::result`](https://doc.rust-lang.org/std/result/) 或者 Scala 里有类似 [`Try`](https://www.scala-lang.org/api/2.12.0/scala/util/Try.html) 的东西。

* * *

*   总时间:约 20 小时