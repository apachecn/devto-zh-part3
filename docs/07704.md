# API 设计:在野外

> 原文：<https://dev.to/samwho/api-design-in-the-wild-83p>

我们已经在以前的帖子中探索了一些指导原则，但是我们还没有使用我们新发现的技能。让我们休息一会儿，看看你能在今天使用的真实世界代码中找到的一些例子，以及我们如何改进它们。

# 围棋的`math/big`包

大多数语言都有表示非常大的数字的库，Go 也不例外。

下面是它的一个例子:

```
package main

import (
  "fmt"
  "math"
  "math/big"
  "os"
)

func main() {
  r := &big.Float{}
  _, ok := r.SetString("2.5")
  if !ok {
    fmt.Fprintln(os.Stderr, "Could not parse float")
    return
  }

  r2 := &big.Float{}
  r2.Mul(r, r)

  pi := big.NewFloat(math.Pi)

  result := &big.Float{}
  result.Mul(pi, r2)

  fmt.Printf("%v\n", result)
} 
```

这将打印出半径为 2.5 的圆的面积。

关于这个 API 的一些事情让我很困扰。

## `SetString`返回一个布尔值，而不是错误

按照惯例，Go 从一个函数返回两个值:结果和错误。然后调用者首先检查是否有错误，如果没有，他们使用结果。Go 的`math/big`返回一个结果和一个布尔值。

返回布尔值而不是错误并不是完全没有先例。例如，对地图进行索引就使用了这种模式。

```
m := make(map[string]string)
m["hello"] = "world"
s, ok := m["world"]
// `ok` will be false to represent that the key was not found 
```

除了映射索引之外，我假设返回了一个错误，并在意识到自己的错误之前花几秒钟阅读一个编译时错误。

在`SetString`中，我更希望看到一个错误，其中包含一些关于出错的信息。该错误可以说明传入的字符串是否为空，或者不是浮点数，或者不是“计算机说不”

## 混淆功能接收器的使用

`math/big`中的操作使用函数接收器作为计算结果。在`a.Add(b, c)`中，`a`被设置为`b + c`的结果。本库在文档中证明了这一点:

> 通过始终经由接收器传递结果值，可以更好地控制内存使用。操作可以重用为结果值分配的空间，并在处理过程中用新的结果覆盖该值，而不必为每个结果分配新的内存。

但我认为这是一种被误导的交易。它以牺牲普通用例为代价，使利基用例变得更容易。想象一下，如果接收者被用作参数，返回值就是结果。

```
package main

import (
  "fmt"
  "math"
  "math/big"
  "os"
)

func main() {
  r := &big.Float{}
  _, ok := r.SetString("2.5")
  if !ok {
    fmt.Fprintln(os.Stderr, "Could not parse float")
    return
  }

  result := r.Mul(r).Mul(big.NewFloat(math.Pi))
  fmt.Printf("%v\n", result)
} 
```

更少的变异，更少的视觉噪音，更少的混淆函数接收器的使用。

如果事实证明仍然需要控制分配的数量，这可能是软件包上的一组不同的函数。大概是这样:

```
package main

import (
  "fmt"
  "math"
  "math/big"
  "os"
)

func main() {
  result := &big.Float{}
  _, ok := result.SetString("2.5")
  if !ok {
    fmt.Fprintln(os.Stderr, "Could not parse float")
    return
  }

  big.MulFloat(result, result, result)
  big.MulFloat(result, result, big.NewFloat(math.Pi))

  fmt.Printf("%v\n", result)
} 
```

第一个参数是结果，第二个和第三个参数是被操作的东西。这保持了常见用例的简单性，同时还为更多的特殊用例提供了工具。

# 爪哇的古老`File.exists()`法

这是我的最爱之一。这有什么不好？

