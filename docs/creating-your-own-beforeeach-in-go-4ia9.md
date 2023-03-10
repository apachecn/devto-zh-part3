# 在 Go 中创建自己的 BeforeEach()

> 原文：<https://dev.to/taylorsilva/creating-your-own-beforeeach-in-go-4ia9>

我正在使用 Go 开发自己的预算应用程序，并且一直在用它学习新东西。对于测试，因为我在 TDD'ing 整个项目，所以我决定只使用内置的 Go 测试框架。我在工作中使用银杏，并喜欢它，但决定看看 go 中的香草测试是什么样的。

在为我的许多样板函数编写了一堆测试之后，我很快就有了许多重复的设置代码，在我所有测试的开始看起来是这样的:

```
g := NewGomegaWithT(t)

db, err := createTestDb()
g.Expect(err).To(BeNil(), ErrorDidNotOccur)

api := NewApi(db, SelectedQueryLang) 
```

我很难搞清楚如何把它变成自己的功能。最后我看到了一个堆栈溢出的回答，为我提供了一个解决方案(现在找不到链接了！).

以下是在使用 Go 的测试框架时，如何创建自己的`BeforeEach()`函数。

首先，创建一个`struct`来保存您的测试需要访问的对象。我的长这样:

```
type TestingBasics struct {
    GoMega *GomegaWithT
    Db     *sql.DB
    Api    *api
} 
```

然后创建您的`BeforeEach()`函数，它将为您的测试创建这个结构。

```
func BeforeEach(t *testing.T, SelectedQueryLang *QueryLang) (tb TestingBasics) {
    tb.GoMega = NewGomegaWithT(t)

    var err error
    tb.Db, err = createTestDb()
    tb.GoMega.Expect(err).To(BeNil(), ErrorDidNotOccur)

    tb.Api = NewApi(tb.Db, SelectedQueryLang)

    return tb
} 
```

现在你需要做的就是在测试开始时调用`BeforeEach()`。在我所有的**测试中，我能够将 4 行代码减少到 1 行。现在，我的每个测试的开始看起来像这样:** 

```
t.Run("should add one transaction", func(t *testing.T) {
    tb := BeforeEach(t, SelectedQueryLang)
... 
```

这肯定可以进一步清理，以至于我甚至不需要在每次测试开始时调用`BeforeEach()`。不过，在那种情况下，我还不如选择银杏。对我和这个项目来说，我对这个设置很满意。