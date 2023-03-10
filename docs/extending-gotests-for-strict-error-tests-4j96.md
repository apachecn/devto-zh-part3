# 为严格错误测试扩展 Gotests

> 原文：<https://dev.to/jdheyburn/extending-gotests-for-strict-error-tests-4j96>

*这是我在 dev.to 上的第一篇帖子，发自我的新个人博客，可以在[这里](https://jdheyburn.co.uk)找到。希望一旦我解决了这个问题，我就可以让 dev .从 RSS
源发布了！*

很高兴收到您的任何反馈！😃

* * *

# Java 中严格的错误测试

我喜欢通过编写测试和实践测试驱动开发(TDD)来确认我的代码的稳定性。对于 Java，JUnit 是我首选的测试框架。当编写测试来确认一个异常被抛出时，我为注释`@Test`使用了可选参数`expected`，但是我很快发现这个解决方案不适用于我为不同的错误消息多次引发同一个异常类的方法，也不适用于对这些消息的测试。

这在编写验证方法时很常见，比如下面的方法，它会接受一个狗的名字，如果它是有效的，就返回一个布尔值。

```
public static boolean validateDogName(String dogName) throws DogValidationException {

    if (containsSymbols(dogName)) {
        throw new DogValidationException("Dogs cannot have symbols in their name!");
    }

    if (dogName.length > 100) {
        throw new DogValidationException("Who has a name for a dog that long?!");
    }

    return true;
} 
```

对于这种方法，仅仅在我们的测试方法上使用`@Test(expected = DogValidationException.class)`是不够的；我们如何确定异常是因为 dogName.length 违规而引发的，而不是因为包含符号？

为了解决这个问题，我在 [Baeldung](https://www.baeldung.com/junit-assert-exception) 上遇到了 JUnit 的`ExpectedException`类，它使我们能够指定预期的错误消息。这里应用于该方法的测试用例:

```
@Rule
public ExpectedException exceptionRule = ExpectedException.none();

@Test
public void shouldHandleDogNameWithSymbols() {
    exceptionRule.expect(DogValidationException.class);
    exceptionRule.expectMessage("Dogs cannot have symbols in their name!");
    validateDogName("GoodestBoy#1");
} 
```

# 适用于戈朗

回到 Golang，有一个名为`testing`的内置库，它使我们能够断言测试条件。当与 [Gotests](https://github.com/cweill/gotests) 结合使用时——一个从你的代码编写测试中生成 Go 测试的工具，再简单不过了！我喜欢它与我选择的文本编辑器 VSCode 的 Go 扩展捆绑在一起的方式...).

将上面的 Java `validateDogName`方法转换成 Golang 会产生类似于:
的结果

```
func validateDogName(name string) (bool, error) {
    if containsSymbols(name) {
        return false, errors.New("dog cannot have symbols in their name")
    }

    if len(name) > 100 {
        return false, errors.New("who has a name for a dog that long")
    }

    return true, nil
} 
```

如果您有一个返回`error`接口的 Go 方法，那么 gotests 将生成一个如下所示的测试:

```
func Test_validateDogName(t *testing.T) {
    type args struct {
        name string
    }
    tests := []struct {
        name    string
        args    args
        want    bool
        wantErr bool
    }{
        name: "Test error was thrown for dog name with symbols",
        args: args{
            name: "GoodestBoy#1",
        },
        want: false,
        wantErr: true,
    }
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            got, err := validateDogName(tt.args.name)
            if (err != nil) != tt.wantErr {
                t.Errorf("validateDogName() error = %v, wantErr %v", err, tt.wantErr)
                return
            }
            if got != tt.want {
                t.Errorf("validateDogName() = %v, want %v", got, tt.want)
            }
        })
    }
} 
```

从上面我们限制了我们可以断言的错误，这里任何返回的错误都将通过测试。这相当于在 JUnit 中使用`@Test(expected=Exception.class)`！但是还有另一种方法...

## 修改生成的测试

我们只需要对生成的测试进行一些简单的修改，就可以断言测试错误消息...

```
func Test_validateDogName(t *testing.T) {
    type args struct {
        name string
    }
    tests := []struct {
        name    string
        args    args
        want    bool
        wantErr error
    }{
        name: "Test error was thrown for dog name with symbols",
        args: args{
            name: "GoodestBoy#1",
        },
        want: false,
        wantErr: errors.New("dog cannot have symbols in their name"),
    }
    for _, tt := range tests {
        t.Run(tt.name, func(t *testing.T) {
            got, err := validateDogName(tt.args.name)
            if tt.wantErr != nil && !reflect.DeepEqual(err, tt.wantErr) {
                t.Errorf("validateDogName() error = %v, wantErr %v", err, tt.wantErr)
                return
            }
            if got != tt.want {
                t.Errorf("validateDogName() = %v, want %v", got, tt.want)
            }
        })
    }
} 
```

从上面来看，有三个变化，让我们分别看一下:

1.  `wantErr error`
    *   我们从`bool`开始改变它，这样我们就可以与函数返回的错误进行比较
2.  `wantErr: errors.New("dog cannot have symbols in their name"),`
    *   这就是我们所期待的错误结构
3.  `if tt.wantErr != nil && !reflect.DeepEqual(err, tt.wantErr) {`
    *   检查以确保测试是预期的错误，如果是，则将其与返回的错误进行比较

第 3 点提供了额外的支持，如果有一个测试用例没有预料到错误的话。请注意`wantErr`是如何从下面的测试用例中完全省略的。

```
{
    name: "Should return true for valid dog name",
    args: args{
        name: "Benedict Cumberland the Sausage Dog",
    },
    want: true,
} 
```

## 定制 Gotests 生成的测试

Gotests 使我们能够为生成测试提供自己的模板，并且可以很容易地集成到您选择的文本编辑器中。我将向您展示如何在 VSCode 中实现这一点。

1.  检查 gotests 并将模板目录复制到您选择的位置

    *   `git clone https://github.com/cweill/gotests.git`
    *   `cp -R gotests/internal/render/templates ~/scratch/gotests`
2.  用[该要点的内容](https://gist.github.com/jdheyburn/978e7b84dc9c197bcdd41afece2edab5)覆盖 function.tmpl 的内容

3.  将以下设置添加到 VSCode 的 settings.json 中

    *   `"go.generateTestsFlags": ["--template_dir=~/scratch/templates"]`

一旦你这样做了，未来的测试将会产生更严格的错误测试！🎉

# 关闭

我知道上面的建议会使你的代码更加脆弱，因为代码会受到下游库的错误信息的影响。然而对我自己来说，我更喜欢编写严格的测试，尽量减少其他错误污染测试的机会。

我也知道好孩子#1 可能是一只狗的有效名字！🐶