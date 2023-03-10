# 测试覆盖率是一个谎言

> 原文：<https://dev.to/smizell/test-coverage-is-a-lie-3i84>

我有一些关于测试覆盖率的坏消息——它没有告诉我们有多少代码被测试覆盖，尽管它的名字说它被覆盖了。它不能帮助我们提高软件的质量。它不能帮助我们找到 bug。感觉是骗人的。也许确实如此。

## 什么是测试覆盖率？

Wikipedia 对测试覆盖率的定义并不深刻——尽管在我看来是正确的。

> 在计算机科学中，测试覆盖率是一种度量，用于描述在特定测试套件运行时程序源代码的执行程度。

正如这里所定义的，测试覆盖率并没有告诉我们代码中的哪一行已经被测试了。它没有告诉我们是否已经测试了所有可能的条件。它只告诉测试运行期间执行了哪些代码行。

这个定义没有提到质量，尽管它继续谈到了 bug。

> 一个测试覆盖率高的程序，以百分比来衡量，在测试期间执行了更多的源代码，这表明与测试覆盖率低的程序相比，它包含未检测到的软件错误的机会更低。

这个想法是更多的覆盖率与更少的 bug 相关联。不管正确与否，这与使用覆盖率作为工具来发现现有的 bug 或者阻止 bug 进入生产环境是不同的。它只是告诉我们，由于测试覆盖范围，出现错误的可能性更低。

## 测试覆盖的原因在于

让我们看一个简短的例子，看看测试覆盖率是如何给我们一个代码测试良好的错觉的。考虑下面的代码。它有四个分支要测试。

```
// This function always returns 12
// ...or does it?
exports.alwaysTwelve = function(a, b) {
  let y, z;

  if (a === true) y = 4;
  else y = 6;

  if (b === true) z = 3;
  else z = 2;

  return y * z;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里有一些测试。

```
const assert = require('assert');
const { alwaysTwelve } = require('.');

