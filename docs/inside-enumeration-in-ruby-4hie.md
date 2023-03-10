# Ruby 中的内部枚举

> 原文：<https://dev.to/appsignal/inside-enumeration-in-ruby-4hie>

欢迎回到 Ruby Magic 的另一个版本！一年前，我们了解了 Ruby 的[模块`Enumerable`T2，它提供了处理数组、范围和散列等可枚举对象时使用的方法。](https://blog.appsignal.com/2018/05/29/ruby-magic-enumerable-and-enumerator.html)

当时，我们创建了一个`LinkedList`类来展示如何通过在对象上实现`#each`方法来使对象可枚举。通过包含`Enumerable`模块，我们能够在任何链表上调用像`#count`、`#map`和`#select`这样的方法，而不必自己实现它们。

我们已经学习了如何使用可枚举数，但是它们是如何工作的呢？Ruby 中可枚举数的魔力部分来自于它们的内部实现，这些实现都基于单个`#each`方法，甚至允许链接枚举数。

今天，我们将学习如何实现`Enumerable`类中的方法，以及`Enumerator`对象如何允许链接枚举方法。

正如您已经习惯的那样，我们将通过实现我们自己版本的`Enumerable`模块和`Enumerator`类来深入研究。所以，戴上你的超工程头盔，我们走吧！

## 链表

在我们开始之前，让我们从之前编写的链表类的新版本开始。

```
class LinkedList
  def initialize(head = nil, *rest)
    @head = head

    if rest.first.is_a?(LinkedList)
      @tail = rest.first
    elsif rest.any?
      @tail = LinkedList.new(*rest)
    end
  end

  def <<(head)
    @head ? LinkedList.new(head, self) : LinkedList.new(head)
  end

  def inspect
    [@head, @tail].compact
  end

  def each(&block)
    yield @head if @head
    @tail.each(&block) if @tail
  end
end 
```

与以前的版本不同，这个实现允许创建空列表，以及包含两个以上项目的列表。这个版本还允许在初始化另一个时传递一个链表作为尾部。

```
irb> LinkedList.new
=> []
irb> LinkedList.new(1)
=> [1]
irb> LinkedList.new(1, 2)
=> [1,[2]]
irb> LinkedList.new(1, 2, 3)
=> [1,[2,[3]]]
irb> LinkedList.new(1, LinkedList.new(2, 3))
=> [1,[2,[3]]]
irb> LinkedList.new(1, 2, LinkedList.new(3))
=> [1,[2,[3]]] 
```

以前，我们的`LinkedLIst`级包括[的`Enumerable`模块](http://ruby-doc.org/core-2.6.3/Enumerable.html)。当使用`Enumerable`的方法之一映射一个对象时，结果存储在一个数组中。这一次，我们将实现我们自己的版本，以确保我们的方法返回新的链表。

## 可枚举的方法

Ruby 的`Enumerable`模块带有类似`#map`、`#count`和`#select`的枚举方法。通过实现`#each`方法并在我们的类中包含`Enumerable`模块，我们将能够直接在我们的链表中使用这些方法。

相反，我们将实现`DIYEnumerable`并导入它而不是 Ruby 的版本。这不是你通常会做的事情，但它会让我们清楚地了解枚举在内部是如何工作的。

先说`#count`。`Enumerable`类中的每个可导入方法都使用我们在`LinkedList`类中实现的`#each`方法来遍历对象以计算它们的结果。

```
module DIYEnumerable
  def count
    result = 0
    each { |element| result += 1 }
    result
  end
end 
```

在这个例子中，我们在一个新的`DIYEnumerable`模块上实现了`#count`方法，这个模块将包含在我们的链表中。它从零开始计数，并调用`#each`方法为每个循环的计数加 1。在循环所有元素后，该方法返回结果计数器。

```
module DIYEnumerable
  # ...

  def map
    result = LinkedList.new
    each { |element| result = result << yield(element) }
    result
  end
end 
```

`#map`方法的实现类似。它没有使用计数器，而是使用一个累加器，从一个空列表开始。我们将遍历列表中的所有元素，并在每个元素上生成传递的块。每个产量的结果都被添加到累加器列表中。

方法在循环输入列表中的所有元素后返回累加器。

```
class LinkedList
  include DIYEnumerable

  #...
end 
```

在我们的`LinkedList`中包含了`DIYEnumerable`之后，我们可以测试我们新添加的`#count`和`#map`方法。

```
irb> list = LinkedList.new(73, 12, 42)
=> [73, [12, [42]]]
irb> list.count
=> 3
irb> list.map { |element| element * 10 }
=> [420, [120, [730]]] 
```

两种方法都管用！`#count`方法正确地对列表中的项目进行计数，`#map`方法对每个项目运行一个块并返回一个更新的列表。

## 反转列表

然而，`#map`方法似乎已经恢复了列表。这是可以理解的，因为我们的链表类上的`#<<`方法将项目预先添加到列表中，而不是追加它们，这是链表的递归特性的一个特征。

对于必须保持列表顺序的情况，我们需要一种在映射列表时反转列表的方法。Ruby 实现了`Enumerable#reverse_each`，反向遍历一个对象。那听起来像是解决我们问题的一个极好的方法。遗憾的是，我们不能使用这样的方法，因为我们的列表是嵌套的。我们不知道这个列表有多长，直到我们把它全部循环一遍。

我们将添加一个版本的`#reverse_each`来完成这两个步骤，而不是反向运行列表上的块。它首先遍历列表，通过创建一个新列表来反转它。之后，它在反向列表上运行该块。

```
module DIYEnumerable
  # ...

  def reverse_each(&block)
    list = LinkedList.new
    each { |element| list = list << element }
    list.each(&block)
  end

  def map
    result = LinkedList.new
    reverse_each { |element| result = result << yield(element) }
    result
  end
end 
```

现在，我们将在我们的`#map`方法中使用`#reverse_each`，以确保它以正确的顺序返回。

```
irb> list = LinkedList.new(73, 12, 42)
=> [73, [12, [42]]]
irb> list.map { |element| element * 10 }
=> [730, [120, [420]]] 
```

有用！每当我们在一个链表上调用我们的`#map`方法时，我们将得到一个新的链表，其顺序与原始链表相同。

## 用枚举器链接枚举

通过在链表类上实现的`#each`方法和包含的`DIYEnumerator`，我们现在可以双向循环并映射链表。

```
irb> list.each { |x| p x }
73
12
42
irb> list.reverse_each { |x| p x }
42
12
73
irb> list.reverse_each.map { |x| x * 10 }
=> [420, [120, [730]]] 
```

然而，如果我们需要*将*反向映射到一个列表上呢？因为我们现在在映射之前反转列表，所以它总是以与原始列表相同的顺序返回。我们已经实现了`#reverse_each`和`#map`，所以我们应该能够将它们链接在一起，以便能够向后映射。幸运的是，Ruby 的`Enumerator`类可以帮助解决这个问题。

上一次，如果调用`LinkedList#each`方法时没有阻塞，我们确保调用`Kernel#to_enum`。这允许通过返回一个`Enumerator`对象来链接可枚举的方法。为了弄清楚[的`Enumerator`类](http://ruby-doc.org/core-2.6.3/Enumerator.html)是如何工作的，我们将实现我们自己的版本。

```
class DIYEnumerator
  include DIYEnumerable

  def initialize(object, method)
    @object = object
    @method = method
  end

  def each(&block)
    @object.send(@method, &block)
  end
end 
```

像 Ruby 的`Enumerator`一样，我们的枚举器类是一个对象方法的包装器。通过向上冒泡到包装的对象，我们可以链接枚举方法。

这是因为`DIYEnumerator`实例本身是可枚举的。它通过调用被包装的对象来实现`#each`，并且包含了`DIYEnumerable`模块，因此所有可枚举的方法都可以在它上面被调用。

如果没有块传递给`LinkedList#each`方法，我们将返回我们的`DIYEnumerator`类的一个实例。

```
class LinkedList
  # ...

  def each(&block)
    if block_given?
      yield @head
      @tail.each(&block) if @tail
    else
      DIYEnumerator.new(self, :each)
    end
  end
end 
```

使用我们自己的枚举器，我们现在可以链接枚举以获得原始顺序的结果，而不必将空块传递给`#reverse_each`方法调用。

```
irb> list = LinkedList.new(73, 12, 42)
=> [73, [12, [42]]]
irb> list.map { |element| element * 10 }
=> [420, [120, [730]]] 
```

## 急切而懒惰的枚举

现在，我们对`Enumerable`模块和`Enumerator`类的实现的研究到此结束。我们已经了解了一些可枚举方法是如何工作的，以及枚举器如何通过包装可枚举对象来帮助链接枚举。

不过，我们的方法存在一些问题。就其本质而言，枚举是*急切的*，这意味着一旦对它调用一个可枚举的方法，它就在列表上循环。虽然这在大多数情况下没问题，但是反向映射一个列表会将列表反转两次，这应该是不必要的。

为了减少循环次数，我们可以使用 [`Enumerator::Lazy`](http://ruby-doc.org/core-2.6.3/Enumerator/Lazy.html) 将循环延迟到最后一刻，并让重复列表反向抵消。

不过，我们得把这一点留到下一集再说。不想错过这个，并进一步探索 Ruby 神奇的内部工作原理？订阅 [Ruby Magic 电子邮件简讯](https://blog.appsignal.com/ruby-magic)，让新文章一发表就发送到你的收件箱。