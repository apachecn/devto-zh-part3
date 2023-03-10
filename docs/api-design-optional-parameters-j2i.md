# API 设计:可选参数

> 原文：<https://dev.to/samwho/api-design-optional-parameters-j2i>

当我们编写函数时，通常希望给用户提供选项来适应各种用例。做这件事有好的方法和坏的方法，这篇文章将探讨它们。

# 指导原则

API 设计很难。一个好的 API 需要:

1.  无需破坏用户代码即可轻松更改。
2.  对于常见用例来说很简单。
3.  无需阅读文档即可轻松理解。
4.  出问题的时候很有帮助。

在我们的案例研究中，我们将使用这些原则来帮助我们理解好的和坏的设计决策。

# 案例研究

假设我们正在用 Go 编写一个 HTTP 客户端库。

```
package http

struct Response {
  StatusCode int
  Headers map[string]string
  Body string
}

func Get(url string) (Response, error) {
  // ...
} 
```

函数体并不是非常重要。我们的目标是为用户提供一个简单的 API，到目前为止我们已经得到了很好的东西，但是它缺乏灵活性。我们不能设置头，我们没有超时的概念，如果能容易地跟踪重定向就好了。简而言之:我们遗漏了许多重要的功能。

# Go:添加参数方式错误

让我们用最简单的方法来解决这个问题。

```
func Get(url string, followRedirects bool, headers map[string]string) (Response, error) {
  // ...
} 
```

这不是最令人愉快的函数调用:

```
rsp, err := http.Get("http://example.com", false, nil) 
```

如果你在不知道函数签名的情况下遇到这种情况，你会想知道`false`和`nil`代表什么。如果你必须调用这个函数，你会对传递你不关心的参数感到不满。需要传递的参数越多，常见用例就越难。

改变是一场噩梦。添加或移除参数会破坏用户代码。添加更多的函数来实现同样的事情，但是使用不同的参数集，会让人感到困惑。

简而言之，这就是一团糟。

# Go:添加参数的好方法

如果我们把参数放在一个结构体中会怎么样？

```
struct Options {
  FollowRedirects bool
  Headers map[string]string
}

func Get(url string, options Options) (Response, error) {
  // ...
}

rsp, err := Get("http://example.com", Options{
  FollowRedirects: true,
  Header: map[string]string{
    "Accept-Encoding": "gzip",
  },
}) 
```

## 优点

*   易于添加新参数。
*   普通读者很容易知道发生了什么。
*   用户只需设置他们关心的参数。

## 弊

*   如果你不想设置任何参数，你仍然需要传入一个空的结构。常见的用例仍然比它需要的更加困难。
*   如果你想设置大量的参数，它会变得笨拙。
*   在 Go 中，很难区分未置位的东西和已经被特别设置为零值的东西。其他语言也有同样的空值问题。

# Go:添加参数的更好方式

这个稍微复杂一点，但是忍耐一下。

```
type options struct {
  followRedirects bool
  headers map[string]string
}

type Option = func(*options)

var (
  FollowRedirects = func(o *options) {
    o.followRedirects = true
  }

  Header = func(key, value string) func(*options) {
    return func(o *options) {
      o.headers[key] = value
    }
  }
)

func Get(url string, os ...Option) (Response, error) {
  o := options{}
  for _, option := range os {
    option(&o)
  }

  // ...
} 
```

使用它的一些例子:

```
rsp, err := http.Get("http://example.com") 
```

如果要指定参数:

```
rsp, err := http.Get("http://example.com",
  http.FollowRedirects,
  http.Header("Accept-Encoding", "gzip")) 
```

## 优点

*   易于添加新参数。
*   通过在使用旧参数时输出警告，很容易废弃旧参数。
*   普通读者很容易知道发生了什么。
*   函数可以做任何事情，所以参数不仅仅是指定值。例如，您可以从磁盘加载配置。

## 弊

*   如果 API 作者不小心，他们可能会创建相互干扰的参数，或者做一些不安全的事情，比如改变全局状态。
*   对于 API 作者来说，设置起来比普通的旧函数参数要复杂一些。

利大于弊。灵活性和干净的 API 表面将会得到回报，只要你一直检查你在`Option`函数内部做了什么。

对于没有一流支持的语言中的可选参数，这是我的首选。

# “但是其他语言呢？”

您可能会想:“但是 Java 有方法重载，这对于可选参数来说不是很好吗？”

