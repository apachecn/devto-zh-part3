# TypeScript 和 ReasonML 中的类型和代码流

> 原文：<https://dev.to/yawaramin/typing-and-code-flow-in-typescript-and-reasonml-2paj>

随着 TypeScript 作为 JavaScript 最流行的静态类型工具的出现，有必要看看 TypeScript 和 ReasonML 是如何处理一些典型的等价代码中的静态类型、推理和代码流问题的。这不是苹果之间的比较；然而，就功能而言，它是非常接近的。我的目标读者主要是打字稿和推理人员。如果您理解其中一种语言(直到泛型的基础)，那么由于它们的相似性，您应该能够在实现之间进行映射。

我在这里使用的例子是一个简单的可变地图数据结构。在 TypeScript 中，构建数据结构最惯用的方法是使用类。对于我们的地图数据结构，我们可以这样实现:

```
// MapImpl.ts

class MapImpl<A, B> {
  private map: {[key: string]: B};

  constructor() { this.map = {}; }
  get(a: A): B | undefined { return this.map[JSON.stringify(a)]; }
  put(a: A, b: B): MapImpl<A, B> {
    this.map[JSON.stringify(a)] = b;
    return this;
  }
} 
```

这个映射的工作原理是将值存储在一个 JavaScript 对象中，由给定键的 JSON 序列化字符串作为键。由于 TypeScript 对类和私有成员的良好支持，我们获得了一个相当好的数据抽象:试图访问`MapImpl`对象中的`map`将是一个编译时错误。

## 理智之内

让我们来看看 Reason 中的对等物(使用 BuckleScript 编译器来针对 JavaScript):

```
/** MapImpl.rei */

type t('a, 'b);

let get: ('a, t('a, 'b)) => option('b);
let make: unit => t('a, 'b);
let put: ('a, 'b, t('a, 'b)) => t('a, 'b);

/* MapImpl.re */

type t('a, 'b) = Js.Dict.t('b);

let make = Js.Dict.empty;
let get(a, t) = a
  |> Js.Json.stringifyAny
  |> Js.Option.andThen((. key) => Js.Dict.get(t, key));
let put(a, b, t) = {
  a
  |> Js.Json.stringifyAny
  |> Js.Option.map((. key) => Js.Dict.set(t, key, b))
  |> ignore;

  t;
}; 
```

你会注意到的第一件事是，与 TypeScript 版本的单个文件相比，这是*的两个*文件。在 OCaml 中(这也是原因)，要使一个数据类型抽象，你需要对其他代码隐藏它的实现细节，最惯用的方法是给它一个*接口文件* ( `.rei`),它只声明类型(`type t('a, 'b)`)，但不定义它。

这是 OCaml 世界中数据抽象的本质——类型`t`是抽象的。我们只看到它有两个类型参数`'a`和`'b`，有三个函数处理这个类型。顺便提一下，我们可以在接口文件中以不同的顺序排列这些类型和函数——无论哪个顺序最适合我们的用例。在这种情况下，我已经按字母顺序对值进行了排序，以便于查找。

## 实现了

在这个例子中，您会注意到 Reason 实现更加详细。这是因为它更加明确:操作在其类型中显示它们返回‘无结果’(`None`)，并且还显示它们使用 JavaScript 对象作为‘字典’数据结构。

相比之下，TypeScript 保留了许多“JavaScript 的感觉”。这并不奇怪，因为它的目标是严格的 JavaScript 超集。然而，它确实导致代码有一个隐藏的意义层。例如，它隐含着某些操作可能会“失败”并导致`undefined`，并且对于如何处理这些`undefined`有自动的规则。

让我们来看一个这样的代码流:`MapImpl#put`方法。在这种情况下，首先`JSON.stringify`操作可能会失败(无声无息)并导致`undefined`。然后，这个`undefined`可以用作`this.map`的索引键，然后它会用键`undefined`插入值。这将导致一个非常微妙的错误，您甚至不会注意到，直到您尝试插入两个键-值对，它们的键不能被字符串化，并在查找时不断得到错误的值。正确的实现方式是:

```
put(a: A, b: B): MapImpl<A, B> {
  const key = JSON.stringify(a);

  if (key) this.map[key] = b;
  return this;
} 
```

在这一点上，值得指出的是，`JSON.stringify`函数的 TypeScript 绑定中的返回类型`string`是不正确的。应该真的是`string | undefined`。可以认为这是可以修复的，但是我相信很多打字稿的打字也是不完整的，因为它们是在打字稿理解任何类型`T`，任何地方，真正的意思是`T | undefined`的时候写的。后来， [TypeScript 编译器改变了](https://www.typescriptlang.org/docs/handbook/release-notes/typescript-2-0.html#null--and-undefined-aware-types)对类似`T`的类型声明的理解，使之真正意味着(不可空)`T`。因此，更改前的任何类型`T`都可以隐含地表示`T | undefined`，这意味着它的代码可能会悄悄地失败。

无论如何，重点是`JSON.stringify`和其他类型仍然可以在库级别修复(一个接一个，例如`JSON.stringify` 的[发布)。但是索引操作`this.map[key]`之类的事情却不是(实际上，无论如何)。如果将`get`方法改为:](https://github.com/Microsoft/TypeScript/issues/18879) 

```
get(a: A): B | undefined {
  const result = this.map[JSON.stringify(a)];
  return result;
} 
```

您会注意到 TypeScript 将`result`的类型推断为`B`。问题是它实际上是`B | undefined`，因为键可能不在对象中，查找可能会失败。这无法在库级别修复——它必须是一个破坏性的 TypeScript 编译器更改(或者至少是一个新的编译器标志)。这是[正在](https://github.com/Microsoft/TypeScript/issues/13778)讨论的主题。

## 啰嗦的好处

当我们考虑语义和隐藏的代码流时，很明显 TypeScript 和 JavaScript 在幕后为我们做了很多工作。为了尽最大努力保持与 JavaScript 的向后兼容，TypeScript 推断各种操作和代码路径的类型。

然而，Reason 试图做的实际上是使用一种完全不同的语义 OCaml 的语义——使这些操作更加明确，并在类型系统本身中得到更好的表示，而不是被操作的特殊规则隐藏起来。底层数据结构可能是 JavaScript 对象，但是我们按照 OCaml 的类型安全规则访问它们。可以使用 pipe-forward ( `|>`)操作符来显示函数体内从左到右、从上到下的数据流，这也没有什么坏处。

如果您正在寻找一种方法来编写 JavaScript 而不处理代码中的特殊规则和隐藏含义，ReasonML 可能值得一试。