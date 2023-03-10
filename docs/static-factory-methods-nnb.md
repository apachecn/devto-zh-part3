# 静态工厂方法

> 原文：<https://dev.to/adtm/static-factory-methods-nnb>

作为一个没有设计模式基础的 JavaScript 开发人员，静态工厂方法在很多情况下会让我的代码更整洁。如果你和我一样——和大多数人一样，当你创建一个类时，你想在某个时候初始化它。

```
 const Coordinate: Coordinate = new Coordinate(x, y)

    class Coordinate {
      public x: number;
      public y: number;

      constructor(x: number, y: number) {
        this.x = x;
        this.y = y;  
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

这种行为非常普遍——我们想要创建类的实例。但是让我们假设您想要创建一个参数`x & y`在初始化时翻倍的`Coordinate`。

简单的和肮脏的是在构造函数中加倍它

```
 constructor(x: number, y: number) {
      this.x = 2 * x;
      this.y = 2 * y;
    } 
```

Enter fullscreen mode Exit fullscreen mode

但是我们大多数人不会这样做，我们会创建一个方法

```
 class Coordinate {
      ...
      public double() { 
        this.x = this.x * 2;
        this.y = this.y * 2;
      }
    }

    const coordinate = new Coordinate(1, 5).double() 
```

Enter fullscreen mode Exit fullscreen mode

正常流动，一切正常。但是，如果想要的结果是创建一个不能使用任何类似于`double`的方法的双`Coordinate`呢？我们不想公开这个方法给其他人使用。如果有人用了 20 次`double`会怎么样？

我们可以隐藏创建逻辑并创建一个静态工厂方法——它将返回一个 double `Coordinate`,这样就一目了然了。正如你所看到的,`constructor`是私有的——`Coordinate`被封装在类中。

```
 class Coordinate {
      // ...
      private constructor(x: number, y: number) {
        this.x = x;
        this.y = y;
      }

      static doubledCoordinate(x: number, y: number) { 
        return new Coordinate(x * 2, y * 2)
      }
    }

    const coordinate = Coordinate.doubleCoordinate(1, 5) // and the naming - fancy 
```

Enter fullscreen mode Exit fullscreen mode

还要记住，如果你想在一个典型的构造函数中引入一个副作用(比如一个`console.log()`)——你将返回一个新的对象，通过使用一个静态方法，你不需要在每次调用它的时候创建一个新的对象。

这可能是一个微不足道的例子，但主要思想是对外界隐藏实现——将对象的构造委托给静态方法，假设您有一个数据库连接，并且您不想公开`Monitor`以及数据库是如何连接的，以及它需要做什么处理操作。

```
 // some methods are fake

    import Monitor from "//.."

    class Database {  
      private static Monitor = new Monitor()
      private static connection: MongoClient;
      private static regionCount: number = 0;

      static getConnection() {
        if (connection.timedOut() ||
            !connection.established) {
          Monitor.sendStatus();
          connection = new MongoClient()
        }

        return this.connection
      }

      static currentRegionCount() {
          regionCount = //..
      }

      static getAvailableRegions() {
        return regionCount;
      }
    } 
```

Enter fullscreen mode Exit fullscreen mode

使用静态变量，您可以缓存值，而无需为内部变量分配新的内存地址。如果您每次都创建一个`new Coordinate()`或`new Database()`，那么内部变量将精确地属于实例类。

因此，当我们调用`Database.getConnection`时，如果建立了连接，我们引用的是同一个`connection`(这是一个例子，我们有一个监听器)。这使得我们的代码更高效、更干净。

我个人在生活中初始化了太多的类——创建多个实例，导出为一个实例，暴露了太多我从未想过的方法——弄得一团糟。JavaScript 是一种自由精神，但是为了编写干净的代码，它需要一些驯服。