# Swift 中的镜头介绍:对象的不变性

> 原文：<https://dev.to/apium_hub/intro-to-lenses-in-swift-immutability-of-objects-1f4k>

为了理解 Swift 中的**镜头，我们应该先回顾一下在谈论一个物体的**不变性**时我们指的是什么。**

我们把一旦被创建就不能被修改的对象理解为不可变对象。

它的使用给了我们很大的优势，比如我们的对象在整个程序执行过程中没有发生变化的可靠性，以及它的**线程安全**的质量，允许我们同时访问它们而没有任何后果。

## Swift 中的镜头简介:物体的不变性

### 镜片

透镜为更新对象的不变状态提供了一种优雅的方式。一个镜头，顾名思义，允许我们在一个物体结构的特定部分
中**放大**以获得、修改或输入一个新值。

我们可以将他们定义为功能性的**获取者**和**设置者**。

在 Swift 中的镜头实现中，我们将看到我们有一个完整的对象(整体)和这个对象的一部分(部分)，它们相当于带有一般 **A** 和 **B** 的实现。

```
 struct Lens <Whole,Part> {
    let from: (Whole) -> Part
    let to: (Part, Whole) -> Whole
}

struct Lens <A,B> {
    let from: (A) -> B
    let to: (B, A) -> A
} 
```

在实现中我们可以看到，镜头的 **getter** 返回它的一个特定部分， **setter** 修改一个值，用新修改的值返回整个对象(整体)，总是在说不可变的对象。

### 案例分析

它将基于一个虚构的图书馆案例。

```
 struct Library {
    let name: String
    let address: Address
    let books: [Book]
}

struct Address {
    let street: String
    let city: String
}

struct Book {
    let name: String
    let isbn: String
} 
```

首先，我们将创建我们的图书馆对象，它基于波尔图市宏伟的图书馆 *Livraria Lello* ，该图书馆是 J. K .罗琳创作哈利波特小说的灵感来源。

```
 let hp1 = Book(name: "Harry Potter and the Philosopher's Stone", isbn: "0-7475-3269-9")
let hp2 = Book(name: "Harry Potter and the Chamber of Secrets", isbn: "0-7475-3849-2")

let lelloBookstore = Library(name: "Livraria Lello",
                             address: Address(street: "R. das Carmelitas 144",
                                              city: "Barcelona"),
                             books: [hp1,hp2]) 
```

正如我们所看到的，图书馆所在城市的位置是错误的。

如果我们想要改变对象* Address *的城市地址，而该对象又是我们库对象的一部分，我们不能通过直接访问我们对象中的值* city *来改变它，因为我们使用的是完全不可变的对象。

```
 lelloBookstore.address.city = "Oporto" //Compiler error 
```

为了改变城市的值，我们将首先创建库的 **getter** 和 **setters** 。

## getter y setter

```
 extension Library {
    func getAddress() -> Address {
        return self.address
    }

    func setAddress(address: Address) -> Library {
        return Library(name: self.name, address: address, books: self.books)
    }
} 
```

方法 **getAddress** 简单地返回所有对象**的地址**，而 **setAddres** 用新地址返回**库**的整个对象。

为了访问城市属性，我们还必须创建**地址**对象的**设置器**和**获取器**。

```
 extension Address {

    func getCity() -> String {
        return self.city
    }

    func setCity(city: String) -> Address {
        return Address(street: self.street, city: city)
    }
} 
```

最后，我们可以改变我们图书馆的城市价值。

```
 let newLibrary = lelloBookstore.setAddress(address: lelloBookstore.getAddress().setCity(city: "Barcelona"))

print(newLibrary.getAddress().getCity()) // Will print "Barcelona" 
```

正如我们所看到的，我们的代码由几个层次组成。

在我们的数据结构中，我们很容易找到像这样具有多层次深度的案例。

### 镜头救场

我们将为库的地址属性创建一个镜头，为地址对象的名称属性创建另一个镜头。

要在 Swift 中创建镜头，我们只需指出输入/输出的类型，并定义它们的 getters 和 setters。

```
 let addressLens = Lens<Library, Address>(
    get: { library in library.address },
    set: { address, library in Library(name: library.name, address: address, books: library.books) }
)

let cityLens = Lens<Address, String>(
    get: { address in address.city },
    set: { city, address in Address(street: address.street, city: city) }
) 
```

