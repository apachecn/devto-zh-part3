# 我第一次去 LoC 已经三个月了🤓 🎓

> 原文：<https://dev.to/biros/its-been-three-months-since-my-first-go-loc----419d>

我用 Golang 写第一行代码已经 3 个月了。

[![biros image](img/f8e520c158b11124ad64d790d5a94ecc.png)](/biros) [## 我从 PHP 到 Go 的旅程

### 鲍里斯·贾莫特·✊/9 月 20 日 184 分钟阅读

#php #go #feedback](/biros/my-journey-from-php-to-go-55bd)

我仍然对学习这门新语言感到兴奋，但似乎我面临了我的第一个围棋问题。这个我以后再说。

在我之前的一篇文章中，我说 PHP 是我的主要语言，我不会明确地转而使用它。也许这可以改变...

我真的很喜欢打字和编译。我可以对我的代码进行实时反馈，而不需要运行它，也不需要额外的第三方工具。Go SDK 在林挺、格式化、测试、编译和运行方面功能齐全。

# 从请求体中读取

在我之前的帖子中，我也说过 Go 是工匠的语言。但这与事实相去甚远。我举一个例子:在 Go 中，如果你想在一个中间件中读取 HTTP 请求的主体来分析它以便记录日志，这就变得很棘手:

```
func LoggingMiddleware(handler http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        // first, read the body and put it in a variable
        body, err := ioutil.ReadAll(r.Body)
        if err != nil {
            log.Printf("Error reading body: %v", err)
            http.Error(w, "can't read body", http.StatusBadRequest)
            return
        }

        log.Printf("incoming request's body: %s", body)

        // And now, re-assign the request's body with the original data
        r.Body = ioutil.NopCloser(bytes.NewBuffer(body))

        // finally, call next handler
        handler.ServeHTTP(mrw, r)
    })
} 
```

正如您可能猜到的那样，读取请求的正文会使它变得空。所以事后要重新分配。这是因为请求体是一种你可以一个字节接一个字节读取的流。如果您想在不读取所有数据的情况下开始处理请求，这可能会很有用。但是在大多数情况下，你不需要那样。

# (取消)-编组

(不)编组是另一件不简单的事情。你需要知道的是，从 JSON、BSON、YAML 或者其他什么到 struct 的解组几乎不会失败。

```
package main

import "encoding/json"
import "fmt"

type User struct {
    Name string `json:"name"`
    Age int `json:"age"`
}

func main() {
    var bob, alice User
    json.Unmarshal([]byte(`{"name":"bob","age":25}`), &bob)
    err := json.Unmarshal([]byte(`{"name":"alice","address":"NY"}`), &alice)

    fmt.Printf("err: %v\n", err)
    fmt.Printf("bob: %v\n", bob)
    fmt.Printf("alice: %v\n", alice)
} 
```

输出:

```
err: <nil>
bob: {bob 25}
alice: {alice 0} 
```

在这个例子中，我省略了 Alice 的 JSON 中的`age`属性，但是去解组它，没有错误。迷惑？这是因为 Go 为所有缺失的属性分配了默认值。事实上，你只需要提供有效的 JSON。

*注意，补充的 JSON 属性被忽略了。*

真正让我困惑的是，我预计解组会因缺少属性而失败。但是如果你想检查你在 JSON 中真正得到了什么，你不能相信解组，你必须自己通过手工检查你的结构的所有字段来完成。如果其中一个字段具有其类型的默认值(例如 0 表示整数，或者空字符串)，那么这可能意味着它在 JSON 中丢失了。但这也可能意味着客户端发送了`{"name":"alice","age":0}`。那么，如何区分缺失属性和有默认值的属性呢？您可以解组到一个`map[string]interface{}`并手动检查没有键丢失，但这将是什么？！

如果有人知道更好的解决方案，不要犹豫！

# 单元测试

编写单元测试是我在 Golang 的开发者之旅中最糟糕的部分。作为一名 PHP 和 Java 的 OOP 开发实验者，我曾经编写了大量的单元测试来获得我所能达到的最好的代码覆盖率。为了实现这一点，我设计了大量使用依赖注入的代码，以允许我在单元测试中用模拟来替换依赖。

在 Go 中，没有 PHPUnit 或 JUnit 的等价物允许快速模仿任何依赖。Go 中的嘲讽是基于接口的。如果你在代码的某个地方使用一个结构作为依赖，你必须为它写一个自定义接口。这就是围棋缺乏一致性的地方。根据你使用的库，如果你幸运的话，它会提供一个接口而不是一个结构(对于[gin-gonic](https://github.com/gin-gonic/gin)&[Uber-go/zap](https://github.com/uber-go/zap)就是这种情况)。但否则，你可能会遭遇嘲讽的地狱(这就是 [mgo mongo 车手](https://github.com/globalsign/mgo)的情况)。

为依赖注入编写接口的缺点是会丢失 IDE 中的完整性。我来解释一下:当你为第三方库写一个接口的时候，你只是把你需要的功能放进去。这意味着，如果有一天你想使用该库的另一个函数，你甚至不知道可用的函数是什么。您不能在对象上按 ctrl+Space 来自动完成，因为它将只显示您的自定义界面的功能，而不是原始库对象的功能。所以你必须在网上浏览官方文件。

知道了这一点，我建议更喜欢“支持嘲讽”的库。这会节省你的时间！

## 嘲讽你的依赖

根据您的测试策略，您可能必须模拟代码的某些部分，或者某些外部库。如前所述，第一件事是在代码中使用接口而不是结构，无论是在函数的原型中还是在依赖注入容器中。

原理非常简单:您只需用实现您想要模仿的接口的结构来切换真实对象。您可以手动完成，或者如果您想要一个全功能的模拟，您可以使用 [gomock](https://github.com/golang/mock) 来完成，允许您计算调用的数量，检查类型，甚至捕获参数。gomock 的唯一缺点是，您必须手动生成模拟，并在每次修改接口时更新它们。您还必须提交模拟代码以及您的“真实”代码。

# 我的定制开发环境

在我关于 Go 的第一篇文章中，我说我尝试了很多 IDE: Atom、Intellij Ultimate 和 Vim。在那之后，我也尝试了 Visual Studio 代码，它们都很不错，但是没有一个让我完全满意。因此，随着我的许多同事使用 Intellij，我给了它一个新的尝试，并最终成功地使它工作。我用它来写代码，也用来调试和浏览代码。

除了 Intellij，我还有一个使用 tmux 的定制终端，有 3 个面板。其中一个窗格是用来玩 git 或输入任何其他命令的。一个致力于 vim。最后一个是运行我的应用程序，检查我的配置并运行测试，所有这些都可以通过文件监视器 [modd](https://github.com/cortesi/modd) 完成:

*   对代码库的任何修改都会触发我的应用程序的新运行(`go run ./...`)，
*   对测试文件的任何修改都会触发测试(`go test ./...`)
*   对我的 conf 文件的任何修改都会触发一个新的检查，使用的是 JSON/YAML 验证器。第三个窗格是一个 Vim，允许我快速编辑文件。

# 结论

作为一名 PHP 开发人员，我最怀念的是社区和在线资源。除了 StackOverflow 上的帖子，似乎官方 Go doc(这很棒)是唯一可用的资源。
同样令人困惑的是，如果你谷歌“go + stuff”之类的东西，你不会得到很多结果。有了“golang + stuff”你运气会更好。

不管怎样，这些障碍并没有阻碍我的动力和我的热情。我喜欢这种简单的语言。

我会在以后的帖子中让你知道我的进展。

感谢阅读！