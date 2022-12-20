# 首先使用 API，下一个库使用 TDD

> 原文：<https://dev.to/anwar_nairi/using-api-first-and-tdd-for-your-next-library-49c2>

我使用这种技术来帮助我创建更多相关的单元测试，我想与您分享它。

如果 TDD 对你来说听起来不熟悉，请快速查看这篇文章:

[![eljayadobe](img/21260f63a131a3a7fa0a4c50f207d01b.png)](/eljayadobe) [## TDD 测试

### eljay-Adobe Oct 4 ' 182 分钟读取

#tdd](/eljayadobe/the-tdd-test-56bh)

# TDD 可能不是开始的正确工具

假设我们正在构建一个节点模块来检查一个日期是否在两个日期之间。姑且称之为`is-date-between`。

让我们现在就创建我们的单元测试。

...

听起来很复杂，不是吗？

# API 第一个出手相救

让我们采取另一种方法:给我一个使用这个库的例子。

```
const isDateBetween = require('is-date-between');

const dateToCheck = new Date('2018-12-26');
const pastDate = new Date('2017-01-01');
const futureDate = new Date('2019-01-01');

console.log(isDateBetween(dateToCheck, pastDate, futureDate)); // true 
```

Enter fullscreen mode Exit fullscreen mode

由此我们可以推断出我们想要避免的不同问题:

*   不应恢复过去和未来的日期
*   所有参数都应该是日期对象
*   检查应该是全面的

它将被转录成:

```
const chai = require('chai');
const expect = chai.expect;
const isDateBetween = require('is-date-between');

describe('isDateBetween', function() {
  it('should throw an exception if the past & future dates are reverted', function() {
    expect(function() {
      const dateToCheck = new Date('2018-12-26');
      const pastDate = new Date('2017-01-01');
      const futureDate = new Date('2019-01-01');

      isDateBetween(dateToCheck, futureDate, pastDate);
    }).to.throw('dates are reverted');
  });

  it('should throw an exception if the date to check is not a date object');
  it('should throw an exception if the past date is not a date object');
  it('should throw an exception if the future date is not a date object');
  it('should return true if the date to check is between');
  it('should return false if the date to check is not between');

  describe('inclusive dates', function() {
    it('should return true if the date to check is equal to the past date');
    it('should return true if the date to check is equal to the future date');
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

看，你做到了！

# 下一级:覆盖率&突变检测

测试依赖于人。从定义上来说，我们并不完美，我们可能会忘记，我们可能会犯错误，我们可能不相关或重复...

一些工具可以帮助你限制这一点，直到埃隆·马斯克把我们变成半机械人。

**代码覆盖率**让你检查你的代码的哪一部分没有被测试。这有助于您覆盖最多的案例。我喜欢[纽约](https://www.npmjs.com/package/nyc)，因为它和[摩卡](https://www.npmjs.com/search?q=mocha)搭配得很好。

**突变测试**是一个强大的工具，用来检查你是否测试了代码的正确部分。它会稍微改变你的源文件，并检查一些测试是否失败:如果没有，你可能已经通过了一些测试，而这些测试本来不会让突变通过你的测试。帮助我们在你的单元测试中达到高质量水平，这很酷。我非常喜欢 [stryker](https://www.npmjs.com/package/stryker) 。

# 总之

2019 年的测试不应该是吓人的，而是可及的。我认为我们倾向于把事情弄得比看起来更复杂。我希望 API first 会让你有开始使用测试的欲望，如果你以前从来没有这样做过的话。

测试将需要更多的时间，但是当您扩展更多的特性和修复时，您将会赢得更多的时间。它实际上可以让你对你的产品的下一个版本更有信心，因为你可以依靠测试来建立坚实的基础。

如果你曾经想过

> 最好不要碰这部分代码，这太复杂了，如果我们添加一些东西，可能会出错。

也许这是考虑单元测试的正确时机；).