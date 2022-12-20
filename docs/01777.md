# 理解 ruby 如何初始化对象

> 原文：<https://dev.to/prathamesh/understanding-how-ruby-initializes-objects-16g4>

[![Understanding how Ruby initializes objects](img/d5a5618042143a536a401ff79848f534.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--4cvIbg2N--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://prathamesh.tech/conteimg/2019/06/age-barros-572456-unsplash.jpg)

在 Rails 源代码的深处，有一段[代码片段](https://github.com/rails/rails/blob/4a86464cbae007f9bf37189bd265eac8415570c5/activesupport/lib/active_support/deprecation/proxy_wrappers.rb#L6-L12):

```
class DeprecationProxy
  def self.new(*args, &block)
    object = args.first

    return object unless object
    super
  end
  ...
end 
```

Enter fullscreen mode Exit fullscreen mode

它是 9 年前添加的，提交消息说:

```
Override new on proxy objects so that they never wrap nil or false. 
```

Enter fullscreen mode Exit fullscreen mode

问题是这是如何工作的？因为我们知道，当使用`new`初始化一个对象时，Ruby 会执行`initialize`方法。

```
class Greeting
  def initialize(message)
    puts "Hello there! #{message}"
  end
end

Greeting.new("Welcome to Ruby world.")
=> "Hello there! Welcome to Ruby world." 
```

Enter fullscreen mode Exit fullscreen mode

注意，我们实际上还没有在任何地方定义`new`方法。那么这个来自 Rails 的代码是如何工作的呢？

为了了解事情是如何进行的，让我们反思一下`new`方法是在类的什么地方定义的。

Ruby 提供了一个名为`method`的简便方法，用于查看方法的定义位置。

```
>> Greeting.method(:new)
=> #<Method: Class#new> 
```

Enter fullscreen mode Exit fullscreen mode

我们可以看到，`new`方法实际上来自于`Class`。但是当我们在一个类上调用`new`时，如何调用`initialize`方法呢？

原来，Ruby 在内部调用对象上的`initialize`，传递所有传递给`new`的参数。

```
greeting = Greeting.new(message)
=> Calls Class.new(message)
=> Calls initialize on the object
=> which results in:
=> greeting.initialize(message) 
```

Enter fullscreen mode Exit fullscreen mode

由于 Ruby 允许我们覆盖任何方法，我们也可以覆盖`new`类方法。

```
class Greeting
  def self.new(args)
    puts args
    puts "New ends here."
  end

  def initialize(args)
    puts "I am getting initialized with #{args}"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们看看创建一个 Greeting 类的对象的输出是什么。

```
Greeting.new({name: "Prathamesh", country: "India"})

>> Greeting.new({name: "Prathamesh", country: "India"})
{:name=>"Prathamesh", :country=>"India"}
New ends here.
=> nil 
```

Enter fullscreen mode Exit fullscreen mode

不出所料，Ruby 愉快地调用了被覆盖的`new`方法，并且不再调用`initialize`方法，因为我们没有从我们的`new`定义中调用它。

现在让我们来看看我们之前讨论过的 Rails 的片段:

```
class DeprecationProxy
  def self.new(*args, &block)
    object = args.first

    return object unless object
    super
  end
  ...
end 
```

Enter fullscreen mode Exit fullscreen mode

很明显，`new`方法被`DeprecationProxy`类覆盖，当对象有 falsey 值时，它返回相同的对象。

```
return nil unless nil
return false unless false 
```

Enter fullscreen mode Exit fullscreen mode

还应该注意的是，这个被覆盖的`new`方法调用最后一行的`super`，它又调用`Class#new`。这确保了 Ruby 的`new`方法的语义得以保留。这将确保在被覆盖的`new`方法被执行后，`DeprecationProxy.new`将调用`DeprecationProxy`的`initialize`方法。

订阅我的[时事通讯](https://www.prathamesh.tech/mailing-list)来了解更多关于 Ruby 如何通过窥探 Rails 代码来工作的信息。