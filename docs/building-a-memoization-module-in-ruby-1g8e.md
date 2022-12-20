# 用 Ruby 构建记忆模块

> 原文：<https://dev.to/dabrorius/building-a-memoization-module-in-ruby-1g8e>

不久前，在一次工作面试中，我被要求用 Ruby 开发一个通用的记忆方法。
目标是能够通过简单地调用
来记忆一个方法

```
memoize :my_awesome_method 
```

这并不像一开始听起来那么难，但是这个任务有很大的深度，并且需要相当好的 Ruby 元编程知识。这就是为什么我决定在这篇博文中重新审视这个任务。

但是，首先，什么是记忆化？维基百科是这样说的:

> 记忆化是一种优化技术，主要用于通过存储昂贵的函数调用的结果并在相同的输入再次出现时返回缓存的结果来加速计算机程序。

每当调用一个记忆化的方法时，我们都要记下调用它的参数和它产生的结果。如果将来用相同的参数再次调用相同的方法，我们跳过计算并返回上次产生的结果。

让我们写一个昂贵的方法，然后我们可以尝试优化。

```
class Snail
  def serve_tea(kind)
    sleep(2)
    "Here's a #{kind} tea for you!"
  end
end 
```

我们有一个带有单一方法`serve_tea`的`Snail`类，在休眠 2 秒钟后返回一个字符串。

## 非通用解决方案

首先，让我们直接实现记忆化作为`serve_tea`方法的一部分。

我们可以使用散列作为缓存存储。我们将使用调用方法的参数作为键，结果作为值返回。

```
class Snail
  # Step 0: Initialize the cache store
  @@cache = {}

  def serve_tea(kind)
    # Step 1: Check if a return value for this parameter is already stored
    return @@cache[kind] if @@cache.key?(kind)

    # Step 2: Do whatever you used to do
    sleep(2)
    @@cache[kind] = "Here's a #{kind} tea for you!" # Step 3: Cache the result
  end
end 
```

直观上，我们为缓存存储使用一个类变量，这意味着 snail 的所有实例将使用相同的缓存。我们将在本文后面讨论这种方法，但是首先，让我们尝试将记忆逻辑提取到一个通用的解决方案中。

## 类属记忆方法

当调用`memoize`方法时，它需要用记忆逻辑包装给定的方法。我们在这里探索的解决方案分两步工作。首先，我们将待记忆的方法存储在一个变量中，然后用记忆后的版本覆盖原来的方法。该方法的记忆版本将调用我们存储在变量中的原始方法。

