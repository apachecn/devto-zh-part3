# 在 Ruby 中分解类、实例和元类

> 原文：<https://dev.to/appsignal/unraveling-classes-instances-and-metaclasses-in-ruby-n71>

欢迎来到新一集的 Ruby Magic！这个月的版本是关于元类的，这个主题是由两个开发者之间的讨论引发的(嗨，Maud！).

通过研究元类，我们将了解类和实例方法在 Ruby 中是如何工作的。在这个过程中，发现通过传递一个显式的“被定义者”来定义一个方法和使用`class << self`或`instance_eval`来定义一个方法之间的区别。我们走吧！

## 类实例和实例方法

为了理解为什么在 Ruby 中使用元类，我们将从检查实例方法和类方法之间的区别开始。

在 Ruby 中，*类*是一个对象，它定义了创建其他对象的蓝图。类定义了在该类的任何实例上可用的方法。

在类中定义一个方法会在该类上创建一个*实例方法*。该类的任何未来实例都可以使用该方法。

```
class User
  def initialize(name)
    @name = name
  end

  def name
    @name
  end
end

user = User.new('Thijs')
user.name # => "Thijs" 
```

在这个例子中，我们创建了一个名为`User`的类，带有一个名为`#name`的*实例方法*，它返回用户名。使用该类，我们然后创建一个*类实例*，并将其存储在一个名为`user`的变量中。因为`user`是`User`类的一个实例，所以它有可用的`#name`方法。

一个类将它的实例方法存储在它的 *[方法表](https://en.wikipedia.org/wiki/Virtual_method_table)* 中。该类的任何实例都引用其类的方法表来访问其实例方法。

## 类对象

一个*类方法*是一个可以直接在类上调用的方法，而不需要首先创建一个实例。在定义类方法时，通过在名称前加上前缀`self.`来创建类方法。

一个类本身就是一个对象。常量引用类对象，因此可以从应用程序中的任何地方调用在它上面定义的类方法。

```
class User
  # ...

  def self.all
    [new("Thijs"), new("Robert"), new("Tom")]
  end
end

User.all # => [#<User:0x00007fb01701efb8 @name="Thijs">, #<User:0x00007fb01701ef68 @name="Robert">, #<User:0x00007fb01701ef18 @name="Tom">] 
```

用前缀`self.`定义的方法不会被添加到类的方法表中。它们被添加到类的元类中。

## 元类

除了一个类，Ruby 中的每个对象都有一个隐藏的元类。元类是单例的，这意味着它们属于单个对象。如果你创建一个类的多个实例，它们将共享同一个类，但是它们都有独立的元类。

```
thijs, robert, tom = User.all

thijs.class # => User
robert.class # => User
tom.class # => User

thijs.singleton_class  # => #<Class:#<User:0x00007fb71a9a2cb0>>
robert.singleton_class # => #<Class:#<User:0x00007fb71a9a2c60>>
tom.singleton_class    # => #<Class:#<User:0x00007fb71a9a2c10>> 
```

在这个例子中，我们看到虽然每个对象都有类`User`，但是它们的单例类有不同的对象 id，这意味着它们是独立的对象。

通过访问元类，Ruby 允许直接向现有对象添加方法。这样做不会给对象的类添加新方法。

```
robert = User.new("Robert")

def robert.last_name
  "Beekman"
end

robert.last_name # => "Beekman"
User.new("Tom").last_name # => NoMethodError (undefined method `last_name' for #<User:0x00007fe1cb116408>) 
```

在这个例子中，我们向存储在`robert`变量中的用户添加了一个`#last_name`。虽然`robert`是`User`的一个实例，但是任何新创建的`User`实例都不能访问`#last_name`方法，因为它只存在于`robert`的元类中。

## 什么是`self`？

当定义一个方法并传递一个接收者时，新方法被添加到接收者的元类中，而不是添加到类的方法表中。

```
tom = User.new("Tom")

def tom.last_name
  "de Bruijn"
end 
```

在上面的例子中，我们通过在定义方法时将`tom`作为接收者，直接在`tom`对象上添加了`#last_name`。

这也是类方法的工作方式。

```
class User
  # ...

  def self.all
    [new("Thijs"), new("Robert"), new("Tom")]
  end
end 
```

这里，我们在创建`.all`方法时显式地将`self`作为接收者传递。在类定义中，`self`指的是类(在本例中是`User`，所以`.all`方法被添加到`User`的元类中。

因为`User`是一个存储在常量中的对象，所以无论何时引用它，我们都将访问同一个对象——和同一个元类。

## 打开元类

我们已经知道类方法是类对象的元类中的方法。了解了这一点，我们将看看其他一些你以前可能见过的创建类方法的技术。

### `class << self`

尽管它已经有点过时了，但是一些库使用`class << self`来定义类方法。这个语法技巧打开了当前类的元类，并与它直接交互。

```
class User
  class << self
    self # => #<Class:User>

    def all
      [new("Thijs"), new("Robert"), new("Tom")]
    end
  end
end

User.all # => [#<User:0x00007fb01701efb8 @name="Thijs">, #<User:0x00007fb01701ef68 @name="Robert">, #<User:0x00007fb01701ef18 @name="Tom">] 
```

这个例子通过向`User`的元类添加一个方法来创建一个名为`User.all`的类方法。我们没有像前面看到的那样为方法显式传递一个接收器，而是将`self`设置为`User`的元类，而不是`User`本身。

正如我们之前所学的，任何没有显式接收者的方法定义都会被添加为当前类的实例方法。在块内部，当前类是`User`的元类(`#<Class:User>`)。

### `instance_eval`

另一种选择是使用`instance_eval`，它做同样的事情，但有一个主要的不同。尽管类的元类接收块中定义的方法，`self`仍然是对主类的引用。

```
class User
  instance_eval do
    self # => User

    def all
      [new("Thijs"), new("Robert"), new("Tom")]
    end
  end
end

User.all # => [#<User:0x00007fb01701efb8 @name="Thijs">, #<User:0x00007fb01701ef68 @name="Robert">, #<User:0x00007fb01701ef18 @name="Tom">] 
```

在这个例子中，我们像以前一样在`User`的元类上定义了一个实例方法，但是`self`仍然指向`User`。虽然通常指向同一个对象，但是“默认被定义者”和`self`可以指向不同的对象。

## 我们学到了什么

我们已经知道类是唯一可以拥有方法的对象，实例方法实际上是对象的元类上的方法。我们知道`class << self`只是简单地交换了`self`来允许你在元类上定义方法，我们也知道`instance_eval`做了几乎相同的事情(但是没有触及`self`)。

虽然您不会明确地使用元类，但是 Ruby 在幕后广泛地使用了它们。了解定义方法时会发生什么可以帮助你理解为什么 Ruby 会有这样的行为(以及为什么你必须在类方法前加上前缀`self.`)。

感谢阅读。如果你喜欢你所阅读的内容，你可能想要[订阅 Ruby Magic](https://blog.appsignal.com/ruby-magic) 以便在我们每月发布一篇新文章时收到一封电子邮件。