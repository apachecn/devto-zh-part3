# 集合的 Ruby 值对象

> 原文：<https://dev.to/databasesponge/ruby-value-objects-for-sets-16kj>

# TL；速度三角形定位法(dead reckoning)

有时，除了单个值本身的属性之外，一组值对象还有自己的属性。

# 背景

当我们有一个单一的值，我们想从中推断行为，例如，当我们有国家代码，我们想能够阅读大陆名称，货币，或国歌的基调，那么一个值对象可能是我们正在寻找的。

当我们有一组这样的单个值时，比如一次旅行中要去的国家的列表，那么我们也想向那个国家列表发送消息。

在简单的一端，我们可能有`#count`，或者`#uniq_count`，如果国家可以出现不止一次，`currencies`(期望返回列表中所有国家的货币列表)，等等..

通常在一个集合中涉及到兼容性或完整性的问题，检测这些问题的代码需要一个家。

那个家大概就是一个价值对象。

```
class Countries
  def initialize(*countries)
    @countries = Array.wrap(countries)
  end

  delegate :count,
           :any?,
           :empty?,
           :entries,
           to: :countries

  def currencies
    countries.map(&:currencies)
  end

  def uniq_count
    countries.uniq.count
  end

  def compatible?
    etc
  end

  def closed_loop?
    countries.first == countries.last
  end
end 
```

# 书目举例

考虑一个例子:我们在[协和](https://www.consonance.app)经常使用的代码列表，图书出版的[主题类别](https://ns.editeur.org/thema/en)。出版商为一本书分配多个代码，以通知第三方，如销售代理、其他出版商、分销商、零售商以及最终的潜在买家，这本书是关于什么以及谁可能对它感兴趣。

通过浏览列表，您可以看到主题编码有很大的范围，反映出一些书籍是为专业或学术从业者、儿童、对宠物有一般兴趣的人准备的，还有地点、时间和特殊主题(生日、季节性假期等)的代码。

其中许多可以合并成一本书，我们当然希望它们都是独立有效的。

但是，当您考虑分配给一本书的所有代码时，可能会出现一些进一步的问题:

*   是否使用了暗示其他人也应在场的代码，如果是，这些其他人是否真的在场？
    *   儿童书籍有年龄范围吗？
    *   法律书籍包含地理限制吗？一般来说，每一本专业法律书籍都与一个特定的地理区域有关。
    *   一本关于考古的书也是时间和地点都编码的吗？
*   是否使用了可能互不兼容的代码？
    *   一本关于学术主题的书也是五岁以下儿童感兴趣的吗？
    *   一本编码为一般兴趣的书也编码为专业兴趣吗？

因此，单个代码可能都是有效的，而它们的组合可能会在许多方面出现问题。

这里重要的一点是，这些`complete?`和`valid?`属性属于代码集，而不是单个代码本身，并且可以实现为:

```
class ThemaSet
  def initialize(*set)
    @set = set
  end

  delegate :any?, to: :set

  def errors
    errors_of_incompatibility + errors_of_incompleteness
  end

  private def childrens?
    any?(&:childrens?)
  end

  private def professional_or_scholarly?
    any?(&:professional_or_scholarly?)
  end

  private def historical?
    any(&:historical?)
  end

  private def child_incompatible?
    childrens? && (professional_or_scholarly? || adult_themed?)
  end

  private def missing_time?
    !timed? && historical?
  end

  private def missing_place?
    !placed? && (historical? || legal?)
  end

  private def errors_of_incompatibility
    [].tap do |errors|
      errors << "codes incompatible with child coding are present" if child_incompatible?
    end
  end

  private def errors_of_incompleteness
    [].tap do |errors|
      errors << "codes should be accompanied by time coding"  if missing_time?      
      errors << "codes should be accompanied by place coding" if missing_place?
    end
  end 
```

# 回到现实世界

所以我认为这种模式是相当清楚的。

集值对象检查单个值对象的属性，以产生集作为一个整体的特征。

尽管给出的例子非常具体地针对一个业务领域，但是这种`complete?`对`incomplete?`以及`compatible?`对`incompatible?`的模式，导致了`valid?`的属性和`errors`的列表，是一种常见的模式。

> 这使得它可以使用这里描述的 ValidValidator

在许多情况下，单个对象不是普通的值对象，而是从 ActiveRecord 支持的`has_many`关联中派生出来的。但是如果对象集有自己的属性，那么它仍然是这种技术的候选对象。

```
class Book
  has_many :thema_codes

  def thema_set
    @_thema_set ||= ThemaSet.new(thema_codes)
  end

  delegate :valid?,
           :errors,
           to:     :thema_set,
           prefix: true
end 
```

# 进一步的功能

对此的一个变体可以用来回答这样一个问题，“对一个集合的特定改变有什么影响？”。

给定一个当前集合和它的一个候选成员，您可以将原始成员与原始+候选值进行比较，并询问它“我是否通过进行这种更改(例如引入(或解决)了任何错误)而更改了集合的属性？”

这一点的实现相当清楚:

```
class SetComparator
  def initialize(set_object, new_value)
    @original_set = set_object
    @new_set      = set_object.class.new(set_object.entries << new_value)
  end

  def errors_added
    new_set.errors - original_set.errors
  end

  def errors_removed
    original_set.errors - new_set.errors
  end
end 
```