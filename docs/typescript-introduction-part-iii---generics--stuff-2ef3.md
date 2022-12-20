# 打字稿简介第三部分——泛型和材料

> 原文：<https://dev.to/areknawo/typescript-introduction-part-iii---generics--stuff-2ef3>

**这篇文章摘自[我的博客](https://areknawo.com)，所以请务必查看更多最新内容😉**

在这里，我们继续**隆重的打字稿介绍**。如果你还没有，一定要看看 **[第一部分](https://areknawo.com/typescript-introduction/)** 和 **[第二部分](https://areknawo.com/typescript-introduction-pt2/)** 来开始使用 TS 并了解本教程中的内容。😉在本文中，我们将最终探索**泛型**，一些**复杂类型**和**声明文件**。在本系列的这一部分之后，您很可能已经掌握了足够的 TS 知识来编写真正复杂的东西。所以，没错，**享受吧！**🙂

# 仿制药

先来点大的！泛型，因为我们将会谈到它们，在 TypeScript 和其他一些包含它们的静态类型语言中非常重要。但是，它们到底是什么呢？

可以有把握地假设，单词 generics 是由单词 general 创造出来的，它在上下文中的意思是**某种相同的东西**。看看下面的函数。

```
function myFunction(arg: any): any {
    return arg;
} 
```

Enter fullscreen mode Exit fullscreen mode

我们的函数接受任何类型的参数，并简单地返回它(我知道，这不是很有用😅).正如我们已经知道的，任何类型都不是非常类型安全的。它也没有指出，返回类型与参数类型相同(虽然可以从代码中读取，但不能被编译器读取)。我们想指出这些类型是完全相同的。没有工会，别名和其他东西-严格相同！这就是泛型发挥作用的地方。

```
function myGenericFunction<T>(arg: T): T {
    return arg;
} 
```

Enter fullscreen mode Exit fullscreen mode

这是我们的通用函数...还有一些新的语法。😄通过使用尖括号(`<>`)，就在类型参数声明部分之前，我们声明了一个`T`类型( **T** 是泛型类型最常用的名称，通常**单字母**比长名称更受青睐)。然后我们指出参数和返回类型是相同的，但是使用了这个`T`类型。这真的很普通😁因为同一个变量类型在多个地方被使用。

但是`T`是什么类型呢？是`string`、`number`等。？嗯，可以是任何一种。调用通用函数有两种方法。

```
myGenericFunction<string>('str'); 
```

Enter fullscreen mode Exit fullscreen mode

第一种方法要求你直接指定真实类型来代替`T`类型。这里我们用的是`string`。我们用类似的尖括号语法来表示这一点(它在泛型中经常使用)。这样，所需参数的类型和返回类型都变成了`string`。这显然是一个比`any`甚至联合类型更好、更**类型安全的**解决方案。

```
myGenericFunction(10); 
```

Enter fullscreen mode Exit fullscreen mode

第二种更常用的方法利用了 TS 类型推理和更具体的**参数类型推理**。这是泛型的亮点。从我们的`10`参数中推断出的`T`类型采用了`number`的类型。这个选择后来在所有使用`T`型的地方都能看到。

至此，您应该对什么是泛型有了很好的理解。但是，有了上面的例子，我知道你可能会怀疑它们的用处。这里，把我的话当作理所当然——你迟早需要使用泛型(如果你显然要用 TS 编码😂)然后你会发现他们的**潜力**。当与一些复杂类型结合时尤其如此，我们将在后面了解更多，或者与类型保护结合，这允许你更大程度地利用泛型。

另外，记住在函数中定位泛型类型。圆括号 ( `()`)也就是 arguments 部分之前应该总是有**。箭头函数也是如此。更普遍的答案是，将它们放在适当的位置，以便以后调用时可以安全地放置尖括号。你很可能会习惯的。**

## 泛世界

所以，是的，有泛型函数，但是你知道泛型遍布 TS 类型系统吗？你可以在任何合适的地方使用它们。最重要的是**类**和**接口**。

```
class MyGenericClass<T, U> {
    myProperty: T;
    myProperty2: U;
    constructor(arg: T) {
        this.myProperty = arg;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，类与泛型配合得非常好。就像在函数中一样，泛型类型在声明的上下文中随处可见。我提到过你可以声明不止一个泛型类型吗？它适用于所有可以使用泛型的地方。只需用逗号(`,`)分隔泛型类型的名称，就可以了。

```
interface MyGenericInterface<T> {
    myProperty: T;
    myProperty2: T[];
} 
```

Enter fullscreen mode Exit fullscreen mode

上面是用接口使用泛型的例子。看起来就像上课一样。注意，第二个属性是一个 T 类型的数组。我只是想再一次展示所有 TS 类型系统组件**如何协同工作**。

由于类和接口不同于函数，所以不能使用参数类型推断来调用它们。剩下的是第一种方法——直接传递特定的类型。否则，T 将等于一个**空对象文字量**。

```
interface MyGenericInterface<T> {
    myProperty: T
}

class MyGenericClass <U> {
    myProperty: MyGenericInterface<U>;

    constructor(arg: U) {
        this.myProperty = {
            myProperty: arg
        }
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

这个例子还展示了如何嵌套和更好地利用泛型。注意我们如何在`myProperty`中将类泛型类型`U`传递给`MyGenericInterface`。

## 另一个阵列

在结束泛型部分之前，还有一件事。还记得我们如何使用特殊的语法来指定数组类型，例如`string[]`。还有另一种方法可以做同样的事情。您可以使用内置的通用**数组接口**，轻松实现与`Array<string>`相同的结果。这是很常见的做法。你可以在官方的 **TS 标准库**(所有 JS 特性的类型/声明文件，Web API&更多)以及其他流行的**声明文件**(我们将在后面介绍)，比如 React 的。

# 复杂类型

有了泛型，一个全新层次的可能性为你打开。现在，我们可以探索当与泛型结合时会给你更好的控制的类型。有了它们，你可以表达非常有趣的结构。然而，也是时候去发现它们了！😎

## 扩展类型

您已经知道了可用于类和接口的关键字`extends`。但是在 TypeScript 中，它也有泛型的用例。在这里，你可以用它来限制/ **指定泛型类型应该从**扩展**的类型**。我举个例子来解释一下。

```
function myGenericFunction<T extends string>(arg: T): T {
    return arg;
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们直接指定我们的泛型类型应该扩展字符串类型。自然，这很可能意味着它应该只是`string`。但是，当您将类型指定为某种类时，它的派生类型也是可赋值的。一般来说，它允许你更好地指定你的泛型类型和它应该有什么属性，就像`extends`的类和接口一样。

## 条件类型

**条件类型**对于 TS 类型系统来说是相当新的。在 TypeScript **v2.8** 中引入，它们让你基于**条件检查**选择正确的类型。可以使用众所周知的关键字`extends`和简单的语法
来执行检查

```
type MyType<T> = T extends string ? boolean : number; 
```

Enter fullscreen mode Exit fullscreen mode

上面我们有一个类型别名(也可以是泛型的)和一个分配给它的条件类型。我们检查泛型 T 类型是否扩展了字符串类型。如果是，我们解析为布尔值，否则为数字。自然，您可以将这种技术用于其他类型，以及嵌套多个 if 语句(无论如何它们都是类型😉).

## 指标类型

### 指标签名

我们已经讨论了当你想在一个类、接口或对象文本中声明一个属性时该怎么做。但是，如果您想要创建一个包含**个未指定数量的键**的对象，并且每个键都具有相同的类型，该怎么办呢？当然，TS 有一个解决方案！😯

```
interface MyInterface {
    [key: string]: number;
} 
```

Enter fullscreen mode Exit fullscreen mode

这个特性被称为**索引签名**，可以用在接口、类和对象文字中。语法由方括号(`[]`)组成，其中包含属性键的通用名称及其类型(通常是字符串，也可以是数字)。之后是属性值的类型。您可以将它理解为每个属性(在本例中为 string 类型的命名键)都应该有一个 number 类型的值。

请记住，TS 类型可以**混合在一起**，因此您可以自由地使用索引签名，比如可选指示符或默认值。此外，当创建一个除了索引签名之外还有其他属性的结构时，要记住它们也必须可以赋给声明的签名！

### Keyof

假设你已经有了一个对象、接口或其他什么东西，你想创建一个函数，把你的对象的属性名作为参数，并返回它的值。自然，你可以将参数类型声明为字符串，但是你不会得到像字符串文字的**联合**那样多的 IDE 支持。这就是`keyof`操作符的用武之地。

```
const myObject = {
    a: 1,
    b: 2,
    c: 3
}

function getProperty<T extends keyof (typeof myObject)>(propertyName: T): (typeof myObject)[T] {
    return myObject[propertyName];
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有一些复杂的打字！花点时间自己分析一下。它基本上让我们通过添加真正特定的返回类型声明，将参数明确地键入为联合类型`'a'|'b'|'c'`。

### 索引访问

在前面的例子中，您应该已经看到返回类型使用类似于 JS **的方括号符号**来访问对象属性。这正是我们在这里所做的，但是是用类型！

```
interface MyInterface {
    myStringProperty: string
}

type MyString = MyInterface['myStringProperty']; 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我们访问`MyInterface`的`myStringProperty`，并将其分配给`MyString`类型的别名，其结果等于字符串。可以理解吧？🚀

## 映射类型

顾名思义，映射类型允许将你的类型映射/转换成不同的形式。有了它们，你可以处理给定的类型，并以任何你想要的方式改变它。

```
type Readonly<T> = {
    readonly [P in keyof T]: T[P];
} 
```

Enter fullscreen mode Exit fullscreen mode

这里我们有一个实际的例子。我们的泛型`Readonly`类型接受`T`类型并对其进行转换，因此每个属性现在都是**只读的**。语法类似于索引签名的语法，但略有不同。我们得到了一个`in`关键字，而不是标准的属性名及其类型对。这允许我们**迭代**(对**的引用)...在**循环中)在类型`T`键的并集上，定义`P`类型(字符串文字)。一般来说，我们迭代 T 类型属性并改变它们来创建一个新的类型。就像 JS 数组的`.map()`方法一样。😉

# 申报文件

TypeScript 作为 JavaScript 的超集可以很容易地受益于 JS 的**巨大的生态系统**和一组库。但是类型推断并不能帮助所有的事情。在这种情况下，使用任何类型都会导致较差的类型安全性。为了处理这个问题，TS 提供了一个选项来创建所谓的**申报文件**(又名**打字员**)。通常以 **.d.ts** 扩展名结尾，这些文件向 ts 编译器提供关于 JS 代码类型的信息。这允许在 TS 中使用具有高质量**类型安全**的 JS 库。

大量流行的 JS 库已经提供了它们自己的类型，或者捆绑在 **NPM** 包中，或者作为 **[明确类型化的](https://definitelytyped.org/)** 库的一部分单独提供。但是，如果您选择的库没有声明文件，那么您可以基于特定工具的文档和其他资源快速创建自己的声明文件。

创建自己的类型并不比编写 TS 代码难多少，只是没有 JS 部分，这意味着**类型只有**。此外，您必须经常在函数和变量之前使用`declare`关键字来声明它们。**官方 TS 文档**提供了关于这个主题的[精彩读物](http://www.typescriptlang.org/docs/handbook/declaration-files/introduction.html)，所以如果你感兴趣的话可以去看看。

# 申报合并

**声明合并**是 TypeScript 中的一个重要概念，它允许您将给定结构的多个声明合并为一个。这是一个合并两个相同接口声明的例子。

```
interface MyInterface {
    myStringProperty: string;
}
interface MyInterface {
    myNumberProperty: number;
} 
```

Enter fullscreen mode Exit fullscreen mode

名为`MyInterface`的结果接口将具有两个单独声明的属性。同样的实践可以用于其他的 TS 结构，比如**类**(部分)、**枚举**和**名称空间**。

## 模块增强

如果你需要增加/改变多个 JS 模块的给定值，为了提供足够的类型安全，你需要使用**模块增加**。您可以通过使用`declare module`关键字对来实现。

```
import MyClass from './classes';

declare module './classes` {
    interface MyClass {
        myBooleanProperty: boolean;
    }
}
MyClass.prototype.myBooleanProperty = true; 
```

Enter fullscreen mode Exit fullscreen mode

# 就这样？

通过这篇文章，我们几乎涵盖了创建专业类型脚本代码所需的一切。仍然有一些更多的特性，比如**名称空间**和**混合**，但是编码了将近 2 年，我真的不觉得它们如此需要或者甚至有用。

说了这么多，我想这个 TypeScript 介绍到此结束。自然，如果你愿意，一定要看完前两部分。也许你想在这个博客上看到更多的 TS 内容？也许是关于 TS 配置文件的完整概述，或者是关于如何利用在本系列中学到的知识的教程？请在评论中告诉我**，或者在下面**告诉我**你的反应。👏**

一如既往，**在 Twitter 上关注我**在我的脸书页面上关注我了解更多内容。还有，考虑考虑看看我的 [**个人博客**](https://areknawo.com) 。🚀

# 资源

*   [**TypeScript -泛型之旅**](https://www.dotnetcurry.com/typescript/1439/typescript-generics) 来自“dotnetcurry . com”；
*   **[迁移到 Typescript:从“medium.com”为第三方 NPM 模块](https://medium.com/@chris_72272/migrating-to-typescript-write-a-declaration-file-for-a-third-party-npm-module-b1f75808ed2)** 写一个声明文件；
*   [**如何从“medium.freecodecamp.org”中掌握高级打字稿模式**](https://medium.freecodecamp.org/typescript-curry-ramda-types-f747e99744ab)；