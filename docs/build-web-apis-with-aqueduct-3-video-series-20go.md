# 使用 Aqueduct #3 构建 Web APIs(视频系列)

> 原文：<https://dev.to/graphicbeacon/build-web-apis-with-aqueduct-3-video-series-20go>

在本文中，我们将看看`Serializable`类，以及它们如何使我们能够处理请求体的有效载荷。然后，我们将继续设置和集成我们的 PostgreSQL 数据库。

这是该系列的第三个视频:

[https://www.youtube.com/embed/sXk9nkdSVq4](https://www.youtube.com/embed/sXk9nkdSVq4)
→ [**在 YouTube 上观看**](http://bit.ly/aqueduct-tutorial-3)

* * *

## 什么是可序列化对象？

这些对象负责对请求的有效负载进行编码和解码。要使用其中一个，扩展`Serializable`类并覆盖`asMap()`和`readFromMap()`方法。

```
class Read extends Serializable {
  String title;
  String author;
  int year;

  @override
  Map<String, dynamic> asMap() => {
    'title': title,
    'author': author,
    'year': year,
  }

  @override
  void readFromMap(Map<String, dynamic> requestBody) {
    title = requestBody['title'] as String;
    author = requestBody['author'] as String;
    year = requestBody['year'] as int;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

因此，`asMap()`方法接受`Read`的实例成员并返回一个`Map`结构，这在返回 JSON 响应时很有用。

`readFromMap()`用于将请求有效负载键提取到我们的实例属性中。这允许我们将实例绑定到 POST 请求方法中的`body`有效负载参数:

```
class ReadsController extends ResourceController {
  ...
  ...
  @Operation.post()
  Future<Response> createNewRead(@Bind.body() Read body) async {
    // Notice the `body` parameter cast to the `Read` Serializable subclass
    ...
  }
  ...
  ...
} 
```

Enter fullscreen mode Exit fullscreen mode

更详细地了解这是如何工作的，以及我们如何在 [**完整视频**](http://bit.ly/aqueduct-tutorial-3) 中集成一个全功能 PostgreSQL 数据库。

→ [**获取源代码**](https://github.com/graphicbeacon/aqueduct-tutorial/tree/part-3)

* * *

[**订阅我的 YouTube 频道**](http://bit.ly/fullstackdart) 获取更多关于 Dart 全栈 web 开发各个方面的视频。

**喜欢、分享和[关注我](https://twitter.com/creativ_bracket)T3】😍有关 Dart 的更多内容。**

## 进一步阅读

1.  [**处理 HTTP 请求**:可序列化对象](http://aqueduct.io/docs/http/request_and_response/#serializable-objects)
2.  [**渡槽文件**:从数据库中读取](http://aqueduct.io/docs/tut/executing-queries/)
3.  [**免费飞镖截屏在 Egghead.io**](https://egghead.io/instructors/jermaine-oppong)