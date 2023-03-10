# 基于属性的测试简介

> 原文：<https://dev.to/gcanti/introduction-to-property-based-testing-17nk>

在上几篇关于 [Eq](https://dev.to/gcanti/getting-started-with-fp-ts-setoid-39f3) 、 [Ord](https://dev.to/gcanti/getting-started-with-fp-ts-ord-5f1e) 、[半群](https://dev.to/gcanti/getting-started-with-fp-ts-semigroup-2mf7)和[幺半群](https://dev.to/gcanti/getting-started-with-fp-ts-monoid-ja0)的帖子中我们看到实例必须遵守某些**定律**。

那么我们如何确保我们的实例是合法的呢？

## 基于属性的测试

基于属性的测试是另一种测试代码的方法，它是对经典单元测试方法的补充。

它通过对照**多个生成的随机条目**进行测试，试图发现导致属性错误的输入。在失败的情况下，基于属性的测试框架提供反例和导致生成的种子。

让我们将基于属性的测试应用于`Semigroup`法则:

**关联性** : `concat(concat(x, y), z) = concat(x, concat(y, z))`

我将使用[快速检查](https://github.com/dubzzz/fast-check)，这是一个用 TypeScript 编写的基于属性的测试框架。

## 测试一个`Semigroup`实例

我们需要三种配料

1.  类型`A`的`Semigroup<A>`实例
2.  编码关联法则的*属性*
3.  一种生成`A`类型随机值的方法

### 实例

作为例子，我将使用下面的

```
import { Semigroup } from 'fp-ts/Semigroup'

const S: Semigroup<string> = {
  concat: (x, y) => x + '  ' + y
} 
```

Enter fullscreen mode Exit fullscreen mode

### 属性

属性只是一个谓词，即返回一个`boolean`的函数。如果谓词返回`true`，我们说该属性成立。

所以在我们的例子中，我们可以将`associativity`属性定义为

```
const associativity = (x: string, y: string, z: string) =>
  S.concat(S.concat(x, y), z) === S.concat(x, S.concat(y, z)) 
```

Enter fullscreen mode Exit fullscreen mode

### `Arbitrary<A>`

一个`Arbitrary<A>`负责生成`A`类型的随机值。我们需要一个`Arbitrary<string>`，幸运的是`fast-check`提供了许多内置的套利

```
import * as fc from 'fast-check'

const arb: fc.Arbitrary<string> = fc.string() 
```

Enter fullscreen mode Exit fullscreen mode

让我们一起来总结一下

```
it('my semigroup instance should be lawful', () => {
  fc.assert(fc.property(arb, arb, arb, associativity))
}) 
```

Enter fullscreen mode Exit fullscreen mode

如果`fast-check`没有引发任何错误，我们可以更加确信我们的实例是定义良好的。

## 测试一个`Monoid`实例

让我们看看当一个实例**无法无天**时会发生什么！

作为例子，我将使用下面的

```
import { Monoid } from 'fp-ts/Monoid'

const M: Monoid<string> = {
  ...S,
  empty: ''
} 
```

Enter fullscreen mode Exit fullscreen mode

我们必须将`Monoid`定律编码为属性:

*   **正确的身份** : `concat(x, empty) = x`
*   **左身份** : `concat(empty, x) = x`

```
const rightIdentity = (x: string) => M.concat(x, M.empty) === x

const leftIdentity = (x: string) => M.concat(M.empty, x) === x 
```

Enter fullscreen mode Exit fullscreen mode

最后写一个测试

```
it('my monoid instance should be lawful', () => {
  fc.assert(fc.property(arb, rightIdentity))
  fc.assert(fc.property(arb, leftIdentity))
}) 
```

Enter fullscreen mode Exit fullscreen mode

当我们运行测试时，我们得到

```
Error: Property failed after 1 tests
{ seed: -2056884750, path: "0:0", endOnFailure: true }
Counterexample: [""] 
```

Enter fullscreen mode Exit fullscreen mode

太棒了，`fast-check`甚至给了我们一个反例:`""`

```
M.concat('', M.empty) = '  ' // should be '' 
```

Enter fullscreen mode Exit fullscreen mode

## 资源

对于一个使测试类型类法则变得容易的库，请查看 [fp-ts-laws](https://github.com/gcanti/fp-ts-laws)