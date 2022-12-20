# 用 JSVerify 进行属性测试第一部分

> 原文：<https://dev.to/glebec/property-testing-with-jsverify-part-i-3one>

**摘要** : [JSVerify](https://github.com/jsverify/jsverify) 是一个用于*基于属性的测试*的 JavaScript 库，灵感来自经典的 [Haskell](https://www.haskell.org/) 库 [QuickCheck](http://hackage.haskell.org/package/QuickCheck) 。JSVerify 的 API 文档似乎假设对“俄罗斯方块”和函数式习惯用法(如[单子](https://fsharpforfunandprofit.com/posts/elevated-world-2/))有一定程度的熟悉。本文演示了 JSVerify 库 API 的基础知识，包括如何创建和使用*任意*值、*数据生成器*和*数据收缩*，以典型的 JavaScript 开发人员能够理解的方式。

# 第一部分:什么是属性测试？

[约翰·休斯](https://en.wikipedia.org/wiki/John_Hughes_(computer_scientist))，QuickCheck 的[原创作者](http://users.cs.northwestern.edu/~robby/courses/395-495-2009-fall/quick.pdf)之一，在下面的视频中令人信服地展示了性能测试的力量。如果你想看，或者继续读下去——我们将在下面进一步讨论要点。

[https://www.youtube.com/embed/AfaNEebCDos](https://www.youtube.com/embed/AfaNEebCDos)

简而言之，性能测试:

*   **指定**所有预期输入的预期规律(又名特性)
*   **生成**大量随机输入进行检查，通常通过方便的 DSL 或 API
*   **验证**预期属性对所有生成的案例都成立
*   **逐步简化**失败案例，找到引入失败的边界

属性测试是通过例子证明代码可能是正确的，或者绝对是不正确的一种形式。它介于单元测试的两个极端(人工指定的个别情况)和正式的机器检查的逻辑证明(在某些语言或系统中不切实际)之间。

## 单元测试

让我们考虑一个小例子。如果我们想验证一个定制`sort`函数的正确性，我们可以写一个通过 [Mocha 测试框架](https://mochajs.org/) :
执行的 [Chai 断言](https://www.chaijs.com/)形式的单元测试

```
describe('`sort`', () => {
    // unit test:
    it('transforms `[2, 1, 3]` to `[1, 2, 3]`', () => {
        expect(sort([2, 1, 3])).to.deep.equal([1, 2, 3])
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

您对`sort`是正确的有多大信心？可能不是很好。`Sort`很容易被破坏*而*不会失败，如果它:

*   仅仅交换了前两个元素
*   总是返回`[1, 2, 3]`
*   对两位数(或更高)的数字失败
*   在包含重复元素的列表中失败
*   在只有第一个(或最后一个)项目无序的列表上失败
*   在空列表上失败
*   在偶数长度的列表上失败

作为人类，我们往往想象不到每一个可能的边缘和角落。这部分是因为我们假设代码将如何被使用，关注于[快乐之路](https://en.wikipedia.org/wiki/Happy_path)。为上面的每一项编写多个单元测试会有所帮助，但是对于`sort`通常是正确的信心来说，这只是一个适度增加的工作量。

## 通过动态断言进行专门的属性测试

当程序员指定排序`[2, 1, 3]`返回`[1, 2, 3]`时，他们不一定关心那个特定的数组。相反，重点是验证排序的某些属性(特征),例如:

*   `sort`产生一个有序数组
*   对一个已经排序的数组不做任何事情(等幂)
*   `sort`仅仅移动元素而不添加、改变或删除

这些属性描述了概念上真正重要的东西，远远超过任何一个特定的情况。测试老手有时会开发特别的技术来确认这些概念，例如通过动态生成输入和断言。

```
const TESTS = 100
const LIMIT_LEN = 50
const LIMIT_NUM = 1000

const randomInt = () =>
    Math.floor(Math.random() * LIMIT_NUM)

const randomArr = () =>
    Array(Math.floor(Math.random() * LIMIT_LEN))
        .fill(null)
        .map(randomInt)

const isOrdered = (arr) => {
    for (let i = 0; i < arr.length - 1; i++) {
        if (arr[i] > arr[i + 1]) return false
    }
    return true
}

describe('`sort`', () => {
    for (let t = 0; t < TESTS; t++) {
        it('outputs ordered arrays`', () => {
            expect(isOrdered(sort(randomArr())).to.equal(true)
        })
    }
}) 
```

Enter fullscreen mode Exit fullscreen mode

这是朝着全面的属性测试的方向迈出的一步，但是如果规范失败了，我们看到的只是下面这个不透明的消息:

```
ERROR: expected `false` to be `true` 
```

Enter fullscreen mode Exit fullscreen mode

上述尝试有一些不小的缺点:

*   大量用于生成随机输入的专用代码
*   `randomArray`函数可能产生了多余的空列表(一次就够了)
*   你有没有注意到`randomInt`函数从来不会产生负数？哎呀！
*   失败是非指导性的，是什么输入导致的？
*   对于那个输入，`sort`实际上返回了什么？
*   如果失败的随机输入很大，实际上是哪个部分导致了失败？
*   如果该套件运行多次，随机性意味着我们可能再也不会遇到那种失败的情况

其中一些可以减轻，例如通过[编写定制的 Chai 匹配器](https://www.chaijs.com/guide/helpers/)(额外的工作)，日志记录(污染测试输出)，或者其他方法。然而，这些问题中的一些已经通过性能测试系统解决了。

## 用 JSVerify 进行一流的性能测试

这是使用专用的生成测试库编写的相同的订购检查。

```
const jsc = require('jsverify')

const isOrdered = (arr) => {
    for (let i = 0; i < arr.length - 1; i++) {
        if (arr[i] > arr[i + 1]) return false
    }
    return true
}

describe('`sort`', () => {
    it('outputs ordered arrays', () => {
        // property test:
        jsc.assert(jsc.forall('[integer]', (arr) => {
            return isOrdered(sort(arr))
        }))
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们不需要编写自己的`randomInt`和`randomArr`函数——js verify 为我们处理了这些。分解一下:

*   这个库的心脏和灵魂，它将数据生成器和属性检查功能联系在一起；`forall`返回一个“属性”值(旨在由其他 JSVerify 函数使用)。
    *   `'[integer]'`–我们希望 JSVerify 生成什么数据的字符串声明，特别是一个整数的*数组。 [JSVerify 解析一种特定于领域的语言(DSL)](https://github.com/jsverify/jsverify#dsl-for-input-parameters) 来描述输入数据(稍后会详细介绍)。*
    *   `(arr) => { ... }`–接收随机生成数据的函数，负责验证预期属性，如果该属性成立，则返回`true`。
*   `jsc.assert`–获取`forall`返回的“属性”，并执行其表示的逻辑作为*断言*(失败时抛出错误)。这使得 JSVerify 可以与测试框架如 Mocha 集成。

运行这段代码给我们一个更有说服力的结果:

```
Error: Failed after 7 tests and 3 shrinks. rngState: 86ac1d5e6093784bf2;
Counterexample: [2, 10] 
```

Enter fullscreen mode Exit fullscreen mode

有意思！这一切意味着什么？

*   摩卡报告说我们的主张失败了。
*   在发现第七个侵犯我们财产的案例之前，JSVerify 生成了六个输入案例。
*   失败的反例可以简化为三个步骤，以达到最小的失败反例(JSVerify 实际上采取了比这更多的步骤，但是谁在计算呢？).
*   `rngState: 86ac1d5e6093784bf2;`–js verify 使用基于种子的伪随机生成器来生成值。与`Math.random()`不同，如果需要，我们可以故意使用这个种子来确定性地重现失败的测试。
*   `Counterexample: [2, 10]`。JSVerify 指出`[2, 10]`是它能找到的未通过我们属性检查的最小的*输入*数据。

如果我们(暂时)像下面这样检测我们的代码，我们可以更深入地研究:

```
describe('`sort`', () => {
    it('outputs ordered arrays', () => {
        jsc.assert(jsc.forall('[integer]', (arr) => {
            const output = sort(arr)
            const ok = isOrdered(output)
+           console.log(
+               ok ? '✔️' : '❌',
+              'input:', arr,
+              'output:', output
+           )
            return ok
-       })) +       }), {
+           rngState: '86ac1d5e6093784bf2'
+       })
    })
}) 
```

Enter fullscreen mode Exit fullscreen mode

注意`jsc.assert`的第二个参数是一个**选项对象**，允许我们手动设置伪随机种子，复制我们之前看到的结果。(或者，[使用摩卡](https://github.com/jsverify/jsverify#usage-with-mocha)时，你可以使用`--jsverifyRngState`强制使用某个特定的`rngState`。)当运行这个修改后的测试时，我们看到记录了以下内容:

| 好 | 投入 | 输出 | 笔记 |
| --- | --- | --- | --- |
| ✔️ | `[]` | `[]` | JSVerify 从“小”输入开始。 |
| ✔️ | `[2]` | `[2]` | 前六种情况都可以。 |
| ✔️ | `[9]` | `[9]` | … |
| ✔️ | `[23]` | `[23]` | … |
| ✔️ | `[-9, 33, 33, 18, 31]` | `[-9, 18, 31, 33, 33]` | … |
| ✔️ | `[3]` | `[3]` | … |
| -好的 | `[20, 9, 10]` | `[10, 20, 9]` | 哎呀！我们的财产在第七个案子上失败了。 |
| -好的 | `[9, 10]` | `[10, 9]` | JSVerify“收缩”了数组，但还是失败了。 |
| ✔️ | `[10]` | `[10]` | 又缩水了，不过现在道具撑得住了。退后。 |
| ✔️ | `[0, 10]` | `[0, 10]` | JSVerify 收缩数组中的值*。`9`->-`0`还是可以的。* |
| -好的 | `[4, 10]` | `[10, 4]` | 减半`9` - > `4`失败，收缩从这里重新开始。 |
| ✔️ | `[10]` | `[10]` | JSverify 并不完美；我们看到一些多余的检查。 |
| ✔️ | `[0, 10]` | `[0, 10]` | … |
| -好的 | `[2, 10]` | `[10, 2]` | 减半`4` - > `2`再次失败；从这里开始收缩。 |
| ✔️ | `[10]` | `[10]` | 更多多余的检查。 |
| ✔️ | `[0, 10]` | `[0, 10]` | … |
| ✔️ | `[1, 10]` | `[1, 10]` | 这一次，`2`->-`1`的结果是通过输出。 |
| ✔️ | `[-1, 10]` | `[-1, 10]` | 负整数怎么办？还是可以的。 |
| ✔️ | `[2]` | `[2]` | 从这里开始，JSVerify 对`-10`和`10`之间的整数进行对数搜索。 |
| ✔️ | `[2, 0]` | `[0, 2]` | … |
| ✔️ | `[2, 5]` | `[2, 5]` | … |
| ✔️ | `[2, -5]` | `[-5, 2]` | … |
| ✔️ | `[2, 7]` | `[2, 7]` | … |
| ✔️ | `[2, -7]` | `[-7, 2]` | … |
| ✔️ | `[2, 8]` | `[2, 8]` | … |
| ✔️ | `[2, -8]` | `[-8, 2]` | … |
| ✔️ | `[2, 9]` | `[2, 9]` | … |
| ✔️ | `[2, -9]` | `[-9, 2]` | 没有失败，所以 JSVerify 认为`[2, 10]`是一个最小的反例。 |

这个实验展示了 JSVerify 对整数数组的收缩过程的几个巧妙之处:

*   它检查较小的数组和数组中较小的值
*   它检查通常被忽略的边缘情况，如`[]`、`0`和负整数
*   它使用二分搜索法来获得良好的性能

## 诊断问题

回顾我们最初的测试，全局`it` + `jsc.assert(jsc.forall(...))`的组合很常见，JSVerify 为此提供了一个`property`方法。(使用这种方便的方法意味着您不能再将 options 对象传递给`assert`，所以请记住这一点。)

```
describe('`sort`', () => { -   it('outputs ordered arrays', () => {
-       jsc.assert(jsc.forall('[integer]', (arr) => {
-           return isOrdered(sort(arr))
-       }))
-   }) +   jsc.property('outputs ordered arrays', '[string]', (arr) => {
+       return isOrdered(sort(arr))
+   }) }) 
```

Enter fullscreen mode Exit fullscreen mode

多次手动运行我们的测试套件(不修复 PRNG 种子)最终会产生至少两个不同的最小失败输入案例供我们考虑:

```
Error: Failed after 4 tests and 13 shrinks. rngState: 04214a57f39806becd;
Counterexample: [2, 10]
...
Error: Failed after 1 tests and 8 shrinks. rngState: 0092ae01573f6d84bc;
Counterexample: [-1, -2]
... 
```

Enter fullscreen mode Exit fullscreen mode

排序爱好者可能已经搞清楚是怎么回事了——就像 [`Array#sort`](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort) ，我们的`sort`按字典顺序操作[*而不是按数字顺序操作*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Array/sort#Description)**。如果开发人员不知道这个问题，他们可以在纸上或使用调试工具手动跟踪这些输入的排序函数的逻辑。由于 JSVerify“收缩”了反例，调试它们将比报告最初的失败更简单。**

 **### 但是等等，还有！看看这个奇怪的把戏

有一个特性(显然还没有记录在案)可以让失败报告提供更多的信息。如果从属性检查中返回除布尔值之外的任何内容，它将作为字符串追加到错误信息中。这让我们可以在失败时包含**输出**(或者任何我们想要记录的东西)。

```
jsc.property('outputs ordered arrays', '[string]', (arr) => {
    const output = sort(arr)
    return isOrdered(output)
+       ? true
+       : 'sort returned ' + JSON.stringify(output) }) 
```

Enter fullscreen mode Exit fullscreen mode

```
Error: Failed after 2 tests and 14 shrinks. rngState: 86fd6b284e01a5c793;
Counterexample: [2, 10]; Error: sort returned [10,2] 
```

Enter fullscreen mode Exit fullscreen mode

## 总结&自己试试

在第一部分中，我们看到了生成测试的基础和 JSVerify 入门。在[第二部分](https://dev.to/glebec/property-testing-with-jsverify-part-ii-1jcg)中，我们将更深入地探索 JSVerify API。但是，在这样做之前，请尝试使用下面的 JSVerify:

`const jsc = require('jsverify') // https://en.wikipedia.org/wiki/De_Morgan%27s_laws const deMorgansLawsHold = (p, q) => { theorem1 = !(p && q) === (!p || !q) theorem2 = !(p || q) === (!p && !q) return theorem1 && theorem2 } // A correct property const result1 = jsc.check(jsc.forall( jsc.bool, jsc.bool, deMorgansLawsHold ), { quiet: true }) // silences console logs // An incorrect property const result2 = jsc.check(jsc.forall( jsc.nearray(jsc.bool), (nonEmptyArrBools) => { const idxOfFalse = nonEmptyArrBools.indexOf(false) return (idxOfFalse >= 0) ? true : 'indexOf found false at: ' + idxOfFalse } ), { quiet: true }) // Predict! Is this property correct or incorrect? Try it and see… const result3 = jsc.check(jsc.forall( '[json]', 'json', // jsc.array(jsc.json), jsc.json, // equivalent spec (arr, jsVal) => { const newArr = arr.concat(jsVal) return newArr.length === arr.length + 1 } ), { quiet: true }) console.log("de Morgan's laws hold?", result1) console.log("index of false in a nonempty array is at least 0?", result2) console.log("concatenating on a random JS value increases length by 1?", result3)`**