# Ruby 中的对象编组

> 原文：<https://dev.to/appsignal/object-marshalling-in-ruby-2gck>

在本文中，我们将深入研究对象编组。我们将解释它是什么，看看马歇尔模块，然后通过一个例子。然后我们将更深入地比较`_dump`和`self._load`方法。我们走吧！

## 什么是对象编组？

当您编写代码时，您可能希望保存一个对象并将其传输到另一个程序，或者在下一个程序执行中重用它。例如，在 Sidekiq 中使用对象编组；当 Sidekiq 作业在 Ruby on Rails 应用程序中排队时，这个作业的序列化——只不过是一个对象——被插入 Redis。然后，Sidekiq 进程能够反序列化这个 JSON，并从 JSON 重新构建原始作业。

在计算机编程中，这个对象的序列化和反序列化的过程就是我们通常所说的*对象编组*。现在，让我们看看 Ruby 本身提供了什么来处理对象编组。

## 元帅模块

由于 Ruby 是一种完全面向对象的编程语言，它提供了一种使用标准库中的`Marshall`模块来序列化和存储对象的方法。它允许您将对象序列化为字节流，该字节流可以在另一个 Ruby 进程中存储和反序列化。

所以，让我们序列化一个字符串，并仔细看看序列化的对象。

```
hello_world = 'hello world!'

serialized_string = Marshal.dump(hello_world) # => "\x04\bI\"\x11hello world!\x06:\x06ET"
serialized_string.class                       # => String

deserialized_hello_world = Marshal.load(serialized_string) # => "hello world!"

hello_world.object_id              # => 70204420126020
deserialized_hello_world.object_id # => 70204419825700 
```

然后我们调用`Marshal.dump`模块方法来序列化我们的字符串。我们将返回值——包含我们的序列化字符串——存储在`serialized_string`变量中。该字符串可以存储在一个文件中，并且该文件可以在另一个进程中重新构成原始对象。然后我们调用`Marshal.load`方法从字节流中重构原始对象。

我们可以看到，这个新重构的字符串有一个不同于`hello_world`字符串的`object_id`，这意味着它是一个不同的对象，但它包含相同的数据。

相当酷！但是`Marshal`模块是如何重建字符串的呢？如果我想控制序列化和反序列化哪些属性呢？

## 对象编组的具体例子

为了回答这些问题，让我们在名为`User`的定制结构上实现一个编组策略。

```
User = Struct.new(:fullname, :age, :roles)

user = User.new('Mehdi Farsi', 42, [:admin, :operator]) 
```

`User`结构定义了 3 个属性:`fullname`、`age`和`roles`。在本例中，我们有一个业务规则，仅当它符合以下标准时才进行序列化:

*   `fullname`包含的字符少于 64 个
*   `roles`数组不包含`:admin`角色

为此，我们可以定义一个`User#marshal_dump`方法来实现我们的定制序列化策略。当我们调用带有一个`User`结构实例作为参数的`Marshal.dump`方法时，这个方法将被调用。我们来定义一下这个方法:

```
User = Struct.new(:age, :fullname, :roles) do
  def marshal_dump
    {}.tap do |result|
      result[:age]      = age
      result[:fullname] = fullname if fullname.size <= 64
      result[:roles]    = roles unless roles.include? :admin
    end
  end
end

user = User.new(42, 'Mehdi Farsi', [:admin, :operator])

user_dump = Marshal.dump(user) # 'in User#marshal_dump'
user_dump                      # => "\x04\bU:\tUser{\a:\bageI\"\x10Mehdi Farsi\x06:\x06ET:\rfullnamei/" 
```

在上面的例子中，我们可以看到，当我们调用 Marshal.dump(user)时，我们的`User#marshal_dump`方法被调用。`user_dump`变量包含的字符串是我们的`User`实例的序列化。

现在我们已经有了转储，让我们反序列化它来重组我们的用户。为此，我们定义了一个负责实现`User`转储的反序列化策略的`User#marshal_load`方法。

所以我们来定义一下这个方法。

```
User = Struct.new(:age, :fullname, :roles) do
  def marshal_dump
    {}.tap do |result|
      result[:age]      = age
      result[:fullname] = fullname if fullname.size <= 64
      result[:roles]    = roles unless roles.include? :admin
    end
  end

  def marshal_load(serialized_user)
    self.age      = serialized_user[:age]
    self.fullname = serialized_user[:fullname]
    self.roles    = serialized_user[:roles] || []
  end
end

user = User.new(42, 'Mehdi Farsi', [:admin, :operator])

user_dump = Marshal.dump(user) # 'in User#marshal_dump'
user_dump                      # => "\x04\bU:\tUser{\a:\bagei/:\rfullnameI\"\x10Mehdi Farsi\x06:\x06ET"

original_user = Marshal.load(user_dump)  # 'in User#marshal_load'
original_user                            # => #<struct User age=42, fullname="Mehdi Farsi", roles=[]> 
```

