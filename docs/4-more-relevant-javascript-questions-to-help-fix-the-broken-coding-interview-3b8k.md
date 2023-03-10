# 4 个更相关的 JavaScript 问题，帮助修复中断的编码面试

> 原文：<https://dev.to/adyngom/4-more-relevant-javascript-questions-to-help-fix-the-broken-coding-interview-3b8k>

快速回顾一下，人们普遍认为技术面试已经过时了。JavaScript 可能是当今最流行的编程语言，当然也是前五名中的一员。

在已经成为多元宇宙的 JS 世界里雇佣合适的候选人正在变成一种实验艺术形式。

像以前的许多人和以后的许多人一样，我见证了不良筛选过程的影响，这是我试图成为解决方案的一部分。

你可以在本系列的第一篇文章中读到更多关于我的使命宣言的内容，但事不宜迟，下面是你要改变的下四个相关内容。

**#8。输入自动完成**
***最适合:*** 高级|专家**阶段:**在家|第 2 轮|现场

**#8A。挑战**
UX 团队坚持认为国家搜索输入表单需要更加用户友好。

他们对一个“简单的”自动完成搜索框有自己的想法。对 UX 团队来说，不那么简单和不明显的是，你有一个巨大的可能结果的数据集，存储在客户端是不可行的。

你的任务是创建一个程序来监听用户输入事件并获取可能匹配的列表。UX 团队不喜欢肮脏匹配，希望列表只显示以用户输入的搜索词开头的国家名称——哦，他们希望匹配在列表结果中突出显示

**8B。背景**
我已经提供了类似的测试，通常有 48 至 72 小时提交回来。我强烈建议您利用分配的所有时间来提出最完善的解决方案。

这样做的好处是在一定程度上模仿了真实的用户故事，并且给了你时间去执行。

请不要盲目地复制和粘贴你没有完全掌握的解决方案。

你将被期望到现场或网上来谈论你的解决方案，你的推理，以及在评估过程中经常会发生的“功能蔓延”，看看你将如何处理它。

对于这个特殊的问题，搜索和过滤的异步特性将是第一个障碍，但我个人不会从那里开始。我可能会从一小组本地数据开始，并专注于使自动完成和高亮显示尽可能可靠。

作为面试官，我希望看到候选人谈论并整合以下所有或大部分概念:

*   油门[连杆](https://css-tricks.com/debouncing-throttling-explained-examples/)
*   去抖[链接](https://davidwalsh.name/javascript-debounce-function)
*   贮藏
*   记忆[链接](https://medium.freecodecamp.org/understanding-memoize-in-javascript-51d07d19430e)
*   错误处理

带回家比看起来更有挑战性。通常，足够让你开始了，但是在你的交付上还有很多期待。

**#7 迷你测试库**
***最适合:*** 初级|高级|专家**阶段:**第 3 轮|现场

**#7A。挑战**
开发团队害羞地首次使用 TDD 方法。他们还不想提交一个完整的库，而是想直接在控制台上编写简单的测试来测试他们最重要的功能。他们将实验项目命名为 dotLog，并提供了以下初始结构:

```
var dotLog = ( function() {
 return {
    describe(desc = 'test description:', testSet = []) {
        console.groupCollapsed(`${desc}: 0 passed - 0 failed`);
            // console out each test
        console.groupEnd();
    }
 }
}()); 
```

**描述**方法采用测试标签串和测试对象数组。测试对象采用以下模型:

```
var testOne = { rule: ( 3 < 4), label: " 3 is less than 4" };
var testTwo = { rule: (Math.max.apply(null,[3,4,1,2,0]) === 4), label: " should output 4" }; 
```

现在这些可以被添加到一个测试集中，并被传递给**描述**方法

```
var testSet = [testOne, testTwo];
dotLog.describe("Basic Math tests:", testSet);
Given the following 'naïve' function
function sum (x, y) {
    return x + y;
} 
```

修复它和 describe 方法，以便显示以下输出:

```
sum(): 3 passed - 1 failed
    √ Should return 0 if no arguments
    √ Should add two numbers
    √ Should add up any amount of numbers
    x Should convert any non-digit to 0 and return the right amount 
```

**#7B。背景**
我曾被拒绝过，但也非常成功地找到了工作。在我参加的所有面试中，只有两家公司提出过类似的挑战。

这件事的特殊性在于，我实际上是和整个开发团队坐在一起，那里最终将成为我的工作站，我还参与了起立鼓掌，并被给予半天时间来解决问题。四个小时结束的时候，我被带去吃午饭，告别。

虽然我都成功了，但有趣的是过程。我开始直接在我的日常环境中工作，挑战是他们积压的实际用户故事。

如果我想成功，我不能在我的软技能上作弊，我必须联系我的“即将成为”的队友来了解更多细节，或者快速设置问题。我不得不通过公关提交我的修改。

给你一个建议，经常提交，用描述性的信息来描述你的改变。

总之，我认为公司可以投资一些时间来适应这种类型的设置。另一种方法可能是将它作为一个有时间限制的在家练习，让候选人来现场与团队讨论她的解决方案。

**#6 自动售货机**
***最适合:*** 初级|高级|专家**阶段:**第 2 轮|第 3 轮|现场

**#6A。挑战**
鲍勃经营着一家成功的自动售货机企业。他想增加一个界面，让它们变得更加用户友好，并且更容易添加和跟踪库存。下面是机器的典型库存模型:

```
const drinks = {
    1: { name: "Mango Juice", stock: 2 },
    2: { name: "Banana Smoothies", stock: 2 },
    3: { name: "Guava Mix", stock: 1 },
    4: { name: "Citrus Blend", stock: 3 }
  }; 
```

编写一个程序，使用以下规范创建自动售货机(VM)的实例:

虚拟机需要传递一个库存对象来创建一个实例
，如果对象没有被传递，程序应该抛出一个描述性错误:

```
const vm = new VM(drinks); // instance get created
// but
const emptyVM = new VM(); // throws a no inventory error 
```

VM 应该将新传递的库存分配给一个私有的饮料变量，该变量不能从实例
中修改

```
vm.drinks; // should output undefined 
```

VM 应该有一个可调用的销售方法，该方法将饮料 id 作为输入，如果有货则返回一个成功消息，如果缺货则返回一个失败消息
VM 将总是在成功销售后从产品 id 库存中扣除一(1)

```
vm.sale(1);
// output: 1 Mango Juice - Thank you, come again!!
vm.sale(1);
// output: 1 Mango Juice - Thank you, come again!!"
vm.sale(1);
// output: Mango Juice is out of stock :( Come back tomorrow
vm.sale(4);
// output: 1 Citrus Blend - Thank you, come again!! 
```

最后，VM 还应该有一个可赎回的股票方法。它不需要任何参数，应该显示可用饮料的总数。

作为例子给出的饮料对象总共有 8 种饮料。买了 2 个芒果汁后，股票法的产量应该是 6。如果为空，它还应该输出“缺货”。

```
vm.stock(); // should output total of all drinks left or Out of stock 
```

**#6B。带有构造函数的上下文**
函数，新的 ES6 类，还是对象创建？？有很多方法可以解决这个问题，所以我希望面试官和应聘者一样做好准备。

对闭包的深刻理解将会非常有帮助。观察候选对象如何处理作用域和私有变量，以及是否隐式或显式地使用了任何设计模式，这也是很有趣的。

我认为，不管考生的水平如何，这种类型的练习应该能够触及 JS 语言的许多关键概念。

**#5 代码分析和可能的重构**
***最适合:*** 高级|专家**阶段:**第 2 轮|第 3 轮|现场

**#5A。挑战**
销售团队想要一个小程序来帮助他们从一批门票中删除任何重复的门票。门票批次一般在**1000**到**10000**未过滤项目之间。您团队中的 3 名开发人员提交了以下解决方案。

**开发者 1**

```
if( !Array.prototype.uniq ) {
    Array.prototype.uniq = function() {
        let _singles = [];
        const arr = this;
        for ( let i = 0; i < arr.length; i++ ) {
            if(_singles.indexOf(arr[i]) === -1 ) {
                _singles.push( arr[i] );
            }
        }
        return _singles;
    }
} 
```

**开发者 2**

```
if(!Array.prototype.uniq) {
    Array.prototype.uniq = function() {
        return this.reduce( (arr, val) => {
            if (!arr.includes(val)) arr.push(val);
            return arr;
        }, [] );
    }
} 
```

**开发者 3**

```
if(!Array.prototype.uniq) {
   Array.prototype.uniq = function() {
       return new Set([...this]);
   }
} 
```

提供两个测试文件 [**onekTickets.js**](https://gist.github.com/adyngom/748873da9cb37828765216a36e7b7ec2) 和 [**tenkTickets.js.**](https://gist.github.com/adyngom/62c25502eed121e9092c97fd9041afa0)

只能将一个解决方案推向生产。如果有的话，你会根据可读性和性能选择哪个？

是否有任何建议的解决方案可以进一步优化以达到更好的效果？关于他们的解决方案，你会给每个开发人员留下什么样的公关信息，以及为什么选择或不选择他们的解决方案？

**#5B。背景**
在一个正常的开发环境中，你会被期望去阅读、评估并最终批判他人的代码。

PR 审查是确保质量代码被推送到主回购的关键步骤。

现在，每个团队或公司都可以有自己的定义质量的方法，但是不能远离清晰的标准、惯例和编码文化。

这种问题对雇主和应聘者都有好处。您与团队的主要互动将通过每天类似的练习进行。

作为一名面试官，我希望应聘者参考一些基准测试工具来帮助确定哪个代码块是最快的。

如果她能注意到并清楚地解释原因，我肯定会印象深刻，但既然我们在谈论给予反馈，展示总是比仅仅讲述更容易。

**console.time** 和 **console.timeEnd** 可能会开启正确方向的对话，【jsperf.com】的[也是业界的最爱之一，但最终寻找、建议、进行基本设置和运行快速基准测试将是明显的赢家。](//jsperf.com)

最后，我想公开讨论一下可读性，以及为什么有时候牺牲一点速度是有益的。

**第二批**
就此结束。今天，我们介绍了一个与输入自动完成相关的 DOM 练习。预计在下一期文章中会有更多这样的内容。

与此同时，不要忘记分享、鼓掌或诸如此类，但最重要的是在评论中加入讨论。

干杯