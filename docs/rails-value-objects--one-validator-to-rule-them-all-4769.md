# Rails 值对象——一个验证器来管理它们

> 原文：<https://dev.to/databasesponge/rails-value-objects--one-validator-to-rule-them-all-4769>

# TL；速度三角形定位法(dead reckoning)

所有值对象的验证可以通过一个小的自定义验证器类来实现。

# 问题

当你在 Ruby 中定义一个值对象时，特别是在 Rails 中，这是其功能的一个自然部分，它应该区分有效和无效的初始化输入。

所以值对象自然会有一个`#valid?`方法，也可能有一个`#errors`方法。

既然 Rails 让您能够对属性应用验证，那么您是否还需要为每个值对象定制一个验证器呢？有没有一种简洁有效的方法来连接 Rails 验证器和你的值对象？

# 背景

一个值对象一般能告诉我们它是否有效。事实上，这可能是创建值对象的主要原因，这样它可以封装所有的验证逻辑，并适当地响应`valid?`。`@email.valid?`、`@zip_code.valid?`、`@isbn.valid?`等。

例如，考虑用两个字母的 ISO 国家代码(如“GB”或“FR ”)初始化的国家对象。它可以引用一个有效的代码列表来定义有效的国家代码列表和各种属性:它的名称、它当前是否存在、可能是一个首都城市的名称。

因此，当输入被接受时，我们需要根据代码列表检查输入值是否有效(也许我们会添加一些东西来清理任何容易纠正的问题，如小写输入或空格)。

因此，我们可能相当期待`Country.new("XA").valid?`会用`false`来响应，`Country.new("x1").errors`应该用`["Country codes must be a two-letter ISO 3166-1 alpha-2 code"]`来响应。你是对的，我确实写出了世界上最好最精确的错误消息。

> 当然，在许多情况下，您已经对前端逻辑中的输入进行了净化，对于来自一个小值列表的代码或者对于一个简单的算法验证，这总是会有所帮助。对于更复杂的情况(例如，ISBN 是否在我们认为由 ISBN International 分配的范围内),我们可以将逻辑委托给服务器，并依赖值对象进行验证。

我们可能想通过在模型类中使用标准的 Rails 验证来保护我们的数据库免受无效值的影响:

```
 validates :country, erm what goes here? 
```

# 解:有效的验证器

如果我们所有的值对象都定义了相同的响应，那么就可以用一个定制的验证器以同样的方式对待它们。

这里有一个类，我们可以用它来验证任何可以枚举其错误的对象:

```
class ValidValidator < ActiveModel::EachValidator
  def validate_each(record, attribute, value)
    value.errors.each { |error| record.errors.add(attribute, error) }
  end
end 
```

我想我们都同意这是一个富有想象力的准确的名字，尽管我也接受`NoErrorsValidator`。

从值对象中读取的所有错误都被添加到记录错误中，并且每个实现了`errors`方法的值对象都可以用这个相同的类进行验证。

所以你可以这样使用它:

```
 validates :country, valid: true 
```

...有了所有常用的选项...

```
 validates :country, valid: true, allow_nil: true 
```

如果值对象用`true`响应`nil?`，那么`allow_nil: true`会导致验证被跳过，所以要确保你的值对象对此做出响应——参见本系列中的 [Ruby nil 值对象](https://dev.to/databasesponge/ruby-nil-value-objects-3bno)。

现在我们有了它——一个主动模型验证器来管理它们。

```
 validates :country,             valid: true, allow_nil: true
  validates :construction_method, valid: true, allow_nil: false
  validates :height,              valid: true, allow_nil: false
  validates :occupancy_class,     valid: true, allow_nil: false 
```

如果我们所说的“所有”是指“价值对象”，那就是。