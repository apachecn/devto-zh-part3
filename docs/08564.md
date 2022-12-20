# 你的单元测试应该是不可变的

> 原文：<https://dev.to/jlouzado/tdd-your-unit-tests-should-be-immutable-119m>

# 图片本...

*   您的项目从一开始就遵循 TDD
*   你的任务是重构一个函数(比如说，提高性能)
*   您做了一个相对较小的更改，然后运行测试
*   您对那个单元的测试通过了，但是一些随机组件的单元测试却失败了！
    *   “到底发生了什么，这些组件甚至没有关联！”我听到你说。

要修复它，你需要改变那些不相关的单元中的测试，这是一个大问题。你的测试需要对重构保持*不变，上面的场景是一个`Test Smell`。*

# 为什么这是个问题？

TDD 有很多好处，但其中一个好处是:

> 一个好的测试套件会给你对系统行为的信心。

然而，工具并不能保证这种信心，它来自于你遵循 TDD 的事实。你写了一个测试，它失败了，然后你写了一些代码，它通过了。因此，该代码是有效的。

这意味着你信任你的代码库的唯一原因是因为你信任你的开发伙伴也遵循 TDD。也就是说，依赖你的测试套件不是事实，而是信任。

因此，一旦测试套件存在，我们需要通过最小化对它的更改来保护我们对它的信任。

回到我们之前假设的情况，重构(根据定义)不应该是行为上的改变。如果您现在被迫在不相关的单元中更新测试代码，您现在需要在系统中恢复信任之前*手动*验证那些单元和(它们的所有用法)。

# 问题原因

例如，您的单元最初设置了一个内部状态的`canProceed`，您将其重构为`shouldProceed`。这些其他单位之所以失败，是因为它们坚持了`canProceed`的价值。

如果你进入那些失败的测试，并用新的假设更新测试，那么*极有可能*出错，你可能断言`false`而不是`true`或任何数量的其他问题。您甚至可能会不小心删除一个测试(在更复杂的场景中)。最糟糕的是，你的测试套件不能把你从这些错误中拯救出来，因为它不能自测；如果测试实现给你一个假阳性，绿灯并不意味着什么。

# 期望的行为

改变测试的唯一原因应该是系统所需的行为发生了变化。要么:

