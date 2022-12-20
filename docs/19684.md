# 技术用语:TDD

> 原文：<https://dev.to/reduncan/tech-speak-tdd-160b>

TDD？什么是 TDD？TDD 是测试驱动开发的缩写。那么什么是测试驱动开发呢？这是一种实践/编程风格，开发人员不首先编写产品代码，而是为代码中的某个单元编写测试。这些测试涵盖了单元可以返回的结果的所有方面。这些被称为阳性和阴性测试。

TDD 背后的思想是只编写足够失败的单元测试，相反，只编写足够通过所有测试的产品代码。有趣的是，除非需要通过测试，否则不应该编写任何额外的代码。

有许多不同的方法来完成单元测试，但是今天我们将讨论*摩卡*和*柴*。Mocha 是一个功能丰富的 JS 测试框架，运行在 Node.js 上和浏览器中。Chai 是一个断言库，可以与任何 JS 测试框架配对。简单地说，断言库包括验证返回值是否正确的函数。现在让我们看一些代码...

你在一家使用 TDD 的公司工作，他们要求你为一个名为 getGPA 的函数编写测试，该函数接收一组数字并返回平均值。我们必须从需要 chai 和我们将要测试的函数开始。

```
const expect = require('chai').expect;
const getGPA = require('./functions.js').getGPA; 
```

我们需要 chai 和 expect 样式，它允许使用可链接的语言，因此您可以构造断言，并将其存储在一个变量名 expect 中。我们还需要程序中包含我们要测试的函数的文件，并存储在与函数名相同的变量中。

既然我们已经获得了测试所需的工具，我们就可以开始编写测试了...

```
describe('getGPA', function() {

  //Positive Test
  it('should find the average of whole numbers', function() {
  expect(getGPA([2, 4, 3])).to.equal(3);
  });

  it('should find the average of the decimal numbers', function() {
  expect(getGPA([3.4, 2.8, 4.0, 3.9])).to.equal(3.525);
  });

  //Negative Test
  it('should return -1 for numeric non-array inputs', function() {
  expect(getGPA(3.4, 2.8, 4.0, 3.9)).to.equal(-1);
  });

  it('should return -1 for arrays of string', function() {
  expect(getGPA(['hiya', 'pal'])).to.equal(-1);
  });
}); 
```

*   *describe* 是接受你正在测试的函数和测试你的函数的函数。
*   it 是一个函数，它接受一个函数应该做什么的字符串和一个使用 chai 构造的函数。
*   *expect* 接受您正在测试的函数，并与 chai 库中的函数链接。重要的是要注意，当把你的函数传递给*时，期望*把一个参数传递给函数，这样它就可以被测试。
*   函数链的最后一部分是*等于*函数。equal 函数接受函数的结果作为参数。

这就是你如何为你的代码单元编写一个简单的测试。

下一次，我们将关注测试的第二部分，编写测试代码。

直到下次:)