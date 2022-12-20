# 类型脚本简介第二部分-类和接口

> 原文：<https://dev.to/areknawo/typescript-introduction-part-ii---classes--interfaces-5302>

**这篇文章摘自[我的博客](https://areknawo.com)，所以请务必查看更多最新内容😉**

在[上一篇文章](https://areknawo.com/typescript-introduction/)中，我谈到了 TypeScript 以及为什么它值得学习。我讲述了像**原始类型**、**顶级类型**、**联合**、**功能**、**类型守卫**等主题。，所以如果这些短语中有你不熟悉的，我建议你先查一下之前的帖子。如果没有，那也很好，因为我将非常依赖第一部分传递的知识。在本教程中，我们将探索一些更复杂、更有趣的 TS 结构和功能。我将向您介绍**接口**、**类**和其他一些 TS 产品，它们肯定会改善您的开发体验、舒适度和 IDE 支持。事不宜迟，我们开始吧！**尽情享受！**😁

# 键入别名

在第一篇文章中，我们发现了许多新类型。我所谓的组合类型有特别长的语法。想象一下，您必须多次使用某种相同的联合类型，一次又一次。它不仅需要大量的输入，而且不是很枯燥，因此会让你的代码有点乱。如何解决这个问题？嗯，TypeScript 给你提供了一些帮助——**类型别名**。顾名思义，类型别名允许你给指定的类型分配一个**不同的名字**。

```
type MyUnionType = string | number;

const myUnionVariable: MyUnionType = "str";
const myUnionVariable2: MyUnionType = 10; 
```

Enter fullscreen mode Exit fullscreen mode

您的类型别名是一个常量，您可以将您的类型赋给它。要自己指定一个，必须使用 type 关键字，选择一个名称，并为其分配一个类型。😉就像普通变量一样！然后，您可以通过别名引用您的类型，就像您在任何地方处理普通类型一样。关于命名 tho 有一点需要注意。以大写字母开头是一种很好的流行做法。这使得它们不同于标准变量。

有了合适的名称，类型别名也可以为您的类型提供更好的文档。想象一下字符串文字的联合类型。指定的名称将提供另一个层次的描述。IDE 还应该发现您的别名，并在您使用它时显示它的名称，而不是长联合类型。

# 类

我希望到 2019 年，每个 JS 开发者都知道 ES6 和 ES-Next 是什么，以及它们带来了什么特性。正如我在第一篇文章中提到的，TypeScript 是 ES-Next 的超集(静态类型系统),这意味着它的编译器可以将 ES-Next 的一些语法特性向下转换到旧的 ES 版本，以获得更好的跨浏览器支持。这些特性包括 **[类](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes)** (在大多数现代浏览器中已经得到很好的支持)和 **[装饰者](https://github.com/tc39/proposal-decorators)** (当时的第二阶段提案)。我不会专门讨论这些，因为它们可能是众所周知的，而且通常更与 JS 相关。如果你愿意，你可以在这里和这里读到更多关于他们的内容。相反，我们将关注 TypeScript 添加到类中的特性，因为，是的，有很多！😮

## 类成员

正如我们已经知道的，在 TS 中所有的东西都必须有一个类型。这包括类成员。在使用`this.`语法访问任何成员之前，你需要首先**声明**为我们的成员。

```
class MyClass {
    myStringMember: string = 'str';
    myBooleanMember?: boolean;

    constructor() {
        this.myStringMember; // 'str'
        this.myNumberMember = 10; // error
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

如果你不早点声明一个属性，你会得到一个访问错误。类成员的声明只不过是在给定的类中指定其名称和类型，如上例所示。可选地，你也可以在声明的时候为你的成员分配一个默认值。你可以使用的另一件事是可选符号(`?`)，有效地使你的成员不是必需的。这两种方法都不需要给构造函数中的特定成员赋值。

## 修饰语

作为一种静态类型的语言，TS 借鉴了其他类似语言的许多思想。其中之一是访问修饰符。要使用它们，您需要在您的类成员之前指定特定修饰符各自的**关键字**。

```
class MyClass {
    private myStringMember: string;
    protected myNumberMember: number;

    public constructor() {
        this.myStringMember = 'str';
        this.myNumberMember = 10;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

您可以将它们与属性、方法甚至构造函数一起使用(有一些限制)。非常重要的是要记住，这些修饰符只为 TS 编译器和 ide 提供信息，但是，当 TS 被转换为 JS 时，在具有不同修饰符的成员之间没有差别。JS 没有提供任何选项来改变类成员的可访问性，因此所有成员在输出的代码中都是可公开访问的。🤨

### 公共

如果没有直接指定的修饰符，则为默认修饰符。表示给定的成员可以被公开访问，意味着在给定的类内部和外部都可以访问。

```
class MyClass {
    public myStringMember: string = 'str';

    public constructor() {
        this.myStringMember; // 'str'
    }
}

new MyClass().myStringMember; // 'str' 
```

Enter fullscreen mode Exit fullscreen mode

它也是可以应用于构造函数的两个修饰符之一(默认情况下是)。公共构造函数允许在代码中的任何地方实例化您的类。

### 私人

Private 修饰符将类成员的可访问性限制在类内部。在外部访问它会抛出一个错误。它遵循 **[封装](https://en.wikipedia.org/wiki/Encapsulation_(computer_programming))** 的 **OOP** 原则👏，允许您隐藏给定范围之外不需要的信息。

```
class MyClass {
    private myStringMember: string = 'str';

    constructor() {
        this.myStringMember; // 'str'
    }
}

new MyClass().myStringMember; // error 
```

Enter fullscreen mode Exit fullscreen mode

一般来说，这种技术非常有用。可惜的是，在 JS 中没有直接的对等物。尽管有人提议这么做，但目前看来，闭包似乎是唯一的选择。这就是为什么在 TS 编译器的输出中，无论如何所有成员都是可公开访问的。

### 受保护

Protected 修饰符充当私有和公共修饰符之间的中间地带。受保护的成员在类及其所有的**派生**中是可访问的(不像 private)。

```
class MyClass {
    protected myStringMember: string = 'str';

    protected constructor() {
        this.myStringMember; // 'str'
    }
}

class MyInheritedClass extends MyClass {
    public constructor() {
        super();
        this.myStringMember; // 'str'
    }
}

new MyClass(); // error

const instance = new MyInheritedClass();
instance.myStringMember; // error 
```

Enter fullscreen mode Exit fullscreen mode

上面的片段应该让您对正在发生的事情有一个正确的理解。请注意，protected 修饰符可以与构造函数一起使用。它有效地使您的类*不可实例化*，这意味着您不能就这样创建它的实例。你必须做的是创建一个继承了前一个类的类，(这使得受保护的构造函数在那里是可访问的)，但是有一个公共的构造函数。这是一个很好的技巧，但并不真正有用。如果你想要一个只用来继承的类，那么使用**抽象类**可能会更好，我们稍后会谈到。

同样，对于以前用 Java 或 C#编程的人来说，修饰符的概念应该并不陌生。但是，当我们在这里谈论 JS 时，这带来了一个全新的可能性，使我们的软件架构变得更好。😉

除了可访问性修饰符，TS 还提供了另外两个(TS 3.3):`readonly`和`static`。虽然`static`是 JS(惊喜)的一部分，但是`readonly`不是。顾名思义，它允许将一个特定的成员指定为只读的**。因此，使其仅在构造函数中声明和时可赋值。** 

```
class MyClass {
    readonly myStringMember: string = 'str';

    constructor() {
        this.myStringMember = 'string'
    }

    myMethod(): void {
        this.myStringMember = 'str'; // error
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

修饰符只适用于使用适当关键字的属性(而不是方法或构造函数)。另外，请记住，readonly 可以按照特定的顺序与其他可访问性修饰符一起使用。

至于`static`修饰符，它通过使给定成员在类上可访问而不是在它的实例上可访问来工作。此外，静态成员不能访问此。相反，您可以通过直接引用其名称来访问您的类成员，例如`MyClass`。静态成员允许你定义跨实例的常量或者使用类作为各种方法的集合。

```
class MyClass {
    static myStringMember: string = 'str';

    constructor() {
        this.myStringMember // error
        MyClass.myStringMember // 'str'
    }

    static myMethod(): void {
        this; // error
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

## 抽象类

在这篇文章的前面，我提到了抽象类。这些是什么？好吧，抽象类只不过是那些**不能自己实例化**的类，因此，只能作为其他继承类的引用。至于语法，抽象类带来的所有新东西都是`abstract`关键字。它用于定义类本身及其特定成员。

```
abstract class MyAbstractClass {
    abstract myAbstractMethod(): void;
    abstract myAbstractStringMember: string;

    constructor() {
        this.myMethod();
    }

    myMethod() {
        this.myAbstractMethod();
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的例子展示了抽象类在(大部分)恰当的方式下的全部潜力。我们已经知道 abstract 是用来声明我们对应的类的。但是当与类成员一起使用时，抽象意味着什么呢？它表示继承的类需要**在自己的**上实现的成员。如果没有找到合适的实现，将会抛出一个错误。任何其他已经实现的成员通常由各自的类继承。🙂

```
class MyClass extends MyAbstractClass {
    myAbstractStringMember: string = 'str';
    myAbstractMethod(): void {
        // code
    };
}
new MyAbstractClass() // error
new MyClass().myAbstractStringMember; // 'str' 
```

Enter fullscreen mode Exit fullscreen mode

## 申报时间

当声明你的类时，实际上，你在做两件事——创建给定类的**实例类型**和所谓的**构造函数**。

创建的实例类型允许您将变量的类型定义为特定类的实例。您可以像使用其他类型一样使用这个类型，利用您的类的名称。

```
const instance: MyClass = new MyClass(); 
```

Enter fullscreen mode Exit fullscreen mode

另一方面，构造函数是在创建给定类的实例时用关键字`new`调用的。

但是如果你想把构造函数本身而不是一个实例赋给一个变量呢？在 JS 中，你可以这样写:

```
const MyClassAlias = MyClass; 
```

Enter fullscreen mode Exit fullscreen mode

但是用 TS 写的时候`classAlias`的实际类型是什么？这里出现了`typeof`这个关键词，以前我们只知道它是一个**型后卫**。它允许您获取任何 JS 值的类型，以便以后使用。所以，来回答这个问题:

```
const MyClassAlias: typeof MyClass = MyClass;
const instance: MyClass = new ClassAlias(); 
```

Enter fullscreen mode Exit fullscreen mode

现在，对于最后一个技巧，你多久使用一次构造函数参数来分配一些类成员？这是一个非常常见的用例，TS 为这个特殊的场合提供了一个捷径。您可以在您的参数前加上任何可访问性或只读修饰符，这样您的参数就可以成为成熟的**类成员**。很有趣，不是吗？😄

```
class MyClass {
    constructor(public myStringMember: string) {}

    myMethod(): void {
        this.myStringMember;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

# 界面

现在，我们已经很好地介绍了 TS 类，是时候探索一下**接口**了！🎉接口是许多静态类型语言的黄金标准。它们允许你定义和使用价值的形状，而不是价值本身。

接口通常用于描述复杂结构的**形状**，比如对象和类。它们指出了最终结构需要哪些公共可用的属性/成员。要定义一个，你必须使用`interface`关键字和适当的语法:

```
interface MyInterface {
    readonly myStringProperty: string = 'str';
    myNumberProperty?: number;

    myMethodProperty(): void
} 
```

Enter fullscreen mode Exit fullscreen mode

在接口声明内部，我们可以使用以前学过的 TS 语法，更具体地说是**只读**和**可选属性**，以及**默认值**。接口还可以包括我们未来的结构需要实现的方法。

接口的主要用例之一是类型。您可以用已知的语法来使用它。

```
const myValue: MyInterface = {
    myStringProperty: "str";
    myMethodProperty() {
        // code
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

接口还允许你描述诸如**函数**和**类构造函数**之类的值。但是，每一种都有不同的语法，分别是:

```
interface MyFunctionInterface {
    (myNumberArg: number, myStringArg: string): void;
} 
```

Enter fullscreen mode Exit fullscreen mode

```
interface MyClassInterface {
    myStringMember: string;
}

interface MyClassConstructorInterface {
    new (myNumberArg: number): MyClassInterface;
} 
```

Enter fullscreen mode Exit fullscreen mode

说到接口，您可以利用它们来创建不同的类型，这将帮助您键入 JS 的**灵活性**。这就是为什么您可以将上面的接口与其他属性结合起来，创建所谓的**混合类型**。😉

```
interface MyHybridInterface {
    (myNumberArg: number, myStringArg: string): void;
    myNumberProperty: number;
    myStringProperty: string;
} 
```

Enter fullscreen mode Exit fullscreen mode

例如，这个接口描述了一个具有两个附加属性的函数。这种模式可能不太流行，但在动态 JavaScript 中很有可能。

## 继承

接口就像类一样可以互相扩展，也可以扩展类的属性！你可以用简单的 extends 关键字语法让你的接口**扩展一个或者更多的**(在类中是不可能的)接口。在这种情况下，扩展接口共享的属性被合并成单个属性。

```
interface MyCombinedInterface extends MyInterface, MyHybridInterface {
    myBooleanProperty: boolean;
} 
```

Enter fullscreen mode Exit fullscreen mode

当一个接口扩展一个类时，它继承所有的类成员，不管它们使用什么可访问性修饰符。但是，当您的接口只能由提供了私有成员或其派生成员的类来实现时，稍后会考虑修饰符。这是可访问性修饰符与接口交互的唯一时刻。否则，它们没有可能也没有必要与接口一起存在，接口只是描述了值的形状。🙂

```
interface MyCombinedInterface extends MyClass {
    myBooleanProperty: boolean;
} 
```

Enter fullscreen mode Exit fullscreen mode

## 类

接口和类有着特殊的联系。仅从它们的声明语法就可以看出相似之处。那是因为类可以实现接口。

```
class MyClass implements MyInterface {
    myStringProperty: string = 'str';
    myNumberProperty: number = 10;
} 
```

Enter fullscreen mode Exit fullscreen mode

通过使用`implements`关键字，您指出给定的类必须具有所有属性**实现**，如特定接口中所述。这允许您稍后更快速地定义变量。

```
const myValue: MyInterface = new MyClass(); 
```

Enter fullscreen mode Exit fullscreen mode

还记得类构造函数接口吗？这就是事情变得有点复杂的地方。当我们谈论类时，我提到当定义一个类时，你正在创建实例类型(称为**实例端**和构造函数(称为**静态端**)。当使用`implements`时，你正在与实例端交互。你告诉编译器，这个类的实例应该有这个接口的属性。这就是为什么你不能写这样的东西:

```
class MyClass implements MyClassConstructorInterface {
    // code
} 
```

Enter fullscreen mode Exit fullscreen mode

这是因为这意味着该类的实例可以自己实例化。相反，你可以做的是使用类构造函数接口来描述你需要什么类，例如作为一个参数。也许一个完整的例子能更好的展示出来。🚀

```
interface MyInterface {
    myStringProperty: string;
}

interface MyClassConstructorInterface {
    new (myNumberArg: number): MyInterface;
}

class MyClass implements MyInterface {
    myStringProperty: string = 'str';

    constructor(myNumberArg: number ){}
}

function generateMyClassInstance(ctor: MyClassConstructorInterface): MyInterface {
    new ctor(10);
}

generateMyClassInstance(MyClass); 
```

Enter fullscreen mode Exit fullscreen mode

对正在发生的事情的简单描述。首先，我们声明两个接口——一个用于实例端，定义`MyClass`实例的**形状**,另一个用于**静态端**,定义其构造函数的外观。然后我们用适当的 implements 语句定义这个类。最后，我们利用`MyClassConstructorInterface`来定义所需的类构造函数(静态端)的形状，该构造函数可以传递给我们的函数，以便稍后用**实例化**。

# 模块

这里有一个很快的提示。📓到现在为止，您可能已经熟悉了什么是 ES6 模块，不是吗？在 TypeScript 中，标准的`[import](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/import)` / `[export](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export)`关键字，除了正常的 JS 值之外，还可以与类型别名、枚举、接口等一起使用。这允许您将代码适当地分成更小的、更容易维护的块。语法和一般规则保持不变。

```
export interface MyInterface {
    myStringProperty: string = 'str';
    myNumberProperty?: number;
} 
```

Enter fullscreen mode Exit fullscreen mode

# 故地重游

在上一篇文章中，我们讨论了枚举作为一种给数值数据起一个更好名字的方式。但是，毫不奇怪，枚举的功能远不止这些。😃

枚举除了数字，还可以由**串**组成。在这种情况下，每个成员必须有一个分配给它的**常量字符串值**。所有其他与 enum 相关的规则都适用。

```
enum MyStringEnum {
    A = 'str1',
    B = 'str2',
    C = 'str3',
} 
```

Enter fullscreen mode Exit fullscreen mode

理论上，如果所有成员都是直接赋值的，那么可以在枚举中自由混合字符串和数值。尽管它没有真正的用例。

枚举也可以作为类似对象的结构在运行时使用。此外，您不仅可以将静态值，还可以将**计算值**/计算值分配给枚举成员。所以下面的赋值是完全正确的。

```
const myNumber: number = 20;

enum MyEnum {
    X = myNumber * 10,
    Y
};
const myObject: {X: number, Y: number} = MyEnum; 
```

Enter fullscreen mode Exit fullscreen mode

编译时，枚举采用 JS 对象的形式。但是，如果您希望您的 enum 仅仅作为常量值的**集合，您可以使用 const 关键字轻松实现。** 

```
const enum MyEnum {
    X,
    Y
} 
```

Enter fullscreen mode Exit fullscreen mode

在这样的常量枚举中，不能像以前一样包含计算成员。这些枚举在编译期间被移除**，因此在它们被引用的地方只留下常量值。**

# 返回功能

我们已经谈了很多函数。但是，因为我们想知道更多，所以是时候看看一些更复杂的方面了。😉

## 默认值

就像类成员一样，也可以为函数参数分配默认值。可以有多个带默认值的参数，但不能有任何必需的参数，后面没有默认值。只有当没有传递参数时，才使用默认值。

```
function myFunc(myNumberArg: number, myDefaultStringArg: string = 'str') {
    // code
}
myFunc(10);
myFunc(10, 'string'); 
```

Enter fullscreen mode Exit fullscreen mode

## 这个

随着箭头函数的引入和 ES6 中引入的更好的`.bind()`方法规范，在函数中处理`this`变得更加容易。但是，如何输入普通函数的`this`？除非您使用`.bind()`或做类似的事情，TS 很可能可以很好地处理自己，内置类型推理。否则，需要指定`this` **参数**。

```
type Scope = {myString: string, myNumber: number};

function myFunc(this: Scope, myStringArg: string = 'str') {
    this.myString;
    this.myNumber;
}

myFunc(); // error
myFunc.bind({myString: 'str', myNumber: 'number'}); 
```

Enter fullscreen mode Exit fullscreen mode

在提供了`this`参数的情况下，TS 编译器会确保函数的上下文是正确的，并在其他情况下抛出一个错误。

对于箭头功能，`this`参数没有选项。箭头函数**无法绑定**，因为它们使用了预先分配的值。因此，任何指派此参数的尝试都将引发错误。

## 超载

**重载**允许你定义不同的函数，共享相同的名字，但是设置不同的参数。当您需要接受不同类型的参数并在一个函数中专门处理它们时，通常会用到它。

```
function myFunc(myArg: number): string;
function myFunc(myArg: string): number;
function myFunc(myArg): any {
    if(typeof myArg === 'number'){
        return 'str';
    }
    if(typeof myArg === 'string'){
        return 10;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

当声明重载时，只需提供多个**函数签名**，然后用更通用的类型定义实际的函数(如示例中的任意类型)。编译器稍后将选择正确的覆盖，并向 ide 提供适当的信息。自然，同样的技术可以用在例如类中。

## 休息参数

ES6 的另一个流行特性是 **[rest 参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/rest_parameters)** 和 [**析构**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) 操作符。TS 对这两个特性都提供了很好的支持。TypeScript 允许您像键入任何其他参数一样键入 rest 参数:

```
function myFunc(myNumberArg: number, ...myRestStringArg: string[]) {
    // code
}

myFunc(10, 'a', 'b', 'c'); 
```

Enter fullscreen mode Exit fullscreen mode

至于析构化，TS 类型推理做得很好。

# 扣人心弦

哇，我们已经谈了很多了，你不觉得吗？有了类和接口，您现在可以自己开始在 TS 中进行一些 OOP 编程了。信不信由你，在利用 OOP 及其原理时，静态类型语言要好得多。反正还有很多要讨论的。我们还没有谈到**泛型**、**索引类型**、**声明合并**，以及其他更复杂的东西。所以，请继续关注我的推特 和脸书主页 上的**[。另外，如果你喜欢这篇文章，请🐱，](https://twitter.com/areknawo) [**分享**](https://twitter.com/share?text=TypeScript%20introduction%20-%20part%20II&url=https://areknawo.com/typescript-introduction-pt2/) [**it**](https://www.facebook.com/sharer/sharer.php?u=https://areknawo.com/typescript-introduction-pt2/) ，这样其他人也可以了解 TypeScript 和这个博客！😅最后，不要忘记在**下面留下你的反应，甚至可能是你想在看到的**评论！**

好了...暂时如此。👏

# 资源

现在，你对 TS 有了更多的了解，是时候**拓宽你的知识面**了。去吧，阅读，编码，学习，然后回来看第三部分！😉

*   **[打印公文](http://www.typescriptlang.org/docs)** 来自【typescriptlang.org】的*；*
**   **[用来自【rachelappel.com】的](http://rachelappel.com/write-object-oriented-javascript-with-typescript/)的**的 TypeScript 写面向对象的 JavaScript*   **[打字稿 cheatsheet](https://devhints.io/typescript)** 来自*dev hints . io*；***