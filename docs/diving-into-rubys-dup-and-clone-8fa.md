# 深入 Ruby 的#dup 和#clone

> 原文：<https://dev.to/appsignal/diving-into-rubys-dup-and-clone-8fa>

在今天的帖子中，我们将了解 Ruby 的`#dup`和`#clone`。我们将从一个引发这种兴趣的真实例子开始。之后，我们将更深入地学习`#dup`是如何在 Ruby 中实现的，以及它与`#clone`相比如何。然后我们将通过实现我们自己的`#dup`方法来结束。我们走吧！

# 我是如何开始使用 Dup 的

当我在一家专门为非政府组织发起募捐活动的公司工作时，我经常不得不复制活动并创建新的活动。例如，2018 年的活动结束后，需要为 2019 年制定一个新的活动。

一个活动通常有很多配置选项，我真的不想再设置了。这将花费相当多的时间，而且容易出错。所以我从复制 DB 记录开始，并从那里开始。

在最初的几个活动中，我实际上是手工复制的。大概是这样的:

```
current_campaign = Campaign.find(1)
new_campaign = current_campaign
new_campaign.id = nil
new_campaign.created_at = nil
new_campaign.updated_at = nil
new_campaign.title = "Campaign 2019"
new_campaign.save! 
```

这是可行的，但是需要大量的输入，更不用说容易出错了。过去有几次忘记设置`created_at`到`nil`了。

因为这感觉有点痛苦，我无法想象这是最好的解决方法。事实证明，有一个更好的方法！

```
new_campaign = Campaign.find(1).dup
new_campaign.title = "Campaign 2019"
new_campaign.save! 
```

这将把 ID 和时间戳设置为`nil`，这正是我们想要完成的。

这就是我第一次开始使用`#dup`的原因。现在，让我们深入了解一下`#dup`实际上是如何工作的。

## 引擎盖下是怎么回事？

`#dup`方法的默认 Ruby 实现允许你给你的对象添加一个特殊的初始化器，只有当一个对象通过`#dup`方法初始化时才会被调用。这些方法是:

*   `initialize_copy`
*   `initialize_dup`

这些方法的实现实际上非常有趣，因为它们默认不做任何事情。它们基本上是供您覆盖的占位符。

这是直接取自 Ruby 源代码:

```
VALUE
rb_obj_dup(VALUE obj)
{
    VALUE dup;

    if (special_object_p(obj)) {
            return obj;
    }
    dup = rb_obj_alloc(rb_obj_class(obj));
    init_copy(dup, obj);
    rb_funcall(dup, id_init_dup, 1, obj);

    return dup;
} 
```

对我们来说，有趣的部分在第 11 行，Ruby 在这里调用初始化方法`#intialize_dup`。

> `rb_funcall`是一个在 ruby C 代码中经常使用的函数。它用于调用对象上的方法。在这种情况下，它将在`dup`对象上调用`id_init_dup`。`1`表示有多少个参数，在本例中只有一个:`obj`

让我们更深入一点，看看这个实现:

```
VALUE
rb_obj_init_dup_clone(VALUE obj, VALUE orig)
{
    rb_funcall(obj, id_init_copy, 1, orig);
    return obj;
} 
```

正如你在这个例子中看到的，除了它调用`id_init_copy`之外，实际上什么也没有发生。现在我们进入了兔子洞，让我们也来看看这个方法:

```
VALUE
rb_obj_init_copy(VALUE obj, VALUE orig)
{
    if (obj == orig) return obj;
    rb_check_frozen(obj);
    rb_check_trusted(obj);
    if (TYPE(obj) != TYPE(orig) || rb_obj_class(obj) != rb_obj_class(orig)) {
    rb_raise(rb_eTypeError, "initialize_copy should take same class object");
    }
    return obj;
} 
```

即使有更多的代码，除了一些内部需要的检查之外，没有什么特别的事情发生(但是这可能是另一个好的主题)。

所以在实现中发生的事情是，Ruby 给你一个端点，给你提供实现自己感兴趣的行为所需的工具。

## Rails 的 Dup 实现

这正是 Rails 在很多地方所做的，但是现在，我们只对如何清除`id`和时间戳字段感兴趣。

ID 在 ActiveRecord 的核心模块中被清除。它考虑到你的主键是什么，所以即使你改变了它，它仍然会重置它。

