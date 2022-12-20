# Typescript 中匹配自定义数据类型的模式

> 原文：<https://dev.to/alexsasharegan/pattern-matching-custom-data-types-in-typescript-4h87>

我们的应用程序数据有各种形状和大小。我们为我们的问题选择最好的数据
结构，但是当我们需要将各种各样的数据
放入同一管道时，我们必须安全地区分我们的数据
类型并处理它们所有可能的变化。

在这篇文章中，我想分享一个我发现的以同构方式处理不同数据类型的模式。您可以将其视为适配器模式的功能性
编程版本。我将介绍
模式匹配、泛型枚举的概念，并看看我们如何利用
TypeScript 的能力来模拟这些模式。

这里的代码示例在 github.com/alexsasharegan/colorjs 的
[以一个更全功能的版本托管。](https://github.com/alexsasharegan/colorjs)

# 什么是模式匹配？

最简单地说，它是一个控制流结构，允许您将类型/值模式与值进行匹配。模式匹配通常是 Haskell、Elixir、Elm、Reason 等函数式编程语言的一个特性。
有些语言，比如 Rust，提供模式匹配，但并不完全符合
功能类别。考虑这个生锈的代码:

```
let x = 1;

match x {
    1 => println!("One"),
    2 | 3 => println!("Two or Three"),
    4 .. 10 => println!("Four through Ten"),
    _ => println!("I match everything else"),
} 
```

这里，`match`表达式根据块
内的模式评估`x`，然后执行相应的匹配臂表达式/块。模式按照自上而下的顺序进行
评估和匹配。第一种模式是字面值
`1`；第二个匹配**或者**一个`2`或者一个`3`；第三个描述了从`4`到`10`的范围
模式；最后一种模式是许多模式匹配系统共有的一种奇怪的语法，它表示一种“包罗万象”的模式，可以匹配任何东西。

大多数模式匹配系统还带有编译时穷举
检查。编译器检查所有的匹配表达式，并对它们进行评估，以保证其中一个分支将被匹配。如果编译器不能保证
匹配表达式是穷举的，程序将无法编译。一些
编译器也可以 lint 你的匹配表达式，在一个匹配臂遮蔽了
一个后续的匹配臂时发出警告，因为它比一个后续的更具体的
臂更通用。穷举检查是避免代码中错误的一个非常强大的工具。

## 模式匹配与枚举

枚举存在于各种语言中，但是除了枚举命名集之外，Rust 的
枚举成员能够保存值。这里有一个来自
[锈书](https://doc.rust-lang.org/book/ch06-01-defining-an-enum.html) :
的例子

```
enum IpAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}

let home = IpAddr::V4(127, 0, 0, 1);
let loopback = IpAddr::V6(String::from("::1")); 
```

`IpAddr`枚举类型有两个成员:`V4`和`V6`。注意，每种可以容纳
不同的类型。这是模拟现实世界问题的一种非常有效的方法。
不同的案例通常伴随着不同的数据建模问题。IPv4 可以很容易地用一个 4 字节的元组(`u8`)来建模，而 IPv6 有许多有效的
表达式，可以用一个字符串来表示。将枚举与值结合起来为我们提供了一种
类型安全的方式，用最佳的数据结构对每种情况进行建模。

# 在打字稿中应用模式匹配

现在让我们看看我们是否可以实现一些类似 Rust 的枚举和模式匹配
功能。对于我们的例子，我们将实现一个 color enum，它能够
匹配三种不同的颜色类型——RGB、HSL 和 Hex。我们将从
取消一些课程开始。我们也可以使用普通的 JavaScript 对象，但是为了方便起见，我们将使用类。

```
class RGBColor {
  constructor(public r: number, public g: number, public b: number) {}
}
class HSLColor {
  constructor(public h: number, public s: number, public l: number) {}
}
class HexColor {
  constructor(public hex: string) {}
} 
```

如果你不熟悉空的构造函数语法，
[是初始化属性](https://basarat.gitbooks.io/typescript/docs/classes.html#define-using-constructor)的简写。
通过声明访问修饰符(`public`、`protected`、`private`)，typescript
自动生成代码，将构造函数参数
的名称指定为类属性。这可能感觉有点像魔法*(我通常
避免使用)*，但是这是一种非常常见的模式，所以我更愿意让 TS 编译器
可靠地为我完成这项工作。

## 第一步:枚举

首先，我们需要表示所有可能的状态。在我们的例子中，这将是三种颜色类型。TypeScript 有
[一个枚举类型](https://www.typescriptlang.org/docs/handbook/enums.html)，但是它
不像 Rust 中那样保存泛型值。TS 枚举创建一组命名的
数字或字符串常量。我们将会使用字符串枚举，因为当它们被用作对象键时将会更好地发挥作用。

```
enum ColorFormat {
  Hex = "hex",
  RGB = "rbg",
  HSL = "hsl",
} 
```

由于`enum`在 JS 中不可用，我认为值得看看
TypeScript 是如何实现`enum`的。

```
var ColorFormat;
(function(ColorFormat) {
  ColorFormat["Hex"] = "hex";
  ColorFormat["RGB"] = "rbg";
  ColorFormat["HSL"] = "hsl";
})(ColorFormat || (ColorFormat = {})); 
```

TS 编译器使用一个普通的 JavaScript 对象从我们的枚举键到它们的值
构建一个查找表。在数字枚举的情况下，它还
构造了一个从值到同一个对象中的键的反向查找。JS 对象
只能有字符串、数字*(被转换成字符串)*和符号作为
键，但 ts 只支持使用字符串或数字枚举。

## 第二步:有区别的联合

虽然名字听起来很复杂，但概念很简单。它本质上是
一组不同的物体形状，虽然不同，但都有一个共同的关键点。
这个键，**判别式**，使 TypeScript 能够区分
对象类型，因为它的值**是一个文字值**。一个文字值应该是
，类似于`"foo"`而不是`string`，或者`1`而不是`number`。

这里有一个来自马里乌斯·舒尔茨的打字博客
*(你一定要看看)* :
的例子

```
interface Cash {
  kind: "cash";
}
interface PayPal {
  kind: "paypal";
  email: string;
}
interface CreditCard {
  kind: "credit";
  cardNumber: string;
  securityCode: string;
}

// PaymentMethod is the discriminated union of Cash, PayPal, and CreditCard
type PaymentMethod = Cash | PayPal | CreditCard; 
```

每个界面都有一个独特的形状，但都有一个共同的键`kind`。注意判别式中使用的类似于`"credit"`的
文字字符串值。当您接收到
类型`PaymentMethod`时，TypeScript 不会让您访问任何唯一的
属性，直到您通过断言`kind`是
`"cash"`、`"paypal"`或`"credit"`来“辨别”它是哪种形状。

我们可以使用 <abbr title="Plain Old JavaScript Object">POJO</abbr> 的*(普通的老式
JavaScript 对象)*作为我们的对象类型，或者我们可以使用类。我将
使用类，但是我们将看两者的例子。使用类将
允许我们以后实现一个更符合人体工程学的匹配。我们需要在判别键上使用
`readonly`修饰符，这样 TS 编译器就知道
`format`值不会改变。

```
// previous code omitted for brevity
class RGBColor {
  readonly format = ColorFormat.RGB;
}
class HSLColor {
  readonly format = ColorFormat.HSL;
}
class HexColor {
  readonly format = ColorFormat.Hex;
}

type Color = HexColor | RGBColor | HSLColor; 
```

现在我们已经定义了可能的颜色组合。为了简洁起见，我们在这里使用类作为类型
，但是将颜色定义为接口(我们的类
满足这一点)将使我们的联合更加灵活。

## 第三步:匹配器对象

接下来，我们将创建一个对象，它的行为就像我们的生锈模式匹配。我们对象的键将与我们枚举的值相对应——这就是为什么
我们需要使用字符串枚举。我们的值不能是表达式，因为在
JavaScript 中，它们会被立即求值。为了确保仅在条件匹配时进行惰性评估
，我们需要值是函数。

```
type ColorMatcher<Out> = {
  [ColorFormat.Hex]: (color: HexColor) => Out;
  [ColorFormat.HSL]: (color: HSLColor) => Out;
  [ColorFormat.RGB]: (color: RGBColor) => Out;
}; 
```

`ColorMatcher`对象要求我们传递一个所有键
都存在的对象。这迫使我们的代码处理所有可能的状态。它也
通用于单个返回值类型。起初这可能会有局限性，但是在
实践中减少了代码复杂性和错误。

## 第四步:匹配实现

由于 JavaScript 中没有真正的匹配语义，我们可以使用一个
`switch`语句。我们的函数需要接受参数:我们的`Color`
联合类型，带有来自[步骤 2](#step-2-discriminated-unions) 、
的判别式，以及来自[步骤 3](#step-3-matcher-objects) 的匹配器对象。我们将打开
我们的颜色的判别式属性，一旦判别式与
案例匹配，TypeScript 就可以推断出`Color`的子类型是`Hex`、`HSL`或`RGB`。
编译器还将确保我们基于
推断的类型调用正确的匹配器函数。实现如下所示:

```
function matchColor<Out>(color: Color, matcher: ColorMatcher<Out>): Out {
  switch (color.format) {
    default:
      return expectNever(color);
    case ColorFormat.Hex:
      return matcher[ColorFormat.Hex](color);
    case ColorFormat.HSL:
      return matcher[ColorFormat.HSL](color);
    case ColorFormat.RGB:
      return matcher[ColorFormat.RGB](color);
  }
} 
```

值得一提的是，当`Color`类型被定义为
接口的联合时，参数值可以是 POJO 或类实例——两种
类型都会满足预期的属性。还要注意助手 func，
，`expectNever`。该函数以一种巧妙的方式利用了`never`类型，为
us 提供编译时穷举检查。简而言之，`never`类型就是
TypeScript 表达一种不可能状态的方式。如果你想知道更多，Marius
Schulz 有另一篇很棒的
[博客文章](https://mariusschulz.com/blog/typescript-2-0-the-never-type)更深入地探讨了这个主题。

```
function expectNever(_: never, message = "Non exhaustive match."): never {
  throw new Error(message);
} 
```

既然我们选择使用类，我们可以通过
在我们的`Color`联合中为每个颜色类实现一个`match`方法来改进`matchColor`函数。由于
方法是在类上定义的，我们的匹配方法实现将只需要
参数`ColorMatcher`——这是对人类工程学的一个很好的奖励。然后，它可以调用
来纠正与自身的匹配“arm”。

```
class RGBColor {
  match<Out>(matcher: ColorMatcher<Out>): Out {
    return matcher[ColorFormat.RGB](this);
  }
}
class HSLColor {
  match<Out>(matcher: ColorMatcher<Out>): Out {
    return matcher[ColorFormat.HSL](this);
  }
}
class HexColor {
  match<Out>(matcher: ColorMatcher<Out>): Out {
    return matcher[ColorFormat.Hex](this);
  }
} 
```

## 第五步:用法

我们已经准备好了所有的部分，最终开始使用我们的 color
类进行制作。我们可以开始添加/重构函数来接受一个`Color`。这个
将允许呼叫者使用对他们来说最方便的东西——HSL、RGB 或 Hex。

我们可以使用我们的`Color`作为组件中的道具:

```
interface Props {
  color: Color;
}

function BgColor(props: Props) {
  let backgroundColor = props.color.match({
    hsl: color => `hsl(${color.h}, ${color.s}%, ${color.l}%)`,
    rgb: color => `rgb(${color.r}, ${color.g}, ${color.b})`,
    hex: color => `#${color.hex}`,
   });

   return <div style={{backgroundColor}}>{{props.children}}</div>
} 
```

我们还可以实现一些颜色转换助手，并创建一些类似 sass 的
助手，如下所示:

```
function lighten(color: Color, percent: number): HSL {
  let hsl = color.match({
    // Already our preferred type, so just return it.
    hsl: c => c,
    rgb: c => convertRGBToHSL(c),
    hex: c => convertHexToHSL(c),
  });

  hsl.l = Math.min(100, hsl.l + percent);

  return hsl;
}
function darken(color: Color, percent: number): HSL {
  let hsl = color.match({
    hsl: c => c,
    rgb: c => convertRGBToHSL(c),
    hex: c => convertHexToHSL(c),
  });

  hsl.l = Math.max(0, hsl.l - percent);

  return hsl;
} 
```

HSL 格式是最容易变亮或变暗的，所以这些函数只是
转换成 HSL，然后提高/降低亮度。注意，虽然我们接受的
参数是类型`Color`，但我们返回的是一个`HSL`实例。我们本可以选择返回 T2。

使用这些
[标记的联合](#step-2-discriminated-unions)时的**最佳实践**是接受最大范围的
(实际)类型，但返回最精确的类型。如果我们将
`Color`返回给调用者，他们需要再次匹配以确定我们
返回了 HSL 颜色——这是我们已经知道的。

# 优点&缺点

将定制数据类型与这种模式相匹配是非常强大的。我们的代码
清楚地记录了枚举中所有可能的状态——这对我们代码的新来者
以及我们自己都有好处。我们的匹配器对象也迫使
我们在与数据交互时承认每一种可能的状态。如果我们急着实现一个特性，编译器会确保我们
没有忘记任何东西。matcher 对象的一致返回类型将帮助我们
避免错误。很难解释这有多重要，但是以我的
经验来看，它极大地改变了我的编码方式，减少了许多不必要的错误。

然而，所有的好处都是有代价的。这种模式非常冗长，需要很多样板文件。我们模仿的是
JavaScript 中不存在的语法，所以冗长是一种可以理解的权衡，但是在决定
使用这种模式是否会改善你的情况时，你需要
考虑这一点。作为一个库作者，我发现这是在
上构建我的自定义数据类型的坚实基础。我将详细程度降低到库
级别，这样我可以为库用户提供方便和清晰。

关于这个主题的进一步阅读，你可以查看另一个
社区成员的类似帖子，手册 Alabor:
[https://pattern-matching-with-typescript.alabor.me/](https://pattern-matching-with-typescript.alabor.me/)。

在我接下来的两篇文章 *(TBD)* 中，我将解释一些更具功能性的
编程启发模式，这些模式利用这种匹配模式在幕后
提供一个抽象和框架，用于处理可空类型、健壮的
错误处理，以及创建健壮的、可链接的任务管道。