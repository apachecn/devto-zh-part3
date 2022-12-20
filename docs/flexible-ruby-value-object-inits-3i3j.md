# 灵活的 Ruby 值对象初始化

> 原文：<https://dev.to/databasesponge/flexible-ruby-value-object-inits-3i3j>

# 问题

Ruby 中的值对象是可爱的东西，在复杂的应用程序中是无价的。

但是有一个问题似乎总是让我犯错误，那就是正确地从其他对象初始化，所以当我们可能想写:

```
class ISBN
  def initialize(value)
    @isbn_string = value
  end

  def etc
  end
end 
```

...实际上，有时您会基于字符串输入创建一个值对象，有时将值以数字形式存储在数据库中可能是有意义的，但您可以确定控制器会以字符串形式接收它，而其他时候它已经是一个值对象了。

废话说够了——你需要能够基于几乎任何可能代表它的值的东西来初始化你的值对象。

# 使其如此

那么你对此怎么看？

```
class ISBN
  def initialize(value)
    @isbn_string = if value.is_a? String
                     value.gsub(/[^[:digit:]]/,"")
                   elsif value.is_a? Integer
                     value.to_s
                   elsif value.is_a? NilClass
                     ""
                   end
  end
end 
```

相当糟糕。

在这个过程中，你可能会学到面向对象设计的一个原则，即这种代码是一件坏事，因为我们不应该关心一个对象是什么类型/类，而应该只关心它能响应什么。

现在，如果我们向一个对象发送一条消息`#address`,我们会做得很好——我们只是希望 Person/Company/Owner/Invoice 等类实现这个方法，因为它们是我们自己的域对象，我们为它们编写代码，所以我们可以定义我们想要它们的任何行为。

但是当对象是 Ruby 核心类之一时，我们该怎么办呢？`String`、`Integer`，甚至`NilClass`？

一个答案是猴子补丁，但现在我想我们都知道这也是一件坏事，我们应该实现一个改进。

这个怎么样？

```
module ISBNInitializerExtensions
  refine String do
    def to_isbn_string
      gsub(/[^[:digit:]]/,"")
    end
  end

  refine Integer do
    def to_isbn_string
      to_s
    end
  end

  refine NilClass do
    def to_isbn_string
      ""
    end
  end
end 
```

这还不算太糟，因为 ISBN 初始化变成了:

```
class ISBN
  using ISBNInitializerExtensions
  def initialize(value)
    @isbn_string = value.to_isbn_string
  end

  def etc
  end
end 
```

我们可以将`ISBN.new(obj)`分散在系统中，ISBN 类不需要深入了解字符串或数字的清理。它假设输入值已经实现了它。

但是我们不怎么做，不是吗？我们反而喜欢`"78".to_i`。(好的，我们可以使用显式方法，但是只有当我们需要随之而来的特定行为时。否则我们就派`#to_i`。

我们还需要知道实现 ISBN 对象的类的名称，以及如何初始化它，但这可能没什么大不了的。

更进一步，我们来看看:

```
module ISBNInitializerExtensions
  refine String do
    def to_isbn
      ISBN.new(gsub(/[^[:digit:]]/,""))
    end
  end

  refine Integer do
    def to_isbn
      ISBN.new(to_s)
    end
  end

  refine NilClass do
    def to_isbn
      ISBN.new("")
    end
  end
end 
```

该模块成为系统中我们将核心 Ruby 类转换为应用程序定义的 ISBN 类的位置，也是我们如果想要更改`ISBN`类的名称时需要进行重构的唯一位置。

我们可以为`#to_zip_code`、`#to_country`、`#to_currency`等实现类似的逻辑。，有类似的好处。

然后，由于知道可能需要返回它的类已经实现了清除，我们可以写:

```
class ISBN
  def initialize(value)
    @isbn_string = value
  end

  def etc
  end
end 
```

我们又回到了起点，进行了一次漂亮、干净的初始化，并且能够:

```
 "978-3-16-148410-0".to_isbn
  9783161484100.to_isbn
  nil.to_isbn 
```

..只要我们首先将`using ISBNInitializerExtensions`添加到我们想要使用它的类或模块中(不可否认，由于某种原因，这在 Rails 视图中似乎很棘手)。

为了完整起见，我们可以...

```
class ISBN
  def initialize(value)
    @isbn_string = value
  end

  def to_isbn
    self
  end

  def to_s
    @isbn_string
  end
end 
```

这使得 Rails 中的值对象定义只需要很少的代码:

```
class Book
  using ISBNInitializerExtensions

  def isbn
    @_isbn ||= self[:isbn].to_isbn
  end

  def isbn=(obj)
    self[:isbn] = obj.to_isbn.to_s
  end
end 
```

# 总结

所以这里有两个选择:

1.  细化参数的清理，将它们从初始化器中取出，并让我们对相当任意的 obj 类做`ISBN.new(obj)`。
2.  细化完整的转换，允许`obj.to_isbn`在任何类或模块中调用适当的细化。

还有一点:为了更明确地暗示这是我们的应用程序对对象行为的细化，使用“as”而不是“to”的命名约定是否明智，例如`9783161484100.as_isbn`？如果你真的想的话，你可以声称“as”是“特定应用”的首字母缩写...