```
import java.io.File;

public final class Exists {
    public static void main(String... args) {
        if (args.length != 1) {
            System.err.println("usage: java Exists filepath");
            System.exit(1);
        }

        if (new File(args[0]).exists()) {
            System.out.println("File \"" + args[0] + "\" exists!");
        } else {
            System.out.println("File \"" + args[0] + "\" does not exist.");
        }
    }
} 
```

你可能会猜测这与`.exists()`方法有关，你可能是对的。我在题目中提到这是一个比较老的 Java API，但是为什么要改呢？

返回一个布尔值，并且不抛出异常。最后一点是关键。在 Linux 上，Java 发出一个 [`stat`](https://linux.die.net/man/2/stat) 系统调用来判断一个文件是否存在。如果没有，`stat`失败，错误代码为`ENOENT`。`stat`也可能因为各种其他原因而失败。

将上述代码保存在名为`Exists.java`的文件中，并运行以下命令:

```
$ javac Exists.java
$ java Exists Exists.java
File "Exists.java" exists!
$ java Exists DoesntExist.java
File "DoesntExist.java" does not exist. 
```

到目前为止，一切顺利。现在让我们用一个叫做 [`strace`](https://strace.io) 的工具来模拟`stat`失败。

```
$ strace -f -qq -P Exists.java -e fault=stat:error=ENOMEM -- java Exists Exists.java
[pid 7199] stat("Exists.java", 0x7f7227c2b780) = -1 ENOMEM (Cannot allocate memory) (INJECTED)
File "Exists.java" does not exist. 
```

我们让它看起来像是我们的计算机内存不足，在这种情况下`.exists()`错误地报告文件不存在！

这实际上是一个[错误](https://bugs.java.com/bugdatabase/view_bug.do?bug_id=5003595)并且在[新的`Files.exists()`](https://docs.oracle.com/javase/10/docs/api/java/nio/file/Files.html#exists(java.nio.file.Path,java.nio.file.LinkOption...)) 中更清楚的是`false`并不总是意味着文件不存在。还有 [`readAttributes()`](https://docs.oracle.com/javase/10/docs/api/java/nio/file/Files.html#readAttributes(java.nio.file.Path,java.lang.Class,java.nio.file.LinkOption...)) ，如果`stat`失败会抛出一个`IOException`，这是你需要确定性时应该使用的。

这里的 API 设计问题是试图在一个只能表示两种结果的数据类型中表示几十种可能的结果。每当你考虑返回一个布尔值时，停下来想一想枚举是否会更好。不过，如果你这样做，尽量避免经典的三态布尔运算。

题外话:Java 的`Math.abs()`函数也有类似的设计问题，如果你能发现的话。

# 阿帕奇`EntityUtils.consume()`

这里的问题并不在于此，而是人们认为这是必要的。让我们先看一个例子，然后讨论问题所在。

```
import org.apache.http.HttpResponse;
import org.apache.http.client.HttpClient;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.HttpClients;

public final class App {
  public final static void main(String[] args) throws Exception {
    HttpClient client = HttpClients.createDefault();

    while (true) {
      HttpGet req = new HttpGet("http://example.com/");
      HttpResponse res = client.execute(req);
      System.out.println(res.getStatusLine());
    }
  }
} 
```

如果你要运行这个，在程序无限期挂起之前，你最多只能得到几个成功的响应。为什么？当然，因为我们忘记了关闭 HTTP 响应。我们需要在我们的`System.out.println()`调用之后添加下面一行:

```
EntityUtils.consume(res.getEntity()); 
```

我不得不在产品代码中不止一次地追踪这个问题，它总是让我思考:为什么这个问题总是发生？没有给我们更好的工具来管理可关闭的资源，这是语言的错吗？图书馆能有更好的界面吗？当它检测到我们犯了这个错误时，会输出一个有用的消息或异常吗？

答案是这些的混合。Java 有一个名为`Closeable`的接口，这是库作者向用户发出需要关闭的信号的最好方式。需要在`HttpResponse`中关闭的东西被埋在里面。这意味着 IDE 没有办法静态分析代码并提醒用户注意问题。

库的作者注意到了这一点，这是他们现在推荐你写的代码:

```
import org.apache.http.client.methods.CloseableHttpResponse;
import org.apache.http.client.methods.HttpGet;
import org.apache.http.impl.client.CloseableHttpClient;
import org.apache.http.impl.client.HttpClients;

public final class App {
    public final static void main(String[] args) throws Exception {
        try (CloseableHttpClient client = HttpClients.createDefault()) {
            while (true) {
                HttpGet req = new HttpGet("http://example.com/");
                try (CloseableHttpResponse res = client.execute(req)) {
                    System.out.println(res.getStatusLine());
                }
            }
        }
    }
} 
```

这暴露了`Closeable`接口，现在 IDEs 可以在您忘记关闭响应时提醒您。这是更好的，但没有帮助穷人调试他们的悬挂过程。我希望看到的是在程序挂起之前输出一条日志消息，说明用户可能忘记关闭他们的响应。

Go 用`defer`关键字很好地处理了资源回收。

```
package main

import (
  "os"
)

func main() {
  f, err := os.Create("myfile")
  if err != nil {
    panic(err)
  }
  defer f.Close()

  // do other stuff with `f`
} 
```

直到函数结束时才会执行`f.Close()`。这个功能使创建和清理彼此靠近，而不是屏幕分开。它并不完美，但确实有帮助。

# 在大多数语言中断言相等

最后，大多数语言的单元测试库中的经典等式断言。我会选择 Ruby，但是几乎所有的都有这个

```
require "minitest/autorun"

class MyTest < Minitest::Test
  def test_equality
    a = 1
    b = 2
    assert_equal a, b
  end
end 
```

`assert_equal`这里取两个参数:`expected`，和`actual`。但是哪个是哪个呢？通常*是*预期的，然后是实际的，但也有不真实的例子。

*   [Ruby MiniTest](https://apidock.com/ruby/MiniTest/Assertions/assert_equal) :预期，实际
*   [科特林](https://kotlinlang.org/api/latest/kotlin.test/kotlin.test/assert-equals.html):预期，实际
*   [锈](https://doc.rust-lang.org/std/macro.assert_eq.html):左、右
*   [JavaScript chai](https://www.chaijs.com/api/assert/#method_equal) :实际，预期
*   [Python](https://docs.python.org/2/library/unittest.html#unittest.TestCase.assertEqual) :第一，第二
*   [开始](https://godoc.org/github.com/stretchr/testify/assert#Equal):预期，实际

我不知道你怎么想，但我总要花点时间想想应该怎么做。“不过，这有关系吗？”你可能会问。是的，经常如此。每个测试库都会生成如下形式的测试失败输出:“期望 X 等于 Y。”这并不总是很明显，尤其是当您比较两个函数调用的输出时。

我们能做得更好吗？是啊！

“流畅的”API 在这里做得非常好，并且是许多单元测试框架正在走的路。

这里有一个在 Ruby 中使用 [rspec](http://rspec.info/) 库的例子:

```
require 'spec_helper'

describe "a test" do
  it "should be equal" do
    a = 1
    b = 2
    a.should eql(b)
  end
end 
```

而在 Java 中使用谷歌的[真相](https://google.github.io/truth/)库:

```
int a = 1;
int b = 2;

assertThat(a).isEqualTo(b);
assertThat(a).isLessThan(b); 
```

流畅的 API 读起来更好，更明显的是`actual`总是排在第一位。在 API 设计术语中，界面确实使用户更难犯错误。这是一个很好的 API 设计原则，我们将在以后的文章中探讨。

# 结论

这里没有太多的结论。我希望您能从真实的例子中得到一些启示，我也希望我建议的改进有意义。如果你不同意，或者你认为事情可以*变得更好*，我非常期待在评论中听到这些。:)