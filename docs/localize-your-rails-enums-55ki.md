# 本地化您的 Rails 枚举

> 原文：<https://dev.to/jkostolansky/localize-your-rails-enums-55ki>

当 Rails 4.1 发布时，它带来了一系列新特性。其中之一是活动记录枚举。

```
class Conversation < ApplicationRecord
  enum status: [:active, :archived]
end

conversation = Conversation.create(status: :active)
conversation.status # => "active" 
```

Enter fullscreen mode Exit fullscreen mode

这是一个方便的特性。但是，如果您尝试本地化您的 Rails 应用程序，例如对于一些选择框，或者只是为了显示当前值，您将很快意识到没有内置的功能来翻译枚举值。

从 Rails 5 开始，所有模型都将继承 ApplicationRecord。我们可以用它来创造我们的通用解决方案。

```
class ApplicationRecord < ActiveRecord::Base
  self.abstract_class = true

  def human_enum_name(enum_name, enum_value)
    enum_i18n_key = enum_name.to_s.pluralize
    I18n.t("activerecord.attributes.#{model_name.i18n_key}.#{enum_i18n_key}.#{enum_value}")
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这个小方法允许我们以如下方式使用语言环境文件:

```
en:
  activerecord:
    attributes:
      conversation:
        statuses:
          active: Active conversation
          archived: Archived conversation 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以简单地使用类方法`human_enum_name` :

```
Conversation.human_enum_name(:status, :active) # => "Active conversation" 
```

Enter fullscreen mode Exit fullscreen mode

并附有实例:

```
conversation = Conversation.create(status: :active)
Conversation.human_enum_name(:status, conversation.status) # => "Active conversation" 
```

Enter fullscreen mode Exit fullscreen mode

实际上，我经常使用这种方法，所以我构建了一个名为 [human_enum_name](https://github.com/jkostolansky/human_enum_name) 的小程序。放心用吧！

如果你想以一种聪明的方式管理你的 YAML 翻译文件，看看我的项目[LocaleData.com](https://www.localedata.com)。

* * *

原文:[本地化你的 Rails 枚举](https://www.kostolansky.sk/posts/localize-rails-enums/)