describe('Always Twelve', function () {
  context('when true', function () {
    it('returns 12', function () {
      const value = alwaysTwelve(true, true);
      assert.equal(value, 12);
    });
  });

  context('when false', function () {
    it('returns 12', function () {
      const value = alwaysTwelve(false, false);
      assert.equal(value, 12);
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码和测试产生了 100%的测试覆盖率，因为这两个测试足以覆盖代码中的四个分支。然而，它们不足以涵盖应用程序代码中所有可能的路径。它们不能帮助我们发现代码逻辑中的明显错误。

我们可以计算执行代码中所有可能路径所需的测试数量。可能的路径是取 2 <sup>n</sup> 的结果，其中`n`是代码中条件的数量。因此，在上面有两个条件的代码中，我们需要四个测试，而不是两个。正如我们所知，我们的测试只测试输入:

*   `true`和`true`(结果为 12)
*   `false`和`false`(结果为 12)

但是，他们还应该测试输入:

*   `true`和`false`(结果为 8)
*   `false`和`true`(结果为 18)

如果我们测试了这些条件，我们会发现我们的测试没有完全测试我们的逻辑，导致了一个 bug。可悲的是，随着测试的通过和 100%的覆盖率，我们可能已经发布了这些代码，并不得不在以后的生产中发现这些错误。那一点也不好玩。

我已经创建了一个库来展示这个例子，你可以检验并尝试一下。您将看到测试以 100%的覆盖率通过。

## 执行的代码没有被代码覆盖

如果测试覆盖率只告诉我们执行了什么代码，这意味着我们可以执行代码，而从不测试它。让我们转换上面的测试套件来展示这个场景。

```
const assert = require('assert');
const { alwaysTwelve } = require('.');

describe('Always Twelve', function () {
  context('when called', function () {
    it('returns 12', function () {
      // Called twice, though nothing is asserted
      alwaysTwelve(true, true);
      alwaysTwelve(false, false);
    });
  });
}); 
```

Enter fullscreen mode Exit fullscreen mode

这个糟糕的测试将会通过，并导致 100%的测试覆盖率。当我们可能需要测试更多的时候，测试覆盖率不仅说“100%”，当我们认为我们测试了所有的东西，但是没有测试的时候，它也向我们显示了“100%”。

## 这告诉我们关于测试覆盖率的什么

从这些例子中我们可以得出一些不幸的结论。

1.  **执行和测试是两个不同的概念**。虽然我们可能知道通过测试覆盖执行了什么代码，但这并不意味着这些代码行被正确地测试或根本没有被测试。
2.  **该百分比是最佳情况下的情况**。如果测试覆盖率是 80%，那就意味着 0-80%的代码被正确测试了。
3.  一旦一行代码被标记为被覆盖，它就不再是一个有用的标记。如上所示，除非我们知道为什么一行代码被认为是被覆盖的，否则我们不能说它已经被正确地测试了。
4.  **未执行的行数是唯一有用的指标**。这种度量可以告诉我们哪些代码需要测试，或者哪些代码区域在测试中是薄弱的。测试覆盖的百分比告诉我们的仅仅是执行了哪些代码，这有一些价值，但并不多。它只在告诉我们哪些代码没有被执行时有用。

测试覆盖并不能帮助我们提高代码质量，探索我们代码的设计，发现错误，防止错误，或者测试代码的逻辑。它只能告诉我们，我们的代码执行时没有错误，我们给它输入。真的就是这样。这就是我们从测试覆盖率中得到的一切。也许那值点钱。

## 更好的方法

好的测试从好的软件架构开始。Gary Bernhardt 有一个名为 [Boundaries](https://www.destroyallsoftware.com/talks/boundaries) 的精彩演讲，他在演讲中讨论了一种思考和设计软件的模型，这种模型可以带来有益的测试实践。在解释他的想法方面，视频比我做得好得多。但是我想从中抽出一些东西来谈谈测试。

Gary 有两类代码。一个类别包含所有的路径和条件，但是没有依赖关系。这类代码包括业务逻辑和策略，但不像数据库、互联网或磁盘那样访问外部世界。它非常适合独立的单元测试，并且它允许开发人员快速测试所有可能的路径(因为 2 个 <sup>n 个</sup>测试加起来)。他称这个类别为功能核心。

另一类代码几乎不包含路径，也没有定义所有的依赖关系。这一类别非常适合集成测试，因为集成测试将数据库、互联网和磁盘连接在一起，并确保它们正常工作。他将这一类别称为命令式外壳。

记住这些类别，我们就可以查看代码，看看是否有外部依赖，以及是否所有的路径都经过了良好的测试。我们可以编写许多快速的单元测试，因为路径与外界隔离，还可以编写一些集成测试，以确保世界正确地连接在一起。

因此，代码评审者可以从依赖测试覆盖转移到考虑路径和依赖。

1.  我们在不应该的地方引入了依赖吗？
2.  我们在代码中不应该添加路径的地方添加了吗？
3.  我们测试了代码中的所有条件吗？
4.  我们是否在编写小函数，这样我们就可以减少我们的测试表面积并创建坚固的边界？

我还想指出另一个视频，我认为它给出了很多关于测试的好想法，叫做[请不要嘲笑我](http://blog.testdouble.com/posts/2018-03-06-please-dont-mock-me)。这是对 Gary Bernhardt 的界限视频的一个很好的补充，并探索了使用测试的糟糕方法和一个好方法。

## 我们应该如何使用测试覆盖率？

当在开发过程中对未执行的代码给出反馈时，这种度量是有用的。它可以向 TDD 实践者展示他们在哪里超越了自己。它可以向开发人员显示他们在打开拉请求之前忘记测试的地方。它可以显示开源维护者遗漏测试的区域。对项目的度量可能不能从整体上说明什么，但是在对代码进行修改的过程中，未执行的值可以提供一些见解。

这是很重要的一点——价值在于知道在我们的测试运行期间哪些代码没有被执行。更好的方法是将度量理解为“在我的测试中，我 10%的代码没有被执行”，而不是“我 90%的代码被测试覆盖”(正如我们所看到的，这可能不是真的)。或者更好的是，“我添加了 15 行在测试期间不执行的代码。”对我来说，这是更有帮助的——也是更诚实的——方法，它帮助我们更有信心地修改代码。