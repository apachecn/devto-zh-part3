# 使用 method_missing 沿 Ruby 祖先链向上

> 原文：<https://dev.to/appsignal/up-the-ruby-ancestor-chain-with-methodmissing-35f>

抓紧你的随身行李，因为今天我们将沿着祖先链一路旅行。我们将跟踪一个方法调用，看看它是如何沿着链向上的，以及如果方法丢失会发生什么。因为我们喜欢玩火，我们不会就此止步，而是继续~~玩火~~压倒`BasicObject#method_missing`。如果你注意的话，我们可能还会在实际例子中用到它。但不能保证。我们走吧！

## 祖先链

让我们从 Ruby 中祖先链的基本规则开始:

*   Ruby 只支持单一继承
*   它还允许一个对象包含一组模块

在 Ruby 中，祖先链由给定类的所有继承类和模块的遍历组成。

让我们看一个例子，向您展示在 Ruby 中如何处理祖先链。

```
module Auth end

module Session end

module Iterable end

class Collection
  prepend Iterable
end

class Users < Collection
  prepend Session
  include Auth
end

p Users.ancestors 
```

产生:

```
[
  Session, Users, Auth,        # Users
  Iterable, Collection,        # Collection
  Object, Kernel, BasicObject  # Ruby Object Model
] 
```

首先，我们调用`ancestors`类方法来访问给定类的祖先链。

我们可以看到对`Users.ancestors`的调用返回了一组类和模块，它们依次包含:

*   `Users`的前置模块
*   `Users`类
*   `Users`类包含模块
*   `Collection`类的前置模块——作为用户的直接父类
*   `Collection`类
*   `Collection`类包含模块—无
*   `Object`类——任何类的默认继承
*   `Kernel`模块——包含在`Object`和保持核心方法中
*   `BasicObject`类 Ruby 中的根类

因此，给定遍历的类或模块的出现顺序总是如下:

*   前置模块
*   类或模块
*   其包含的模块

当在对象或类上调用一个方法时，祖先链主要由 Ruby 遍历。

## 方法查找路径

当一个消息被发送时，Ruby 遍历消息接收者的祖先链，并检查他们中是否有人对给定的消息做出响应。

如果祖先链的给定类或模块响应消息，则执行与该消息相关联的方法，并停止祖先链遍历。

```
class Collection < Array
end

Collection.ancestors # => [Collection, Array, Enumerable, Object, Kernel, BasicObject]

collection = Collection.new([:a, :b, :c])

collection.each_with_index # => :a 
```

这里，`collection.each_with_index`消息由可枚举模块接收。然后为该消息调用`Enumerable#each_with_index`方法。

这里，当调用`collection.each_with_index`时，Ruby 检查:

*   收藏回复`each_with_index message` = > **否**
*   阵列响应`each_with_index message` = > **否**
*   可枚举响应`each_with_index message` = > **是**

因此，从这里开始，Ruby 停止祖先链遍历，并调用与该消息相关联的方法。在我们的例子中，`Enumerable#each_with_index`方法。

在 Ruby 中，这种机制被称为*方法查找路径*。

现在，如果组成给定接收者的祖先链的类和模块都不响应消息，会发生什么？

## `BasicObject#method_missing`

玩够了！让我们以开发人员的方式打破常规:抛出异常。我们将实现一个`Collection`类，并在它的一个实例上调用一个未知方法。

```
class Collection
end

c = Collection.new
c.search('item1') # => NoMethodError: undefined method `search` for #<Collection:0x123456890> 
```

这里，`Collection`类没有实现`search`方法。于是一个`NoMethodError`被提了出来。但是这种错误产生的原因是什么呢？

该错误在`BasicObject#method_missing`方法中出现。当*方法查找路径*没有找到任何对应于给定消息的方法时，这个方法被调用。

行...但是这个方法只引发了一个`NoMethodError`。因此，如果能够在我们的`Collection`类的上下文中覆盖该方法，那就太好了。

## 压倒了`BasicObject#method_missing`的方法

你猜怎么着？覆盖`method_missing`完全没问题，因为该方法也受*方法查找路径*的机制约束。与普通方法的唯一区别是，我们确信这个方法至少会被*方法查找路径*找到一次。

事实上，`BasicObject class`——Ruby 中任何类的根类——定义了这个方法的最小版本。经典的红宝石魔法，不是吗？

所以让我们在`Collection class` :
中覆盖这个方法

```
class Collection
  def initialize
    @collection = {}
  end

  def method_missing(method_id, *args)
    if method_id[-1] == '='
      key = method_id[0..-2]
      @collection[key.to_sym] = args.first
    else
      @collection[method_id]
    end
  end
end

collection = Collection.new
collection.obj1 = 'value1'
collection.obj2 = 'value2'

collection.obj1 # => 'value1'
collection.obj2 # => 'value2' 
```

这里，`Collection#method_missing`充当了`@collection`实例变量的委托者。实际上，这是 Ruby 处理对象委托的大致方式——c . f:`delegate`库。

如果缺少的方法是 setter 方法(`collection.obj1 = 'value1'`)，那么方法名(`:obj1`)被用作键，参数(`'value1'`)被用作`@collection`散列条目(`@collection[:obj1] = 'value1'`)的值。

## 一个 HTML 标签生成器

既然我们知道了`method_missing`方法是如何在幕后工作的，让我们实现一个可重现的用例。

这里，目标是定义下面的 DSL:

```
HTML.p    'hello world'             # => <p>hello world</p>
HTML.div  'hello world'             # => <div>hello world</div>
HTML.h1   'hello world'             # => <h1>hello world</h1>
HTML.h2   'hello world'             # => <h2>hello world</h2>
HTML.span 'hello world'             # => <span>hello world</span>
HTML.p    "hello #{HTML.b 'world'}" # => <p>hello <b>world</b></p> 
```

为此，我们将实现`HTML.method_missing`方法，以避免为每个 HTML 标签定义一个方法。

首先，我们定义一个`HTML`模块。然后我们在这个模块中定义了一个`method_missing`类方法:

```
module HTML
  def HTML.method_missing(method_id, *args, &block)
    "<#{method_id}>#{args.first}</#{method_id}>"
  end
end 
```

例如，我们的方法将简单地使用缺少的`method_id` — `:div`为对`HTML.div`的调用构建一个 HTML 标签。

注意，类方法也受制于方法查找路径。

我们可以通过以下方式增强我们的 HTML 标记生成器:

*   使用 block 参数处理嵌套标签
*   处理单个标签— `<br/>`例如

但是请注意，只需几行代码，我们就可以生成大量的 HTML 标签。

所以，概括一下:

`method_missing`是创建 DSL 的一个很好的切入点，其中大多数命令将共享一组已识别的模式。

## 结论

我们沿着 Ruby 的祖先链一路向上，深入到了`BasicObject#method_missing`。`BasicObject#method_missing`是*红宝石挂钩方法*的一部分。它用于在对象生命周期的特定时刻与对象进行交互。像其他*红宝石挂钩方法*一样，这个挂钩方法必须小心使用。我们所说的小心，是指它永远不应该修改 *Ruby 对象模型*的行为——除非你在玩它或者在它上面写博客；-)

给你！

客座作者 Mehdi Farsi 是[www.rubycademy.com](https://www.rubycademy.com)的创始人，该网站即将推出，届时将提供学习 Ruby 和 Ruby on Rails 的酷课程。