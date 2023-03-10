# 如何测试一个使用 os 的函数？Exec？

> 原文：<https://dev.to/quii/how-to-test-a-function-that-uses-osexec-4m5a>

我已经写了 [Learn Go with tests](https://github.com/quii/learn-go-with-tests) 有一段时间了，在这段时间里，我读到人们问

> 我如何测试`X`？

我认为这将是本书新章节“问答”的一个很好的前提，在那里我将尝试并展示如何测试一段可能会困扰测试新手的代码。

这是它的第一章，如果你有时间请留下一些反馈。

**[你可以在这里找到所有的代码](https://github.com/quii/learn-go-with-tests/tree/master/q-and-a/os-exec)**

[keith6014](https://www.reddit.com/user/keith6014) 在 [reddit](https://www.reddit.com/r/golang/comments/aaz8ji/testdata_and_function_setup_help/) 上提问

> 我正在使用 os/exec 执行一个命令。生成 XML 数据的命令()。该命令将在一个名为 GetData()的函数中执行。
> 
> 为了测试 GetData()，我创建了一些 testdata。
> 
> 在 my _test.go 中，我有一个调用 GetData()的 TestGetData，但它将使用 os.exec，相反，我希望它使用我的 testdata。
> 
> 实现这一点的好方法是什么？当调用 GetData 时，我是否应该有一个“测试”标志模式，这样它将读取一个文件，即 GetData(模式字符串)？

一些事情

*   当某个东西很难测试时，通常是因为关注点的分离不太正确
*   不要在你的代码中添加“测试模式”,而是使用[依赖注入](///dependency-injection.md),这样你就可以建模你的依赖和分离关注点。

我冒昧地猜测了代码可能是什么样子的

```
type Payload struct {
    Message string `xml:"message"`
}

func GetData() string {
    cmd := exec.Command("cat", "msg.xml")

    out, _ := cmd.StdoutPipe()
    var payload Payload
    decoder := xml.NewDecoder(out)

    // these 3 can return errors but I'm ignoring for brevity
    cmd.Start()
    decoder.Decode(&payload)
    cmd.Wait()

    return strings.ToUpper(payload.Message)
} 
```

Enter fullscreen mode Exit fullscreen mode

*   它使用`exec.Command`,允许您对流程执行外部命令
*   我们在`cmd.StdoutPipe`中捕获输出，它返回给我们一个`io.ReadCloser`(这将变得很重要)
*   其余的代码或多或少是从[优秀文档](https://golang.org/pkg/os/exec/#example_Cmd_StdoutPipe)中复制粘贴的。
    *   我们将来自 stdout 的任何输出捕获到一个`io.ReadCloser`中，然后我们`Start`该命令，然后等待通过调用`Wait`读取所有数据。在这两次调用之间，我们将数据解码成我们的`Payload`结构。

这里是包含在`msg.xml`
里面的内容

```
<payload>
    <message>Happy New Year!</message>
</payload> 
```

Enter fullscreen mode Exit fullscreen mode

我写了一个简单的测试来展示它的作用

```
func TestGetData(t *testing.T) {
    got := GetData()
    want := "HAPPY NEW YEAR!"

    if got != want {
        t.Errorf("got '%s', want '%s'", got, want)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 可测试代码

可测试的代码是分离的和单一目的的。对我来说，这段代码有两个主要的问题

1.  检索原始 XML 数据
2.  解码 XML 数据并应用我们的业务逻辑(在本例中是在`<message>`上的`strings.ToUpper`)

第一部分只是从标准库中复制例子。

第二部分是我们的业务逻辑，通过查看代码，我们可以看到逻辑中的“接缝”从哪里开始；这是我们得到`io.ReadCloser`的地方。我们可以使用这个现有的抽象来分离关注点，并使我们的代码可测试。

GetData 的问题是业务逻辑与获取 XML 的方法相结合。为了让我们的设计更好，我们需要将它们解耦

我们的`TestGetData`可以作为我们两个关注点之间的整合测试，所以我们将保持这一点，并确保它继续工作。

下面是新分离的代码的样子

```
type Payload struct {
    Message string `xml:"message"`
}

func GetData(data io.Reader) string {
    var payload Payload
    xml.NewDecoder(data).Decode(&payload)
    return strings.ToUpper(payload.Message)
}

func getXMLFromCommand() io.Reader {
    cmd := exec.Command("cat", "msg.xml")
    out, _ := cmd.StdoutPipe()

    cmd.Start()
    data, _ := ioutil.ReadAll(out)
    cmd.Wait()

    return bytes.NewReader(data)
}

func TestGetDataIntegration(t *testing.T) {
    got := GetData(getXMLFromCommand())
    want := "HAPPY NEW YEAR!"

    if got != want {
        t.Errorf("got '%s', want '%s'", got, want)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

既然`GetData`只从`io.Reader`获取输入，我们就让它可测试，它不再关心如何检索数据；人们可以通过任何返回`io.Reader`(这非常常见)的东西来重用该函数。例如，我们可以从 URL 而不是命令行获取 XML。

```
func TestGetData(t *testing.T) {
    input := strings.NewReader(`
<payload>
    <message>Cats are the best animal</message>
</payload>`)

    got := GetData(input)
    want := "CATS ARE THE BEST ANIMAL"

    if got != want {
        t.Errorf("got '%s', want '%s'", got, want)
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

下面是一个针对`GetData`的单元测试的例子。

通过在 Go 中分离关注点并使用现有的抽象，测试我们的重要业务逻辑变得轻而易举。

您在测试代码中的特定内容时遇到过困难吗？取得联系！