# 语言级依赖注入

> 原文：<https://dev.to/horia141/language-level-dependency-injection-2103>

我最近读了我想写的语言和 shell 的替代品，我没有时间实现它们，我得出的结论是，我更喜欢写一些假设的语言，而不是出去建立一个原型。毕竟后者工作量很大，你应该只为*严肃的事情*去做，而不是脑子里突然冒出来的任何愚蠢的想法！因此，写下这些愚蠢的东西，并把它从我的系统中清除出去会好得多。以下是我对这类文学的贡献。

我希望存在的语言只是一个`${generic_oo_programming_language]`的版本，但是有依赖注入(DI)作为语言级别的构造。而不是通过图书馆附加的东西。我认为语言特性的最佳来源之一是查看常见的编码模式，并在语言层面上提升它们。对于某一类应用程序——想想后端服务、交互式应用程序、高度可配置的系统——控制反转及其 DI 实现是组织它们的方法。那么为什么不把这个正式化，在语言层面为 DI 提供便利呢？

这在实践中意味着什么？首先，我需要指出，DI 和 DI 框架/容器本质上只是以一种语法上很好的方式构造和连接对象的工具。所以我们的重点实际上是语法糖[1]类型的特征。你完全可以不使用它们，只写一堆手工样板文件，以便把所有的东西连接起来。在 [Bolt](https://careers.bolt.eu/) 我们实际上是这样做的，有了我们的微服务方法，就不会这么麻烦了。但这是另一篇博文的主题。一般来说，应用程序越大，手动操作就越难管理。

我将使用 TypeScript 作为`${generic_oo_programming_language}`的替身，但是示例应该容易理解，不需要任何先前的 TypeScript 知识。

假设我们有一个`LibraryService`类的如下结构:

```
class LibraryService {

    private readonly bookRepository: BookRepository;
    private readonly userRepository: UserRepository;

    public LibraryService(bookRepository: BookRepository, userRepository: UserRepository) {
       this.bookRepository = bookRepository;
       this.userRepository = userRepository;
    }

    public countBorrowedBooks(): number {
        // Don’t code like this in real life though!
        return this.bookRepository.getAll().filter(b => b.isBorrowed).length;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

基本上只有一个构造函数和两个依赖项。除此之外，该类看起来就像任何常规的 TypeScript 类。定义时不需要发生什么特别的事情。

一个服务`main`函数使用一个`LibraryService`来建立一个小型的类似 express 的 web 服务器，可能看起来像这样:

```
public main(): void {

    const libraryService = new LibraryService;
    const app = new WebServer; // Assume this is another class
    app.get(“/count-borrowed”, (req, res) => {
        res.write(libraryService.countBorrowedBooks());
        res.end();
    });
    app.start();
} 
```

Enter fullscreen mode Exit fullscreen mode

对`new LibraryService`的调用指示编译器生成构建`LibraryService`实例所需的所有代码。这意味着它负责实例化`UserRepository`和`BookRepository`——甚至没有使用空的参数列表。这些操作可能会递归地演化以生成大量代码。

这里也没有新的语法，只是现有对象创建语法的新语义。

当谈到“一个实例”时，我们实际上指的是`LibraryService`的实例。再次调用`new LibraryService`将返回最初创建的实例，很像每个 DI 框架的默认行为。事实上，您可以将 DI 系统想象成从`DI key`到实例的地图。DI 键的一个核心组件是类型名，但是正如我们稍后将看到的，它可以更复杂。当遇到正确格式的`new`时，或者从 DI 存储中检索实例(如果可用的话),或者创建一个新的实例。这个商店的所有管理和复杂性都推给了这里的语言实现。

与框架相比，一个很好的改进是，您是在现有的所有其他语言级别特性的基础上构建的。因此，您可以重用模块可见性和代码结构信息，丢弃模块之类的东西，只处理类型(或需要更精确解析的 DI 键)。

所有这些都可以尽可能地在编译时而不是运行时实现。我没有把事情想得很透彻，但是有可能在编译时解决所有问题，而不会损失太多的表达能力。

另一个优势是一致性。尤其是 wrt 标准库。这些人往往不知道 DI 框架。所以那里的代码通常*与其他代码*不同。但是它可以更均匀。尤其是关于时间和测试。还可以集成其他第三方代码。你可以假设他们知道你用的是同一个 DI 系统。

DI 和非 DI 类之间也没有区分。你可以在类似于`Point2D`的东西上调用`new`，它会试着工作。如果这个函数有一个空的构造函数，它会直接调用它，或者如果有什么不能满足的，你会在编译时知道。

在语言集成的基础上，我想在这里介绍一些更复杂的情况。

例如，工厂或供应商方法也可以作为语言级别的构造来实现。假设我们有一个 DB 连接字符串，可以从配置存储中获取，但它是加密存储的，我们必须在构建连接对象之前解密。代码可能是这样的:

```
factory buildConnection(configStore: ConfigStore): Connection {
    const dbConnEnc = configStore.get(“DB_CONN”);
    const dbConn = decrypt(dbConnEnc, BAKED_IN_SECRET);
    return new Connection(dbConn);
} 
```

Enter fullscreen mode Exit fullscreen mode

上面的代码不是你想在构造函数中使用的那种代码，甚至不是作为一个类的静态方法，因为它是非常具体的应用程序，所以我们为它提供了新的`factory`函数类型。由于这是一个语言级的构造，你不需要在任何特殊的地方注册工厂，也不需要引入依赖关系的模块等等。但是构建对象的方式应该是唯一的，所以每个 DI 键应该只有一个工厂函数，并且它将优先于构造函数等等。

一个高级的例子是使用多个类型作为“注入”键。仅仅拥有类型是有很多限制的，尤其是对于值类型。因此，除了实际类型之外，有一种用一些字符串或符号类型扩展 DI 键的做法。可能是这样的:

```
factory getEnvironment(): [:ENV] string {
    return readFile(“.env”);
}

// later on you might have something like
class Logger {

    private readonly env: string;

    public Logger(env: [:ENV] string): void {
        this.env = env;
    }
} 
```

Enter fullscreen mode Exit fullscreen mode

`[:ENV]`充当任何类型的修饰符，与类型一起用来标识 DI 系统可以提供的对象。大多数 DI 容器也停在这里。在这里，我们可以走得更远，但可能不值得。

我们还可以将注入范围的库级概念与实际的编程语言范围结合起来[2]。这在服务器端软件中特别有用，在服务器端软件中，您可能有应用程序级范围和请求范围之类的东西。再考虑一下请求时间。为了便于调试、数据完整性、防止故障[3]等。当我们处理一个特定的请求时，所有需要时间的地方都使用相同的“请求”时间，这可能是有意义的，当请求开始在框架代码深处被处理时，就产生一次“请求”时间。

```
class WebServer {

    // big codes here

    public get(url: string, handler: HandlerFn): void {

        discope(:Request) {
            factory getTime(): [:RequestTime] Date {
                return new Date();
            }

            handler(req, res);
        }
    }

} 
```

Enter fullscreen mode Exit fullscreen mode

关键字`[:RequestTime] Date`甚至只能从`discope`语句中获得，静态分析可以确保所有需要`[:RequestTime] Date`关键字的方法都被能够在编译时提供该关键字的方法调用。

另一件好事可能是构造函数中参数列表的部分规范，作为一种覆盖 DI 系统使用的任何内容的机制。大概是:

```
new LibraryService(userRepository=new MockUserRepository()) 
```

Enter fullscreen mode Exit fullscreen mode

行为是直观的- `bookRepository`是通过 DI 系统提供的，而`userRepository`是`MockUserRepository`。

最后——为什么停止在对象构造上？有很多地方需要提供给定类型的对象，那么为什么不在那里扩展这种机制呢？这对于部分指定参数的可能性很有效。例如，Bolt 代码库中的一个常见模式是使用特定框架功能所需的“上下文”对象。这从一个函数传递到另一个函数，但是在请求的整个生命周期中基本上是静态的。我们可以有这样的代码:

```
const logger = new Logger(“MakePrediction”);

function makePrediction(ctx: Context, features: FeatureSet): number {

    if (features.user_id < 0) {
        logger.error(ctx, “One of those pesky negative user ids”);
        throw new Error(“Negative user id encountered”);
    }

    // do some other stuff with good features
}

// later on we might call makePrediction and the DI mechanisms could automatically provide
// the existing instances we want.

makePredictions(new, features); 
```

Enter fullscreen mode Exit fullscreen mode

这最后一个可能值得更仔细的关注。这可能会导致程序结构的大崩溃。隐式而非显式地提供了本地操作所需的太多全局知识。

无论如何，这是我在这篇博文中所能阐述的全部内容，也可能是在没有实际计划的情况下最有意义的探索。很高兴我把它从我的身体里赶走了。

PS。事实证明，在公交车上写这种东西并不是一个好主意——打字错误除外。已经有一堆这样或那样的语言存在了。没什么大不了的——否则我们会*所有人*都知道这件事，但在这里不是完全未知的领域。

* * *

[1]告诫→句法糖导致分号的癌症。[2]我认为这是 PL 设计中未被充分利用的技术之一。使用/try-with-resources 有点像是语法上的天赐之物，但我对《编程语言中的结构化并发性笔记》中描述的方法印象深刻。[3]确保请求期间读取的时间是单调的。