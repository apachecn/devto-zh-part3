# 通过测试了解错误类型

> 原文：<https://dev.to/quii/learn-go-with-tests---error-types-4ng9>

这是来自我的书[学习围棋测试](https://github.com/quii/learn-go-with-tests)

**[你可以在这里找到所有的代码](https://github.com/quii/learn-go-with-tests/tree/master/q-and-a/error-types)**

为错误创建自己的类型是整理代码的一种优雅方式，使代码更容易使用和测试。

佩德罗趴在地鼠身上问道

> 如果我创建了一个类似于`fmt.Errorf("%s must be foo, got %s", bar, baz)`的错误，有没有一种不用比较字符串值就能测试相等的方法？

让我们创建一个函数来帮助探索这个想法。

```
// DumbGetter will get the string body of url if it gets a 200
func DumbGetter(url string) (string, error) {
    res, err := http.Get(url)

    if err != nil {
        return "", fmt.Errorf("problem fetching from %s, %v", url, err)
    }

    if res.StatusCode != http.StatusOK {
        return "", fmt.Errorf("did not get 200 from %s, got %d", url, res.StatusCode)
    }

    defer res.Body.Close()
    body, _ := ioutil.ReadAll(res.Body) // ignoring err for brevity

    return string(body), nil
} 
```

Enter fullscreen mode Exit fullscreen mode

编写一个可能因为不同原因而失败的函数并不罕见，我们希望确保正确处理每种情况。

正如 Pedro 所说，我们*可以*像这样写一个状态错误的测试。

```
t.Run("when you dont get a 200 you get a status error", func(t *testing.T) {

    svr := httptest.NewServer(http.HandlerFunc(func(res http.ResponseWriter, req *http.Request) {
        res.WriteHeader(http.StatusTeapot)
    }))
    defer svr.Close()

    _, err := DumbGetter(svr.URL)

    if err == nil {
        t.Fatal("expected an error")
    }

    want := fmt.Sprintf("did not get 200 from %s, got %d", svr.URL, http.StatusTeapot)
    got := err.Error()

    if got != want {
        t.Errorf(`got "%v", want "%v"`, got, want)
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

这个测试创建了一个总是返回`StatusTeapot`的服务器，然后我们使用它的 URL 作为`DumbGetter`的参数，这样我们可以看到它正确地处理了非`200`响应。

## 这种测试方式的问题

这本书试图强调*倾听你的测试*并且这个测试*感觉*不好:

*   我们正在构建与生产代码相同的字符串来测试它
*   读书写字很烦
*   确切的错误信息字符串是我们真正关心的吗？

这告诉我们什么？我们测试的人机工程学将反映在另一段试图使用我们代码的代码上。

我们代码的用户对我们返回的特定类型的错误有什么反应？他们能做的最好的事情就是查看错误字符串，这是一个非常容易出错并且很难写的字符串。

## 我们应该做什么

有了 TDD，我们就有了进入以下思维模式的优势:

> 我将如何使用这个代码？

我们能为`DumbGetter`做的是为用户提供一种使用类型系统来理解发生了什么样的错误的方法。

如果`DumbGetter`可以返回类似于
的东西呢

```
type BadStatusError struct {
    URL    string
    Status int
} 
```

Enter fullscreen mode Exit fullscreen mode

我们有实际的数据来处理，而不是一个神奇的字符串。

让我们改变现有的测试来反映这种需求

```
t.Run("when you dont get a 200 you get a status error", func(t *testing.T) {

    svr := httptest.NewServer(http.HandlerFunc(func(res http.ResponseWriter, req *http.Request) {
        res.WriteHeader(http.StatusTeapot)
    }))
    defer svr.Close()

    _, err := DumbGetter(svr.URL)

    if err == nil {
        t.Fatal("expected an error")
    }

    got, isStatusErr := err.(BadStatusError)

    if !isStatusErr {
        t.Fatalf("was not a BadStatusError, got %T", err)
    }

    want := BadStatusError{URL:svr.URL, Status:http.StatusTeapot}

    if got != want {
        t.Errorf("got %v, want %v", got, want)
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

我们必须让`BadStatusError`实现错误接口。

```
func (b BadStatusError) Error() string {
    return fmt.Sprintf("did not get 200 from %s, got %d", b.URL, b.Status)
} 
```

Enter fullscreen mode Exit fullscreen mode

### 测试做什么？

我们不是检查错误的确切字符串，而是对错误做一个[类型断言](https://tour.golang.org/methods/15)，看看它是否是一个`BadStatusError`。这反映了我们对错误更清晰的*类*的渴望。假设断言通过，那么我们可以检查错误的属性是否正确。

当我们运行测试时，它告诉我们没有返回正确的错误类型

```
-------- FAIL: TestDumbGetter (0.00s)
    --- FAIL: TestDumbGetter/when_you_dont_get_a_200_you_get_a_status_error (0.00s)
        error-types_test.go:56: was not a BadStatusError, got *errors.errorString 
```

Enter fullscreen mode Exit fullscreen mode

让我们通过更新我们的错误处理代码来使用我们的类型
来修复`DumbGetter`

```
if res.StatusCode != http.StatusOK {
    return "", BadStatusError{URL: url, Status: res.StatusCode}
} 
```

Enter fullscreen mode Exit fullscreen mode

这一变化产生了一些真正的积极影响

*   我们的`DumbGetter`函数变得更加简单，它不再关心错综复杂的错误字符串，它只是创建一个`BadStatusError`。
*   我们的测试现在反映(并记录)了我们代码*的用户可以做什么，如果他们决定要做一些比日志更复杂的错误处理。只需做一个类型断言，然后就可以很容易地访问错误的属性。*
*   它仍然“只是”一个`error`，所以如果他们愿意，他们可以将它向上传递给调用堆栈，或者像其他`error`一样记录它。

## 包装完毕

如果您发现自己在测试多个错误条件，不要陷入比较错误消息的陷阱。

这导致了不可靠和难以读/写的测试，并且它反映了你的代码的用户将会遇到的困难，如果他们也需要根据已经发生的错误的种类开始做不同的事情。

一定要确保你的测试反映了你希望如何使用你的代码，所以在这方面考虑创建一个错误类型来封装你的错误。这使得代码用户更容易处理不同类型的错误，也使得编写错误处理代码更简单、更易读。