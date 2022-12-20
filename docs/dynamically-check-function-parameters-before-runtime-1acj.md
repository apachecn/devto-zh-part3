# 运行前动态检查函数参数

> 原文：<https://dev.to/hemaka/dynamically-check-function-parameters-before-runtime-1acj>

# 前奏

感谢 TypeScript 的条件语句和自引用类型；可以指示编译器做出*动态*选择。这就是我所说的“动态”——当然，TypeScript 仍然是一个静态的编译时类型检查器。

# 简介

在 TypeScript 中，可以使用`extends`来限制可以赋给泛型的值，但是有一些限制。在本文中，我们将看到如何使用类型别名和交集操作符`&`来增强我们对泛型的限制

# 关于仿制药

在 TypeScript 中，泛型使我们能够表达多态函数、处理各种类型的对象、函数的返回值等等。人们可以认为泛型本质上是一个变量或一个被赋值的占位符，只要它在这个过程中没有被扩大。默认情况下，泛型接受类型`{}`，这几乎是 JavaScript 中的任何东西。比如:

```
declare function id<A>( a: A ) : A 
```

我们提供的任何变量`a`都将是泛型`A`的类型。但是我们也可以在赋值前限制泛型。考虑这个地图功能:

```
declare function map<A, B, FN extends (a:A) => B>( xs: A[], fn: FN ) : B[] 
```

编译器希望`FN`是一个将`A`传递给`B`的函数。`A`和`B`可以是任何东西；但是`FN`必须是一个 arity 为 1 的函数。这是因为通过使用`extends`，已经将`FN`缩小为给定结构的函数。

但是我们能做的有限。例如，如果我们想要构造一个合并函数来静态地检查两个对象是否共享任何键呢？

我们可以从创建一个类型别名开始，它返回两个对象之间的共享密钥:

```
type CommonKeys< A, B > = Extract<keyof A, keyof B> 
```

但是我们不能这么做:

```
type Validate<A,B> = CommonKeys<A,B> extends never ? B : never
// 🚫 Error, B has a circular constraint
declare function safeMerge< A, B extends Validate<A,B>>( 
  objA: A, 
  objB: B 
): A & B 
```

`B`声明时不能引用自身；无论如何，声明中不允许使用 and 条件。那么我们该如何着手呢？

# 路口

为了解决这个问题，我们观察到任何类型与自身的交集就是自身；而有了`never`就永远是`never`。因此，我们首先分配类属和*，然后使用交集进行*验证。让我们看看如何用它来构造一个合并函数，要求两个对象都有唯一的键:

```
type CommonKeys< A, B > = Extract< keyof A, keyof B > 
declare function safeMerge< A, B >( 
  objA: A, 
  objB: B & ( 
    CommonKeys<A,B> extends never 
      ? B // B & B is B
      : never // B & never is never
  ) 
): A & B 
```

有用吗？

```
// 🚫 Error
const mergedObj = safeMerge( { a: 1 }, { a : 2, b: 3 } ) 
```

很好，我们得到了一个编译错误！两个对象在键“a”上重叠，这是我们不允许的。

```
// ✅ Works!
const mergedObj = safeMerge( { a: 1 }, { b : 2 } ) 
```

有用！两个对象都不共享一个键，我们的合并不会覆盖任何键-值对。

# 将验证类型别名作为模式

这段类型代码要求我们思考这里发生了什么:

```
 B & ( 
    CommonKeys<A,B> extends never 
      ? B // B & B is B
      : never // B & never is never
  ) 
```

我们可以使用三个助手使它更简洁一点:

```
type IsNever<T> = [T] extends [never] ? 1 : 0;
type WhenNever<Evaluate,Return> = IsNever<Evaluate> extends 1 ? Return : never
type WhenNotNever<Evaluate,Return> = IsNever<Evaluate> extends 1 ? never : Return 
```

如果你想知道为什么`IsNever`把`T`放在一个数组中，这是为了防止联合分布。我们想知道`T`是否是`never`，而不是它的联合是否包含 never。我们将使用这个测试来确定是否想要引发一个编译器错误。

对于 merge 函数，我们希望在`A`和`B`共享键时创建一个错误。我们不想让密钥被覆盖。没有公共键会导致`never`，这正是我们想要的。所以我们将如下构造我们的类型助手:

```
/**
 * Validates that keys in object A do not overlap with object B
 * return A if true and never if not.
 */
type ValidateKeysDontOverlap<A,B> = WhenNever< CommonKeys<A,B>, A > 
```

我们可以将它放入类型验证库中，并遵循所有验证类型都以`Validate`开始的惯例。那么我们可以这样使用:

```
declare function safeMerge< A, B >( 
  objA: A, 
  objB: B & ValidateKeysDontOverlap<B,A>
): A & B 
```

# 一个验证型标准

如果这种模式按照某种约定被标准化，那将会非常有用。我个人目前的想法是，**验证**类型应该在有效时返回第一个参数，否则返回`never`。因为 TypeScript 类型别名可以有可选的泛型，例如:

```
type Example<A,B = A> = .... 
```

这里，如果没有通过，第二个泛型 B 将被设置为 A。因此至少需要第一个参数。然而，据我所知，还没有很多人谈论这个，我所知道的用例范围仅限于我自己的。

# 结论

TypeScript 使我们能够在某些限制范围内缩小泛型的范围。我们可以通过将泛型与类型别名交叉来克服这些限制，这将静态地计算复杂的情况，并返回与我们交叉的相同类型，或者如果我们想让编译器抱怨我们静态分析的结果，则返回`never`。

为了更好地交流我们正在做的事情，为这种模式采用某种标准可能是有用的。用于这种模式的类型别名可能以名称`Validate`开始，并返回它需要的第一个参数，否则返回类型`never`。