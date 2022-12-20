# 可读代码:堆叠令牌

> 原文：<https://dev.to/burdettelamar/readable-code-stacking-tokens-4f73>

如果一个令牌集合*作为一个列表服务*，考虑让*看起来*像一个列表。

作为一个例子，我将使用 Ruby 的类方法`attr_accessor`，它接受任意数量的参数。

未堆叠:

```
 attr_accessor :arity, :name, :original_name, :owner, :parameters, :receiver, :source_location, :super_method 
```

堆叠:

```
 attr_accessor \
    :arity,
    :name,
    :original_name,
    :owner,
    :parameters,
    :receiver,
    :source_location,
    :super_method 
```

可读性更强，不是吗？

注意:对于接受许多参数的用户定义方法，堆栈可能不是最佳选择。请考虑这样的方法是否应该接受对象。

尽管如此:

```
 def initialize(
    name:,
    address:,
    phone:,
    date_of_birth:,
    debit_card_number:,
    debit_card_pin:,
  )
  end 
```

另一个值得堆叠的符号:

```
 %w/
    apple
    banana
    orange
    peach
    persimmon
    pineapple
    scuppernong
    tomato
  / 
```

当然，几乎每个人都已经这么做了:

```
 {
      :cabbages => 0,
      :cucumbers => 2,
      :peas => 200,
      :potatoes => 4,
      :radishes => 8,
      :sweet_potatoes => 0,
  } 
```

其他？在 Ruby？还是用另一种语言？