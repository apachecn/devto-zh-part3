# 用 JSVerify 进行属性测试第二部分

> 原文：<https://dev.to/glebec/property-testing-with-jsverify-part-ii-1jcg>

# 第二部分:js 验证文件和细节

在第一部分中，我们探索了什么是属性检查，它相对于传统单元测试的好处，并且了解了使用 JSVerify 库的一些基础知识。

JSVerify 的官方描述是“基于属性的检查。比如 QuickCheck。”由于 QuickCheck 是一个 [Haskell](http://haskell.org) 库，JSVerify 使用了 JavaScript 开发人员可能不熟悉的函数式术语和习惯用法。在第二部分中，我们仔细看看 [JSVerify 的 API 文档](https://github.com/jsverify/jsverify#readme)的语言和概念。

## 类型文档

函数式编程和静态类型是高度互补的。类型签名为给定的函数指定了允许的输入和结果输出的集合，这反过来通知程序员如何使用和组合函数。

在 JSVerify 文档中，“类型和函数签名是以受 Coq/Haskell 影响的风格编写的”。JS 中 Haskell 风格的类型签名的最佳解释之一是 [Ramda Wiki](https://github.com/ramda/ramda/wiki/Type-Signatures) 。然而，JSVerify 的风格与 Ramda 有些不同，所以下面是对其特定类型语法的简短介绍。

| 证明文件 | 意义 |
| --- | --- |
| `x : y` | `x`属于类型`y` /必须属于类型`y` /返回类型`y` |
| `myFunc( )` | 名为`myFunc`的库方法 |
| `myFunc(thing: string) : bool` | `myFunc`接受一个参数`thing`，该参数必须是一个`string`，并返回一个`bool` |
| `a` | 任何类型，只要它与类型签名中的其他“`a`”一致 |
| `a -> [a]` | 一个(可能是匿名的)函数，它接受任何类型的参数`a`，并返回一个`a`类型值的数组 |
| `a -> array a` | 同上 |
| `a -> b` | 接受任何类型的参数`a`并返回任何类型的值`b`(可能与`a`相同)的函数 |
| `message: string?` | 一个**可选的**参数`message`必须是一个`string` |
| `...` | 可变数量的附加参数或元素 |
| `= true` | 前述参数的默认值 |
| `arbLike: {…}` | 必须是对象的参数`arbLike` |
| `{ b: bool }` | 其`b`属性属于类型`bool`的对象 |
| `void` | `undefined` |

下面是文档中的一行，例如:

```
assert(prop: property, opts: checkoptions?) : void
  ^      ^       ^       ^         ^     ^      ^
  A      B       C       D         E     F      G

A. documenting the `jsc.assert` function
B. first parameter, named `prop`
C. `prop` must be of type `property` (see below)
D. second parameter, named `opts`
E. `opts` must be of type `checkoptions` (see below)
F. `opts` is an optional parameter
G. `jsc.assert` returns `void` (i.e. `undefined`) 
```

Enter fullscreen mode Exit fullscreen mode

## 键 JSVerify 类型

上面的`property`和`checkoptions`类型肯定不像大家更熟悉的`string`、`bool`等。在类型化的函数式语言中，我们经常根据需要发明自己的类型，以简洁而明确地说明哪个函数使用哪个值。

Vanilla JS 没有自定义类型的一流概念。然而， [JSVerify 的文档](https://github.com/jsverify/jsverify#types)使用自定义类型名作为某些函数返回或预期的特定值的别名。下面是一些最重要的例子。

### `generator a`

> "一个函数`(size: nat) -> a`"

类型`a` 的*生成器是将“大小”(自然数，int > = 0)映射到类型`a`的伪随机值的任何函数。例如，`generator string`类型的函数可能将`0`映射到空字符串，`1`映射到随机选择的字符，`2`映射到随机构造的双字符字符串。*

实际上，`size`输入可以根据发生器的不同而有不同的解释，并不一定是字面上的长度或值。一些例子:

*   `jsc.generator.array`:生成数组的长度将达到 size param 的*对数*。这大概是与性能相关的限制。
*   `jsc.number.generator`:大小是最大的绝对值，例如`5`的大小可能导致`-5`和`+5`之间的任何数字。
*   `jsc.bool.generator`:忽略 size 参数，每个 bool 以 0.5 的概率返回。

```
// example `generator hydra`
const generatorHydra = (size) => {
    const hydra = { heads: size ** 2 }
    return hydra
} 
```

Enter fullscreen mode Exit fullscreen mode

#### 确定性随机性

类似 QuickCheck 的属性测试库的一个主要特征是它们是确定性伪随机的。给定一个种子字符串(通过`jsc.assert`的 options 对象，或者`--jsverifyRngState` CLI 标志)，他们将准确地重现失败的测试用例，帮助开发人员调查失败。

当实现一个定制生成器时，遵守这个实现细节是很重要的，并且**而不是**使用`Math.random`。JSVerify 反而提供了实用函数 [`jsc.random`和`jsc.random.number`](https://github.com/jsverify/jsverify#random-functions) ，它们在内部使用种子来产生可复制的结果。

```
const randomGreetingGenerator = (ignoredSize) => {
    const pseudoRandomIndex = jsc.random(0, 2)
    return ['hi', 'yo', 'sup'][pseudoRandomIndex]
}) 
```

Enter fullscreen mode Exit fullscreen mode

### `shrink a`

> 函数`a -> [a]`，返回*较小的*值

类型为`a` 的*收缩是一个函数，它给定类型为`a`的值，返回类型为`a`的“较小”值的数组。“更小”是一个抽象的概念，每种类型都不同，但通常意味着更少的元素、更接近零的数字等。*

*   `jsc.shrink.integer`:如果输入的整数`i`是`0`，收缩返回一个空数组`[]`；否则，`i`被映射到有限级数`[0, ⌊1/2·i⌋, -⌊1/2·i⌋, ⌊3/4·i⌋, -⌊3/4·i⌋, …]`。
*   `jsc.bool.shrink`:本质上是`bool => bool ? [false] : []`
*   `jsc.elements.shrink`:元素生成器从一个数组中返回随机选择的值，如`[a, b, c]`；elements shrink 返回正在收缩的元素左侧的子数组。萎缩的`c`将回归`[a, b]`；收缩`a`回归`[]`。

```
// example `shrink hydra`
const shrinkHydra = (hydra) => {
    if (hydra.heads <= 0) return []
    // theoretically valid but impractically inefficient:
    return [{ heads: hydra.heads - 1 }]
} 
```

Enter fullscreen mode Exit fullscreen mode

当输入案例没有通过属性检查时，JSVerify 使用 shrink 函数来搜索同样失败的较小案例。如果 JSVerify 发现输出数组中的早期值失败，它可以跳过属性检查和对后期值的收缩。因此，好的收缩将小的、可能失败的值放在它们输出的开始附近:

```
// example `shrink hydra`
const shrinkHydra = (hydra) => {
    if (hydra.heads <= 0) return []
    // a more practical shrink:
    return [
        { heads: 0 }, // a common & minimal edge case
        { heads: Math.floor(hydra.heads * 1/2) }, // potential time-saver
        { heads: Math.floor(hydra.heads * 3/4) },
        { heads: Math.floor(hydra.heads * 7/8) },
        { heads: hydra.heads - 1 } // thorough search
    ]
} 
```

Enter fullscreen mode Exit fullscreen mode

### `show`

> "一个函数`a -> string`"

在 Haskell 中，`show`函数将`Show`类中的任何数据结构转换成一个`String`。最佳实践是该字符串是有效的 Haskell 代码，因此您可以通过将该字符串复制到您的程序中来重建数据。

对于 JSVerify，每当报告一个失败的情况时，`show`将被调用，结果字符串作为`counterexample`包含在错误消息中。

*   `jsc.show.array`:本质上是`arr => '[' + arr.join(', ') + ']'`
*   默认情况下，大多数内置的`show`函数只是`JSON.stringify`

```
// example `show : hydra -> string`
const showHydra = (hydra) => `{ heads: ${hydra.heads} }` 
```

Enter fullscreen mode Exit fullscreen mode

因为可以使用报告的 PRNG 种子来复制失败的案例，所以`show`并不一定要返回有效的 JS 代码，但是出于与 Haskell 相同的原因，这通常是一个好的实践。

### `arbitrary a`

> "三重生成器，收缩和显示功能."
> `{generator: nat -> a, shrink: a -> array a, show: a -> string}`

类型`a` 的*任意值是一个将类型`a`的生成器、收缩和显示功能捆绑在一起的概念。它是在 JSVerify 中通过包装这三个函数的对象实现的。例如，`abitrary string`类型是任何可以按需生成、收缩和显示字符串的对象。* 

```
// example `arbitrary hydra`
const arbitraryHydra = {
    generator: generatorHydra,
    shrink: shrinkHydra,
    show: showHydra
} 
```

Enter fullscreen mode Exit fullscreen mode

在某些情况下，收缩和显示功能实际上是可选的。然而，如果没有收缩，JSVerify 将无法简化失败的案例；他们将被如实报道。

### `property`

在撰写本文时，`property`在 JSVerify 文档中至少有三种不同的用法:

*   参考**库方法** `jsc.property`
*   作为整体的**返回类型**为`jsc.forall`(参见下面的`Y`)
*   作为`forall`的`prop`参数的**返回类型**(见下面的`X`)

```
forall(
    arbs: arbitrary a ...,
    userenv: (map arbitrary)?,
    prop : a -> property       // X
): property                    // Y 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，`X`和`Y`实际上并不是指同一个类型，所以依我拙见[`X`类型应该改名为`verification`](https://github.com/jsverify/jsverify/issues/295) 。在本文的剩余部分，一个`property`类型将指的是`Y`。

那么这个`property`到底是什么类型呢？简而言之，`forall`充当“属性构造者”，其他方法像`assert`、`check`充当“属性消费者”。就官方 JS 类型而言，`forall`返回的值属于`function` :
类型

```
const prop_strSliceEql = jsc.forall('string', str => str.slice() === str)
console.log(typeof prop_strSliceEql === 'function') // true 
```

Enter fullscreen mode Exit fullscreen mode

然而，手动调用一个`property`并不是开发人员的职责。相反，`property`值将被传递给`check`或`assert`来实际使用它们。

```
// assert(prop: property, opts: checkoptions?) : void
jsc.assert(prop_strSliceEql) // runs without error 
```

Enter fullscreen mode Exit fullscreen mode

能够浏览基于类型的文档，注意哪些函数输出和输入是一致的，这是类型化函数编程世界中的一项关键技能。我们实际上没有必要知道`property`类型的值是如何实现的，因为它们是 JSVerify 库方法产生和使用的中间值。

```
forall( ... ) → property
                   ↓
         assert(property, ...) → void 
```

Enter fullscreen mode Exit fullscreen mode

### 验证(非官方)

如上所述，从`prop`返回的`property`和从`forall`返回的`property`不是同一个类型，所以出于我们的目的，我们将(非正式地)把前者称为“验证”

```
forall(
    arbs: arbitrary a ...,
    userenv: (map arbitrary)?,
-   prop : a -> property +   prop : a -> verification ): property 
```

Enter fullscreen mode Exit fullscreen mode

验证值可以是任何类型，但是 JSVerify 根据类型表现出不同的行为:

| 确认 | 行为 |
| --- | --- |
| `true` | 属性被视为已验证 |
| 虚伪的价值观 | 属性被视为失败 |
| 最真实的价值观 | 属性被视为失败，该值被追加到错误信息中 |
| 满足以下条件的表格 | 属性取决于履行价值(见上文) |
| 拒绝的答案 | 属性被视为失败，原因附加到错误信息中 |
| 功能 | 属性验证依赖于函数返回值 |

上面的一些行为没有明确记录，根据使用情况，可能会以未指定的或非预期的方式发生作用。顾客小心上当。

### `checkoptions`

options 对象可选地传递给`check`或`assert`的对象，用于配置测试运行的数量、生成器的大小参数、rng 种子等。这里记载了。

### `env` / `typeEnv` / `map arbitrary`

以下库方法的可选参数:

*   `forall`
*   `compile`
*   `assertForall`
*   `checkForall`
*   `record`
*   `suchthat`

`env`类型是一个对象，它将**仲裁人的名字**(作为字符串键)映射到**仲裁人的定义**(作为对象)。例如:

```
const arbitraryHydra = {
    generator: generatorHydra,
    shrink: shrinkHydra,
    show: showHydra
}

// : env
const env = {
    hydra: arbitraryHydra
} 
```

Enter fullscreen mode Exit fullscreen mode

环境对象用新的自定义仲裁扩展了 JSVerify 内置的任意数据 DSL(例如字符串、数字、仲裁数组)。当您将一个环境传递给`forall`时，它会与缺省值合并，因此您可以从两个集合中指定任意混合的数据:

```
const exampleProp = jsc.forall(
    'nat',             // asking for built-in `arbitrary natural`
    'hydra',           // asking for custom `arbitrary hydra`
    env,               // object providing the `hydra` arbitrary
    (nat, hydra) => {  // property verifier receiving both data
        return hydra.heads + nat > 0
    }
) 
```

Enter fullscreen mode Exit fullscreen mode

(**注意，上面的例子还不行，**，因为我们还没有涉及 JSVerify 的[、【祝福】、](https://github.com/jsverify/jsverify#blessing)生成器、收缩和套利的概念。我们将在[第三部分](https://dev.to/glebec/property-testing-with-jsverify-part-iii-5bp0)中介绍`bless`方法。)

另一方面，不使用字符串 DSL，您可以将自定义仲裁直接传递给`forall`:

```
const exampleProp = jsc.forall(
    'nat',             // asking for built-in `arbitrary natural`
    arbitraryHydra,    // asking for custom `arbitrary hydra`
    (nat, hydra) => {  // property verifier receiving both data
        return hydra.heads + nat > 0
    }
) 
```

Enter fullscreen mode Exit fullscreen mode

我们将在第三部分的[中看到更多关于使用 DSL 与 JS 值的内容。](https://dev.to/glebec/property-testing-with-jsverify-part-iii-5bp0)