在上面的例子中，我们可以看到当我们调用`Marshal.load(user_dump)`时，我们的`User#marshal_load method`被调用。`original_user`变量包含一个结构，它是我们的用户实例的重构。

请注意，`original_user.roles`不同于`user.roles`数组，因为在序列化过程中，`user.roles`包含了`:admin`角色。所以`user.roles`没有被序列化到`user_dump`变量中。

## _ 转储和自我。_ 加载方法

当调用`Marshal.dump`和`Marshal.load`时，这些方法调用作为这些方法的参数传递的对象上的`marshal_dump`和`marshal_load`方法。

但是，如果我告诉你，`Marshal.dump`和`Marshal.load`方法试图在作为参数传递的对象上调用另外两个名为`_dump`和`self._load`的方法呢？

## _ 转储方法

`marshal_dump`和`_dump`方法的区别在于:

*   当使用`_dump`方法时，您需要在较低的层次上处理序列化策略——您需要返回一个表示要序列化的数据的字符串
*   如果两者都被定义了，`marshal_dump`方法优先于`_dump`

让我们来看看下面的例子:

```
User = Struct.new(:age, :fullname, :roles) do
  def _dump level
    [age, fullname].join(':')
  end
end

user = User.new(42, 'Mehdi Farsi', [:admin, :operator])

Marshal.dump(user) # => "\x04\bIu:\tUser\x1342:Mehdi Farsi\x06:\x06EF" 
```

在`User#_dump`方法中，我们必须实例化并返回序列化对象——代表序列化的字符串。

在下面的例子中，我们定义了`User#marshal_dump`和`User#_dump`方法，并返回一个字符串来查看哪个方法被称为

```
User = Struct.new(:age, :fullname, :roles) do
  def marshal_dump
    'in User#marshal_dump'
  end

  def _dump level
    'in User#_dump'
  end
end

user = User.new(42, 'Mehdi Farsi', [:admin, :operator])

user_dump = Marshal.dump(user) # "in User#marshal_dump" 
```

我们可以看到只有`User#marshal_dump`被调用，尽管它们都被定义了。

## 自我。_ 加载方法

现在，让我们看看`marshal_load`和`_load`方法。

`marshal_load`和`_load`方法的区别在于:

*   当使用`_load`方法时，您需要在较低的级别处理反序列化策略——您负责实例化原始对象。
*   当`_self.load`方法将序列化的字符串作为参数时，`marshal_load`方法将反序列化的对象作为参数。
*   当`self._load`是一个类方法时，`marshal_load`方法是一个实例方法。

我们来看看下面这个例子:

```
User = Struct.new(:age, :fullname, :roles) do
  def _dump level
    [age, fullname].join(':')
  end

  def self._load serialized_user
    user_info = serialized_user.split(':')
    new(*user_info, Array.new)
  end
end

user = User.new(42, 'Mehdi Farsi', [:admin, :operator])

user_dump = Marshal.dump(user)
user_dump # => "\x04\bIu:\tUser\x1342:Mehdi Farsi\x06:\x06EF"

original_user = Marshal.load(user_dump)
original_user # => #<struct User age="Mehdi Farsi", fullname=42, roles=[]> 
```

在`User._load`方法中:

*   我们反序列化由`User#_dump`方法返回的字符串
*   我们通过传递反序列化的信息来实例化一个新的`User`

我们可以看到，我们负责分配和实例化用于重建原始用户的对象。

所以耦合到`marshal_load`的`Marshal.load`负责实例化重构的原始对象。然后它调用`marshal_load`方法，将序列化的对象作为参数传递给新实例化的对象。

相反，对耦合到`_load`的`Marshal.load`的调用让`self._load`类方法负责:

*   反序列化由`_dump`方法返回的数据
*   实例化重构的原始对象

## 结论

根据您的需要，您可以决定实现更高或更低的序列化/反序列化策略。为此，您可以使用与适当的封送挂钩方法耦合的封送模块。

给你！

客座作者 Mehdi Farsi 是[www.rubycademy.com](https://www.rubycademy.com)的创始人，该网站即将推出，届时将提供学习 Ruby 和 Ruby on Rails 的酷课程。