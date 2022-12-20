# 如何修复 JSON.stringify 返回空对象

> 原文：<https://dev.to/kameronkales/how-to-fix-json-stringify-returning-an-empty-object-4man>

我写了很多 Javascript，今天遇到了一个非常糟糕的 bug，花了我 3 个小时才弄明白。为了给别人节省同样多的时间，我在这里写下了我是如何解决这个问题的。

我正在开发一个 chrome 扩展，让互联网再次私人化。没有跟踪，没有坏演员等等。我已经写了几个库，用于这个项目的简单的东西，如谷歌和 http 请求的认证。

我通常使用 axios 之类的东西，但我想开发自己的东西，并与原始的 XML HTTPRequests 接口。昨晚我跑出门去，但是做了一些我不太清楚的代码修改。糟糕的举动。

我有两种方法，最后都成了解决问题的方法。

在我的 auth.js 方法中，我被传递了一个来自 Google 的对象，如下:

```
{ "email": "kameronkales", "id": 1298179834} 
```

在我的 history.js 中，我创建了一个我认为是对象的东西(但实际上是一个数组)。

我是这样创建数组的:

```
var i;
var example_list = []
for (i = 0; i < bad_list.length; i++) {
var clean = bad_list[i]
    example_list.push(clean)
} 
```

这不是我想的那样。

当我控制台记录来自 example_list 的数据时，我得到:

```
{ 
    0: "https://google.com",
    1: "https://google.com",
    2: "https://google.com
} 
```

现在看来这显然是错误的。但是看了数据才发现。

JSON.stringify 不知道怎么处理那些 int 字段。所以，它放弃了他们。

我什么时候会跑

```
JSON.stringify(example_list) 
```

我会得到这样的回报:

```
[] 
```

这让我发疯！因为我知道数组里有字段。但是我没有给予足够的关注(我知道，我知道。我不会再那样做了。)

我最终能够通过以下方式解决这个问题:

```
var urls = {}   <= an actual object

.....HTTP requests to get the data I wanted 

urls['data'] = data 
```

这会返回一个漂亮的对象，如下所示:

```
{
    'data': 'https://google.com',
    'data': 'https://google.com',
    'data' : 'https://google.com'
} 
```

有很多方法可以清理这种情况并更好地格式化。但是我花了太长时间试图找出为什么这个简单的方法不能像我希望的那样工作。

希望这能节省你一些时间。