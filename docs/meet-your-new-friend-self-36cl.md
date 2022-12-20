# 认识你的新朋友:自我

> 原文：<https://dev.to/mercier_remi/meet-your-new-friend-self-36cl>

编码时，知道什么是什么并不总是容易的。为什么我不能在这个数组上调用那个方法？在一个方法中，我到底在处理什么？在这些情况下，`self`可以证明是一个超级骗子朋友！

即使你是初级开发人员，你也可能在类方法定义中遇到过`self`。

```
class Hello
  def self.from_the_class
    "👋 from the class method."
  end
end

Hello.from_the_class # => "👋 from the class method." 
```

但通常就是这样。

现在，当你对正在发生的事情的理解有点模糊时，`self`也可以派上用场。让我们编码一些东西！

假设我想重新编码`.map`方法。

```
 # Native method
  p [1, 2, 3].map { |integer| integer * 2 } # => [2, 4, 6]

  # Let's write our own!
  p [1, 2, 3].my_map { |integer| integer * 2 } 
```

我要做的第一件事是从定义`.my_map`开始，让它输出一些好的东西。

```
 def my_map
    "Hello from my_map"
  end

  p [1, 2, 3].my_map { |integer| integer * 2 } 
```

当运行这段代码时，我所期望的是得到`"Hello from my_map"`。

```
 NoMethodError: private method `.my_map` called for [1, 2, 3]:Array
  from (pry):8:in `<main>` 
```

🤔咄。

私有方法是什么意思？让我们用`self`输出我们当前的上下文(即我们到底在哪里)。

```
 p "Where are in: #{self}"

  def my_map
    "Hello from my_map"
  end

  p [1, 2, 3].my_map { |integer| integer * 2 } 
```

```
 "Where are in: main"
  NoMethodError: private method `my_map` called for [1, 2, 3]:Array
  from (pry):8:in `<main>` 
```

`main`意味着我们当前处于对象的上下文中。还记得有人告诉你，Ruby 中的所有东西都是对象吗？好吧，即使是物体也是物体。

Ruby 顶级类(或对象)是 BasicObject > Kernel > Object。

BasicObject 是 Ruby 中所有类的父类。这里定义了`==`、`!`、`!=`、`equal?`等基本方法。内核是一个模块，具有基本的公共/私有方法，如`puts`、`gets`或`chomp`。对象继承自 BasicObject，并混合在内核模块中，以便更好地测量。所有其他 Ruby 对象都继承自 Object。通过这种方式，Ruby 对象获得了大量的方法，后者是在适当的抽象层次上定义的。来自对象的一些方法包括:`to_s`或`nil?`。幻想对吗？

好吧，但是我的那个`NoMethodError`呢？

以下是我的错误:我忘了在课堂上写`.my_map`。所以默认情况下它是在 Object 对象中定义的。然而，我试图在一个数组上调用`.my_map`。

为了调用数组上的`.my_map`,我需要在 array 类中打开并定义它。

```
class Array
  p "Where are in: #{self}"

  def my_map
    "Hello from my_map"
  end
end

p [1, 2, 3].my_map { |integer| integer * 2 } 
```

```
 Where are in: Array
  Hello from my_map 
```

👏有用！Ruby 中的类可以被打开和修改。现在我已经在 Array 类中定义了`.my_map`，我可以在 arrays 上调用它。很简单！

补充说明:如果你喜欢，你可以修改真实的`.map`，让它也做一些奇怪的事情。

好了，现在我想把`{ |integer| integer * 2 }`块传给`.my_map`。我知道我应该在数组中循环，并在某个时候产生这个块。但是既然我没有把数组作为参数传递，我该在哪里调用`.each`?

我们来看看`self`有什么说的。

```
class Array
  def my_map
    # Instantiate an empty array to store results
    results = []
    # Output yield to see the current context
    "Where are in: #{self}"
  end
end

p [1, 2, 3].my_map { |integer| integer * 2 } 
```

```
 "Where are in: [1, 2, 3]" 
```

当我们在`.my_map`中时，我们使用的默认值是调用的数组`.my_map`。我现在知道我可以在`self`(即`[1, 2, 3]`)上给`.each`打电话。

```
class Array
  def my_map
    results = []
    # Loop through the array and output yield just to see what's what
    self.each do |i|
      yield
    end
  end
end

p [1, 2, 3].my_map { |integer| integer * 2 } 
```

```
 NoMethodError: undefined method `*' for nil:NilClass
  from (pry):94:in `block in <class:Array>' 
```

首先，我可以从错误消息中读到我在`<class:Array>`中。那很好。那么，这个`NoMethodError: undefined method '*' for nil:NilClass`呢？嗯，它只是简单的说在 NilClass <sup id="fnref1">[1](#fn1)</sup> 中，没有`*`定义的 <sup id="fnref2">[2](#fn2)</sup> 方法。

意味着我的块`{ |integer| integer * 2 }`不能执行乘法，因为它里面的`integer`变量是`nil`。`yield`尽管可以接受争论。所以在循环内部，我将把当前的整数- `i` -传递给`yield`。

```
class Array
  def my_map
    results = []
    # Loop through the array and execute the block
    self.each do |i|
      # Give yield the current integer and store result in array
      results << yield(i)
    end
    # Return final array
    results
  end
end

p [1, 2, 3].my_map { |integer| integer * 2 } 
```

```
 [2, 4, 6] 
```

可以这样重构:

```
class Array
  def my_map
    results = []
    # Loop through the array and execute the block
    each { |i| results << yield(i) } # self is implicite so I can remove it
    results
  end
end

p [1, 2, 3].my_map { |integer| integer * 2 } 
```

🥳搞定了。

确定变量的范围也是非常有用的。但这可以作为另一篇文章的素材。

今天的关键要点是:下次你不知道自己在代码中的什么位置时，调用`self`来拯救你！

干杯，

雷米

* * *

1.  是的，`nil`也有自己的阶级。 [↩](#fnref1)

2.  是的，`*`是定义在特定对象上的方法🤪。要检查 NilClass 是否定义了`*`方法，运行`irb`中的`NilClass.public_methods.include? '*'`和`NilClass.private_methods.include? '*'`。 [↩](#fnref2)