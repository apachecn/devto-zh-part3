# JavaScript 中的 Singleton

> 原文：<https://dev.to/tomekbuszewski/singleton-in-javascript-1d5i>

你好，昨天我写了我的第一篇技术文章！它是关于 JavaScript 中的单例设计模式。我把它放在 Reddit 上，得到了一些关于模式本身的负面反馈。**这个帖子是严格告知性的**，我没有说“使用汗衫”或“不要使用汗衫”。我只是说“这是你可以实现它的方式”。你是否喜欢它，它是否适合你的应用或者解决你的问题，都取决于你自己。

此外，由于这是我的第一篇文章(不包括 10 年前关于 jQuery 点击绑定的一些东西)，我将非常感谢反馈，无论是关于技术术语(我并不以专家自居，我可能在许多地方都是错误的)还是语言。

* * *

# JavaScript 中的单例

Singleton 是编程中最著名的模式之一。虽然被一些人视为反模式，但有必要了解一下。

创建这样的类并不难，但是有一些注意事项。首先，它的构造函数必须返回实例。其次，这样的类不能被任何祖先扩展或修改。一般来说，扩展只会链接到初始实例。

那么，如何编写单例类呢？首先，我们像平常一样开始:

```
class SingletonClass {
    constructor() {}
} 
```

这是 ES2015 格式的标准类别符号。请记下名字并记住它。在创建任何东西，尤其是单例类时，**明智地选择名字**。我知道给事物命名很难，但是这里我们将使用名称而不是`this`。

第二步是在类中定义实例键。请注意，这是静态值，**它指的是类，而不是类**的实例。

```
class SingletonClass {
    constructor() {
        if (!!SingletonClass.instance) {
            return SingletonClass.instance;
        }

        SingletonClass.instance = this;

        return this;
    }
} 
```

让我们做一些解释。`constructor`从检查`SingletonClass.instance`是否存在开始。为什么不是`this`？就像我之前说的，我们指的是类。这是它的静态值，与实例无关。如果条件被满足，这意味着该类已经在某个时候被创建了，旧的实例可以被返回而不是创建一个新的。

接下来我们将`SingletonClass.instance`赋值给`this`，这意味着我们将当前实例绑定到类，耦合它。

最后，我们返回`this`。这可能会令人困惑，但请向上看。我们之前已经返回过`SingletonClass.instance`。没有这个`return`，它也可以工作，但是保持从一个方法返回一致性是一个很好的实践。

好吧，这一切都很好，但是我们如何证明创建新实例是不可能的呢？好问题。让我们用一些事情来增强我们的类，例如，让它返回我们给它起的名字(耶，编程！).

```
class SingletonClass {
    constructor(name = "") {
        if (!!SingletonClass.instance) {
            return SingletonClass.instance;
        }

        SingletonClass.instance = this;

        this.name = name;

        return this;
    }

    getName() {
        return this.name;
    }
} 
```

现在让我们创建一些实例:

```
const instanceOne = new SingletonClass("One");
const instanceTwo = new SingletonClass("Two");
const instanceThree = new SingletonClass(); 
```

好吧，就这么简单。所以，现在我们可以记录它:

```
console.log(`Name of instanceOne is "${instanceOne.getName()}"`);
console.log(`Name of instanceTwo is "${instanceTwo.getName()}"`);
console.log(`Name of instanceThree is "${instanceThree.getName()}"`); 
```

你能猜到，这些会被记录下来吗？

> 实例名 One 是“一”
> 
> 实例名 Two 是“一”
> 
> 实例名 Three 是“一”

这是为什么呢？因为是 singleton 类！它总是使用开始时创建的一个实例。尝试改变顺序，将`instanceThree`移到`instanceOne`上方。那些`console.log`现在在说什么？

另一件事是延伸。这是面向对象编程的一个非常流行的特性。流行、误用、滥用等。单例，技术上来说，不能扩展，他们没有任何祖先。但是眼见为实。让我们创建一个新类来扩展旧类:

```
class Extending extends SingletonClass {
    shoutName() {
        return this.name.toUpperCase();
    }
} 
```

所以，正常情况下`Extending`应该有两个——`getName`来源于`SingletonClass`，而`shoutName`是它自己的。让我们看看:

```
const A = new Extending();

console.log("getName" in A);
console.log("shoutName" in A); 
```

您在控制台中看到了什么？

> 真
> 假

为什么？因为实例是在我们定义`instanceOne`的时候创建的。扩展过程甚至不能开始，因为`SingletonClass` `constructor`首先返回实例。

扩展单例类的唯一方法是在初始化任何实例之前做这件事。但是这是一个极端的反模式，因为在你进行扩展之前，你不能确定有人不会使用基类。当然，您可以在声明之后立即扩展它，但是...为什么？

* * *

所以，我们现在知道了如何创建一个只有一个实例的类。有用吗？如果你想使用一个类的好处，但不允许自由使用它。听起来讽刺，但不是。想象一个伐木工人。为什么需要创建多个记录器？您应该只使用一个，它可以使用 singleton 构建。想想缓存数据库。您希望所有数据都可用，而不考虑在某个地方共享状态。

* * *

在我的 [CodePen](https://codepen.io/tomekbuszewski/pen/mQYJbq?editors=0010) 中可以获得完整的代码，以及对非信徒的多个实例的额外检查。