*   您的实现中有一个需要修复的错误，或者
*   有一个需求是[突破性的变化](https://semver.org/)

> 测试并不能保证代码没有错误，它们只是为了实现更快的反馈。

在所有其他情况下，我们期望测试是`Immutable`的，也就是说，重构不需要改变测试。

# 实现测试的不变性

因为我们的测试套件没有测试套件，所以在实现时遵循干净代码指南的压力越来越大。一旦测试被编写，意图就是它们应该被封装在 ember 中，所以我们应该致力于第一次就把它做好。

以下是一些建议:

## 明确地命名你的测试

坏:`test('should set correct headers')`

好:`test('should set the 'device' header to 'mobile')`

如果您正在编写测试名称，并且它运行得有点长，那么您可能试图测试太多的东西，或者可能有太多的状态值要列出来。

没关系，喜欢一个明确的名字而不是一个简短的句子。至少很清楚。不管怎样，你可以在下一点解决这个问题。

## 使用领域语言来命名你的测试

### 使用与你的用户相关的术语

*   坏:`$1.00 - $2000.00 = No Interest`
*   好:`Loans of $2000 and below are interest-free`

### 描述测试名称中的预期行为，而不是`expected`结果中的预期行为

*   过得去:

```
test('$2001.00 Loan Amount', () => {
  assert.strictEqual(
    calculateInterest('$2001.00' /*actual*/, '$0.09' /*expected*/)
  )
}) 
```

Enter fullscreen mode Exit fullscreen mode

*   更好:

```
test('A loan of $2001.00 owes $0.09 interest', () => {
  assert.strictEqual(
    calculateInterest('$2001.00' /*actual*/, '$0.09' /*expected*/)
  )
}) 
```

Enter fullscreen mode Exit fullscreen mode

你的单元测试也可以作为文档；让他们尽可能地对其他可能没有你现在所拥有的上下文的开发人员可读。

## 从语义上对测试进行分组

*   阅读 [RSpec]((https://lmws.net/describe-vs-context-in-rspec))
    *   使用`describe`来表示您正在测试的行为，
    *   使用`context`根据共同的假设或世界状态对测试进行分组

```
describe('launch the rocket', () => {
  context('all ready', () => {
    test('should launch rocket')
  })
  context('not ready', () => {
    test('should do nothing')
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

*   您的测试语句最好读起来像文档，
    *   这有一个额外的好处，就是让你的测试更容易审查
*   以这种方式分解也避免了长测试名
    *   在重构过程中，定位和调试失败的测试也变得更加容易

## 每次测试单个断言

*   一旦你的测试名称是离散的，每个测试只有一个`assert`
*   如果多个断言是“必要的”,那么您可能试图检查多个东西
    *   缺点是这些额外的东西没有记录在测试名中
    *   明天那些额外的断言可能会被删除，因为它们看起来并不重要
*   避免所有这些戏剧性事件，只需在每个测试中保持一个断言。

## 最小化测试间的数据共享

您可能想开始定义公共对象，并在测试中重用它们，但请抵制这种冲动。

*   这看起来像是你在跟着[干](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)，但是这使得你的测试可读性更差
*   它也可能无意中在你的测试代码中引入错误
*   如果某个东西真的如此通用，以至于可以跨测试包含，那么就用它做一个库
    *   并且确保对你使用的任何库进行 100%的测试覆盖。

## 不断言一个对象

以下面的代码为例:

```
describe('createRequest', () => {
  test('set correct headers', () => {
    const actual = createRequest(
      { _csrf: 'abcd', __csrf: 'pqrst' }, // cookies
      {
        // params
        url: 'AAA',
        variables: { A: 1, B: 2 },
      }
    )
    const expected = {
      'X-CSRF': 'pqrst',
      'content-type': 'application/json',
      device: 'pwa',
    }
    assert.deepEqual(actual.headers, expected)
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

“这看起来很好”，我听到你说，“有什么问题？”。嗯，这基本上只是每个测试有多个断言的隐藏版本。

*   这里有三个断言:
    *   一个用于`X-CSRF`标题，
    *   一个用于`content-type`标题，以及
    *   一个给`device`。
*   更糟糕的是，测试标题没有给出任何关于期望输出是什么的信息。

像这样重构你的测试:

```
describe('createRequest', () => {
  describe('headers', () => {
    test('should set X-CSRF header correctly', () => {
      const actual = createRequest(
        {_csrf: 'abcd', __csrf: 'pqrst'}, //cookies
        {
          //params
          ...
        }
      )
      const expected = 'pqrst'
      assert.equal(actual.headers['X-CSRF'], expected)
    })
    test('should set `content-type` correctly', () => {
      const actual = createRequest(
        {}, //cookies
        {
          //params
          ...
        }
      )
      const expected = 'application/json'
      assert.equal(actual.headers['content-type'], expected)
    })
    test('should set `device` header correctly', () => {
      const actual = createRequest(
        {}, //cookies
        {
          //params
          ...
        }
      )
      const expected = 'pwa'
      assert.equal(actual.headers.device, expected)
    })
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

## 比起你的应用程序代码，更仔细地检查你的测试代码

*   您的测试基本上包含了所有关于新组件设计的信息
    *   不仅是它的行为，还有它的接口和依赖关系
*   如果测试代码是可靠的、写得好的、详尽的，那么重构应用程序代码将是微不足道的！
    *   如果测试中有差距，那只会在集成测试(或生产)中感觉到
*   这是一个过程，如何实施取决于团队

## 管好你的分公司

*   解决测试代码中的合并冲突时要格外小心
*   同样，测试代码不能自我测试。
*   如果对现有的测试进行了更改，它通过的事实并不能证明什么。

# 总结

最终维护测试套件是一个纪律、代码指南和良好的代码审查过程的问题，也是一个在信任的环境中的一组可靠的团队成员的问题。

作为个体开发人员，我认为如果我们要成为任何事情的工匠，我们应该致力于测试代码。我们未来的自己会为此感谢我们。

祝你好运。:)

# 进一步阅读

*   [你不知道 TDD](https://itnext.io/you-dont-know-tdd-691efe670094)
    *   在实现放债者示例时，这 4 部分系列将深入探讨。
    *   TDD 无穷迷人；甚至解决编译错误也是一个[红色/绿色/重构](https://martinfowler.com/bliki/TestDrivenDevelopment.html)的循环
    *   这一系列的帖子确实是美丽的见证，强烈推荐。:)
*   [更多的输入，更少的测试|静态类型的 TDD](https://spin.atomicobject.com/2014/12/09/typed-language-tdd-part1/)
    *   在这个讨论中加入静态类型会让一切都变得过度。
    *   基本上，它在[红绿重构](https://martinfowler.com/bliki/TestDrivenDevelopment.html)循环中增加了一个`Add Types`步骤
    *   类型有助于创建更具体的接口。

# 参考文献

*   [UncleBob 期待专业](https://youtu.be/BSaAMQVq01E?t=2105) (35:05 - 41:40)
*   [单元测试气味](https://dzone.com/articles/unit-testing-smells-what-are-your-tests-telling-yo)
*   [TDD 完成正确——测试不可变重构](https://developer20.com/tdd-done-right/)

> 最初发布于[blog.patchcording.com](https://blog.patchcording.com/2019-03-24-tdd-immutable-tests/)