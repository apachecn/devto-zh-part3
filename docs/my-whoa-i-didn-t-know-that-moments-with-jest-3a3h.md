# 我的“哇，我不知道！”玩笑时刻

> 原文：<https://dev.to/briwa/my-whoa-i-didn-t-know-that-moments-with-jest-3a3h>

Jest 一直是我的首选单元测试工具。它如此强大，我开始认为我一直没有充分利用它。虽然测试通过了，但随着时间的推移，我在这里和那里重构了我的测试，因为我不知道 Jest 可以做到这一点。每次我查看 Jest 文档都是不同的代码。

所以，我将与 Jest 分享一些我最喜欢的技巧，你们中的一些人可能已经知道了，因为你们没有像我一样跳过阅读这些文档(真可耻)，但我希望这对那些跳过的人有所帮助！

FWIW，我使用 Jest v24.8.0 作为参考，所以请注意，如果某些东西在您当前使用的 Jest 版本上不起作用。同样，这些例子并不代表实际的测试代码，它仅仅是一个演示。

### #1。`.toBe`对`.toEqual`

起初，所有这些主张对我来说都很好

```
expect('foo').toEqual('foo')
expect(1).toEqual(1)
expect(['foo']).toEqual(['foo']) 
```

Enter fullscreen mode Exit fullscreen mode