这是一个很好的问题，当我们不想改变任何东西时，我们可以避开指定一个默认的`Options` struct 的需要。让我们探索一下在 Java 中使用方法重载会是什么样子。

# Java 中的案例研究

这是最初的案例研究，但是用 Java 重述了一遍。

```
public final class Http {
  public static final class Response {
    public final int statusCode;
    public final InputStream body;
    public final Map<String, String> headers;
  }

  public static Response get(String url) throws IOException {
    // ...
  }
} 
```

对它的调用应该是这样的:

```
try {
  Response res = Http.get("https://example.com");
} catch (IOException e) {
  // ...
} 
```

# Java:方法重载方法(坏)

现在让我们使用方法重载来避免必须指定一个默认的`Options`结构。

```
public final class Http {
  public static Response get(String url) throws IOException {
    return get(url, null);
  }

  public static Response get(String url, Map<String, String> headers) throws IOException {
    return get(url, headers, false);
  }

  public static Response get(String url, boolean followRedirects, Map<String, String> headers) throws IOException {
    // ...
  }
} 
```

这通常被称为“望远镜功能”，因为当您添加新参数时，整个事情会变得更长，就像扩展望远镜一样。

下面是一个使用它的例子:

```
public final class Main {
  public static final void main(String... args) throws IOException {
    Response res;
    res = Http.get("https://example.com");
    res = Http.get("https://example.com", true);
    res = Http.get("https://example.com", true, Map.of("Accept-Encoding", "gzip"));
  }
} 
```

## 优点

*   用户无需担心指定所有参数。常见的用例很简单。
*   易于添加新参数。

## 弊

*   有很多样板文件要设置。
*   虽然常见的用例很简单，但是如果您想要调整一个参数，您可能必须指定许多您不关心的其他参数。

这不是一个理想的解决方案。它改进了前面的代码，但是我不建议您在自己的代码中使用它。

# Java:好办法

```
public final class Http {
  private static final class Options {
    boolean followRedirects;
    Map<String, String> headers;

    Options() {
      this.followRedirects = false;
      this.headers = new HashMap<>();
    }
  }

  public static Consumer<Options> followRedirects() {
    return o -> o.followRedirects = true;
  }

  public static Consumer<Options> header(String key, String value) {
    return o -> o.headers.put(key, value);
  }

  public static Response get(String url, Consumer<Options>... os) throws IOException {
    Options options = new Options();
    for (Consumer<Options> o : os) {
      o.accept(options);
    }

    // ...
  }
} 
```

还有一个使用这个 API 的例子:

```
public final class Main {
  public static final void main(String... args) throws IOException {
    Response res;
    res = Http.get("https://example.com");
    res = Http.get("https://example.com", Http.followRedirects());
    res = Http.get("https://example.com", Http.followRedirects(), Http.header("Accept-Encoding", "gzip"));
  }
} 
```

它拥有我们在 Go 中看到的所有优点，并且在 Java 中仍然看起来很好。它还解决了我们在前面部分看到的用户可能只想指定一个参数的问题。

虽然上面说的很好，但这不是你在野外使用 Java 时会看到的。你更有可能看到“建筑工人”

# Java:使用构建器的惯用解决方案

```
public final class HttpClient {
  private final Map<String, String> headers;
  private final boolean followRedirects;

  private HttpClient(Map<String, String> headers, boolean followRedirects) {
    this.headers = headers;
    this.followRedirects = followRedirects;
  }

  public static final class Builder {
    private Map<String, String> headers = new HashMap<>();
    private boolean followRedirects = false;

    private Builder() {}

    public Builder withHeader(String key, String value) {
      this.headers.put(key, value);
      return this;
    }

    public Builder followRedirects() {
      this.followRedirects = true;
      return this;
    }

    public Client build() {
      return new Client(headers, followRedirects);
    }
  }

  public static Builder builder() {
    return new Builder();
  }

  public Response get(String url) throws IOException {
    // ...
  }
} 
```

还有一个使用它的例子:

```
public final class Main {
  public static final void main(String... args) {
    HttpClient client =
      HttpClient.builder()
        .withHeader("Accept-Encoding", "gzip")
        .followRedirects()
        .build();

    Response res = client.get("https://example.com");
  }
} 
```

这可能感觉有点偏离向方法传递可选参数。用 Java 创建新对象是廉价的，并且比长方法签名更受欢迎。

