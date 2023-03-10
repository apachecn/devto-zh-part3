# 轻量级轻量级

> 原文：<https://dev.to/arturmartsinkovskyi/rubyish-flyweight-3afp>

Ruby 是一种优秀而优雅的语言，它的表现力非常吸引人。由于它的灵活性，它允许自然和直观的解决方案，而其他语言需要样板文件和膨胀来达到相同的目标。这里有一个例子。

最近，我翻阅了一本游戏设计模式手册，发现了一个在游戏开发中随处可见的有趣模式。这个图案就是 [*蝇量级*](http://gameprogrammingpatterns.com/flyweight.html) 。

[![Flyweight](img/f1afd3b3503209c3362349a040c92328.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--ufD9IHSi--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/frdix37cxdjrtki5g3kg.png)

正如维基百科所说:“在计算机编程中，flyweight 是一种软件设计模式。flyweight 是通过与其他类似对象共享尽可能多的数据来最小化内存使用的对象；当简单的重复表示会使用不可接受的内存量时，这是一种大量使用对象的方法。通常，对象状态的某些部分可以共享，通常的做法是将它们保存在外部数据结构中，并在使用时临时传递给对象。

通常在大多数语言中，这种模式是使用工厂、抽象类和该类的继承来实现的。在 Ruby 中，它的实现可能噪音更小。由于 Object.new 不是一个特殊的语言构造，而只是一个方法，我们可以让自己的类构造函数作为一个工厂，只需覆盖它。下面你可以看到它是如何工作的。

```
module Flyweightable
  def new(*attributes)
    stored_instance = store[attributes]
    return stored_instance if stored_instance

    instance = allocate
    instance.send(:initialize, *attributes)
    store[attributes] = instance
  end

  private

  def store
    @store ||= {}
  end
end

class Point
  attr_reader :x, :y
  extend Flyweightable

  def initialize(x, y)
    @x = x
    @y = y
  end

  def euclidian_distance(point)
    Math.sqrt(((x - point.x) ** 2) + ((y - point.y) ** 2))
  end

  def manhattan_distance(point)
    (x.round - point.x.round).abs + (y.round - point.y.round).abs
  end
end 
```

Flyweightable 模块重写了新方法，并在类内部创建了一个内存化的存储，用于保存所有实例化的 flyweights 的注册表。如果构造函数中传递了参数的 flyweight 存在，它就返回它，否则它创建一个 flyweight 并将其写入注册表。

这种 Flyweight 实现具有原始模式的所有优点，不影响初始化，使外部接口像创建类 Flyweight 之前一样流畅。重要的是要记住，这种模式应该用于你认为值的类，如点、纹理或其他可唯一标识的可枚举对象，而不是具有可变状态的对象的引用。

警告:

*   作为 Flyweight 的一个要求，混合在该模块中的类必须是只读的(否则，当对象在其他地方更新状态时，就会变得笨拙和不同步)
*   使用 hash 实现的 Registry 很可能贯穿程序的整个运行时，所以在有大量唯一对象的情况下，Flyweight 可能不合适

相同的技术(构造函数覆盖)可以以多种方式使用，比如动态构建分派或多个具有不同名称的专用构造函数来打破每个类一个构造函数的限制。

黑客快乐！