我们可以通过调用返回一个 [UnboundMethod](https://ruby-doc.com/core-trunk/UnboundMethod.html) 对象的`instance_method`来完成第一步。这样，我们从一个方法中创建了一个对象，我们可以将它存储在一个变量中，稍后通过`call`方法调用它。

好的方面是对底层类的后续更改不会影响未绑定的方法，这意味着我们可以自由地覆盖原始方法，然后从变量中调用未更改的变量。我们可以简单地通过定义一个与旧方法同名的新方法来覆盖原来的方法。

这为我们提供了创建通用`memoize`方法所需的所有工具。

```
class Snail
  @@cache = {}

  def self.memoize(method_name)
    # Step 1: Objectify the method
    inner_method = instance_method(method_name)

    # Step 2: Overwrite the old method with the memoized version
    define_method method_name do |arg|
      return @@cache[arg] if @@cache.key?(arg)
      @@cache[arg] = inner_method.bind(self).call(arg)
    end
  end

  def serve_tea(kind)
    sleep(2)
    "Here's a #{kind} tea for you!"
  end
  memoize :serve_tea
end 
```

## 提取一个模块的记忆方法

我们直接在`Snail`类上将`memoize`方法定义为类方法。如果我们想在不同的类中使用它，我们需要将它提取到一个模块中。

我们将使用`extend`关键字而不是`include`关键字将这个模块添加到`Snail`类中，因为我们希望`memoize`是一个类方法，而不是一个实例方法。

```
module Memoize
  @@cache = {}
  def memoize(method_name)
    inner_method = instance_method(method_name)
    define_method method_name do |arg|
      return @@cache[arg] if @@cache.key?(arg)
      @@cache[arg] = inner_method.bind(self).call(arg)
    end
  end
end

class Snail
  extend Memoize
  def serve_tea(kind)
    sleep(2)
    "Here's a #{kind} tea for you!"
  end
  memoize :serve_tea
end 
```

```
snail_one = Snail.new
snail_two = Snail.new

puts snail_one.serve_tea('green') # Takes two seconds to calculate
puts snail_two.serve_tea('green') # Returns instantly, using the cached value 
```

## 固定缓存键

虽然前面的实现看起来工作正常，但是有两个重要的问题。

首先，我们只能记忆带单个参数的方法。如果我们试图像这样扩展我们的方法

```
def serve_tea(kind, sugar)
  sleep(2)
  "Here's a #{kind} tea #{sugar ? 'with' : 'without'} sugar for you!"
end
memoize :serve_tea 
```

我们将无法运行代码。

```
`serve_tea': wrong number of arguments (given 1, expected 2) (ArgumentError) 
```

我们可以通过使用 splat 操作符很容易地解决这个问题。Splat 运算符将收集传递给该方法的所有参数，并将它们转换为数组，然后在调用内部方法时将它们转换回参数列表。

您会注意到，现在我们使用一个`Array`对象作为缓存对象的`Hash`键。这很好，因为在 Ruby 中，Hash 允许使用任何对象类型作为键。

```
module Memoize
  @@cache = {}
  def memoize(method_name)
    inner_method = instance_method(method_name)

    # *args will collect all arguments into an array
    define_method method_name do |*args|
      return @@cache[args] if @@cache.key?(args)

      # *args will convert the args Array object back into a list of arguments
      @@cache[args] = inner_method.bind(self).call(*args)
    end
  end
end 
```

然而，这种实现还有另一个问题。在缓存方法结果时，我们没有考虑方法名。如果我们记忆两个接收相同参数的不同方法，我们将缓存并返回不正确的结果。这是这种情况的一个例子。

```
class Snail
  extend Memoize
  def serve_tea(kind)
    sleep(2)
    "Here's a #{kind} tea for you!"
  end
  memoize :serve_tea

  def serve_coffee(kind)
    sleep(2)
    "Here's a #{kind} coffee for you!"
  end
  memoize :serve_coffee
end 
```

```
snail_one = Snail.new
snail_two = Snail.new

puts snail_one.serve_tea('black') # => Here's a black tea for you!
puts snail_two.serve_coffee('black') # => Here's a black tea for you. 
```

即使第二次调用的是`serve_coffee`方法，我们的记忆模块还是从`serve_tea`方法返回了结果。

为了解决这个问题，我们可以将缓存键构建为一个数组，其中第一个元素是方法名，第二个元素是参数数组。虽然这个键相当复杂，因为散列键可以是任何对象类型，但我们仍然很好。

```
module Memoize
  @@cache = {}
  def memoize(method_name)
    inner_method = instance_method(method_name)
    define_method method_name do |*args|
      # Use both arguments and the method name as caching key
      key = [method_name, args]
      return @@cache[key] if @@cache.key?(key)
      @@cache[key] = inner_method.bind(self).call(*args)
    end
  end
end 
```

## 传承血泪史

我们的`Memoization`模块使用一个类变量作为缓存存储。直觉上，这似乎是一个好的解决方案，然而，我们很容易遇到问题。

```
class Snail
  extend Memoize

  def serve_tea(kind)
    sleep(2)
    "Here's a #{kind} tea for you!"
  end
  memoize :serve_tea
end

class EvilSnail
  extend Memoize

  def serve_tea(kind)
    sleep(2)
    "Here's a POISONED #{kind} tea for you!"
  end
  memoize :serve_tea
end

evil_snail = EvilSnail.new
snail = Snail.new
puts evil_snail.serve_tea('green') # => Here's a POISONED green tea for you!
puts snail.serve_tea('green') # => Here's a POISONED green tea for you! 
```

哦，不，我们把毒茶端错了人！
这里的问题是缓存类变量属于`Memoize`模块，并在使用该模块的类之间共享。我们可以通过用类实例变量替换类变量来避免这个问题，因为这个原因，这被认为是一个好的实践。

```
module Memoize
  def memoize_cache
    @memoize_cache ||= {}
  end

  def memoize(method_name)
    inner_method = instance_method(method_name)
    define_method method_name do |*args|
      key = [method_name, args]
      cache = self.class.memoize_cache
      return cache[key] if cache.key?(key)
      cache[key] = inner_method.bind(self).call(*args)
    end
  end
end 
```

现在每个使用`Memoization`模块的类都有自己的包含缓存的实例变量。这将防止兄弟类之间的缓存混淆，并使茶可以再次安全饮用。

## 结束语

和其他编程问题一样，问题似乎出在细节上。虽然我们在这篇博文中讨论了一些边缘情况，但我仍然不建议在现实应用中使用这种解决方案，因为更多的问题肯定隐藏在角落后面。

要获得更完整的解决方案，您可以查看 [memoist](https://github.com/matthewrudy/memoist) gem。