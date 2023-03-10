# Ruby 的隐藏宝石，委托人和可转发

> 原文：<https://dev.to/appsignal/ruby-s-hidden-gems-delegator-and-forwardable-3pkh>

在今天探索 Ruby 标准库中隐藏的宝石的过程中，我们将看看委托。

不幸的是，这个术语——像许多其他术语一样——随着时间的推移变得有些混乱，对不同的人有不同的含义。据[维基百科](https://en.wikipedia.org/wiki/Delegation_(object-oriented_programming)):

> 委托是指在另一个原始对象(发送方)的上下文中评估一个对象(接收方)的成员(属性或方法)。委托可以通过将发送对象传递给接收对象来显式完成，这可以在任何面向对象的语言中完成；或者隐式地由语言的成员查找规则来定义，这需要对特性的语言支持。

然而，更多的时候，人们也用这个术语来描述一个对象调用另一个对象的相应方法，而不将自身作为参数传递，这可以更准确地称为“转发”。

解决了这个问题后，在本文的其余部分，我们将使用“委托”来描述这两种模式。

## 委托人

让我们通过查看标准库的 [`Delegator`](https://ruby-doc.org/stdlib-2.6.2/libdoc/delegate/rdoc/Delegator.html) 类开始探索 Ruby 中的委托，该类提供了几种委托模式。

### 简单委托者

其中最简单的，也是我在野外遇到最多的，是 [`SimpleDelegator`](https://ruby-doc.org/stdlib-2.6.2/libdoc/delegate/rdoc/SimpleDelegator.html) ，它包装了一个通过初始化器提供的对象，然后将所有缺失的方法委托给它。让我们来看看这是怎么回事:

```
require 'delegate'

User = Struct.new(:first_name, :last_name)

class UserDecorator < SimpleDelegator
  def full_name
    "#{first_name}  #{last_name}"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

首先，我们需要`require 'delegate'`使`SimpleDelegator`对我们的代码可用。我们还使用了一个 [`Struct`](https://ruby-doc.org/core-2.6.2/Struct.html) 来创建一个简单的`User`类，带有`first_name`和`last_name`访问器。然后我们添加了`UserDecorator`,它定义了一个`full_name`方法，将各个名称部分组合成一个字符串。这就是`SimpleDelegator`发挥作用的地方:因为`first_name`和`last_name`都没有在当前类上定义，所以它们将在包装的对象上被调用:

```
decorated_user = UserDecorator.new(User.new("John", "Doe"))
decorated_user.full_name
#=> "John Doe" 
```

Enter fullscreen mode Exit fullscreen mode

`SimpleDelegator`还让我们用`super`覆盖委托方法，在包装的对象上调用相应的方法。在我们的例子中，我们可以只显示名字的首字母，而不是完整的名字:

```
class UserDecorator < SimpleDelegator
  def first_name
    "#{super[0]}."
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
decorated_user.first_name
#=> "J."
decorated_user.full_name
#=> "J. Doe" 
```

Enter fullscreen mode Exit fullscreen mode

### 委托人

在阅读上面的例子时，你是否想知道我们的`UserDecorator`是如何知道委托给哪个对象的？答案就在`SimpleDelegator`的父类——`Delegator`中。这是一个抽象基类，通过为`__getobj__`和`__setobj__`分别提供实现来获取和设置委托目标，从而定义自定义委托方案。使用这些知识，我们可以很容易地构建我们自己版本的`SimpleDelegator`用于演示目的:

```
class MyDelegator < Delegator
  attr_accessor :wrapped
  alias_method :__getobj__, :wrapped

  def initialize(obj)
    @wrapped = obj
  end
end

class UserDecorator < MyDelegator
  def full_name
    "#{first_name}  #{last_name}"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这与在`SimpleDelegator`的`initialize`方法中调用`__setobj__`的真实实现略有不同。因为我们的自定义代理类不需要它，所以我们完全省略了那个方法。

这应该和我们之前的例子完全一样；的确如此:

```
UserDecorator.superclass
#=> MyDelegator < Delegator
decorated_user = UserDecorator.new(User.new("John", "Doe"))
decorated_user.full_name
#=> "John Doe" 
```

Enter fullscreen mode Exit fullscreen mode

### 委托方法

最后一个委托模式`Delegate`提供给我们的是有点奇怪命名的`Object.DelegateClass`方法。这将为一个特定的类生成并返回一个代理类，我们可以从这个类继承:

```
 class MyClass < DelegateClass(ClassToDelegateTo)
    def initialize
      super(obj_of_ClassToDelegateTo)
    end
  end 
```

Enter fullscreen mode Exit fullscreen mode

虽然这乍一看可能令人困惑——特别是继承的右边可以包含任意的 Ruby 代码——但它实际上遵循了我们之前探索过的模式，也就是说，它类似于从`SimpleDelegator`继承。

Ruby 的标准库使用这个特性来定义它的 [`Tempfile`](https://ruby-doc.org/stdlib-2.6.2/libdoc/tempfile/rdoc/Tempfile.html) 类，该类将其大部分工作委托给 [`File`](https://ruby-doc.org/core-2.6.2/File.html) 类，同时设置一些关于存储位置和文件删除的特殊规则。我们可以使用相同的机制来建立一个自定义的`Logfile`类，如下所示:

```
class Logfile < DelegateClass(File)
  MODE = File::WRONLY|File::CREAT|File::APPEND

  def initialize(basename, logdir = '/var/log')
    # Create logfile in location specified by logdir
    path = File.join(logdir, basename)
    logfile = File.open(path, MODE, 0644)

    # This will call Delegator's initialize method, so below this point
    # we can call any method from File on our Logfile instances.
    super(logfile)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

## 可转发

有趣的是，Ruby 的标准库以 [`Forwardable`](https://ruby-doc.org/stdlib-2.6.2/libdoc/forwardable/rdoc/Forwardable.html) 模块及其`def_delegator`和`def_delegators`方法的形式为我们提供了另一个用于委托的库。

让我们用`Forwardable`重写我们原来的`UserDecorator`例子。

```
require 'forwardable'

User = Struct.new(:first_name, :last_name)

class UserDecorator
  extend Forwardable
  def_delegators :@user, :first_name, :last_name

  def initialize(user)
    @user = user
  end

  def full_name
    "#{first_name}  #{last_name}"
  end
end

decorated_user = UserDecorator.new(User.new("John", "Doe"))
decorated_user.full_name
#=> "John Doe" 
```

Enter fullscreen mode Exit fullscreen mode

最明显的区别是委托不是通过`method_missing`自动提供的，而是需要为我们想要转发的每个方法显式声明。这允许我们“隐藏”包装对象的任何不想向客户公开的方法，这给了我们对公共接口更多的控制，也是我通常更喜欢`Forwardable`而不是`SimpleDelegator`的主要原因。

`Forwardable`的另一个很好的特性是通过`def_delegator`重命名委托方法的能力，它接受一个可选的第三个参数来指定所需的别名:

```
class UserDecorator
  extend Forwardable
  def_delegator :@user, :first_name, :personal_name
  def_delegator :@user, :last_name, :family_name

  def initialize(user)
    @user = user
  end

  def full_name
    "#{personal_name}  #{family_name}"
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

上面的`UserDecorator`只暴露了别名的`personal_name`和`family_name`方法，而仍然转发给被包装的`User`对象的`first_name`和`last_name`:

```
decorated_user = UserDecorator.new(User.new("John", "Doe"))
decorated_user.first_name
#=> NoMethodError: undefined method `first_name' for #<UserDecorator:0x000000010f995cb8>
decorated_user.personal_name
#=> "John" 
```

Enter fullscreen mode Exit fullscreen mode

这个特性有时会非常有用。我过去曾成功地使用它在具有相似接口但对方法名有不同期望的库之间移植代码。

## 标准库外

尽管标准库中已有委托解决方案，但 Ruby 社区多年来已经开发了几种替代方案，接下来我们将探讨其中的两种。

### 委托

考虑到 Rails 的流行，它的 [`delegate`](https://apidock.com/rails/Module/delegate) 方法可能是 Ruby 开发者最常用的委托形式。下面是我们如何用它来重写我们可靠的老`UserDecorator` :

```
# In a real Rails app this would most likely be a subclass of ApplicationRecord
User = Struct.new(:first_name, :last_name)

class UserDecorator
  attr_reader :user
  delegate :first_name, :last_name, to: :user

  def initialize(user)
    @user = user
  end

  def full_name
    "#{first_name}  #{last_name}"
  end
end

decorated_user = UserDecorator.new(User.new("John", "Doe"))
decorated_user.full_name
#=> "John Doe" 
```

Enter fullscreen mode Exit fullscreen mode

这与`Forwardable`非常相似，但是我们不需要使用`extend`，因为`delegate`是直接在`Module`上定义的，因此在每个类或模块体中都可用(是好是坏，你自己决定)。然而，`delegate`自有妙招。首先，有一个`:prefix`选项，它将在委托的方法名称前加上我们要委托的对象的名称。所以，

```
delegate :first_name, :last_name, to: :user, prefix: true 
```

Enter fullscreen mode Exit fullscreen mode

会生成`user_first_name`和`user_last_name`方法。或者，我们可以提供一个自定义前缀:

```
delegate :first_name, :last_name, to: :user, prefix: :account 
```

Enter fullscreen mode Exit fullscreen mode

我们现在可以访问用户名的不同部分，如`account_first_name`和`account_last_name`。

`delegate`的另一个有趣的选项是它的`:allow_nil`选项。如果我们委托的对象当前是`nil`——例如由于未设置的`ActiveRecord`关系——我们通常会以`NoMethodError` :
结束

```
decorated_user = UserDecorator.new(nil)
decorated_user.first_name
#=> Module::DelegationError: UserDecorator#first_name delegated to @user.first_name, but @user is nil 
```

Enter fullscreen mode Exit fullscreen mode

然而，使用`:allow_nil`选项，这个调用将成功并返回`nil`而不是

```
class UserDecorator
  delegate :first_name, :last_name, to: :user, allow_nil: true

  ...
end

decorated_user = UserDecorator.new(nil)
decorated_user.first_name
#=> nil 
```

Enter fullscreen mode Exit fullscreen mode

### 铸造

我们要看的最后一个委托选项是吉姆·盖伊的 [`Casting`](https://github.com/saturnflyer/casting) gem，它允许开发人员“在 Ruby 中委托方法并保留自己”。这可能是最接近委托的严格定义，因为它使用 Ruby 的动态特性来临时重新绑定方法调用的接收者，类似于下面的:

```
UserDecorator.instance_method(:full_name).bind(user).call
#=> "John Doe" 
```

Enter fullscreen mode Exit fullscreen mode

最有趣的一点是，开发人员可以在不改变超类层次结构的情况下向对象添加行为。

```
require 'casting'

User = Struct.new(:first_name, :last_name)

module UserDecorator
  def full_name
    "#{first_name}  #{last_name}"
  end
end

user = User.new("John", "Doe")
user.extend(Casting::Client)
user.delegate(:full_name, UserDecorator) 
```

Enter fullscreen mode Exit fullscreen mode

这里我们用`Casting::Client`扩展了`user`，这使我们可以访问`delegate`方法。或者，我们可以在`User`类中使用`include Casting::Client`来赋予所有实例这种能力。

此外，`Casting`提供了在块的生命周期内临时添加行为的选项，或者直到再次手动删除为止。为此，我们首先需要启用缺失方法的委托:

```
user.delegate_missing_methods 
```

Enter fullscreen mode Exit fullscreen mode

要为单个块的持续时间添加行为，我们可以使用`Casting`的`delegating`类方法:

```
Casting.delegating(user => UserDecorator) do
  user.full_name #=> "John Doe"
end

user.full_name
#NoMethodError: undefined method `full_name' for #<struct User first_name="John", last_name="Doe"> 
```

Enter fullscreen mode Exit fullscreen mode

或者，我们可以添加行为，直到我们再次显式调用`uncast`:

```
user.cast_as(UserDecorator)
user.full_name
#=> "John Doe"
user.uncast
NoMethodError: undefined method `full_name' for #<struct User first_name="John", last_name="Doe"> 
```

Enter fullscreen mode Exit fullscreen mode

虽然比其他提出的解决方案稍微复杂一些，`Casting`提供了很多控制，Jim 在他的书 [Clean Ruby](http://www.clean-ruby.com/) 中展示了它的各种用途和更多。

## 总结

委托和方法转发是在相关对象之间划分责任的有用模式。在普通的 Ruby 项目中，`Delegator`和`Forwardable`都可以使用，而 Rails 代码倾向于使用它的`delegate`方法。为了最大限度地控制委托的内容，`Casting` gem 是一个很好的选择，尽管它比其他解决方案稍微复杂一些。

客座作者迈克尔美国柯尔百货公司 T2 与鲁比的恋情始于 2003 年。他也喜欢写作和谈论语言，并共同组织了 [Bangkok.rb](https://www.meetup.com/bangkok-rb/members/) 和 [RubyConf Thailand](https://rubyconfth.com/) 。