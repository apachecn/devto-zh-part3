# 一个学究关于优化数组迭代回调函数的实验

> 原文：<https://dev.to/somedood/a-pedant-s-experiment-on-optimizing-callback-functions-for-array-iteration-30fi>

对于一些人来说，提到 JavaScript 的性能可能像是一个笑话。鉴于它是一种解释语言，它永远不会像 C/C++和 Java 这样的原生编译语言那样快。尽管这是真的，但这是对现代 JavaScript 能力的极大曲解。现代的 JavaScript 引擎跳过重重关卡，使用一系列技巧来优化代码。人们甚至可以说，由于这些优化，JavaScript 确实很快。

这让我想到了我最近的兴趣:*存储功能*。自从我学习 C++到现在已经有半年了(写这篇文章的时候)，我已经变得越来越不能容忍糟糕的性能，即使是在最小的范围内。就好像在过去的六个月里，我对最大化每个 CPU 周期产生了一种迂腐的痴迷。

这种痴迷影响了我思考编写 JavaScript 代码的方式。我开始问自己:重用函数能让 JavaScript 运行得更快吗？现代 JavaScript 引擎是否足够智能来针对这种情况进行优化？假设缓存数据(或本例中的函数)以备后用会使 JavaScript 程序的性能更好，这安全吗？

简短的回答是肯定的...明确地...*某种程度上*。

# 存储功能

```
// Explicit storing (function expression)
const explicitFunc = function() {};

// Implicit storing (function declaration)
function implicitFunc() {} 
```

Enter fullscreen mode Exit fullscreen mode

存储函数的概念非常简单。我们可以通过将一个函数初始化为一个表达式来显式地将它存储到一个变量中。另一方面，函数声明允许我们隐式地存储一个。抛开不谈，这两段代码达到了同样的目的:将一个函数存储到一个变量中以备后用。

此时，我开始痴迷于内存优化。我好奇的想知道使用存储函数是否对数组迭代的性能有积极的影响。我的直觉认为情况确实如此。尽管如此，我还是做了一个实验来检验我的假设。

# `Function`。实例。到处都是。

```
const someNums1 = [ 1, 2, 3 ];
const someNums2 = [ 4, 5, 6 ];
const add1 = x => x + 1;

// Defining a new `Function` instance for each `Array#map` call
someNums1.map(x => x + 1);
someNums2.map(x => x + 1);

// Using a previously defined function
someNums1.map(add1);
someNums2.map(add1); 
```

Enter fullscreen mode Exit fullscreen mode

我的实验围绕着这个概念(如上图所示)。例如，当我们使用`Array#map`方法迭代数组时，我们经常将单一用途的箭头函数作为回调函数传入。如果在整个代码库中重复重新定义相同的箭头函数，就可能会出现问题，如上面的代码片段所示。每次我们定义一个函数时，都会创建一个新的`Function`实例，不管它是否与其他函数共享相同的定义。随着时间的推移，这可能被证明是无效的。

```
// Functions `a` and `b` share the same definition,
// but they are two different `Function` instances.
const a = x => x;
const b = x => x;
console.log(a === b); // false 
```

Enter fullscreen mode Exit fullscreen mode

这个问题的解决方案非常简单:我们必须将经常使用的函数存储到变量中。从内存中检索函数肯定比构建相同函数定义的全新实例要快...*或者是？*

# 方法论

| 五金器具 | 规格 |
| --- | --- |
| 中央处理器 | Intel Core i5-8250U 1.6GHz (x8) |
| 随机存取存储 | 8192 MB DDR3 |
| 操作系统（Operating System） | Windows 10.0.17763.437 |

| 运行时间 | 软件版本 | V8 发动机版本 |
| --- | --- | --- |
| 铬 | 73.0.3683.103 | 7.3.492.27 |
| 节点. js | 11.14.0 | 节点 18 |

为了进一步研究，我编写了一个脚本，记录缓存和未缓存的函数迭代一个特定大小的数组需要多长时间。我还测试了常规函数和 arrow 函数之间的性能差异。我在我的笔记本电脑(硬件正常)上，在浏览器环境(使用 Chrome)和 Node.js 运行时运行这个脚本。

