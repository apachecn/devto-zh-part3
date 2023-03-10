# Ruby 值对象实例缓存

> 原文：<https://dev.to/databasesponge/ruby-value-object-instance-caching-3h6f>

# TL:DR

具有很少不同值的值对象可以被缓存以节省内存。

# 问题

我们喜欢有价值的对象，但是它们也受到系统的影响。每个实例都使用内存，我们使用的内存越少越好。

一些有值对象自然会有很多不同的值(ISBN、SSN、电子邮件地址)，甚至可能是唯一的，但有些对象只有很少的值。例如，世界上只有几百个国家可以共享您的 100，000 条客户记录，而实际上您可能只使用了其中的一小部分。

如果您想根据国家或国家的属性对您的所有客户进行分类，那么您可能不愿意初始化国家值对象的 100，000 个实例。

# 解

提供一个缓存机制，这样对`Country.new("ES")`的多次调用返回`Country`类的同一个实例。

> 值对象的原则之一是它们是不可变的，所以这很好。

```
class Country
  def self.new(code)
    @instance_cache ||= {}
    @instance_cache[code] = super(code) unless @instance_cache.key?(code)
    @instance_cache.fetch(code)
  end

  def initialize(code)
    @code = code
  end
end 
```

> 其他记忆技巧可能对个人来说更令人愉快。选择一个适合你的。

调用`Country.new("DE")`的原因:

1.  `@instance_cache`如果尚未定义，则设置为空散列。
2.  `@instance_cache`检查“DE”是否已经是一个密钥——如果不是，初始化一个新的`Country`实例并添加到`@instance_cache`中。
3.  从`@instance_cache`读取并隐式返回“DE”的实例。

另外，这也比实例化一个新实例要稍微快一些。

# 演示

考虑两个值对象:

```
class CountryCached
  def self.new(code)
    @instance_cache ||= {}
    @instance_cache[code] = super(code) unless @instance_cache.key?(code)
    @instance_cache.fetch(code)
  end

  def initialize(code)
    @code = code
  end
end

class CountryUncached
  def initialize(code)
    @code = code
  end
end 
```

```
2.4.4 :001 > class CountryCached
2.4.4 :002?>     def self.new(code)
2.4.4 :003?>         @instance_cache ||= {}
2.4.4 :004?>         @instance_cache[code] = super(code) unless @instance_cache.key?(code)
2.4.4 :005?>         @instance_cache.fetch(code)
2.4.4 :006?>       end
2.4.4 :007?>   
2.4.4 :008 >       def initialize(code)
2.4.4 :009?>         @code = code
2.4.4 :010?>       end
2.4.4 :011?>   end
 => :initialize 
2.4.4 :012 > 
2.4.4 :013 >   class CountryUncached
2.4.4 :014?>     def initialize(code)
2.4.4 :015?>         @code = code
2.4.4 :016?>       end
2.4.4 :017?>   end
 => :initialize 
2.4.4 :018 > 
2.4.4 :019 >   
2.4.4 :020 >   CountryCached.new("DE")
 => #<CountryCached:0x00007f8bff83b430 @code="DE"> 
2.4.4 :021 > CountryCached.new("DE")
 => #<CountryCached:0x00007f8bff83b430 @code="DE"> 
2.4.4 :022 > CountryCached.new("FR")
 => #<CountryCached:0x00007f8bff037ce8 @code="FR"> 
2.4.4 :023 > CountryCached.new("DE") == CountryCached.new("DE")
 => true 
2.4.4 :024 > CountryCached.new("DE") == CountryCached.new("FR")
 => false 
2.4.4 :025 > 
2.4.4 :026 >   
2.4.4 :027 >   
2.4.4 :028 >   CountryUncached.new("DE")
 => #<CountryUncached:0x00007f8bfe877a08 @code="DE"> 
2.4.4 :029 > CountryUncached.new("DE")
 => #<CountryUncached:0x00007f8bfe85f958 @code="DE"> 
2.4.4 :030 > CountryUncached.new("FR")
 => #<CountryUncached:0x00007f8bfe854670 @code="FR"> 
2.4.4 :031 > CountryUncached.new("DE") == CountryUncached.new("DE")
 => false 
2.4.4 :032 > CountryUncached.new("DE") == CountryUncached.new("FR")
 => false 
```

对于缓存值，两个“DE”值的对象 id 是相同的:`0x00007f8bff83b430`。对于未缓存的值，它们是不同的:`0x00007f8bfe877a08`和`0x00007f8bfe85f958`。

还要注意一个好处，对象本身现在可以直接比较:

```
2.4.4 :023 > CountryCached.new("DE") == CountryCached.new("DE")
 => true 
```

vs

```
2.4.4 :031 > CountryUncached.new("DE") == CountryUncached.new("DE")
 => false 
```

# 于是总结如下...

通过覆盖类`new`方法缓存值对象的实例:

*   不是很难
*   提高内存使用率
*   使对象可以直接比较

很好。