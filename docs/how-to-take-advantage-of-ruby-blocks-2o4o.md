# 如何利用 Ruby 块

> 原文：<https://dev.to/mickeytgl/how-to-take-advantage-of-ruby-blocks-2o4o>

ruby 中的块是该语言的一个非常强大的方面。与语言的其他特性相比，它们通常很少被使用，而且不是每个人都习惯使用它们，更不用说编写消耗块的代码了。所以先来个小介绍:

**什么是积木？**
本质上，一种没有名字的方法。它是一段代码，由`{ }`或`do... end`分隔。我们可以在特定的上下文中调用，例如:

```
# The following syntaxes are equivalent. They will both execute the block twice.

2.times do
  puts "foo"
end

2.times { puts "foo" } 
```

一个更复杂的例子:让我们假设对于一个伪装成 gem 的用户，您想要一个方法，在这个方法中您可以传入一个用户，授予他们所有的权限，用这些权限运行一些代码，然后在代码完成运行后重置旧的权限。这是积木大放异彩的地方。一个非常简单的测试套件应该是这样的:

```
RSpec.describe UserImpersonator do
  it "grants permissions while inside the block" do

    UserImpersonator.grant_all_permissions valid_user do
      expect(valid_user.roles[:admin]).to be_true
    end

    expect(valid_user.roles[:admin]).to be_false
  end
end 
```

我们首先需要一个接受用户的`UserImpersonator`类和`grant_all_permissions`方法，以及当您拥有所有权限时想要运行的**代码块**。

```
class UserImpersonator
  def self.grant_all_permissions(user, &block)
    new(user).run(&block)
  end

  def initialize(user)
    @user = user
  end

  def run(&block)
    begin
      cache_old_permissions
      assign_all_permissions
      block.call
    ensure
      reset_permissions
    end
  end
end 
```

我们来分解一下上面的代码:

`self.grant_all_permissions`方法定义上的`&block`让方法知道它可以在块中被传递。这将实例化一个新的`UserImpersonator`对象，并对其调用 run 方法，转发`&block`参数。

`run`方法在`begin`关键字之后的前两行是设置好的，我们想要缓存旧的权限，这样我们可以在以后重置它们并分配新的权限。在这之后，我们希望实际运行传入的块，在这之后，我们希望重置权限，而不管中间是否出现了错误，因此使用了`ensure`关键字。

好了，我们刚刚创建了一个方法，它接受一个块并在我们想要的任何地方运行它，这当然很有用，但是我们可以更深入

现在，让我们尝试将一个特定的上下文传递到我们的块或块变量中，如果您使用过 rails，即使只使用了一点点，那么当您遍历数据库记录时，您可能会看到这种情况，例如:

```
User.all.each do |user|
  user.update admin: false
end 
```

块变量是管道之间的内容，在本例中为`user`。

**新示例:**假设我们在经营一家餐馆，我们想计算一份订单的成本。由于订单可以由不同的项目组成，我们希望能够在订单的上下文中添加尽可能多或尽可能少的项目。同样，从测试开始，我们可以有这样的期望:

```
RSpec.describe Order do
  it "allows to make operations" do
    result = Order.cost do |c|
      c.add_taco
      c.add_taco
      c.add_guacamole
      c.add_beer
    end

    expect(result).to eq 44
  end
end 
```

为了通过上面的测试，一个可能的实现是:

```
class Order
  def self.cost
    yield Actions.new
  end

  class Actions
    def initialize
      @cost = 0
    end

    def add_taco
      @cost += 10
    end

    def add_guacamole
      @cost += 6
    end

    def add_beer
      @cost += 18
    end
  end
end 
```

需要注意的事项:

1)块变量，或者在测试中的`c`是`Actions`类的一个实例，这意味着它可以访问`add_taco`、`add_guacamole`和`add_beer`方法

2)您不需要在`cost`方法中指定`&block`作为参数，因为您在方法内部使用了`yield`

3) `yield`使一个块成为一个**可选的**参数，这意味着你可以调用 cost 而根本不传递任何块。然而，这将失败，并抱怨一个

```
LocalJumpError (no block given (yield)) 
```

为了解决这个问题，一个叫做`block_given?`的方便的小方法允许你验证一个块是否被传入。所以你可以重构`cost`方法来处理:

```
def self.cost
  yield Actions.new if block_given?
end 
```

我真的希望这篇深入探究 ruby 更有趣的特性的文章是有用的。就我个人而言，发现我在 **RSpec** 或 **factory_bot** 中经常使用的工具的内部工作方式是如何一步步组合在一起的，我觉得很有启发