姚军利用[柴](https://www.chaijs.com/api/assert/)来做等式断言(`to.equal`)，这很自然。事实上，Jest 不会抱怨，这些断言会像往常一样过去。

但是，Jest 有`.toBe`和`.toEqual`。前者用于使用 [`Object.is`](https://github.com/facebook/jest/blob/v24.8.0/packages/expect/src/matchers.ts#L76) 断言相等，后者用于断言对象和数组上的深度相等。现在，`.toEqual`可以使用 [`Object.is`](https://github.com/facebook/jest/blob/v24.8.0/packages/expect/src/jasmineUtils.ts#L91) ，如果事实证明它不需要深度相等，比如在原始值上断言相等，这解释了为什么前面的例子通过得很好。

```
expect('foo').toBe('foo')
expect(1).toBe(1)
expect(['foo']).toEqual(['foo']) 
```

Enter fullscreen mode Exit fullscreen mode

因此，如果你已经知道你正在测试什么样的值，你可以通过使用`.toBe`跳过`.toEqual`中的所有假设。

一个常见的错误是使用`.toBe`断言非原始值相等。

```
expect(['foo']).toBe(['foo']) 
```

Enter fullscreen mode Exit fullscreen mode

如果你查看[的源代码](https://github.com/facebook/jest/blob/v24.8.0/packages/expect/src/matchers.ts#L90)，当`.toBe`失败时，它会通过调用[被`.toEqual`使用的函数](https://github.com/facebook/jest/blob/v24.8.0/packages/expect/src/matchers.ts#L562)来尝试查看你是否确实犯了那个错误。这可能是优化测试时的一个瓶颈。

如果您确定您断言的是原始值，那么您的代码可以这样重构，以达到优化的目的:

```
expect(Object.is('foo', 'foo')).toBe(true) 
```

Enter fullscreen mode Exit fullscreen mode

查看[文档](https://jestjs.io/docs/en/expect#tobevalue)中的更多详细信息。

### #2。更合适的媒人

从技术上讲，您可以使用`.toBe`来断言任何类型的值。使用 Jest，您可以特别使用某些匹配器，使您的测试更具可读性(在某些情况下，更简短)。

```
// 🤔
expect([1,2,3].length).toBe(3)

// 😎
expect([1,2,3]).toHaveLength(3) 
```

Enter fullscreen mode Exit fullscreen mode

```
const canBeUndefined = foo()

// 🤔
expect(typeof canBeUndefined !== 'undefined').toBe(true)

// 🤔
expect(typeof canBeUndefined).not.toBe('undefined')

// 🤔
expect(canBeUndefined).not.toBe(undefined)

// 😎
expect(canBeUndefined).toBeDefined() 
```

Enter fullscreen mode Exit fullscreen mode

```
class Foo {
  constructor(param) {
    this.param = param
  }
}

// 🤔
expect(new Foo('bar') instanceof Foo).toBe(true)

// 😎
expect(new Foo('bar')).toBeInstanceOf(Foo) 
```

Enter fullscreen mode Exit fullscreen mode

这些只是我从[文档](https://jestjs.io/docs/en/expect)中的一长串笑话匹配器中挑选出来的几个，你可以看看其余的。

### #3。对非 UI 元素进行快照测试

你可能听说过关于[快照测试的笑话](https://jestjs.io/docs/en/snapshot-testing)，它帮助你监控 UI 元素的变化。但是快照测试并不局限于此。

考虑这个例子:

```
const allEmployees = getEmployees()
const happyEmployees = giveIncrementByPosition(allEmployees)

expect(happyEmployees[0].nextMonthPaycheck).toBe(1000)
expect(happyEmployees[1].nextMonthPaycheck).toBe(5000)
expect(happyEmployees[2].nextMonthPaycheck).toBe(4000)
// ...etc 
```

Enter fullscreen mode Exit fullscreen mode

如果你不得不雇用越来越多的员工，那就太乏味了。此外，如果结果是每个雇员有更多的断言要做，用雇员数乘以新断言的数量，您就明白了。

通过快照测试，所有这些都可以简单地完成:

```
const allEmployees = getEmployees()
const happyEmployees = giveIncrementByPosition(allEmployees)

expect(happyEmployees).toMatchSnapshot() 
```

Enter fullscreen mode Exit fullscreen mode

无论何时出现回归，您都会确切地知道节点中的哪棵树与快照不匹配。

现在，这种便利是有代价的:它更容易出错。您可能不知道快照实际上是错误的，但最终还是会提交它。因此，仔细检查您的快照，就好像它是您自己的断言代码一样(因为它就是)。

当然，快照测试还有更多的内容。查看完整的[文档](https://jestjs.io/docs/en/snapshot-testing)。

### #4。`describe.each`和`test.each`

您是否编写过类似的测试？

```
describe('When I am a supervisor', () => {
  test('I should have a supervisor badge', () => {
    const employee = new Employee({ level: 'supervisor' })

    expect(employee.badges).toContain('badge-supervisor')
  })

  test('I should have a supervisor level', () => {
    const employee = new Employee({ level: 'supervisor' })

    expect(employee.level).toBe('supervisor')
  })
})

describe('When I am a manager', () => {
  test('I should have a manager badge', () => {
    const employee = new Employee({ level: 'manager' })

    expect(employee.badges).toContain('badge-manager')
  })

  test('I should have a manager level', () => {
    const employee = new Employee({ level: 'manager' })

    expect(employee.level).toBe('manager')
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

这是刻意重复，对不对？想象用更多的案例来做。

使用`describe.each`和`test.each`，您可以将代码压缩如下:

```
const levels = [['manager'], ['supervisor']]
const privileges = [['badges', 'toContain', 'badge-'], ['level', 'toBe', '']]

describe.each(levels)('When I am a %s', (level) => {
  test.each(privileges)(`I should have a ${level} %s`, (kind, assert, prefix) => {
    const employee = new Employee({ level })

    expect(employee[kind])[assert](`${prefix}${level}`)
  })
}) 
```

Enter fullscreen mode Exit fullscreen mode

然而，我还没有在我自己的测试中实际使用它，因为我更喜欢我的测试是冗长的，但是我只是认为这是一个有趣的技巧。

查看[文档](https://jestjs.io/docs/en/api#describeeachtable-name-fn-timeout)了解更多关于参数的细节(剧透:表格语法真的很酷)。

### #5。嘲笑全局函数一次

在某些时候，你必须测试一些依赖于特定测试用例上的全局函数的东西。例如，使用 Javascript 对象`Date`获取当前日期信息的函数，或者依赖于它的库。棘手的部分是，如果它是关于当前日期的，您永远无法得到正确的断言。

```
function foo () {
  return Date.now()
}

expect(foo()).toBe(Date.now())
// ❌ This would throw occasionally:
// expect(received).toBe(expected) // Object.is equality
// 
// Expected: 1558881400838
// Received: 1558881400837 
```

Enter fullscreen mode Exit fullscreen mode

最终，您必须覆盖`Date`全局对象，以便它是一致的和可控的:

```
function foo () {
  return Date.now()
}

Date.now = () => 1234567890123

expect(foo()).toBe(1234567890123) // ✅ 
```

Enter fullscreen mode Exit fullscreen mode

然而，这被认为是一种不好的做法，因为覆盖在测试之间持续存在。如果没有其他依赖于`Date.now`的测试，你不会注意到它，但是它正在泄漏。

```
test('First test', () => {
  function foo () {
    return Date.now()
  }

  Date.now = () => 1234567890123

  expect(foo()).toBe(1234567890123) // ✅
})

test('Second test', () => {
  function foo () {
    return Date.now()
  }

  expect(foo()).not.toBe(1234567890123) // ❌ ???
}) 
```

Enter fullscreen mode Exit fullscreen mode

我曾经用一种不会泄露的方式‘黑’过它:

```
test('First test', () => {
  function foo () {
    return Date.now()
  }

  const oriDateNow = Date.now
  Date.now = () => 1234567890123

  expect(foo()).toBe(1234567890123) // ✅
  Date.now = oriDateNow
})

test('Second test', () => {
  function foo () {
    return Date.now()
  }

  expect(foo()).not.toBe(1234567890123) // ✅ as expected
}) 
```

Enter fullscreen mode Exit fullscreen mode

然而，有一种更好、更简单的方法可以做到这一点:

```
test('First test', () => {
  function foo () {
    return Date.now()
  }

  jest.spyOn(Date, 'now').mockImplementationOnce(() => 1234567890123)

  expect(foo()).toBe(1234567890123) // ✅
})

test('Second test', () => {
  function foo () {
    return Date.now()
  }

  expect(foo()).not.toBe(1234567890123) // ✅ as expected
}) 
```

Enter fullscreen mode Exit fullscreen mode

总之，`jest.spyOn`监视全局`Date`对象并模仿`now`函数的实现，只为了一次调用。这反过来会使`Date.now`在剩余的测试中保持不变。

关于开玩笑的话题，肯定有更多的内容。请务必查看完整的[文档](https://jestjs.io/docs/en/mock-functions)了解更多详情。

* * *

这篇文章越来越长，我想就写到这里吧。这些仅仅是 Jest 功能的皮毛，我只是强调了我的最爱。如果你有其他有趣的事实，也让我知道。

此外，如果你经常使用 Jest，请查看一下 [Majestic](https://github.com/Raathigesh/majestic) ，这是 Jest 的一个零配置 GUI，是从枯燥的终端输出中逃离的一个好方法。我不确定作者是否在 dev.to 中，但是对着那个人大声喊出来。

一如既往，感谢您阅读我的帖子！

* * *

封面图片来自[https://jestjs.io/](https://jestjs.io/)