```
// This import only applies to the Node.js runtime.
const { performance } = require('perf_hooks');

// This specifies how big the array (to be iterated upon)
// can be. At the same time, it also determines how many times
// the test array must (exponentially) increase in size.
const ORDERS_OF_MAGNITUDE = 8;

// These are the cached functions.
// I tested both regular functions and arrow functions
// to see if there are any differences between the two.
function plus1Func(x) { return x + 1; }
const plus1Arrow = x => x + 1;

for (let i = 1; i < 10 ** ORDERS_OF_MAGNITUDE; i *= 10) {
  // This is the test array. Its maximum size is determined
  // by the specified `ORDERS_OF_MAGNITUDE`. The test begins
  // by filling this array with only `1` element.
  // It exponentially increases in size by a factor of `10`
  // after each iteration.
  const test = new Array(i).fill(0, 0, i);

  // Uncached (regular function)
  const a0 = performance.now();
  test.map(function(x) { return x + 1 });
  const a1 = performance.now();
  const uncachedRegular = a1 - a0;

  // Cached (regular function)
  const b0 = performance.now();
  test.map(plus1Func);
  const b1 = performance.now();
  const cachedRegular = b1 - b0;

  // Uncached (arrow function)
  const a2 = performance.now();
  test.map(x => x + 1);
  const a3 = performance.now();
  const uncachedArrow = a3 - a2;

  // Cached (arrow function)
  const b2 = performance.now();
  test.map(plus1Arrow);
  const b3 = performance.now();
  const cachedArrow = b3 - b2;

  // Log results here.
  const currentTestNumber = `Test #${Math.log10(i) + 1}`;
  const elementCount = i.toLocaleString();
  console.group(`${currentTestNumber}: Testing ${elementCount} elements...`)
    console.group('Regular Function');
      console.log(`Uncached: ${uncachedRegular}ms`);
      console.log(`Cached: ${cachedRegular}ms`);
    console.groupEnd();
    console.group('Arrow Function');
      console.log(`Uncached: ${uncachedArrow}ms`);
      console.log(`Cached: ${cachedArrow}ms`);
    console.groupEnd();
  console.groupEnd();
} 
```

Enter fullscreen mode Exit fullscreen mode

# 结果和讨论

### 比较两种运行时环境

[![Node.js Runtime Results](img/40f4efc148fa57caf907a7659e65aef4.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--zLvq1bnP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/m08h6ujzm97gvrx3tpls.png)
[![Google Chrome Browser Environment Results](img/25fe45c12855288ef53aeeea9fb619d3.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--OPmFQXkO--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/15ido3zxqls3odlx9007.png)

诚然，结果并没有显示出任何接近这种规模的突破。数据点彼此非常相似，甚至看不到存储函数的效果。

然而，值得指出的是*在最极端的情况下*，Node.js 运行时明显比 Chrome 浏览器环境慢。这两个图表的纵轴描绘了脚本迭代特定大小的数组所花费的时间(横轴)。对比两个纵轴可以看到，在迭代`10,000,000`个元素时，Node.js 运行时完成执行需要≈ `1300`毫秒。这与浏览器环境的≈ `160`毫秒相差甚远。

这种差异可能是因为 Node.js 运行时使用了 V8 JavaScript 引擎的一个分支，比 Chrome 的版本落后三个次要版本。三个小版本肯定包含了对引擎的许多改进和优化。

尽管如此，我必须强调，这并不是说 Chrome 浏览器环境*总是*比 Node.js 运行时更好地优化数组迭代。迭代`10,000,000`元素是极其罕见的情况。把我的结论建立在这样的案例之上是不公平的。对于通常的、每天的场景，我们只迭代几个元素:如果我做一个非常保守的猜测，可能在`2-100`元素附近的某个地方。在这个范围内，两个运行时环境之间的性能差异可以忽略不计，因此对它们进行优化是没有意义的。

### 放大到适当的比例

为了正确地看到存储函数的效果，我们必须在实际范围内以较小的比例放大和分析数据。为了安全起见，我选择将数据限制为`1-1,000`个元素。结果如下:

[![Node.js Runtime Results (Limited)](img/136970e606aa8448b911ee74b9540a10.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--SSzOLuk8--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nosfqyqiv6dmfvrj1xon.png)
[![Google Chrome Browser Environment Results (Limited)](img/d78e5fbef430f0fd1d3a034e5b80fd19.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s--i_RhGfYP--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/5x6jsn8a20sddplkzzey.png)

除了 Node.js 运行时产生的结果比浏览器环境更一致这一点很明显之外，上面的两个图表显示了常规函数和 arrow 函数之间的一个共同模式(不管它们是否被缓存到内存中)。如果用作`Array#map`方法的单一用途回调函数，箭头函数往往比常规函数执行得更好。

JavaScript 引擎必须针对 arrow 函数的[没有绑定到自己的`this`、`arguments`、`super`和`new.target`关键字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Arrow_functions)进行优化。它可以安全地跳过生成这些绑定，从而带来更好的性能。这种优化在浏览器环境中尤其明显。重复实例化新的`Function`实例，并将其绑定到前面提到的关键字(对于每个`Array#map`调用),使得未缓存的常规函数(蓝线)的性能通常比对应的函数差。

