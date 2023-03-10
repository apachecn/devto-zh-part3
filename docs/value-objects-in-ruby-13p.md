# Ruby 中的值对象

> 原文：<https://dev.to/codeandclay/value-objects-in-ruby-13p>

## 什么是价值对象？

> 一个简单的小对象，如钱或日期范围，其相等性不是基于身份。
> [马丁·福勒](https://martinfowler.com/eaaCatalog/valueObject.html)

Ruby 中的对象通常被认为是*实体对象*。两个对象可能有匹配的属性值，但我们认为它们不相等，因为它们是不同的对象。

在这个例子中`a`和`c`不相等:

```
class Panserbjorn
  def initialize(name)
    @name = name
  end  
end

a = Panserbjorn.new('Iorek')
b = Panserbjorn.new('Iofur')
c = Panserbjorn.new('Iorek')

a == c #=> => false

# Three distinct objects:
a.object_id #=> 70165973839880
b.object_id #=> 70165971554200
c.object_id #=> 70165971965460 
```

Enter fullscreen mode Exit fullscreen mode

*值对象*另一方面，通过值进行比较。当两个不同的值对象的属性值匹配时，它们被视为相等。

`Symbol`、`String`、`Integer`和`Range`是 Ruby 中值对象的例子。

这里，`a`和`c`被认为是相等的，尽管它们是不同的对象:

```
a = 'Iorek'
b = 'Iofur'
c = 'Iorek'

a == b #=> false
a == c #=> true

# Three distinct objects:
a.object_id #=> 70300461022500
b.object_id #=> 70300453210700
c.object_id #=> 70300461053840 
```

Enter fullscreen mode Exit fullscreen mode

## 如何创建值对象？

假设我想要一个类来表示一周中的每一天，并且我还想要该类的实例被认为是相等的，如果它们表示同一天的话。一个*星期日*对象应该等于另一个*星期日*对象。一个*星期一*对象应该等于另一个*星期一*对象，等等…

我可能会从下面的类开始:

```
class DayOfWeek
  DAYS = {
    1 => 'Sunday',
    2 => 'Monday',
    3 => 'Tuesday',
    4 => 'Wednesday',
    5 => 'Thursday',
    6 => 'Friday',
    7 => 'Saturday'
  }.freeze

  def initialize(day)
    raise ArgumentError, 'Day outside range' unless (1..7).cover?(day)

    @day = day
  end

  def to_i
    day
  end

  def to_s
    DAYS[day]
  end

  private

  attr_accessor :day
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，我将实例化三个对象来表示一周中我吃披萨、付钱给送牛奶的人，以及把回收的东西拿出来收集的日子:

```
pizza_day = DayOfWeek.new(5)
milk_money_day = DayOfWeek.new(2)
recycling_collection_day = DayOfWeek.new(5) 
```

Enter fullscreen mode Exit fullscreen mode

我知道我晚餐吃披萨，就在我把回收品拿出来的当天。我认为这些物体代表同一个东西:星期四。它们应该是等价的。但是他们没有:

```
pizza_day == recycling_collection_day #=> false 
```

Enter fullscreen mode Exit fullscreen mode

那是因为它们还不是价值对象。`#==`比较对象的身份。

我应该覆盖`#==`。我将使用 pry 来找出该方法的来源，这样我们就可以看到它是如何获得其当前行为的。

```
pizza_day.method(:==).owner #=> BasicObject 
```

Enter fullscreen mode Exit fullscreen mode

`DayOfWeek`继承`BasicObject`的`#==`。

`BasicObject#==` [页面显示](https://ruby-doc.org/core-2.5.0/BasicObject.html#method-i-3D-3D):

> ==仅当 obj 和 other 是同一个对象时，返回 true。通常，此方法在子类中被重写，以提供特定于类的含义。

啊哈！在这种情况下，*类的特定含义*是我想通过值来比较它的实例。

我知道这些对象公开了一个整数。与之相比是有意义的，但我不想将一天与一个实际的整数进行比较。星期四不应该等同于数字 *5* 。

我还知道`DayOfWeek`也公开了一个字符串。因此，任何等效的天数都将返回匹配的字符串和整数值:

```
class DayOfWeek
  # ...

  def ==(other)
    to_i == other.to_i &&
    to_s == other.to_s
  end

  alias eql? ==

  # ...
end 
```

Enter fullscreen mode Exit fullscreen mode

我将`#eql?`化名为`#==`。`BasicObject` [文档](https://ruby-doc.org/core-2.5.0/BasicObject.html#method-i-3D-3D)解释道:

> 对于类对象的对象，eql？与==同义。子类通常通过别名 eql 来延续这个传统？到它们被覆盖的==

答对了。我们有价值对象。`pizza_day`和`recycling_collection_day`被认为是等价的:

```
pizza_day == recycling_collection_day #=> true 
```

Enter fullscreen mode Exit fullscreen mode

我可以忽略其他比较方法，`<=`、`<`、`==`、`>=`、`>`和`between?`，因为说`Monday`小于`Tuesday`或`Friday`大于`Thursday`是有道理的，但我已经决定现在不需要这样做。

然而，还有一个更重要的步骤我需要实施。这些对象是等价的，所以当用作散列键时，我希望它们指向同一个桶。

`Hash` [文献](https://ruby-doc.org/core-2.6.0.preview2/Hash.html#class-Hash-label-Hash+Keys)建议权:

> 当两个对象的散列值相同并且这两个对象是 eql？对彼此。
> 
> 如果散列和 eql？方法被重写以提供有意义的行为。默认情况下，单独的实例引用单独的哈希键。

按照这个建议，我需要改变`#hash`的默认行为。我已经知道 Ruby 中的整数是值对象。我可以看到等价的整数总是返回相同的`#hash`。

```
a = 1
b = 1

a.object_id #=> 3
b.object_id #=> 3
1.object_id #=> 3

1.hash == 2.hash #=> false
[a,b,1].map(&:hash).uniq.count #=> 1
101.hash == (100 + 1).hash #=> true 
```

Enter fullscreen mode Exit fullscreen mode

字符串也是如此:

```
a = 'Svalbard'
b = 'Svalbard'

# Note the different object ids:
a.object_id #=> 70253833847520
b.object_id #=> 70253847146940
'Svalbard'.object_id #=> 70253847210020

# The hash values of equivalent strings match:
'Svalbard'.hash == 'Bolvanger'.hash #=> false
[a,b,"Svalbard"].map(&:hash).uniq.count #=> 1
'Svalbard'.hash == ('Sval' + 'bard').hash #=> true 
```

Enter fullscreen mode Exit fullscreen mode

我将使用它的字符串和整数属性生成散列。

```
 def ==(other)
    to_i == other.to_i
  end

  alias eql? ==

  def hash
    to_i.hash ^ to_s.hash
  end 
```

Enter fullscreen mode Exit fullscreen mode

根据文档中的[示例](https://ruby-doc.org/core-2.5.3/Hash.html#class-Hash-label-Hash+Keys)，我使用了 XOR 运算符(`^`)来导出新的哈希值。

现在我已经覆盖了`#hash`，我可以看到等价的`DayOfWeek`实例指向同一个桶:

```
day_1 = DayOfWeek.new(1)
day_2 = DayOfWeek.new(1)

day_1 == day_2 #=> true

notes = {} #=> {}
notes[day_1] = 'Rest'
notes[day_2] = 'Party'

notes.length #=> 1
notes #=> {#<DayOfWeek:0x00007fa193e44170 @day=1>=>"Party"} 
```

Enter fullscreen mode Exit fullscreen mode

## 结构

如果我想要多个值对象，我可能必须为每个类覆盖`#hash`和`#==`。

我可以决定使用[结构](https://ruby-doc.org/core-2.5.0/Struct.html)来代替。

> 结构是一种使用存取方法将许多属性捆绑在一起的便捷方式，而不必编写显式类。

默认情况下，结构是值对象。当然，我们现在已经知道这是如何工作的了。[文档](https://ruby-doc.org/core-2.5.0/Struct.html#public-instance-method-details)解释道:

> Equality 如果 other 具有相同的 struct 子类并具有相等的成员值(根据 Object#==)，则返回 true。

正如我们所想的！

```
DayOfWeek = Struct.new(:day) do
  DAYS = {
    1 => 'Sunday',
    2 => 'Monday',
    3 => 'Tuesday',
    4 => 'Wednesday',
    5 => 'Thursday',
    6 => 'Friday',
    7 => 'Saturday'
  }.freeze

  def to_s
    DAYS[day]
  end

  def to_i
    day
  end
end

a = DayOfWeek.new(1)
b = DayOfWeek.new(2)
c = DayOfWeek.new(1)

a == c #=> true
a == b #=> false 
```

Enter fullscreen mode Exit fullscreen mode

## 总结

我们现在知道了实体对象和值对象之间的区别。我们已经知道，如果我们的值对象要用作散列键，我们需要覆盖`#hash`和`#==`。而且，我们已经知道，结构提供了开箱即用的值对象行为。