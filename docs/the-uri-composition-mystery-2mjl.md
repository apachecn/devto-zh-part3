# Uri 组合之谜

> 原文：<https://dev.to/joaofbantunes/the-uri-composition-mystery-2mjl>

## [T1】简介](#intro)

这将是其中一个令人羞愧的帖子，我会用它来确保下次我遇到同样的问题时，我不会浪费时间去解决它😛。

## 那么，有什么问题呢？

所以我使用了一个`HttpClient`，为请求传递了一个`Uri`。为了构造`Uri`，使用了得到两个`Uri`的构造函数，第一个`Uri`代表基址，所以是绝对的`Uri`，而第二个应该是相对地址。

考虑到这一点，查看下面的代码，您希望将什么内容写入控制台？

```
var baseUri = new Uri("https://api.dev/v3");
var routeUri = new Uri("stuff", UriKind.Relative);
var fullUri = new Uri(baseUri, routeUri);

Console.WriteLine(fullUri); 
```

Enter fullscreen mode Exit fullscreen mode

我的期望是`https://api.dev/v3/stuff`，但是，唉，这不是我们得到的！输出是`https://api.dev/stuff`，因为我没有在基址后面加一个`/`。如果基址以`/`结尾，那么组合将按预期工作。

但是等等！还有更多...

即使基址中有斜杠，如果相对地址以斜杠开头，它也将替换基址的相对部分。

所以，下面的代码:

```
var baseUri = new Uri("https://api.dev/v3/");
var routeUri = new Uri("/stuff", UriKind.Relative);
var fullUri = new Uri(baseUri, routeUri);

Console.WriteLine(fullUri); 
```

Enter fullscreen mode Exit fullscreen mode

也会输出`https://api.dev/stuff`。

## “雪上加霜”

比我在这上面浪费的时间更糟糕的是，这种行为在[文档](https://docs.microsoft.com/en-us/dotnet/api/system.uri.-ctor?view=netcore-2.2#System_Uri__ctor_System_Uri_System_Uri_)中有描述。

> 评论
> 
> 此构造函数通过组合绝对 Uri 实例 baseUri 和相对 Uri 实例 relative Uri 来创建新的 Uri 实例。如果 relativeUri 是一个绝对 Uri 实例(包含一个方案、主机名和一个可选的端口号)，则只使用 relativeUri 创建 Uri 实例。
> 
> 如果 baseUri 有相对部分(比如/api)，那么如果 baseUri 的相对部分要保留在构造的 Uri 中，那么相对部分必须以斜杠(比如/api/)结束。
> 
> 此外，如果 relativeUri 以斜杠开头，那么它将替换 baseUri 的任何相对部分
> 
> 此构造函数不确保 Uri 引用可访问的资源。

这种行为对我来说太奇怪了(尽管可能有一个很好的理由)，我没有考虑查看文档，而是继续搜索代码，寻找其他原因。

## 其他

总结一下，如果我们想确保`Uri`组合运行良好，我们应该用一个`/`结束基址，并且用一个**而不是**开始相关部分。

```
var baseUri = new Uri("https://api.dev/v3/");
var routeUri = new Uri("stuff", UriKind.Relative);
var fullUri = new Uri(baseUri, routeUri);

Console.WriteLine(fullUri);
// outputs -> https://api.dev/v3/stuff 
```

Enter fullscreen mode Exit fullscreen mode

希望下次我能记住这一点！🙃