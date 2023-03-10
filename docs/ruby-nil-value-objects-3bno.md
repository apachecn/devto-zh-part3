# Ruby Nil 值对象

> 原文：<https://dev.to/databasesponge/ruby-nil-value-objects-3bno>

# TL；速度三角形定位法(dead reckoning)

如果您专门用一个不同的类来处理空值，那么可以更简洁地定义一个值对象。空值类可以是 Ruby singleton，所以只有一个实例被实例化，节省了内存。

# 问题

所以 Ruby 中的值对象很棒。

但是并不是所有的东西都有价值。nil 无处不在，你如何有效地处理它们？

如果你做错了，那么你将会盯着这种东西

```
class ISBN
  def initialize(input)
    @isbn_string = input
  end

  def gs1_prefix
    if isbn_string.nil?
      nil
    else
      isbn_string[0..2]
    end
  end

  def checksum_number
    if isbn_string.nil?
      nil
    else
      isbn_string[-1]
    end
  end

  def valid?
    if isbn_string.nil?
      false
    else
      etc
    end
  end

  delegate :nil?, to: :isbn_string
end 
```

...或者也许你依赖空弦给你正确的行为...

```
class ISBN
  def initialize(input)
    @isbn_string = input.to_s
  end

  def gs1_prefix
    isbn_string[0..2]
  end

  def checksum_number
    isbn_string[-1]
  end

  def valid?
    etc
  end

  def nil?
    isbn_string.empty?
  end
end 
```

空字符串并不等于零，所以这是一种便利，但也有一点错误。

# 一解

一个可以考虑的解决方案是显式的“nil object”方法，它为 ISBN 实例响应的所有内容提供响应，并为没有指定值或丢失的 ISBN 提供适当的值。

额外的好处是，你只需要这个对象的一个实例，所以你可以用一个 Ruby singleton 来表示它。

```
class ISBN
  class NilObject
    include Singleton

    def gs1_prefix
      nil
    end

    def checksum
      nil
    end

    def valid?
      false
    end

    def nil?
      true
    end
  end
end 
```

...而你的 ISBN 对象的实例方法可以简化为:

```
class ISBN
  def initialize(input)
    @isbn_string = input
  end

  def gs1_prefix
    isbn_string[0..2]
  end

  def checksum_number
    isbn_string[-1]
  end

  def valid?
    etc
  end

  def nil?
    false
  end
end 
```

如何调用这个对象？这个很好用...

```
class ISBN
  def self.new(input)
    if input.nil?
      NilObject.instance 
    else
      super(input)
    end
  end

  def initialize(input)
    @isbn_string = input
  end

  etc
end 
```

是的，没错，我们覆盖了`new`类方法来返回一个不同的类。如果你喜欢类型检查，那就有问题了，但是我不认为你应该这样做——你应该更关心返回的结果是否正确。

# 进一步的想法

如果这不是你的风格，请看这里考虑的初始化方法[和考虑:](https://dev.to/databasesponge/flexible-ruby-value-object-inits-3i3j) 

```
module ISBNInitializerExtensions
  refine String do
    def to_isbn
      ISBN.new(gsub(/[^[:digit:]]/,""))
    end
  end

  refine NilClass do
    def to_isbn
      ISBN::NilObject.instance
    end
  end
end 
```

这让`nil.to_isbn`返回一个简洁定义的单例，它有适当的行为。

为什么是`#nil?`方法？您将需要它来进行验证...后文另发。

确保你有 nil 对象类的代码覆盖，并且你已经测试过它响应所有它需要响应的东西。

最后，也可以定义其他特殊的对象类型，因此对于用无效值初始化的对象的行为不同于有效值的复杂情况，您可能有`ISBN`、`ISBN::NilObject`和`ISBN::Invalid`。