```
# activerecord/lib/active_record/core.rb
def initialize_dup(other) # :nodoc:
  @attributes = @attributes.deep_dup
  @attributes.reset(self.class.primary_key)

  _run_initialize_callbacks

  @new_record               = true
  @destroyed                = false
  @_start_transaction_state = {}
  @transaction_state        = nil

  super
end 
```

时间戳在时间戳模块中被清除。它告诉 Rails 清除所有 Rails 可以用来创建和更新的时间戳(`created_at`、`created_on`、`updated_at`和`updated_on`)。

```
# activerecord/lib/active_record/timestamp.rb
def initialize_dup(other) # :nodoc:
  super
  clear_timestamp_attributes
end 
```

这里一个有趣的事实是，Rails 故意选择覆盖`#initialize_dup`方法，而不是`#initialize_copy`方法。为什么会这样？我们来调查一下。

## 对象#initialize_copy 解释

在上面的代码片段中，我们看到了当你在一个方法上使用`.dup`时，Ruby 是如何调用`#initialize_dup`的。但是也有一种`#initialize_copy`方法。为了更好地解释这在哪里使用，让我们看一个例子:

```
class Animal
  attr_accessor :name

  def initialize_copy(*args)
    puts "#initialize_copy is called"
    super
  end

  def initialize_dup(*args)
    puts "#initialize_dup is called"
    super
  end
end

animal = Animal.new
animal.dup

# => #initialize_dup is called
# => #initialize_copy is called 
```

我们现在可以看到调用顺序是什么。Ruby 先调出`#initialize_dup`，再调用`#initialize_copy`。如果我们将对`super`的调用保留在`#initialize_dup`方法之外，我们就永远不会调用`initialize_copy`，所以保留这一点很重要。

## 复制东西还有其他方法吗？

现在我们已经看到了这个实现，您可能想知道拥有两个`#initialize_*`方法的用例是什么。答案是:还有一种复制对象的方式，叫做`#clone`。如果你想复制一个对象，包括它的内部状态，你通常使用`#clone`。

这就是 Rails 对 ActiveRecord 使用的`#dup`方法。它使用`#dup`来允许您复制没有“内部”状态(id 和时间戳)的记录，并将`#clone`留给 Ruby 来实现。

当使用`#clone`方法时，拥有这个额外的方法还需要一个特定的初始化器。为此，您可以覆盖`#initialize_clone`。该方法使用与`#initialize_dup`相同的生命周期，并将调用`#initialize_copy`。

知道了这一点，初始化器方法的命名就更有意义了。我们可以使用`#initialize_(dup|clone)`进行特定的实现，这取决于你是使用`#dup`还是`#clone`。如果我们有用于两者的支配性行为，您可以将它放在`#initialize_copy`中。

## 克隆一种动物

(举个例子，这篇博文没有伤害任何动物)

现在让我们来看一个它在实践中如何工作的例子。

```
class Animal
  attr_accessor :name, :dna, :age

  def initialize
    self.dna = generate_dna
  end

  def initialize_copy(original_animal)
    self.age = 0
    super
  end

  def initialize_dup(original_animal)
    self.dna = generate_dna
    self.name = "A new name"
    super
  end

  def initialize_clone(original_animal)
    self.name = "#{original_animal.name} 2"
    super
  end

  def generate_dna
    SecureRandom.hex
  end
end

bello = Animal.new
bello.name = "Bello"
bello.age = 10

bello_clone = bello.clone
bello_dup = bello.dup

bello_clone.name # => "Bello 2"
bello_clone.age # => 0

bello_dup.name # => "A new name"
bello_dup.age # => 0 
```

让我们来分析一下这里到底发生了什么。我们有一个名为`Animal`的类，根据我们如何复制动物，它应该有不同的行为:

*   当我们克隆动物时，DNA 保持不变，它的名字将是原来的名字加上 2。
*   当我们复制一个动物时，我们会在原来的基础上创造一个新的动物。它有自己的 DNA 和新名字。
*   在所有情况下，动物都是从婴儿开始的。

我们实现了三种不同的初始化器来实现这一点。`#initialize_(dup|clone)`方法将一直调用到`#initialize_copy`，从而确保年龄被设置为 0。

## 围捕克隆人和其他动物

从解释我们需要挠自己的痒开始，我们研究复制一个数据库记录。我们从活动示例中的手动复制转变为`#dup`和`#clone`。然后我们把它从实用带到有趣的地方，并研究这是如何在 Ruby 中实现的。我们还和 T2 和 T3 动物一起玩耍。我们希望你喜欢我们的深潜，就像我们写它一样。