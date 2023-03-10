# 了解 Ruby on Rails ActiveRecord 验证

> 原文：<https://dev.to/justalever/understanding-ruby-on-rails-activerecord-validations-2m0a>

数据是一个伟大的东西，但错误的数据可以非常非常糟糕。幸运的是，如果你是一个 Ruby on Rails 程序员，你有一个很好的 ActiveRecord 验证助手 API 供你使用。

本指南探索了在给定的 Ruby on Rails 应用程序中常见的和不太常见的 ActiveRecord 验证。使用它来帮助保持您的数据库愉快！

[https://www.youtube.com/embed/c3hoXWO_6ao](https://www.youtube.com/embed/c3hoXWO_6ao)

### 为什么要使用验证？

以确保只将有效数据保存到数据库中。在客户端验证之上，模型级验证为 Ruby on Rails 应用程序中的所有表单字段提供了额外的安全层。

您可以提供各种级别的验证，但都有各自的优点和缺点。

*   **数据库约束**–如果需要，数据库级别的唯一约束允许没有重复的字段或列。这可以比唯一性更深入。即默认值、非零约束、范围等…
*   客户端验证(Client-side validation)–这些在视觉上很有用，可以提供更好的用户体验，但通常更不可靠。我几乎总是使用这种方法提供一个备份计划。
*   **模型级**(Rails)——我们现在说的！
*   **控制器级**(Rails)——很容易使用，但测试和维护更困难。让您的控制器尽可能轻是一个惯例/最佳实践。

### 验证实际发生在什么时候？

直接来自 ruby on rails 文档:

> 有两种活动记录对象:一种对应于数据库中的行，另一种不对应。当您创建一个新对象时，例如使用`new`方法，该对象还不属于数据库。一旦对该对象调用了`save`，它将被保存到适当的数据库表中。活动记录使用`new_record?`实例方法来确定一个对象是否已经在数据库中。

创建一个新的活动记录类让我们有机会使用一个`new_record?`方法来解决这一切。

```
class Article < ApplicationRecord
end 
```

这样，我们可以点击`rails console`来确定流程。

```
$ rails console

>> article = Article.new(title: "My Article")
=> #<Article id:nil title: "My Article", created_at: nil, updated_at: nil>
>> article.new_record?
=> true
>> article.save
=> true
>> article.new_record?
=> false 
```

实际上，调用`save`是将它提交给数据库，并在幕后创建必要的`SQL`语句来完成这一任务。因此，通常会在将数据完全发送到数据库之前运行验证。

以下方法可在使用时进行验证:

*   `create`
*   `create!`
*   `save`
*   `save!`
*   `update`
*   `update!`

> 如果记录无效，bang 版本(如`save!`)会引发异常。非 bang 版本没有:`save`和`update`返回`false`，`create`只是返回对象。

### 跳过验证的方法

使用以下任何一种方法都会完全跳过验证。**谨慎使用有价值的数据！**

*   `decrement!`
*   `decrement_counter`
*   `increment!`
*   `increment_counter`
*   `toggle!`
*   `touch`
*   `update_all`
*   `update_attribute`
*   `update_column`
*   `update_columns`
*   `update_counters`

## 验证助手

我喜欢 Ruby on Rails 的一点是，它为你做相当困难的事情提供了捆绑的好处。当我在野外部署一个新的应用程序时，验证助手是让我感到温暖和舒适的事情之一。这为您可能从客户端收集的大量数据提供了大量支持。

每个助手接受一个`:on`和`:message`选项。这些选项定义了什么时候应该运行验证，以及如果验证失败，应该将什么消息添加到一个`errors`集合中(您可以使用这些选项来帮助在您的视图中注入什么错误，以便用户知道应该更改/改变什么)。

有很多验证助手，下面是我最常用的一些。请查看关于活动记录验证的[指南](https://guides.rubyonrails.org/active_record_validations.html)了解更多使用案例。

### 确认

当两个文本字段需要具有相同的条目时，这对于验证非常有用。以电子邮件确认为例。

```
class User < ApplicationRecord
  validates :email, confirmation: true
end 
```

现在在你的视图中你可以有两个字段:

```
<%= text_field :user, :email %>
<%= text_field :user, :email_confirmation %> 
```

这里的一个问题是，只有当模型中的`presence`选项也设置为 true 时，才会执行该验证。

因此`User`模型得到了更新:

```
class User < ApplicationRecord
  validates :email, confirmation: true
  validates :email_confirmation, presence: true  
end 
```

### 排斥

如果您需要保留任何单词或确保给定条目是唯一的，请使用`exclusion`。

```
class Profile < ApplicationRecord   
  validates :username, exclusion: { in: %w(username1 username2 username3), message: "%{value} has already been taken." }
end 
```

注意我在这里也是如何使用`:message`的。这在所有验证帮助器上都有提供。

你也可以用相反的方式使用`inclusion`。有关更多信息，请参见文档！

### 格式

格式化字符串有时非常适合您的应用程序。您可以在验证中使用正则表达式来改变您想要的东西。

```
class Plan < ApplicationRecord
  validates :plan_type, format: { with: /\A[a-zA-Z]+\z/, message: "Plans can only inlude letters"}
end 
```

### 长度

我一直用这个来限制给定字段的字符长度。

```
class User < ApplicationRecord
  validates :name, length: { minimum: 5 } # sets a minimum
  validates :bio, length: { maximum: 500 } # sets a maximum
  validates :handle, length: { in: 5..16 } # provide a range
end 
```

也可以在这里随意使用`:message`个性化消息。以下内容都可以用来定制消息。

*   `wrong_length`
*   `too_long`
*   `too_short`

示例

```
class User < ApplicationRecord
    validates :bio, length: { maximum: 800, too_long: "%{count} characters is the maximum." }
end 
```

### 在场

这大概是我工具袋里用的最广的帮手了。存在检查字段**不是**空。

```
class User < ApplicationRecord
  validates :name, :email, presence: true
end 
```

您还可以测试模型上存在的关联

```
class Article < ApplicationRecord
  belongs_to :user
  validates :user, presence: true # looks for a user_id field on the article
end 
```

在相反的模型上，您可以用一个名为`inverse_of`
的 catch 做同样的事情

```
class User < ApplicationRecord
  has_many :articles, inverse_of: :user
end 
```

### 唯一性

也许你想在你的应用程序上使用独一无二的用户名？独特性有助于这一点。

```
class User < ApplicationRecord
  validates :username, uniqueness: true, { message: "Username already taken" }
end 
```

这实际上是在`User`表中搜索现有记录，以确定`uniqueness`

您可以更进一步，将`uniqueness`限制为一个`scope`。

```
class User < ApplicationRecord
  validates :plan, uniqueness: { scope: :year, message: "can only update one per year"}
end 
```

## 条件验证

有时你只需要在给定的条件下进行验证。您可以使用类似下面的方法执行条件。

```
class Purchase < ApplicationRecord
  validates :card_number, presence: true, if: :paid_with_card?

  def paid_with_card?
    payment_type == "card"
  end
end 
```

需要对多个条件进行分组？你可以这么做

```
class User < ApplicationRecord
  with_options if: :is_admin? do |admin|
    admin.validates :location, length: { minimum: 10 }
    admin.validates :job_title, presence: true
  end
end 
```

## 自定义方法

有时助手是不够的，你可能需要更多的定制。您可以创建自己的方法，自己进行一些验证。

```
class Invoice < ApplicationRecord
    validate :active_subscriber, on: :create

    def active_customer
        errors.add(:customer_id, "is not active") 
        unless subscriber.active?
    end
end 
```

注意这里我使用`:on`选项来验证这个方法，这个选项让你决定在哪里初始化验证。在这种情况下我选择了`:create`。

您也可以挂钩到`errors`集合，因为如果需要，我必须修改客户端的输出。在这种情况下，我们没有自定义方法上的`:message`。[阅读更多关于错误的信息](https://guides.rubyonrails.org/active_record_validations.html#working-with-validation-errors)。

### 最后的话

希望验证为你打开了一些思路，让你的应用更受约束(以一种好的方式)。使用 Ruby on Rails 及其所有方面允许安全的数据进入您选择的任何数据库。从 CSRF 令牌到数据库级别的验证，有无数种方法可以增强您的应用程序对输入的数据保持冷静。

### 不要脸的塞！

如果你喜欢这篇文章，我在 [YouTube](https://youtube.com/c/webcrunch) 和我的[博客](https://web-crunch.com)上有更多视频。希望收件箱中有更多这样的内容吗？[订阅我的简讯](https://web-crunch.com/subcribe)自动获取。

#### 查看我的课程

 [![https://i2.wp.com/i.imgur.com/KIaaJEB.jpg?ssl=1](img/083250001f26c8017cdaafe107a730f9.png)

T6】](https://hellorails.io)

[![☝](img/fae51c4eecba568e9e0dec504d714268.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--WU8p7Cey--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://s.w.oimg/core/emoji/11/72x72/261d.png) 想从头开始学习 Ruby on Rails？查看我即将推出的课程 [Hello Rails](https://hellorails.io) 。

帖子[了解 Ruby on Rails ActiveRecord 验证](https://web-crunch.com/understanding-ruby-on-rails-activerecord-validations/)最早出现在[的 Web-Crunch](https://web-crunch.com) 上。