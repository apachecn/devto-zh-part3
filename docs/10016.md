# FactoryBot。查找或创建依据

> 原文：<https://dev.to/jooeycheng/factorybot-findorcreateby-3h8k>

### **TL；博士**

```
factory :country do
  to_create do |instance|
    instance.id = Country.find_or_create_by(name: instance.name, code: instance.code).id
    instance.reload
  end
end 
```

我是如何想出这个解决方案的？见下文。

似乎没有清晰的文档来说明如何做这件事。所以，这是我尝试的。

在我们开始之前，让我们准备我们的测试环境，我们将使用一个`Country`模型，带有属性`name`和`code` :

```
# models/country.rb

class Country
  validates :name, presence: true
  validates :code, presence: true, length: { is: 2 }, uniqueness: { case_sensitive: false } # ISO-3166-ALPHA-2
end 
```

```
# spec/factories/countries.rb

FactoryBot.define do
  factory :country do
    name { 'Canada' }
    code { 'CA' }
  end
end 
```

现在，我们都是优秀的工程师，优秀的工程师编写单元测试。这将使验证结果更加容易。RSpec 。

### 企图 1。覆盖`initialize_with`块

这是 StackOverflow 上发布的关于[问题](https://stackoverflow.com/a/11799674/3956879)的投票最多的答案。我们可以用
覆盖工厂的默认初始化行为

```
factory :country do
  initialize_with { Country.find_or_create_by(name: name, code: code) }
end 
```

虽然解决方案确实有效，但它*打破了工厂*。调用`build(:country)`将会在数据库中创建一个不存在的记录。

查看评论部分，我们看到一个[建议](https://stackoverflow.com/questions/7145256/find-or-create-record-through-factory-girl-association#comment51187522_11799674)使用`.find_or_initialize_by` :

```
factory :country do
  initialize_with { Country.find_or_initialize_by(name: name, code: code) }
end 
```

这部分恢复了`build`的行为，因为它不再创建记录。但是，如果找到的话，它仍然会返回一个持久化的记录。

这种方法打破了`build`的原始行为，即仅*初始化记录*，而不返回持久化的记录。访问数据库来执行一个`Country.find_by`也会有性能代价。理想的解决方案是只访问`create`上的数据库来创建记录。我们应该不需要在`build`上读写数据库。

[RSpec 结果](https://gist.github.com/jooeycheng/a09c4701d1dfb68bdc9d3cc6028c9e24#attempt-1)

### 企图二。覆盖`to_create`块(v1)

深入研究 FactoryBot 文档，我们看到一个关于 **[自定义方法来持久化对象](https://www.rubydoc.info/gems/factory_bot/file/GETTING_STARTED.md#Custom_Methods_to_Persist_Objects)** 的部分。

> 默认情况下，创建一条记录将调用实例上的`save!`；由于这可能并不总是理想的，您可以通过在工厂中定义`to_create`来覆盖这种行为

来试试吧:

```
factory :country do
  to_create { |instance| Country.find_or_create_by(name: instance.name, code: instance.code) }
end 
```

[RSpec 结果](https://gist.github.com/jooeycheng/a09c4701d1dfb68bdc9d3cc6028c9e24#attempt-2)

从结果中，我们看到`build`和`create`按预期工作。
但是，`create`似乎返回的是*工厂预保存实例*而不是*创建的实例*，这就解释了为什么`country.id`是`nil`，而`Country.count`增加了。🤔

好的，我们快到了。我们所需要的就是在调用`.find_or_create_by`之后将属性设置回它自己的实例，这样它就可以假设找到或创建的对象的身份。

### 企图三。覆盖`to_create`块(v2)

现在，我们知道`to_create { |instance| ... }`中的`instance`是模型对象本身，它是一个 ActiveRecord 模型。因此，我们应该能够像这样排序强制更新属性:

```
factory :country do
  to_create { |instance| instance.attributes = Country.find_or_create_by(name: instance.name, code: instance.code).attributes }
end 
```

[RSpec 结果](https://gist.github.com/jooeycheng/a09c4701d1dfb68bdc9d3cc6028c9e24#attempt-31)

哦💩成功了。但是最后一个障碍- `country.persisted?`返回 false。解决这个问题最简单的方法是通过`.reload`-调用实例:

```
factory :country do
  to_create do |instance|
    instance.attributes = Country.find_or_create_by(name: instance.name, code: instance.code).attributes
    instance.reload
  end
end 
```

耶，全绿了！🚦

[RSpec 结果](https://gist.github.com/jooeycheng/a09c4701d1dfb68bdc9d3cc6028c9e24#attempt-32)

但是，`#reload`并不是理想的解决方案，因为它执行了一个不必要的[数据库读取](https://apidock.com/rails/v4.2.7/ActiveRecord/Persistence/reload)，而我们想要的只是`#persisted?`正确地运行。查看 [Rails 文档](https://apidock.com/rails/ActiveRecord/Persistence/persisted%3F)，我们发现`#persisted?`是`#new_record?`的逆，它的值来自实例变量`@new_record`。

这意味着，我们有可能这样做:

```
factory :country do
  to_create do |instance|
    instance.attributes = Country.find_or_create_by(name: instance.name, code: instance.code).attributes
    instance.instance_variable_set('@new_record', false)
  end
end 
```

该死的成功了😎

[RSpec 结果](https://gist.github.com/jooeycheng/a09c4701d1dfb68bdc9d3cc6028c9e24#attempt-33)

现在，我同意这是 *hacky* 。为了安全起见，也许应该坚持使用`.reload`。如果这与你无关，也许可以完全忽略`.reload`。

*封面图片由 [Freepik](https://www.freepik.com/free-photos-vectors/abstract) 拍摄。*