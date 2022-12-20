# 测试不会让你的软件正确

> 原文：<https://dev.to/itamarst/tests-wont-make-your-software-correct-715>

自动化测试非常有用。一旦你开始编写测试，并且看到了它们的价值，那么编写没有测试的软件就变得不可思议了。

但是与任何技术一样，您需要了解它的局限性。当谈到自动化测试时——单元测试、BDD、端到端测试——很容易认为如果你的测试通过了，你的软件就是正确的。

但是测试不会，测试*不能*告诉你你的软件是正确的。我们来看看为什么。

## 如何写出正确的软件

要实现一个特性或缺陷修复，需要经历多个阶段；它们可能被压缩或省略，但它们总是必要的:

1.  识别:找出你试图解决的问题是什么。
2.  **解决方案:**想出解决方案。
3.  **规范:**定义一个规范，即解决方案将如何实现的细节。
4.  **实现:**用代码实现规范。

您的软件可能会在以下任何一点上出现错误:

1.  你可能会发现错误的问题。
2.  你可能会选择错误的解决方案。
3.  您可能会创建一个与解决方案不匹配的规范。
4.  您可能会编写不符合规范的代码。

## 只有人类的判断才能决定正确性

自动化测试也是软件的一种形式，同样容易出错。您的自动化测试通过的事实并不能告诉您您的软件是正确的:您可能仍然识别出了错误的问题，或者选择了错误的解决方案，等等。

即使要确保你的实现符合你的规范，测试本身也不能验证正确性。考虑下面的测试:

```
def test_addition():
    assert add(2, 2) == 5 
```

从代码的角度来看——从一个没有理解能力的自动机的角度来看——正确答案 4 是会导致它失败的答案。但是仅仅通过阅读你就能知道这是错的:你，人类，是关键。

正确是只有一个人才能决定的事情。

## 测试的价值:过程

虽然通过测试不能证明正确性，但是编写测试并使其通过的*过程*可以帮助你的软件变得正确。这是因为编写测试涉及到应用人类的判断:这个测试应该断言什么？是否符合规格？这真的解决了我们的问题吗？

当您经历编写测试、编写代码和检查测试是否通过的循环时，您不断地应用您的判断:代码是错误的吗？测试错了吗？我忘了一个要求吗？

你把测试写在上面，然后重读一遍，然后说“等等那不对，2 + 2 = 4”。您修复它，然后您可能会在您的一次性硬编码测试中添加一些基于核心算术原则的额外测试。正确性来自于过程的应用，而不是过程产生的工件。

这看起来像是卖弄学问:正确性的来源是测试本身还是编写测试的过程有什么关系呢？但这很重要。理解人类的判断是正确的关键可以让你不认为通过测试就足够了:你还需要其他形式的应用人类判断，比如代码审查和手工测试。

(正式方法*用自动化手段增强*人类的判断……但那是[另一个讨论](https://www.hillelwayne.com/post/augmenting-agile/)。)

## 测试值:稳定性

因此，如果正确性来自于编写测试，而不是测试本身，为什么我们还要保留测试呢？

**因为测试保证稳定性。一旦我们判断软件是正确的，测试可以防止软件被修改，从而减少软件出错的机会。测试永远是不够的，因为即使软件没有改变，世界也会改变，但是稳定性有它的价值。**

(如果您使错误的抽象层稳定，稳定性也会有代价……)

## 测试是有用的，但还不够

概括一下:

1.  编写自动化测试。
2.  做那些测试。
3.  不要把通过测试误认为是正确的:你可能需要额外的过程和技术来实现这一点。

* * *

### 我们都会犯错，我有 20 年的经验:从每天凌晨 4 点让生产崩溃的代码，到接受一份糟糕透顶的工作邀请。

### 每一次痛苦的失败都给了我一个教训——但已经太迟了。

### 你可以做得更好！加入 3300 名其他程序员，每周你都会学习如何[避免我的另一个错误](https://codewithoutrules.com/softwareclown/?ref=dev.to)。