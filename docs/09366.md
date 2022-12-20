# 如何在打字稿中思考和打字

> 原文：<https://dev.to/areknawo/how-to-think-and-type-in-typescript-5141>

**这篇文章摘自[我的博客](https://areknawo.com)，所以请务必查看更多最新内容😉**

前阵子我写过一篇 **[3](https://areknawo.com/typescript-introduction/) [部分](https://areknawo.com/typescript-introduction-pt2/) [教程](https://areknawo.com/typescript-introduction-pt3/)** 关于**学习打字稿**。在那里，我讨论了**不同的类型**、**语法**以及更多的内容，并对每种类型进行了详细的描述。但是自然地，学习编程语言不仅仅是简单的语法。你需要知道事物是如何相互作用的，你应该如何正确地利用它们，以及应该遵循什么样的最佳实践。这是我认为值得深入研究的问题。让我们开始吧！😃

# 语法组合

在前面提到的教程中，我甚至简单地提醒了几次，不同类型的**在一起工作得有多好。编程结构、技术和其他东西的合作形成了我们所知的编程——这并不新鲜。但是，特别是在打字稿中，这个概念把**带到了另一个层次**。🚀原因如下。**

TS 属于所谓的翻译语言的范畴。这里的关键词是 transpilation。它通常被用作编译的同义词，但实际上，它在不同的范围内工作。你知道，术语编译语言通常指 C、C++、Java 等。即使它们的目标是不同的输出，例如 JVM(字节码)或机器码，它们都是从高级抽象(如 Java 的语法)编译到低级机器码。另一方面，TS 被转换成 JS，JS 实际上是一种 JIT 编译/解释语言。**抽象为两个**的幂。😅

我在这里想说的是，TypeScript 受到 JavaScript 的限制。它所能提供的并不比 JS 自己所能提供的更多。但是 TS 接受了这一点，这似乎是一个缺点，但只不过是符合 ES-Next 的语言和静态类型系统的 T2。是的，我知道我已经在以前的文章中写过了，但这可能是这里需要理解的最重要的事情，尤其是对新手来说。这就是为什么选择像 TS 这样的东西作为你的第一门编程语言来学习是一个愚蠢的想法。先把 JS 学得足够好，这样你就可以稍后再回到 TypeScript。同样的规则也适用于这篇文章。👏

现在，我说 TS 是特别的，当涉及到把东西放在一起好好利用的时候。这是因为它的基本思想——为动态语言设计的语法提供静态类型系统。有时这可能感觉很容易，但也可能真的很难。为了提供相同级别的**灵活性**，就像动态类型语言一样，TypeScript 需要为其类型系统准备好良好的**设计**和**架构**。这当然会导致许多复杂的类型结构和变化。这就是迫使你以不同的方式思考——以打字稿的方式思考。🔥

# 不同的群体

为了充分利用 TypeScript 类型系统，您首先必须了解它的内部结构...或者说是他们的特定群体。这正是我们在 TS 简介系列中所做的。因此，在这里，我想做一个更全面的总结。从各自群体的角度来看所有这些类型。因为这是我们编程&思维过程中的关键。👨‍💻

## 基本类型

最基本的类型称为**原始类型**和**顶级类型**。包括`number`、`string`、`object`、`any`、`void`、`null`等类型。基本类型是指甚至在标准 JS 社区中众所周知的数据类型。它们有与其值相对应的名称，例如`boolean`、`number`、`string`和*可空类型*。另一方面，Top 类型只包括更特定于 TS 和其他静态类型语言的类型，例如`object`、`unknown`和声名狼藉的`any`。这两种类型组都为您提供了正确键入代码所需的一切...在基层。它们也可以和下面所有更复杂的类型组一起使用。

## 设定类型

另一种类型😅我们可以区分的是**集合类型**。在这里，所有允许你**分组其他类型**的类型都能找到自己的位置。各种不同的类型都可以归入这一类。*接口*，*枚举*，*联合*——任何可以以这种或那种方式收集其他类型、基本类型甚至集合类型的东西！这让您可以创建真正深刻而复杂的结构，这在任何静态类型的语言中都是必备的。当然，现在你不能仅仅因为一个方面就喜欢上那群如此不同的类型。该组中的所有类型都是唯一的。它们中的每一个都有不同的用途，并有自己独特的属性。例如，联合与接口完全不同等等。只有一个相似的**分组属性**将他们团结在这个特定的组中。

## 效用类型

我们可以尝试在 TS 中区分的最后一组可以称为**公用事业类型**。类型属于这一类，或者我应该说 utils，像`extends`、`keyof`、`typeof`、*索引签名*、*映射类型*等等。这些的特别之处在于语法上没有任何相似之处。他们彼此都不一样。它们服务于不同的目的，并且在大多数情况下，没有其他的支持类型，它们不能被使用。这就是为什么我称它们为实用类型。此外，由于这种独特性，这个组可能是最难正确使用的组之一。🤔

至少有些秩序总是好的。尤其是如果这涉及到你每天必须正确使用的东西。上面的分组只是一个例子。您自己的排序可以或多或少复杂一些，也可以根本不存在。现在，有了上面的结构，是时候对我们的技能进行真正的测试了！

# 用例

键入函数或创建界面是一项简单的任务。但是，如果在您创建令人敬畏的代码的过程中，出现了一个巨大的障碍，该怎么办呢？这就是为什么我想探索一些你可能偶然发现的真实的、**复杂的瓶颈**。通过这种方式，您可以获得真正类型良好的代码！🎉

## 带附加属性的索引签名

想象一种情况，在这种情况下，你必须正确地键入一个对象，这个对象除了它自己的、唯一的属性之外，还存储一些更一般的属性。例如，它可以是一个状态对象，有自己的 get 和 set 方法，但也将所有值作为标准属性存储在自身中。我们想为此创建一个合适的界面。首先，我们肯定应该创建一个*指数签名*。我们希望我们的值只是静态的，意思是- `number`、`string`、`boolean`或`undefined`。

```
type StaticValue = number | string | boolean | undefined;

interface State {
    [key: string]: StaticValue;
} 
```

Enter fullscreen mode Exit fullscreen mode

很简单，是吧？但是我们的方法呢？

```
interface State {
    [key: string]: StaticValue;
    get(key: string): StaticValue; // error
    set(key: string, value: StaticValue): void; // error
} 
```

Enter fullscreen mode Exit fullscreen mode

好戏来了！索引签名要求给定结构的所有属性都有一个声明的类型，不出所料，这会导致在声明我们的方法时出错。

现在，我知道上面的例子对每个人来说都不是那么有说服力。你甚至可以争论做这样的事情是不是反模式。但是，现实是，在不久的将来，你很容易会遇到这种问题。那么，如何解决呢？

在 TS (v.3.3)的当前状态下，用一个接口做这样的事情是不可能的。相反，我们将不得不使用*类型别名*和*交集类型*来达到期望的结果。👏以下是方法。

```
type State = {
    get(key: string): StaticValue;
    set(key: string, value: StaticValue): void;
} & {
    [key: string]: StaticValue;
} 
```

Enter fullscreen mode Exit fullscreen mode

这可能看起来有点脏，但这是唯一可能的方法。事实上，*交集类型*，它们是如此不起眼的结构，却能解决很多日常问题。这就把我们带到了下一个案例...

## 具有属性的函数

JS 中另一个不常见但可能的模式是具有附加属性的函数。它有时被称为**可调用对象**。但是，不要把它误认为是一门课程(尽管有时课程可能更适合这种工作)。

我们知道 TS 允许这样的构造，因为它有特殊的内置接口语法来定义*函数类型*。那么，让我们为我们的数据创建一个接口，好吗？

```
interface CallableObject {
    (param: string): string;
    myNumberProp: number;
    myBooleanProp: boolean;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里没有陷阱——只有一个简单的接口。一切都应该正常工作，不需要更多的努力。但是，如何实际使用这样的界面呢？我们有两个选择。

首先，我们可以使用**函数表达式**(必要性)和**类型转换**。这允许我们稍后轻松地分配我们之前列出的属性，而不会出现任何错误。

```
const callableObj = <CallableObject>((param) => param);

callableObj.myNumberProp = 10;
callableObj.myBooleanProp = true;
callableObj("str"); 
```

Enter fullscreen mode Exit fullscreen mode

注意，在上面的例子中，我们需要将箭头函数放在括号中——普通的函数表达式不需要这样做。此外，您也不必键入您的函数——类型转换已经为您完成了！😉

上面的方法非常清楚，非常好，但是如果你想在一次操作中定义你的可调用对象呢？有趣的是，还有另一种方法，可能更强大。这就需要我们用一些 ES6 的善，也就是`Object.assign()`。

```
const callableObj = Object.assign(
    (param: string) => param, {
    myNumberProp: 10,
    myBooleanProp: true
}); 
```

Enter fullscreen mode Exit fullscreen mode

值得注意的是，与以前的方法相比，我们在这里只需输入很少的内容。自动返回一个合适的交集类型，包括我们的函数及其属性。同样，通过这种方式，我们可以更接近于声明步骤来定义我们的属性，这很简洁！😄当然，如果你想在这里创建一个特定的接口，你可以使用这种类型转换的方法，就像前一个方法一样！但是，依我看，如果你正在做一个界面，第一种方法应该更适合你。🤔

## 式推理&式迭代

下一个案例将我们带到循环和迭代。更具体地说，到**对象迭代**。与我们之前的例子相比，这里我们有一个更常见的情况。在一个对象上循环通常非常有用！让我们首先创建我们的对象。

```
const obj = {
    myNumberProp: 10,
    myStringProp: "str",
    myBooleanProp: true
}; 
```

Enter fullscreen mode Exit fullscreen mode

这里我们依靠类型推理来为我们的对象正确地创建一个好的对象文字类型。现在，有很多方法可以迭代对象。其中之一是使用**进行...在**循环中。

```
for (const key in obj) {
    const value = obj[key]; // error
} 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们的收获！用给定的键访问 object 属性将导致我们的值为 any 类型，或者在**严格模式**下出错(顺便说一句，我总是在严格模式下使用 TS，并向任何人强烈推荐它，以充分利用 TS🙂).让我们进一步调查我们的问题！

首先，值得注意的是我们的对象的类型是一个对象文字类型。它的所有属性都有严格的定义和类型。它确保我们不会访问这个对象中不存在的属性。接下来，看看我们的循环，我们需要记住 TS 类型推理背后的基本规则- **最佳公共类型**。它基本上表明推断出的类型应该尽可能通用和合适。这意味着我们的`key`变量的类型是一个字符串。因此，使用与`string`一样通用的类型的`key`访问我们的对象的属性会导致上面提到的错误。我们的对象的属性只能通过合法的字符串文字来访问，例如`"myNumberProp"`。那么，如何解决这个问题呢？

最好的选择是我们自己正确地输入变量。但是，对于像 key 这样的循环变量，这不是一个选项。所以，唯一的出路就是充分了解我们的所作所为可能带来的后果，并使用**类型化**。我们最好的办法是创建一个正确的类型转换函数，而不是每次我们想访问一个属性时就转换我们的关键变量。这里有一个例子。

```
function loopObj<T extends object>(
    obj: T,
    callback: (key: keyof T, value: T[keyof T]) => void
) {
    for (const key in obj) {
        callback(key, obj[key]);
    }
}

loopObj(obj, (key, value) => {
    // code
}); 
```

Enter fullscreen mode Exit fullscreen mode

如你所见，我们正在很好地利用泛型和我们的老朋友- `keyof`。这样，你就可以把这个函数放在你的代码中，享受使用**严格类型代码**(使用联合和字符串文字)和可能更好的语法的良好开发体验。

关于这个特殊问题的最后一点说明。如果你出于某种原因使用`Object.keys()`或`.values()`或`.entries()`方法来迭代一个对象，上面的规则仍然适用。这就是 TS 类型推理和最佳公共类型规则的本质。使用上述方法，您仍然必须使用某种形式的类型转换来获得最佳结果(尽管这里不一定需要整个函数)。此外，类型推断在涉及值时也是一样的(类型推断为`number|string|boolean`)，这很自然，在这种情况下，完全有意义。👍

## 申报合并

最后，我想谈谈**声明合并**。这其实不是什么问题，但这是一个如此复杂的话题，一些解释可能值得我们花费宝贵的时间。

这里，我还有另一个实际使用的问题。我有一个类，它允许用户以函数集合的形式注册额外的方法。这些应该稍后可以通过类访问，包括正确的类型和智能感知。而且，虽然这样一个高级场景可能需要一些调整，但仍然可以在声明合并的帮助下完成。我们先来设置我们的类。

```
class MyClass {
    myNumberProp: number = 10;
    myStringProp: string = "str";

    registerExtension(ext: {[key: string]: Function}) {
        for (const key in ext) {
            // @ts-ignore
            this[key] = ext[key];
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这里发生了一件非常糟糕的事情。使用`@ts-ignore`注释，它禁止 TS 编译器检查下一行。这是必要的，因为这里我们需要给我们的类添加一个指定名称的属性，给定的类没有索引签名，我们前面已经讨论过这个问题。这里我们有一个例外，除了省略检查没有别的办法。我们可以更加狡猾，在我们严格的**配置中使用`suppressImplicitAnyIndexErrors`**。这有效地禁止了我们的 TS 代码中所有类型的与索引签名相关的错误。但是，老实说，最好保持严格，省略检查一行代码，而不是检查整个代码。😕

现在，让我们创建我们的扩展对象！

```
interface Extension {
    myNumberMethod(param: number): number;
    myStringMethod(param: string): string;
}
const ext: Extension = {
    myNumberMethod(param) {
        return param;
    },
    myStringMethod(param) {
        return param;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

使用上面的代码，我们创建了我们的扩展对象和相应的接口。在这里，一切都很简单。现在，是时候用下面的代码把它们粘在一起了！

```
interface MyClass extends Extension {};

const instance = new MyClass();
instance.registerExtension(ext);

instance.myStringMethod("str"); 
```

Enter fullscreen mode Exit fullscreen mode

在第一行中，我们创建了与我们的类同名的接口。这激活了声明合并过程，**将**我们的`Extension`接口与`MyClass`合并。因此，`Extension`接口的两个方法现在是`MyClass`的一部分。在接下来的两行中，我们实际上从代码端实现了这一点，用前面定义的方法注册了我们的扩展。最后，我们让我们的自动完成工作，并用最后一行测试它。不错！👏

关于在上述情况下使用`this`的最后一点注意。您可以通过直接将扩展方法的`this` **参数**输入为`MyClass`来轻松实现。然后你只需要记住关于**绑定**你的`.registerExtension()`方法和 boom 中的方法！你已经准备好了！🎉

# 只是觉得...

我真的希望我上面的真实用例至少对您有一点帮助，改善您的类型脚本思维和编程过程。当然，我会非常乐意在**评论区**和从你的**反应表情符号**中听到**你对一些 TS 相关问题的观点**，他们各自的解决方案和这篇文章的整体🤯下面。此外，如果你有任何 TS 问题，你想得到一些帮助-在评论中打我，我们将尝试一起解决它！😁

一如既往，继续编码，用 JS 和 TS 保持强大！如果你喜欢这个内容，可以考虑 [**与他人分享**](https://twitter.com/share?text=How%20to%20think%20and%20type%20in%20TypeScript&url=https://areknawo.com/how-to-think-and-type-in-typescript/) [**it**](https://www.facebook.com/sharer/sharer.php?u=https://areknawo.com/how-to-think-and-type-in-typescript/) ，在 Twitter 上 **[关注我](https://twitter.com/areknawo)****[在我的脸书页面](https://www.facebook.com/areknawoblog)** 查看 [**我的个人博客**](https://areknawo.com) ！

# 资源

*   **[《高级类型脚本概念:类和类型](https://www.sitepen.com/blog/advanced-typescript-concepts-classes-and-types/)** 》来自“site pen . com”；
*   来自“medium.com”的 **[高级打字稿图案](https://medium.com/@dhruvrajvanshi/advanced-typescript-patterns-6cf8826c7944)**；
*   **[打字稿中的思维](https://medium.com/web-on-the-edge/thinking-in-typescript-cb7f8a6434c0)** 来自“medium . com”；