### 要缓存还是不要缓存？

实际上，数据显示这并不重要，尤其是箭头函数。即使在大规模的情况下，性能开销也是难以察觉的。然而，如果我们选择迂腐，一般来说，缓存函数是安全的选择，尤其是如果这些函数是常规函数。与直觉相反，缓存箭头函数可能不是最好的主意。

两个图表都给出了支持这一观点的证据。当检查大小为`1`的数组的结果时，Node.js 运行时总共需要≈ `0.25`毫秒来创建一个正则`Function`的全新实例，并迭代单元素数组。虽然它只是一个大小为`1`的数组，但是实例化的开销是显而易见的。预先缓存正则函数——从而消除了完全重新实例化的需要——使其性能与其 arrow 函数的对应函数相匹配。

从浏览器环境的图表中可以看出，缓存箭头函数并不一定会提高大小为`1-100`的数组的性能。缓存只成为较大阵列的可行优化。由于数组的大小通常为`2-100`(正如我在前面几节中保守地假设的那样)，所以内联定义一个箭头函数回调可能比将其存储在变量中以备后用更好。

### 趋势的变化

[![Extended Results for Node.js Runtime](img/f44d743ccad2ad0ace0b91b40d62d7f7.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--lMliA1UJ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/btwdovwa0cbx2c3fs7qo.png)
[![Extended Results for Google Chrome Browser Environment](img/441e302a35058560db086bac52990229.png)T6】](https://res.cloudinary.com/practicaldev/image/fetch/s---HTktGDr--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/bzv6smvko12d16n6z8kf.png)

将范围扩展到`1,000,000`个元素，未缓存的正则函数的图形(蓝线)会发生一些有趣的事情。随着测试数组中元素数量的增加，未缓存的正则函数的性能会下降。换句话说，随着更多的元素被引入到阵列中，蓝线的梯度变得更陡。这在浏览器环境中的`10,000`和`100,000`元素之间尤为突出。

趋势在`100,000`元素后中断。未缓存的常规函数可能会突然表现得和其他测试用例一样好。此时，JavaScript 引擎拥有了尽可能优化函数所需的所有信息。这似乎是 JavaScript 中函数优化的巅峰。

无论是否缓存，当迭代一个*`length`属性大于`100,000`的大型数组时，可以放心地假设选择一个常规函数而不是一个箭头函数作为`Array#map`方法的回调对性能没有影响。与其优化回调函数，不如将我们的注意力转移到数组本身。也许有更好的设计和体系结构，首先不需要如此大的阵列。*

 *# 结论

作为一般的经验法则，缓存总是一个安全的赌注。对于常规函数来说尤其如此，但对于箭头函数来说就不是这样了。箭头函数的设计很简单，只考虑了数组迭代。如果事先在内存中存储了一个箭头函数，这几乎没有关系。然而，学究式地说，对于大小为`1-100`(这是典型的用例)的数组，一般来说*内联定义箭头函数比将它们存储到变量中更好*。

由于缓存通常是一个安全的赌注，人们可能会认为它总是会提高数组迭代的性能。对于典型的用法来说，这是正确的，但是在最大的范围内，常规函数和箭头函数的缓存或偏好都将是重要的。事实上，前面的建议都不重要，因为现代 JavaScript 引擎已经有足够的信息来尽可能优化数组迭代。拥有一个至少包含`100,000`个元素的数组就足以向引擎发出信号，不要在意这种情况的微妙之处。

换句话说，所有的测试用例最终都会以足够大的数组接近峰值优化。为此，将我们的注意力转移到数组本身，而不是优化回调函数，可能对我们最有利。允许这样大小的阵列可能表明设计和架构需要改进。数组最初通常不应该那么大([，即使理论上允许它们拥有与`2**32`](https://www.ecma-international.org/ecma-262/6.0/#sec-array-exotic-objects)*一样大的`length`属性)，除非用例真正认为有必要处理大量数据*。

说到底，我做的实验是微秒和毫秒级的。这毕竟是一本“学究指南”。它只是在数组迭代的上下文中演示回调函数的微妙之处。现代 JavaScript 引擎确实在优化我们编写的代码方面做得很好，但是在如此小的时间范围内，这些类型的优化通常不会对程序的整体性能产生重大影响。如果有一件事真的需要优化，那就是 JavaScript 程序中数组的大小。JavaScript 引擎可以尽可能地优化回调函数，*但是它永远不能优化固有的大输入。*

数组大小很重要。*