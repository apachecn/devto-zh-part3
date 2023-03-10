# 用类包装基于 JSON 的 ActiveRecord 属性

> 原文：<https://dev.to/evilmartians/wrapping-json-based-activerecord-attributes-with-classes-4apf>

*贴在[火星编年史](https://evilmartians.com/chronicles/wrapping-json-based-active-record-attributes-with-classes)中*

您有没有尝试过通过使用基于 JSON 的列来使您的数据库结构稍微反规范化？不言而喻，巨大的~~权力~~灵活性伴随着巨大的责任，但是在某些情况下，这种方法做得很好，并且简化了很多事情。

例如，假设您正在为另一个电子商务网站构建一个功能，该功能允许用户通过一组规则过滤产品，并在每天晚上自动订购。显然，我们希望指定要订购的产品数量，但是我们如何表示过滤规则(例如，最低/最高价格、出版日期等)？我们可以为每条规则创建一个专栏，但是 JSON 专栏看起来更有前途。我们不打算按这些列中的任何一个进行过滤，但是，将来我们很有可能会添加更多的规则，JSON 列允许我们避免每次都迁移我们的数据库。

这就是我们通常将 JSONB 列添加到 PostgreSQL 表中的方式:

```
create_table :auto_buyers do |t|
  t.integer :count, null: false
  t.jsonb :rules, null: false, default: {}
end 
```

Enter fullscreen mode Exit fullscreen mode

这个属性只是一个散列:

```
buyer = AutoBuyer.create(count: 1, rules: { max_price: 10 })

if item.price < buyer.rules["max_price"]
  # buy item...
end 
```

Enter fullscreen mode Exit fullscreen mode

如果我们想要更复杂的东西，比如验证这样的字段，该怎么办？这个看起来有点乱:

```
class AutoBuyer < ApplicationRecord
  validate do
    if rules["max_price"] && rules["min_price"] && rules["min_price"] > rules["max_price"]
      errors.add(:max_price, "should be greater then min price")
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

假设 ActiveRecord 模型有自己的行为，我们用这样的验证增加了更多的复杂性——在这种情况下，将 JSON 属性周围的逻辑移到单独的类中可能是有意义的，而且使用属性而不是散列也是很好的。[属性 API](https://api.rubyonrails.org/classes/ActiveRecord/Attributes/ClassMethods.html) 可以救我们！

然而，文档非常混乱，很难从第一次尝试就把事情做好。让我们试着从定义一个类开始，这个类将我们的散列表示为一个对象:

```
class AutoBuyerRules
  include ActiveModel::Model
  include ActiveModel::Attributes

  attribute :min_price, :integer
  attribute :max_price, :integer
end 
```

Enter fullscreen mode Exit fullscreen mode

之后，我们需要定义属性类来将散列打包到这个类:

```
class AutoBuyerRulesType < ActiveModel::Type::Value
  def type
    :jsonb
  end

  # rubocop:disable Style/RescueModifier
  def cast_value(value)
    case value
    when String
      decoded = ActiveSupport::JSON.decode(value) rescue nil
      AutoBuyerRules.new(decoded) unless decoded.nil?
    when Hash
      AutoBuyerRules.new(value)
    when AutoBuyerRules
      value
    end
  end
  # rubocop:enable Style/RescueModifier

  def serialize(value)
    case value
    when Hash, AutoBuyerRules
      ActiveSupport::JSON.encode(value)
    else
      super
    end
  end

  def changed_in_place?(raw_old_value, new_value)
    cast_value(raw_old_value) != new_value
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

最后，让我们告诉我们的 ActiveRecord 模型使用这个新属性并试用它:

```
class AutoBuyer < ApplicationRecord
  attribute :rules, AutoBuyerRulesType.new
end

buyer = AutoBuyer.create(count: 1, rules: { max_price: 10 })

if item.price < buyer.rules.max_price
  # buy item...
end 
```

Enter fullscreen mode Exit fullscreen mode

它看起来更好，但是我们应该每次都使用这么多代码来获得这种行为吗？不尽然，有请 [store_model](https://github.com/DmitryTsepelev/store_model) gem！

长话短说(你可以在 *README* 中找到龙商店)让我们来看看同一个例子:

```
class AutoBuyerRules
  include StoreModel::Model

  attribute :min_price, :integer
  attribute :max_price, :integer

  validate :min_price, presence: true
end

class AutoBuyer < ApplicationRecord
  attribute :rules, AutoBuyerRules.to_type

  validate :rules, store_model: { merge_errors: true }
end

buyer = AutoBuyer.new
puts buyer.valid? # => false
puts buyer.errors.messages # => { min_price: ["can't be blank"] } 
```

Enter fullscreen mode Exit fullscreen mode

就是这样，您不需要包含大量的模块或手动定义任何自定义类型！它还提供了验证支持—您可以验证商店模型字段，然后将这些错误合并到父记录中(或者定义任何自定义策略)。

让我们回顾一下:

*   JSON 列适用于经常变化的数据
*   您可以将它们作为散列来处理，但这可能会很冗长
*   你可以使用属性 API 用对象包装它们或者使用 [store_model](https://github.com/DmitryTsepelev/store_model) gem
*   如果您想做同样的事情，但是在 ActiveRecord 模型本身上定义了属性，那么可以考虑使用 [store_attribute](https://github.com/palkan/store_attribute) 或 [jsonb_accessor](https://github.com/madeintandem/jsonb_accessor)

* * *

阅读更多关于 https://evilmartians.com/chronicles 的文章！