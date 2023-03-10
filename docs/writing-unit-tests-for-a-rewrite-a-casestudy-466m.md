# 为重写编写单元测试:案例研究

> 原文：<https://dev.to/azure/writing-unit-tests-for-a-rewrite-a-casestudy-466m>

这篇博文是讨论我如何将 [Raspi IO](https://github.com/nebrius/raspi-io/) 转换成打字稿并更新其架构的系列文章中的第一篇。本系列博客将探讨如何编写专门用于重新构建或重写项目的单元测试，如何创建在多个 TypeScript 和非 TypeScript 项目之间共享的 TypeScript 基类和功能，以及如何将现有代码库一次性转换为 TypeScript。

*这篇文章最初发表在[蓝色媒体刊物](https://medium.com/microsoftazure/converting-to-typescript-part-1-unit-tests-87c20329d6c7)上。*

所有的代码库都会随着时间的推移而老化和成熟。随着年龄的增长会带来稳定性，因此老的项目通常更可靠。

然而，随着最初的架构努力跟上现代用户的需求，年龄也带来了吱吱嘎嘎的声音。时间也带来了开发这些项目的更新、更好的方法，而曾经的尖端技术往往变得笨拙而缓慢。

所以这些项目的问题变成了:重写，还是不重写？我面临这样一个问题，当时我最老的项目还在开发中: [Raspi IO](https://github.com/nebrius/raspi-io/) 。

Raspi IO 是 Johnny-Five node . js 机器人和物联网框架的一个插件，使 Johnny-Five 能够在 Raspberry Pi 上运行。我在 2014 年首次将它作为一个整体来创建，但随着我添加更多功能，最初的架构很快就遇到了限制。第二年我重写了这个库，并把它分成了多个模块。这种重写允许项目随着更多特性的增加而扩展。

[![Raspi IO Architecture Diagram](img/9676030f6268acde0d2ef29046a47df6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--rzCSJ7uK--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/vpw9w5fq3cr5n1q7mduh.png)

Raspi IO 目前由 11 个模块组成。其中 9 个模块组成了我所谓的 [Raspi.js](https://github.com/nebrius/raspi) ，可以独立于 Raspi IO 和 Johnny-Five 使用。这些模块共同提供了一个完整的 API，用于以统一的方式与 Raspberry Pi 上的硬件进行交互。Raspi IO 和 Raspi IO Core 一起为 Johnny-Five 提供了从 Raspi.js 到 [IO 插件规范](https://github.com/rwaldron/io-plugins)的转换层。Raspi IO 内核是平台无关的，Raspi IO 将 Raspi.js 注入 Raspi IO 内核，创建一个 Raspberry Pi 特定的 IO 插件。

随着时间的推移，所有的 Raspi.js 都被转换成了 TypeScript，并根据现代编码实践进行了更新。然而，Raspi IO 和 Raspi IO Core 三年来几乎没有变化。这对于只包含 32 行代码的 Raspi IO 来说没问题，但是对于 Raspi IO 内核来说就不行了。在里面，有 1000 行密集的 JavaScript，充满了对奇怪的边缘情况和错误的攻击。这个代码库绝对属于“害怕做出改变，因为它可能会破坏一切”的经典案例它也急需更新到 TypeScript 和现代编码惯例。

我头脑中有了清晰的需求，于是坐下来设计了一个重写 Raspi IO Core 的计划，而不会为我的用户破坏它。这次重写的第一步是实现具有高度代码覆盖率的单元测试，因为 Raspi IO Core 由于历史原因没有单元测试(涉及硬件的单元测试很难)。

虽然主要的重构和重写为它们带来了很多优势，比如最先进的最佳实践和现代工具，但是从破坏用户的角度来看，它们本身就有风险。单元测试就像保险一样，确保重写对用户尽可能透明。

## 方法论

那么，如何为一个没有单元测试并且需要重写的项目实现单元测试呢？非常有条不紊，并遵循规范。

如前所述，Raspi IO Core 实现了一个名为 [IO 插件规范](https://github.com/rwaldron/io-plugins)的公开规范。这个规范为模块应该如何运行提供了一个蓝图，实际上也为单元测试本身提供了一个蓝图。

不是所有的项目都实现 API 规范，但是希望有设计文档或其他文档描述项目应该做什么。如果没有，那么实现单元测试的第一步就是编写这样一个规范。这是一项艰巨的工作，但我保证它会对以后的工作有很大帮助。除了使单元测试更容易实现之外，它还为所有的利益相关者，而不仅仅是编码者，提供了一个对项目进行输入并使其更好的地方。如果你不确定从哪里开始，[阅读文档](https://apiguide.readthedocs.io/en/latest/build_and_publish/documentation.html)有关于编写质量规范的好内容。

下一步是决定单元测试技术栈。我决定使用开源 Node.js 模块的公共堆栈，因为我已经对它们很熟悉了，现在还不想学习新的工具或平台:

*   [Jasmine](https://jasmine.github.io/) :一个[行为驱动开发](https://en.wikipedia.org/wiki/Behavior-driven_development) (BDD)测试框架。
*   [伊斯坦布尔](https://istanbul.js.org/):一个 JavaScript [代码覆盖](https://en.wikipedia.org/wiki/Code_coverage)工具。代码覆盖工具测量单元测试执行了多少代码库，并提供了一个有用的代理方法来测量单元测试测试了多少代码。
*   Travis CI T1:一个托管的单元测试平台，使得在 GitHub activity 上运行单元测试变得容易(例如，当一个 PR 被提交时，当推/合并到 master 时，等等)。尽管重写并没有严格要求，但是将单元测试绑定到一个托管平台(比如 Travis CI)上通常是一个好主意。这使得考虑使用您的库的开发人员可以查看单元测试结果，而不必下载您的代码并自己运行测试。
*   [Coveralls](https://coveralls.io/) :与 Travis CI 集成的托管代码覆盖平台，提供 Travis CI 的所有价值，除了代码覆盖而不是单元测试本身。

有了规范和单元测试基础设施，是时候编写我的单元测试了！

## 一个单元测试的预排

为了说明如何编写有效的单元测试，我将对 IO 规范的一部分:`digitalRead`方法进行一次深入的演练。IO 插件规范是这样描述`digitalRead`方法的:

**数字读取(pin，处理器)**

*   为`pin`启动新的数据读取过程
*   推荐的新数据读取频率大于或等于 200Hz。每个平台的读取周期可能会降低到 50Hz，但不会更低。
*   调用`handler`进行所有新的数据读取，其中数据与之前的数据有所不同，使用一个参数，即从引脚读取的当前值。
*   一个相应的`digital-read-${pin}`事件被创建并发出，用于所有新的数据读取，其中数据已经从先前的数据改变，具有一个参数，即从引脚读取的当前值(这可以用于调用处理程序)。

我们可以将这个规范中说我们必须做的事情分解成几个我们需要测试的不同的事情，这将成为我们的单元测试集。通读规范，我确定了以下五个测试:

*   第三个要点表示我们需要测试通过`handler`参数读取一个值，因为 pin 值随时间变化。
*   第四个要点表示我们需要测试通过`digital-read-${pin}`事件读取一个值，因为 pin 值随时间变化。
*   第二个要点表示我们需要测试`handler`是否以 50hz 或更快的频率被调用。
*   第三和第四点表明我们需要测试该方法不会连续两次报告相同的值。
*   该规范的这一部分和其他部分隐含的意思是，我们需要测试`digitalRead`即使在模式变为输出模式时也能继续读取，并报告通过`digitalWrite`设置的输出值。

既然我们已经确定了我们想要编写的五个单元测试，下一步就是弄清楚*如何*编写它们。在一天结束时，单元测试的存在是为了确认在给定合理完整的输入采样的情况下，生成了正确的输出。所以任何单元测试的第一步都是识别输入和输出。

我们倾向于认为输入和输出是我们传递给函数的参数，以及它们返回的值。然而，这些并不是唯一存在的输入。例如，如果我们测试一个将值保存到数据库的函数，那么除了函数返回的内容或它调用的回调之外，对数据库的调用也是一个输出。在`digitalRead`的例子中，我们调用了与硬件对话的其他模块(更多的输出和输入！).一般来说，有两组*或更多组*输入和输出是很常见的。

单元测试的诀窍是弄清楚如何在下图的“后端”测量输入和输出。大多数情况下，这是通过使用[模仿](https://en.wikipedia.org/wiki/Mock_object)来完成的，这也是我在这里选择使用的解决方案。Raspi IO Core 的架构使得这一点非常简单，因为我们可以传入 Raspi.js 中所有模块的[模拟版本。我们正在测试的全套输入和输出如下所示:](https://github.com/nebrius/raspi-io-core/blob/master/spec/mocks.js)

[![All Inputs and Outputs for digitalRead Tests](img/e46e2721201f264585c2219be7ee0f4b.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--cPFqiiX7--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5pmec326r2bow6gcrcvz.png)

这些模拟版本包括硬件的虚拟实现，并向该模块公开输入/输出，以便我们可以在单元测试中验证它们。对于这个单元测试，我们使用了`DigitalInput` mock，它的代码如下:

```
class DigitalInput extends Peripheral {
  constructor(...args) {
    super([ 0 ]);
    this.value = OFF;
    this.args = args;
  }
  read() {
    return this.value;
  }
  setMockedValue(value) {
    this.value = value;
  }
} 
```

Enter fullscreen mode Exit fullscreen mode

我们添加了一个名为`setMockedValue`的额外方法，它在真正的 Raspi GPIO `DigitalInput`类中并不存在。这允许我们精确地控制 Raspi IO 内核将要读取的内容。我们还添加了一个名为`args`的新属性，我们可以用它来查看传递给类构造函数的参数。有了这个，我们就可以测量我们测试的黑盒“后端”的所有输入和输出。

现在是单元测试本身的时候了。我们将看一下使用回调读取值的单个单元测试:

```
it('can read from a pin using the `digitalRead` method',
    (done) => createInstance((raspi) =>
{
  const pin = raspi.normalize(pinAlias);
  raspi.pinMode(pinAlias, raspi.MODES.INPUT);
  const { peripheral } = raspi.getInternalPinInstances()[pin];

  let numReadsRemaining = NUM_DIGITAL_READS;
  let value = 0;
  peripheral.setMockedValue(value);
  raspi.digitalRead(pinAlias, (newValue) => {
    expect(value).toEqual(newValue);
    if (!(--numReadsRemaining)) {
      done();
      return;
    }
    value = value === 1 ? 0 : 1;
    peripheral.setMockedValue(value);
  });
})); 
```

Enter fullscreen mode Exit fullscreen mode

我们从一些初始化代码开始，以获得一个准备读取的测试管脚。然后我们调用`getInternalPinInstances`，这是一个特殊的钩子方法，只有在我们运行单元测试时才会暴露。这返回了`DigitalInput`的模拟实例，因此我们可以访问上面讨论过的`DigitalInput`中的钩子。

然后，我们设置一些状态监控变量。因为这个方法应该连续读取数据，所以我们必须测试它是否可以多次读取。记录我们已经读了多少次，还剩下多少次。我们在每次回调时都切换值，因为如果值不变，它就不会调用回调。在每次回调中，我们测试 Raspi IO Core 报告的值是否与我们在模拟的`DigitalInput`类中设置的值相同。

至此，单元测试完成了！如果你想看到组成`DigitalInput`测试的所有单元测试，你可以[在 GitHub](https://github.com/nebrius/raspi-io-core/blob/master/spec/gpio.spec.js#L86-L237) 上找到它们。

## 吸取教训

在这个过程中，我学到了一些关于单元测试和重写的重要经验。

### 边缘案件比普通案件*更*重要。

我们对常见的情况进行了大量的测试，并且我们的代码是根据这些常见的情况编写的。边缘案例通常是通过反复试验或用户报告发现的。因此，当我们重写现有的代码库时，我们希望确保我们移植了边缘案例，因为它们不太可能在“一开始”就被修复。让单元测试来测试这些边缘案例是确保我们在重写中包含这些边缘案例的最有效的方法。

### 永远要具体，不要笼统

当编写单元测试时，很容易快速地写出或多或少测试我们想要的东西。例如，如果我们要测试一个函数在参数不正确时是否会抛出异常，我们可以这样写:

```
expect(() => {
  add(NaN, `I'm not a number`);
}.toThrow(); 
```

Enter fullscreen mode Exit fullscreen mode

这确实会通过，但是我们怎么知道它通过了，因为`add`方法正确地检测到我们试图添加两个非数字？如果代码中有一个合理的错误碰巧出现在相同的输入上，那会怎么样呢？我们应该把这个测试写成:

```
expect(() => {
  add(NaN, `I'm not a number`);
}.toThrow(new Error(`non-numbers passed as arguments to "add"`); 
```

Enter fullscreen mode Exit fullscreen mode

这样，我们可以确保它按照我们期望的方式投掷。如果我们没有复制粘贴错误信息，这也有助于防止输入错误。这看起来没什么大不了的，但有时用户的代码取决于错误消息的内容，因为他们需要根据抛出哪个错误的*做出决定。如果我们改变我们的错误信息，我们打破了这个代码。要深入讨论为什么错误消息很重要(也很棘手)，我推荐阅读 Node.js 项目本身如何[改变它处理错误的方式](https://medium.com/the-node-js-collection/node-js-errors-changes-you-need-to-know-about-dc8c82417f65)。*

### 良好的代码覆盖率对重写来说比日常开发更重要。

在理想的世界中，我们都有 100%的代码覆盖率。然而，在实践中，100%的代码覆盖率很少是理想的，有时甚至是不可能的。事实上，Raspi IO Core 的覆盖率达到了 93%,因为大多数没有被测试的代码都是死代码。这些死代码大部分是 Babel 自己引入的运行时代码，无可否认是一个过时的版本。其余的是我认为必要的代码，但在实践中很可能是死代码。也有这样的情况，一些代码与测试期间不存在的东西(比如说，外部传感器)绑定得如此紧密，以至于模仿所有必要的东西会导致单元测试实际上只是测试模仿，而不是测试代码本身。

预计不会有 100%的代码覆盖率，但是对于重写来说，比日常编码有更高的代码覆盖率更重要。这是因为统计数据。在重写过程中，我们改变了大量的代码，这些代码最终被大量的单元测试覆盖，从而导致大量的边缘案例。不过，日常编码很少会有如此深远的变化。因此，在重写期间，回归的机会更高。一般来说，拥有高代码覆盖率是防止回归的最有效的方法，因此当我们处理具有高回归风险的变更(比如重写)时，高代码覆盖率尤其重要。

### 根据规范编写单元测试也会改进规范

尽管我们希望规范不会出错，但它们是由人类创造的。就像编写代码的人一样，编写规范的人有时会犯错误，在规范中引入错误。根据规范编写单元测试通常会突出规范中不明确或包含错误的地方。在为 Raspi IO Core 创建单元测试时，我发现了规范中的多个问题。在[案例](https://github.com/rwaldron/io-plugins/pull/19)的[三个](https://github.com/rwaldron/io-plugins/pull/17)中，我们只是忘了更新添加了一些新特性的规范。在[两个](https://github.com/rwaldron/io-plugins/issues/16)其他[案例](https://github.com/rwaldron/io-plugins/issues/18)中，规格不明确。在编写单元测试的过程中，找出规范中的问题可能是一种非常有效的方法。

## 结论

在过去，我已经尝试了 4 到 5 次将 Raspi IO 内核转换成 TypeScript。之前的每一次尝试都失败了，因为我很快变得不确定我是否能为我的用户提供一个无痛的升级途径。没有单元测试，我对自己的改变没有信心。编写这些单元测试是之前尝试中缺少的关键要素，现在我准备将 Raspi IO Core 转换为 TypeScript，并在此过程中重新设计其主要部分。

这一努力确实重申了单元测试的重要性，以及理解我们测试什么、如何测试以及为什么测试的重要性。