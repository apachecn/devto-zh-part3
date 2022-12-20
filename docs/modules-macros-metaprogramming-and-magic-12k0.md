# 模块、宏、元编程和魔法

> 原文：<https://dev.to/jsrn/modules-macros-metaprogramming-and-magic-12k0>

如果你使用过 [Rails](https://rubyonrails.org/) 框架，你可能会认出这个:

```
class Comment < ApplicationRecord
  belongs_to :article
end 
```

Enter fullscreen mode Exit fullscreen mode

这段代码意味着三件事:

1)我们有一个意见表。我们有一个商品目录。每个评论都通过某个 ID 与一篇文章相关联。

Rails 用户会理所当然地认为，如果他们有一个 Comment 类的实例，他们将能够执行`some_comment.article`来获得与评论相关的文章。

这篇文章将给你一个极其简单的视角，看看像 Rails 的 ActiveRecord 关系是如何实现的。首先，做一些基础工作。

# 模块

Ruby 中的模块可以用来扩展一个类的行为，有三种方法可以做到这一点:`include`、`prepend`和`extend`。三者的区别？它们在方法查找链中的位置。

```
class MyClass
  prepend PrependingModule
  include IncludingModule
  extend ExtendingModule
end 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中:

*   来自`PrependingModule`的方法将被创建为**实例方法**和**覆盖来自`MyClass`的**实例方法。
*   来自`IncludingModule`的方法将被创建为**实例方法**，但是**不会覆盖来自`MyClass`的**方法。
*   来自`ExtendingModule`的方法将被添加为`MyClass`上的**类方法**。

我们可以用`extend`做有趣的事情。

# 在解释期间执行代码

```
module Ownable
  def belongs_to(owner)
    puts "I belong to #{owner}!"
  end
end

class Item
  extend Ownable
  belongs_to :overlord
end 
```

Enter fullscreen mode Exit fullscreen mode

在上面的代码中，我们只是定义了一个模块和一个类。从未创建过该类的实例。然而，当我们在一个 IRB 会话中执行*这个代码*时，你会看到“我属于霸王！”作为输出。为什么？因为我们在定义类时编写的代码是在解释类定义时执行的。

如果我们使用 Ruby 的 [define_method](https://apidock.com/ruby/Module/define_method) 重新编写我们的模块来定义一个方法会怎么样？

```
module Ownable
  def belongs_to(owner)
    define_method(owner.to_sym) do
      puts self.object_id
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们作为参数传递给`belongs_to`的任何东西都将成为我们的`Item`类的实例上的方法。

```
our_item = Item.first
our_item.overlord
# => 70368441222580 
```

Enter fullscreen mode Exit fullscreen mode

非常好。你可能以前听说过这个术语，但这是“元编程”。写代码就是写代码。你刚刚进行了元编程。

# 绑在一起

您可能还会注意到，我们越来越接近我们对 Rails 的预期行为。

假设我们有自己的`Item`类，并且我们正在制作一个视频游戏，那么我们会说我们的物品属于一个玩家。

```
class Item
  extend Ownable
  belongs_to :player
end 
```

Enter fullscreen mode Exit fullscreen mode

我们的类 Rails 系统可以对此做出一些假设。

1)数据库中有一个表叫做`players`。
2)在我们的`items`表中有一列叫做`player_id`。
3)玩家模型由类`Player`表示。

让我们回到我们的模块，并根据这些假设进行调整。

```
module Ownable
  def belongs_to(owner)
    define_method(owner.to_sym) do
      # We need to get `Player` out of `:player`
      klass = owner.to_s.capitalize.constantize
      # We need to turn `:player` into `:player_id`
      foreign_key = "#{owner}_id".to_sym
      # We need to execute the actual query
      klass.find_by(id: self.send(foreign_key))
      # SELECT * FROM players WHERE id = :player_id LIMIT 1
    end
  end
end

class Item
  extend Ownable
  belongs_to :player
end

my_item = Item.first
my_item.player
# SELECT * FROM players WHERE id = 1 LIMIT 1
# => #<Player id: 12> 
```

Enter fullscreen mode Exit fullscreen mode

干净利落。