## 优点

*   可以在不破坏用户代码的情况下添加新参数。
*   普通读者很容易知道发生了什么。
*   构建器上的函数补全告诉您哪些参数是可用的。

## 弊

*   给图书馆作者很多很多样板文件。

这是我建议您在 Java 中支持可选参数的方式。这可能感觉工作量很大，但是有一些[库](https://github.com/google/auto/blob/master/value/userguide/index.md)可以帮助减少样板文件。像 IntelliJ 这样的 ide 也有[工具](https://www.jetbrains.com/help/idea/replace-constructor-with-builder.html)来帮助你生成大部分无聊的东西。

## 为什么这个比另一个方法更惯用？

在爪哇，建筑者早于兰达斯。

# “但是支持可选参数的语言呢？”

又一个很棒的问题。在这些情况下，使用该语言提供给你的东西是最有意义的。

## 巨蟒

```
class Http:
  @staticmethod
  def get(url, follow_redirects=False, headers=None):
    pass

res = Http.get("https://example.com")
res = Http.get("https://example.com", follow_redirects=True)
res = Http.get("https://example.com", headers={"Accept-Encoding": "gzip"}) 
```

## 红宝石

```
class Http
  def self.get(string, follow_redirects: false, headers: nil)
  end
end

res = Http.get("https://example.com")
res = Http.get("https://example.com", headers: {"Accept-Encoding": "gzip"})
res = Http.get("https://example.com", follow_redirects: true, headers: {"Accept-Encoding": "gzip"}) 
```

## 优点

*   易于添加新参数。
*   普通读者很容易知道发生了什么。
*   对于 API 作者来说，几乎没有样板文件。

## 弊

*   关于设置默认参数值的一些奇怪之处，我们将在后面讨论。

# 用于动态语言的警示词

## 当心* *夸尔斯

Ruby 和 Python 都能够将它们的命名参数“打包”到字典中:

```
class Http
  def self.get(string, **kwargs)
    # kwargs[:headers]
    # kwargs[:follow_redirects]
  end
end

res = Http.get("https://example.com")
res = Http.get("https://example.com", headers: {"Accept-Encoding": "gzip"})
res = Http.get("https://example.com", follow_redirects: true, headers: {"Accept-Encoding": "gzip"}) 
```

```
class Http:
  @staticmethod
  def get(url, **kwargs):
    # kwargs["headers"]
    # kwargs["follow_redirects"]
    pass

res = Http.get("https://example.com")
res = Http.get("https://example.com", follow_redirects=True)
res = Http.get("https://example.com", headers={"Accept-Encoding": "gzip"}) 
```

我会建议**反对**使用这些。显式让读者更清楚他们可以传入什么，这也让作者有机会设置合理的默认值。

## 当心可变默认值

你可能在几节前就想写这个了:

```
class Http:
  @staticmethod
  def get(url, follow_redirects=False, headers={}):
    pass 
```

注意`headers={}`与上面的不同，我们用 Python 写了`headers=None`，用 Ruby 写了`headers=nil`。

Python 中的问题是，每次调用该方法时，不会创建空字典。它在定义类时创建一次，因此在调用之间共享。在 Ruby 中这是不正确的。

这里有一个例子:

```
class Http:
  @staticmethod
  def get(url, follow_redirects=False, headers={}):
    if "counter" not in headers:
        headers["counter"] = 0
    headers["counter"] += 1
    print(headers)

Http.get("https://example.com")
Http.get("https://example.com", headers={"counter": 100})
Http.get("https://example.com") 
```

此输出:

```
{'counter': 1}
{'counter': 101}
{'counter': 2} 
```

Ruby 中的等价物:

```
class Http
  def self.get(url, follow_redirects: false, headers: {})
    headers[:counter] ||= 0
    headers[:counter] += 1
    puts headers
  end
end

Http.get("https://example.com")
Http.get("https://example.com", headers: { counter: 100 })
Http.get("https://example.com") 
```

输出:

```
{:counter=>1}
{:counter=>101}
{:counter=>1} 
```

尽管这个问题在 Ruby 中并不存在，但我还是喜欢避免它。

# 结论

我希望这篇文章能给你一些启发，并向你展示一些你没有考虑过的好技术，以及它们为什么好。

如果你知道其他方法来实现这个目标，我还没有在这里探索，我很乐意阅读它。