# 一个简单的 9 行代码的链表

> 原文：<https://dev.to/chenge/a-simple-linked-list-by-9-lines-of-code-4846>

今天从 AppSignal 看了 [Ruby 的神奇可枚举模块](https://blog.appsignal.com/2018/05/29/ruby-magic-enumerable-and-enumerator.html)。它清楚地解释了主题。并且给出了链表的简短代码，我喜欢。

```
class LinkedList
  def initialize(head, tail = nil)
    @head, @tail = head, tail
  end

  def <<(item)
    LinkedList.new(item, self)
  end

  def inspect
    [@head, @tail].inspect
  end
end 
```

```
irb> LinkedList.new(73) << 12 << 42
=> [42, [12, [73, nil]]] 
```

然后用一个 each 方法和一个 include，你可以使它可枚举。

```
class LinkedList
  include Enumerable

  def initialize(head, tail = nil)
    @head, @tail = head, tail
  end

  def <<(item)
    LinkedList.new(item, self)
  end

  def inspect
    [@head, @tail].inspect
  end

  def each(&block)
    block.call(@head)
    @tail.each(&block) if @tail
  end
end 
```

```
irb> list = LinkedList.new(73) << 12 << 42
=> [42, [12, [73, nil]]]
irb> list.count
=> 3
irb> list.max
=> 73
irb> list.map { |item| item * item }
=> [1764, 144, 5329]
irb> list.select(&:even?)
=> [42, 12] 
```

我喜欢逐渐解释代码的方式。你呢？