现在我们有了镜头，我们将使用它们来尝试重复我们之前所做的相同操作，将图书馆的城市更改为巴塞罗那。

```
 addressLens.set(
    cityLens.set("Barcelona", addressLens.get(lelloBookstore)
    ), lelloBookstore
) 
```

透镜组返回一个新的库，其中包含改变后的城市，但是我们的代码仍然比使用不带透镜的 getters 和 setters 的代码可读性差。

但是，如果我们回到我们镜头的代码，我们可以看到第一个镜头的输出值与第二个镜头的输入值相同。

这给了我们一个线索，每当我们发现一个函数的输出参数与另一个函数的输入参数类型相同时，我们可以从函数的组合中受益。

### 功能组成

我们将定义一个**组合**函数来帮助我们组合函数。

```
 func compose<A,B,C>(_ lhs: Lens<A, B>,_ rhs: Lens<B,C>) -> Lens<A, C> {
    return Lens<A, C>(
        get: { a in rhs.get(lhs.get(a)) },
        set: { (c, a) in lhs.set(rhs.set(c, lhs.get(a)),a)}
    )
} 
```

现在，我们可以创建一个新的镜头，将前两者结合起来。

```
let addressCityLens = compose(addressLens, cityLens) 
```

使用这个新的镜头，我们可以直接改变这个城市。

```
 let newLibrary = addressCityLens.set("Barcelona", lelloBookstore)
newLibrary.address.city // Print Barcelona 
```

### 运算符

让我们通过使用操作符来进一步简化它。

```
 func * <A, B, C>(_ lhs: Lens<A, B>,_ rhs: Lens<B,C>) -> Lens<A, C> {
    return compose(lhs, rhs)
} 
```

我们看到如何使用操作符来连接两个透镜。

```
 (addressLens * cityLens).set("Barcelona", lelloBookstore) 
```

现在我们可以使用它们的扩展将镜头代码移动到它们各自的对象中，就像我们对 getters 和 setters 所做的那样。

```
 extension Library {
    static let addressLens = Lens<Library, Address>(
        get: { $0.address },
        set: { a, l in Library(name: l.name, address: a, books: l.books) }
    )
} 
```

我们将为地址做同样的事情，最后我们可以在需要的地方组合我们的镜头，以更深入地聚焦我们的对象。

```
 let newLibrary = (Library.addressLens * Address.cityLens).set("Barcelona", lelloBookstore)
newLibrary.address.city //Print Barcelona 
```

如果你有兴趣获得更多关于 Swift 或 movile 开发的技巧，我强烈推荐你订阅我们的每月简讯[这里](http://eepurl.com/cC96MY)。

## 如果你觉得 swift 上这篇关于镜头的文章很有趣，你可能会喜欢…

[功能 Javascript:镜头](https://dev.to/apium_hub/functional-lenses-in-javascript-1aa6-temp-slug-4094718)

[iOS 快照测试](https://dev.to/apium_hub/ios-snapshot-testing-2i79)

[iOS Objective-C app:成功案例研究](https://dev.to/apium_hub/protected-ios-objective-c-app-cornerjob-successfull-case-study-89e)

[年度移动应用开发趋势](https://dev.to/apium_hub/mobile-app-development-trends-of-the-year)

[Banco Falabella 可穿戴设备案例研究](https://apiumhub.com/tech-blog-barcelona/banco-falabella-wearable-ios-android/)

[移动开发项目](https://apiumhub.com/software-projects-barcelona/)

[面向 iOS 应用的 Viper 架构优势](https://apiumhub.com/tech-blog-barcelona/viper-architecture/)

[为什么是科特林？](https://dev.to/apium_hub/why-kotlin-language-android-why-did-google-choose-kotlin--639)

[软件架构会议](https://dev.to/apium_hub/apiumhub-software-architecture-meetups-in-barcelona-31df)

[安卓纯 MVP](https://dev.to/apium_hub/pure-model-view-presenter-in-android-1736)

[Java vs Kotlin:比较和例子](https://dev.to/apium_hub/java-vs-kotlin-5a33)

《Swift:物体的不变性》中介绍镜头的帖子[最早出现在](https://apiumhub.com/tech-blog-barcelona/lenses-swift-immutability-objects/) [Apiumhub](https://apiumhub.com) 上。