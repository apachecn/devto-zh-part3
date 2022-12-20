# 用 JSVerify 进行属性测试第三部分

> 原文：<https://dev.to/glebec/property-testing-with-jsverify-part-iii-5bp0>

# 第三部分:中级 JSVerify 用法

*   在第一部分中，我们探索了属性测试的概念，并看到了 JSVerify 的基本用法。
*   在[第二部分](https://dev.to/glebec/property-testing-with-jsverify-part-ii-1jcg)中，我们详细查看了 JSVerify 自述文件的语言，包括`generator`、`shrink`和`arbitrary`等类型的含义。
*   有了概念知识和特定于库的术语，我们现在准备更深入地使用 JSVerify。

## 利用内置套利

JSVerify 的特色是基于字符串的[领域特定语言](https://en.wikipedia.org/wiki/Domain-specific_language) (DSL)文档[这里](https://github.com/jsverify/jsverify#dsl-for-input-parameters)用于描述你想要测试的任意数据。最简单的例子是通过名字使用内置的[原始套利](https://github.com/jsverify/jsverify#primitive-arbitraries)。

```
// a dummy property used to produce example output
jsc.assert(jsc.forall(
    'integer', 'nat', 'number', 'bool', 'falsy', 'char',
    (int, nat, num, bool, fls, chr) => {
        console.log(int, nat, num, bool, fls, chr)
        return true
    }
)) 
```

Enter fullscreen mode Exit fullscreen mode

| `int` | `nat` | `num` | `bool` | `fls` | `chr` |
| --- | --- | --- | --- | --- | --- |
| `6` | `4` | `-5.293295592069626` | `false` | `''` | `´` |
| `-3` | `6` | `6.371534138917923` | `false` | `false` | `"` |
| `-6` | `2` | `6.8075778381899` | `false` | `NaN` | `-` |
| `5` | `2` | `0.37970419274643064` | `false` | `null` | `ø` |
| `-1` | `6` | `-8.00378399901092` | `true` | `0` | `\u001e` |
| `-5` | `3` | `11.162152161821723` | `true` | `false` | `n` |
| `4` | `4` | `-8.52733048191294` | `false` | `NaN` | `Z` |
| `14` | `12` | `-20.764524302445352` | `false` | `undefined` | `' '` |
| `4` | `14` | `-2.3603379679843783` | `false` | `undefined` | `«` |
| `...` | `...` | `...` | `...` | `...` | `...` |

但是，请注意，使用 DSL 是可选的；DSL 引用的功能可直接使用，也是`forall`的有效输入。

```
// equivalent to previous example
jsc.assert(jsc.forall(
    jsc.integer, jsc.nat, jsc.number, jsc.bool, jsc.falsy, jsc.char,
    (int, nat, num, bool, fls, chr) => {
        console.log(int, nat, num, bool, fls, chr)
        return true
    }
)) 
```

Enter fullscreen mode Exit fullscreen mode

一些内置的原始仲裁是参数化的——它们充当带参数的函数，用于最小和最大界限或从一系列选项中进行选择。然而，使用字符串 DSL 来表达这一点可能会很棘手或者不被支持。在这种情况下，使用普通的 JS 函数更容易:

```
// a dummy property used to produce example output
jsc.assert(jsc.forall(
    jsc.integer(-2, 2), jsc.nat(5), jsc.elements([true, null]),
    (int, nat, trueOrNull) => {
        console.log(int, nat, trueOrNull)
        return true
    }
)) 
```

Enter fullscreen mode Exit fullscreen mode

| `int` | `nat` | `trueOrNull` |
| --- | --- | --- |
| `2` | `2` | `true` |
| `2` | `1` | `null` |
| `-1` | `5` | `null` |
| `1` | `5` | `true` |
| `0` | `0` | `null` |
| `...` | `...` | `...` |

## 组合子:从现有套利交易中衍生出新的套利交易

我们已经看到:

*   原子(“原始”)仲裁，例如
    *   `jsc.bool : arbitrary bool`
    *   `jsc.integer : arbitrary integer`
*   将普通值映射到任意值的函数，例如
    *   `jsc.integer(min: integer, max: integer) : arbitrary integer`
    *   `jsc.elements(args: array a): arbitrary a`

该层级中的下一级是:

*   *接受套利*并返回*新套利*的函数，例如
    *   `jsc.array(arb: arbitrary a): arbitrary (array a)`
    *   `dict(arb: arbitrary a): arbitrary (dict a)`

这种库设计被称为[组合子模式](https://wiki.haskell.org/Combinator_pattern)，是构建复杂功能的一种强大而简洁的方式。给定各种原始值和选择的组合子，可以产生新的值，这些值可以反馈到这些组合子中，以此类推——这是一个自我强化的循环，创造了组合爆炸的可能性。

我们已经看到了一个示例组合符:`jsc.array`函数接受一个任意值(例如`jsc.nat`)并返回一个新的任意值(它生成该任意值的*数组)。* 

```
jsc.forall(
    '[nat]', jsc.array(jsc.nat), // these are both equivalent
    (arrayOfNats1, arrayOfNats2) => { /* use generated values */ }
) 
```

Enter fullscreen mode Exit fullscreen mode

| `arrayOfNats1` | `arrayOfNats2` |
| --- | --- |
| `[7, 40, 4, 2]` | `[18, 40, 15, 6]` |
| `[11]` | `[26]` |
| `[]` | `[]` |
| `[]` | `[14, 13, 17, 12]` |
| `[3]` | `[3, 6, 5]` |
| `...` | `...` |

由于`jsc.array`的返回值本身是一个`arbitrary`，它可以再次成为`jsc.array`的输入，从而产生数组中的数组:

```
jsc.forall(
    '[[nat]]', jsc.array(jsc.array(jsc.nat)), // again, equivalent
    (arrArrNat1, arrArrNat2) => { /* use generated values */ }
) 
```

Enter fullscreen mode Exit fullscreen mode

| `arrArrNat1` | `arrArrNat2` |
| --- | --- |
| `[ [], [ 2, 10 ] ]` | `[ [ 1 ] ]` |
| `[ [ 10, 26, 9, 7 ] ]` | `[]` |
| `[ [ 22, 3, 17, 16 ], [ 13 ], [ 0, 23 ] ]` | `[ [ 5, 10, 18, 4, 17 ], [ 17, 21 ] ]` |
| `...` | `...` |

至此，我们可以开始明白为什么 JSVerify 包含了一个基于字符串的 DSL 来指定输入类型。`'[[nat]]'`比`jsc.array(jsc.array(jsc.nat))`简洁多了。另一方面，方法调用可以被类型化(例如，对于 TypeScript)，不需要学习新的语法，并且是 DSL 可能实现的功能的超集。

下面展示了更多的组合子示例:

| 配合 | 字符串 DSL 形式 | 方法调用表单 | 两个示例生成值 |
| --- | --- | --- | --- |
| `pair` | `'pair nat bool'` | `jsc.pair(jsc.nat, jsc.bool)` | ①`[0, true]`②`[0, true]` |
| `tuple` | `'nat & nat & bool'` | `jsc.tuple([jsc.nat, jsc.nat, jsc.bool])` | ① `[0, 18, true]` ② `[9, 2, true]`(每一代将使用所有类型) |
| `sum` | `'char ¦ nat ¦ bool'` | `jsc.sum([jsc.char, jsc.nat, jsc.bool])` | ① `9` ② `'h'`(每代随机选择一种) |
| `fn` | `'a -> bool'` | `jsc.fn(jsc.bool)` | ①将`1`映射到`true`、`'hi'`映射到`false`、`null`映射到`true`的功能……②将所有输入映射到`false`的功能…… |
| `record` | `'{ name: asciinestring; age: nat }'` | `jsc.record({ name: jsc.asciinestring, age: jsc.nat })` | ①`{ name: '^L', age: 37 }`②`{ name: '^L', age: 37 }` |

⚠️ ( *注意:对于`sum`类型，我们用“断条”符号`¦`代替管道字符`|`，因为 Markdown 语法在表中使用管道字符。*

综上所述，您可以用最少的工作量指定相当复杂的任意数据。如果您的属性失败，JSVerify 可以自动收缩失败的输入用例。

```
jsc.forall(
    '[{ start: integer & integer; end: integer & integer }]',
    (lineSegments) => { /* use generated values */ }
) 
```

Enter fullscreen mode Exit fullscreen mode

| `lineSegments` |
| --- |
| `[ {start: [26, 6], end: [20, -4]} ]` |
| `[]` |
| `[ {start: [11, -6], end: [-14, 8]}, {start: [-14, -15], end: [11, -15]} ]` |
| `...` |

## 转化套利

有了提供的原始套利和组合子，你可以做很多事情，但不是所有事情。举一个非常小的例子，如果非常明确地想要任意的*偶数的自然数*呢？一个策略是从一些接近的东西(整数)开始，并在你的属性中转换它们:

```
jsc.assert(jsc.forall(
    'integer',
    (integer) => {
        const evenInt = integer * 2
        return funcThatUsesEvens(evenInt) === 'cool'
    }
)) 
```

Enter fullscreen mode Exit fullscreen mode

这是可行的(对于某些问题来说)，但是有一个缺点:如果我们遇到失败，记录的失败输入案例是*预转换*。

```
Error: Failed after 13 tests and 9 shrinks. rngState: 93bc7e5e6160384cf8;
Counterexample: 7 
```

Enter fullscreen mode Exit fullscreen mode

我们真正想要的是一个类型为*任意偶数*的值，所以失败的情况是输入到我们测试函数中的偶数。JSVerify 提供了通过[`.smap`(满射映射)原型方法](https://github.com/jsverify/jsverify#arbitrary-data) :
将现有的任意对象转换成新的任意对象的能力

```
.smap(f: a -> b, g: b -> a, newShow: (b -> string)?): arbitrary b 
```

Enter fullscreen mode Exit fullscreen mode

换句话说，给定`arbA : arbitrary a`，您可以通过提供(至少)两个函数来创建转换后的`arbB : arbitrary b`:

*   `f: a -> b`是一个将输入案例转换成你想要的形式的函数
*   `g: b -> a`是一个将派生值映射回原始类型的函数(收缩所必需的)

应用于我们偶数的例子:

```
// arbitrary even
const arbEven = jsc.integer.smap(
    n => n * 2, // function to transform integer numbers to evens
    e => e / 2  // function to map even numbers back to integers
)

jsc.assert(jsc.forAll(arbEven, e => {
    return funcThatUsesEvens(e) === 'cool'
})) 
```

Enter fullscreen mode Exit fullscreen mode

正如文档中提到的，传递给`smap`的第一个函数应该是 [*满射*](https://en.wikipedia.org/wiki/Surjective_function#Surjections_as_right_invertible_functions) 。如果您使用第二个函数“映射回”您的导出值`x`，那么第一个函数应该会将您**准确地带回`x`** 。也就是说，`f(g(x))`总是等于`x`。

随着我们的`arbitrary even`类型的实现，我们的失败现在显示失败的实际偶数:

```
Error: Failed after 13 tests and 9 shrinks. rngState: 93bc7e5e6160384cf8;
Counterexample: 14 
```

Enter fullscreen mode Exit fullscreen mode

## 从零开始定义自定义套利

在第二部分中，我们看到了一个假想的“hydra”类型值的示例生成器、收缩和显示函数。

```
const generatorHydra = (size) => {
    const hydra = { heads: size ** 2 }
    return hydra
}

const shrinkHydra = (hydra) => {
    if (hydra.heads <= 0) return []
    return [{ heads: hydra.heads - 1 }] // valid but inefficient
}

const showHydra = (hydra) => `{ heads: ${hydra.heads} }` 
```

Enter fullscreen mode Exit fullscreen mode

这三个函数*几乎*一起构成了自定义`arbitrary hydra`值的完整手动定义。

```
const almostArbitraryHydra = {
    generator: generatorHydra,
    shrink: shrinkHydra,
    show: showHydra
} 
```

Enter fullscreen mode Exit fullscreen mode

然而，在 JSVerify 中使用它作为`arbitrary hydra`之前，我们需要应用一点香料。JSVerify 假设生成者、收缩者和套利者会有特定的方法——例如，套利者会有前面显示的`.smap`。为了将这些方法添加到您自己的从头仲裁中，JSVerify 提供了三个名为`bless`的帮助函数。

```
const arbitraryHydra = jsc.bless({
    generator: jsc.generator.bless(generatorHydra),
    shrink: jsc.shrink.bless(shrinkHydra),
    show: showHydra
}) 
```

Enter fullscreen mode Exit fullscreen mode

*   `jsc.bless(arbLike: {...}) : arbitrary a`
*   `jsc.generator.bless(genLike: (size: nat) -> a) : generator a`
*   `jsc.shrink.bless(shrinkLike: a -> [a]) : shrink a`
*   (没有`show`的`bless`——不需要)

`bless`函数将伪生成器/伪收缩/伪仲裁“升级”成适合于例如`jsc.forall`使用的真正完整版本。

随着我们的神圣九头蛇完全进化，我们可以在一个属性中使用它:

```
jsc.assert(jsc.forAll(hydra, (h) => {
    return h.heads >= 0
})) 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想使用字符串 DSL 通过名称任意引用 hydra，我们可以将它添加到一个名称空间对象中，并将该映射作为倒数第二个参数传递给`forall` :

```
const env = { hydra: hydra }

jsc.assert(jsc.forAll('hydra', env, (h) => {
    return h.heads >= 0
})) 
```

Enter fullscreen mode Exit fullscreen mode

## 衍生发电机

当定义新的套利交易时，有时从现有的套利交易开始，并对它们使用组合子(如`array`)或变换(如`smap`)是有用的。类似地，当定义我们自己的生成器时，从一个现有的生成器开始有时是有用的。

### 生成器组合子

一些相同的[组合子](https://github.com/jsverify/jsverify#generator-functions)存在于发电机和套利交易中。例如:

*   `jsc.generator.array(gen: generator a): generator (array a)`
    *   给定一个`a` 的**生成器，`array`方法返回一个`a`** 的**数组的生成器**
*   `jsc.generator.tuple(gens: (generator a, generator b...)): generator (a, b...)`
    *   给定一个**阵列的生成器**用于`a`、`b`等。，`tuple`方法返回一个生成器，用于生成 `[a, b]`等形式的**数组**

```
// (size: nat) -> [char, [nat], bool]
const generatorCharNatsBool = jsc.generator.tuple([
    jsc.char.generator,
    jsc.generator.array(jsc.nat.generator),
    jsc.bool.generator
])
// example outputs:
// ['h', [0, 3], true]
// ['#', [91, 3, 84], false] 
```

Enter fullscreen mode Exit fullscreen mode

注意，生成器组合子方法存在于`jsc.generator.COMBINATOR_NAME`中，而原语的现有生成器可以在`jsc.PRIMITIVE_NAME.generator`中找到。

### 改造发电机

转换一个生成器比转换一个完整的任意。对于任意值，`smap`需要一对函数——一个派生数据，一个将派生数据映射回原始类型。发电机有一个`map`方法，只需要变压器:

```
(gen: generator a).map(f: a -> b): generator b 
```

Enter fullscreen mode Exit fullscreen mode

```
// (size : nat) -> nat
const evenGenerator = jsc.generator.nat.map(n => n * 2)
// example outputs:
// 12
// 0
// 54

const yellGenerator = jsc.generator.string.map(s => s + '!')
// example outputs:
// '!'
// 'c><92&X.!'
// 's'72$!' 
```

Enter fullscreen mode Exit fullscreen mode

给定一个`a`类型值的生成器和一个转换函数`a -> b`,`map`方法将会给你一个`b`类型值的生成器。

* * *

*顺便提一下:这正是定义一个[函子](https://medium.com/@tzehsiang/javascript-functor-applicative-monads-in-pictures-b567c6415221#e4de)所需要的类型——一个可以被映射的结构。就像数组可以被映射(变换数组中的值，但不去管数组结构)，生成器可以被映射(变换函数的输出，但不去管它的函数性质/输入类型)。函子在函数式编程中大量出现，但是你不需要知道它们的理论来使用`map`。*

* * *

### 独立生成的值

让我们考虑更具挑战性的东西——由其他生成器定制的生成器。例如，我们可能希望生成一个非空的数组值，以及该数组上的一个随机索引，例如传递给`includes`。

```
for all a ∈ Array x | a.length > 0,
    for all i ∈ Nat | i < a.length,
        a.includes(a[i]) 
```

Enter fullscreen mode Exit fullscreen mode

*   我们知道如何生成任意的非空数组，例如`jsc.nearray(jsc.bool)`。
*   我们知道如何生成最大尺寸的任意自然图像，例如`jsc.nat(8)`。

我们还没有看到的是，如何使一个生成器(例如`nat`)的输入依赖于另一个生成器(例如`nearray`)输出的值。作为第一次尝试，像`map`这样的东西似乎很有希望:

```
// Will not work
const genArrIdx =
    jsc.generator.nearray(jsc.bool.generator) // gen [bool]
        .map(arrBools => { // [bool] ->
            const maxIdx = arrBools.length - 1
            const arbIdx = jsc.nat(maxIdx)
            return arbIdx.generator // gen nat
        }) // -> gen (gen nat) 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，这并不奏效——我们实际上已经创建了一个(nat 的生成器)的*生成器。考虑这个从数组映射返回一个数组的类似例子:* 

```
const result =
    [1, 2, 3] // array int
        .map(num => { // int ->
            return [num, num] // array int
        }) // -> array (array int)

console.log(result) // [[1, 1], [2, 2], [3, 3]] 
```

Enter fullscreen mode Exit fullscreen mode

正如将数组映射到数组会返回嵌套数组一样，将生成器映射到生成器会返回嵌套生成器。为了取消这种嵌套，我们需要一个新函数`.flatMap`。

```
// Almost there…
const genArrIdx =
    jsc.generator.nearray(jsc.bool.generator)
-       .map(    arrBools => { +       .flatMap(arrBools => {
            const maxIdx = arrBools.length - 1
            const arbIdx = jsc.nat(maxIdx)
            return arbIdx.generator // gen nat
-       }) // -> gen (gen nat) +       }) // -> gen nat 
```

Enter fullscreen mode Exit fullscreen mode

这给了我们一个没有嵌套的`generator nat`。但是您可能还记得，我们真的想要一个数组以及在该数组上随机生成的有效索引，而不仅仅是索引。为了解决这个问题，我们可以使用一个内部的`map`来组合我们的两个结果:

```
// Works
const genArrIdx =
    jsc.generator.nearray(jsc.bool.generator)
        .flatMap(arrBools => {
            const maxIdx = arrBools.length - 1
            const arbIdx = jsc.nat(maxIdx)
            return arbIdx.generator
+               .map(idx => {
+                   return [arrBools, idx]
+               }) // -> gen [array bool, nat] -       }) // -> gen nat +       }) // -> gen [array bool, nat] 
```

Enter fullscreen mode Exit fullscreen mode

更简洁一点，并且突出显示了 JS 语法:

```
// generator [array bool, nat]
const genArrIdx =
    jsc.generator.nearray(jsc.bool.generator).flatMap(arrBools => {
        const maxIdx = arrBools.length - 1
        const arbIdx = jsc.nat(maxIdx)
        return arbIdx.generator.map(idx => [arrBools, idx])
    }) 
```

Enter fullscreen mode Exit fullscreen mode

除了传递给它的函数应该返回值的**生成器而不仅仅是普通值之外，`flatMap`方法就像`map`。在`map`会生成嵌套生成器的地方，`flatMap`会移除嵌套。** 

```
(generator a)    .map(a ->            b ) : generator b
(generator a).flatMap(a -> (generator b)) : generator b 
```

Enter fullscreen mode Exit fullscreen mode

* * *

*顺便提一下:这正是定义[单子](https://medium.com/@tzehsiang/javascript-functor-applicative-monads-in-pictures-b567c6415221#42a9)所需要的类型——一种在映射后可以取消嵌套的结构。像函子一样，单子也经常出现在函数式编程中，尽管对于这个库，你真正需要知道的是“在传递给 flatMap 的函数中返回一个生成器”。*

* * *

从技术上来说，`flatMap`只是一个方便的方法。我们可以手动定义数组+索引生成器，如下所示:

```
const genArrIdx =
    jsc.generator.bless((size) => {
        // create and use array generator:
        const genArrBool = jsc.generator.nearray(jsc.bool.generator)
        const arrBools = genArrBool(size)
        const maxIdx = arrBools.length - 1
        // create and use index generator:
        const genIdx = jsc.nat(maxIdx).generator
        const idx = genIdx(size)
        // return resulting generated values
        return [arrBools, idx]
    }) 
```

Enter fullscreen mode Exit fullscreen mode

请注意我们自己是如何将`size`参数传递给所有生成器的；`flatMap`为我们处理这个。

不管你喜欢哪种风格(应用`size`还是使用`flatMap`),当你对相关生成器排序时，你必须为结果编写你自己相应的收缩函数。心理医生不是自动提供的，不像 T2 那样。

## 写作缩

像完整的`arbitrary a`和更低级的`generator a`函数一样，`shrink`函数可以从零开始编写，或者从现有的收缩派生出[。推导方法是`smap`，和套利一样。](https://github.com/jsverify/jsverify#shrink-functions) 

```
(shrink a).smap(f: a -> b, g: b -> a): shrink b 
```

Enter fullscreen mode Exit fullscreen mode

```
const shrinkEven = jsc.nat.shrink.smap(
    n => n * 2, // convert nat to even
    e => e / 2  // convert even to nat
)

console.log(shrinkEven(32)) // [ 16, 24, 28, 30 ] 
```

Enter fullscreen mode Exit fullscreen mode

前面，我们定义了自己的`generator [array bool, nat]`函数，用于构造具有随机有效索引(小于数组长度)的随机数组对。相应的收缩必须保持`nat`值仍然是有效索引的属性。在这种情况下，第一次尝试可能会重用现有的收缩:

```
// Looks good, but there is an error…
const shrinkArrIdx = jsc.shrink.bless(([arr, idx]) => {
    // shrink arrays, ensure valid index
    const shrinkArrBool = jsc.shrink.nearray(jsc.bool.shrink)
    const shrunkArrs = shrinkArrBool(arr)
    const shrunkArrsWithIdx = shrunkArrs.map((shrunkArr) => {
        const validIdx = Math.min(shrunkArr.length - 1, idx)
        return [shrunkArr, validIdx]
    })
    // shrink index, re-use existing array
    const shrunkIdxs = jsc.nat.shrink(idx)
    const shrunkIdxsWithArr = shrunkIdxs.map((shrunkIdx) => {
        return [arr, shrunkIdx]
    })
    // flat list of shrunk values? nope, this breaks:
    return [...shrunkArrsWithIdx, ...shrunkIdxsWithArr]
}) 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，这失败了。

```
TypeError: shrunkArrsWithIdx is not iterable 
```

Enter fullscreen mode Exit fullscreen mode

多奇怪啊——收缩函数不返回数组吗？这就是类型注释所承诺的:

> "收缩是一个函数`a -> [a]`，返回较小的值."

然而实际上，基于数组的收缩[目前返回由 JSVerify 的同一作者](https://github.com/jsverify/jsverify/blob/c03a67efe525e79ec37b3f314fc9f6a30182be70/lib/shrink.js#L189-L197)[`lazy-seq`包](https://github.com/phadej/lazy-seq)定义的*惰性序列* 。这提高了收缩阵列的性能，否则阵列的组合成本会很高。

查看 [`lazy-seq` API 文档](https://github.com/phadej/lazy-seq#api)，我们可以看到惰性序列可以使用`append`方法进行组合——这就是我们在这里要做的。

```
const shrinkArrIdx = jsc.shrink.bless(([arr, idx]) => {
    if (!arr.length) return []
    const shrinkArrBool = jsc.shrink.nearray(jsc.bool.shrink)
    const shrunkArrs = shrinkArrBool(arr)
    const shrunkArrsWithIdx = shrunkArrs.map((shrunkArr) => {
        const validIdx = Math.min(shrunkArr.length - 1, idx)
        return [shrunkArr, validIdx]
    })
    const shrunkIdxs = jsc.nat.shrink(idx)
    const shrunkIdxsWithArr = shrunkIdxs.map((shrunkIdx) => {
        return [arr, shrunkIdx]
    })
-   return [...shrunkArrsWithIdx, ...shrunkIdxsWithArr] +   return shrunkArrsWithIdx.append(shrunkIdxsWithArr) }) 
```

Enter fullscreen mode Exit fullscreen mode

JSVerify 中还没有记录这一点，所以请再次注意，在收缩数组上使用`lazy-seq`方法可能不被官方支持，也不受 [SEMVER](https://semver.org/) 规则的约束。[参见第 297 期](https://github.com/jsverify/jsverify/issues/297)

## 把所有的东西放在一起

下面是我们相对复杂的`arbitrary <array bool, index>`的一个完整的工作实现。

```
// generator <array bool, index>
const genArrIdx =
    jsc.generator.nearray(jsc.bool.generator).flatMap(arrBools => {
        const maxIdx = arrBools.length - 1
        const arbIdx = jsc.nat(maxIdx)
        return arbIdx.generator.map(idx => [arrBools, idx])
    })

// shrink <array bool, index>
const shrinkArrIdx = jsc.shrink.bless(([arr, idx]) => {
    // shrink arrays, ensure valid index
    const shrinkArrBool = jsc.shrink.nearray(jsc.bool.shrink)
    const shrunkArrs = shrinkArrBool(arr)
    const shrunkArrsWithIdx = shrunkArrs.map((shrunkArr) => {
        const validIdx = Math.min(shrunkArr.length - 1, idx)
        return [shrunkArr, validIdx]
    })
    // shrink index, re-use existing array
    const shrunkIdxs = jsc.nat.shrink(idx)
    const shrunkIdxsWithArr = shrunkIdxs.map((shrunkIdx) => {
        return [arr, shrunkIdx]
    })
    // lazy sequence of shrunk values
    return shrunkArrsWithIdx.append(shrunkIdxsWithArr)
})

// arbitrary <array bool, index>
const arbArrIdx = jsc.bless({
    generator: genArrIdx,
    shrink: shrinkArrIdx
}) 
```

Enter fullscreen mode Exit fullscreen mode

这个故事的寓意可能是“套利者的发电机排序其他发电机比他们的价值更麻烦。”当然，这将取决于手头的实际问题，并且在实践中，似乎并不经常需要这样的序列发生器。很多时候，可以使用`smap`获得一个新的任意值。此外，严格地说，任意可能会忽略收缩，但这在一定程度上降低了属性测试的有用性。最终要由开发者来权衡这些选择。

## 总结

*   任意性:带有生成器、收缩和显示 fns 的“受祝福”对象
    *   JSVerify 提供了一系列类似于`jsc.nat`的原始(原子)仲裁。
    *   使用类似`jsc.array`的组合函数将套利组合成更复杂的套利。
    *   使用`.smap`方法将套利转化为新的套利。
    *   通过在包装了生成器、收缩和可选显示函数的对象上使用`jsc.bless`方法，从头开始定义自定义仲裁。
    *   仲裁可以直接传递给`forall`，或者在`env`参数的帮助下通过字符串 DSL 名称引用。
*   生成器:从大小到伪随机数据的“祝福”函数
    *   内置仲裁的生成器可以作为`jsc.ARB_NAME.generator`访问。
    *   使用提供的组合器如`jsc.generator.array`组合生成器。
    *   使用`map`方法转换发电机。
    *   使用`flatMap`方法的序列发生器。
    *   通过对从大小到随机数据的函数调用`jsc.generator.bless`来定义自定义的从头生成器。
    *   应该使用`jsc.random`和/或通过使用`size`调用现有生成器来实现从零开始的生成器中的随机性。
*   收缩:从数据到更小数据集合的“祝福”功能
    *   内置套利的收缩可以作为`jsc.ARB_NAME.shrink`访问。
    *   使用提供的组合子(如`jsc.shrink.array`)合并收缩。
    *   使用`smap`方法进行变换收缩。
    *   通过对从数据到“较小”值数组(或`lazy-seq`值)的函数调用`jsc.shrink.bless`来定义自定义的从头收缩。
    *   一个好的收缩应该把小的和/或可能失败的值放在输出集合的开始附近。
*   显示:从数据到字符串的函数
    *   默认的`show`功能是`JSON.stringify`
    *   对于一个`show`函数来说，输出有效的 JS 代码是一个很好的实践，尽管不是绝对必要的

使用现有的任意值本身就是快速、简单和强大的。组合子和`smap`有时也是一种相对不费力的方式，可以产生想要的任意值。更高级的用例，构建具有相互依赖性的复杂数据，可能需要更流畅地运用`flatMap`和/或手动定义收缩函数。然而，为您的属性测试提供一个完全定义的任意测试的好处可能是值得的。

在最好的情况下，属性测试可以为少量的工作和代码提供大量的价值和信心。我希望随着 JavaScript 社区对这种方法的更多了解，像 JSVerify 这样的库可以得到更广泛的探索、维护、扩展和支持。考虑使用 JSVerify 来扩充你的下一个项目的测试套件——你可能会发现它能捕捉到一些你在几个单元测试中没有注意到的错误。

### 补遗

在写这篇文章的过程中，我逐渐意识到 JSVerify 是有用的，但也有一些粗糙的边缘和突出的问题。我鼓励任何对 JS 属性测试感兴趣的人也看看这个领域的一些竞争工具，包括:

*   [快速检查](https://github.com/dubzzz/fast-check/)
*   [测试检查](https://github.com/leebyron/testcheck-js)