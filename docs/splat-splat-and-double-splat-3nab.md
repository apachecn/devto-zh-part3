# 探索 Ruby:啪嗒，啪嗒，双啪嗒！

> 原文：<https://dev.to/dabrorius/splat-splat-and-double-splat-3nab>

Ruby 定义方法的方式非常灵活，可以接受任意数量的参数。我们经常遇到 splat 操作符，所以我想花点时间更深入地探索它们。

让我们从一个可能引起你探究这个话题的问题开始。
如果我们有下面的方法调用:

```
my_method('Go', 'Now', name: 'John Goblikon') 
```

当方法以下列方式定义时，什么将被指定为参数？

```
def my_method(*args) 
```

```
def my_method(**args) 
```

```
def my_method(*args1, **args2) 
```

让我们来了解一下！

## Splat 运算符(*)

Splat 操作符允许我们将一个`Array`转换成一个参数列表，反之亦然。

让我们看一个示例函数，它接受任意数量的参数。

```
def things_i_like(*things)
  things.each { |thing| puts "I like #{thing}" }
end 
```

我们可以这样调用这个方法:

```
things_i_like('pie', 'doggos', 'grog')
# => I like pie
# => I like doggos
# => I like grog 
```

所有传递的参数将被收集到一个`Array`中，并作为一个名为`things`的参数传递。

```
things # => ["pie", "doggos", "grog"]
things.class # => Array 
```

### 搅和起来

我们也可以结合 splat 参数定义常规位置参数。

```
def likes(main_interest, *others)
  puts "I like #{main_interest} the most!"
  others.each do |other|
    puts "I also like #{other}"
  end
end 
```

```
likes('pokemon', 'cats', 'chairs')
# => I like pokemon the most!
# => I also like cats
# => I also like chairs 
```

这种类型的参数被称为“位置参数”是有原因的——参数的顺序决定了它们的赋值方式。

在这种情况下，第一个参数与其他参数是分开的。然而，splat 参数不一定是最后一个参数。这样做是完全可以接受的:

```
def likes(main_interest, *others, last_one) 
```

然而，我们能有多疯狂是有限度的。同一方法中不允许有多个 splat 参数。

```
def likes(main_interest, *others, last_one, *and_some_more)
# => syntax error, unexpected * 
```

### 反过来说

splat 操作符也以相反的方式工作。
它可以把一个`Array`转换成一个参数列表！

```
def three_things_i_like(a, b, c)
  puts "I like #{a}, #{b} and #{c}."
end 
```

我们现在可以像往常一样用三个参数调用这个方法。

```
three_things_i_like('pie', 'doggos', 'grog') 
```

然而，我们可以使用 splat 操作符传入一个`Array`来代替。

```
array_of_likes = %w(pie doggos grog)
three_things_i_like(*array_of_likes) 
```

这可能非常有用。但是要谨慎！如果数组没有正好三个元素，我们将得到一个`ArgumentError`。

```
array_of_likes = %w(pie doggos)
three_things_i_like(*array_of_likes)
# =>  wrong number of arguments (2 for 3) (ArgumentError) 
```

### 命名参数

Ruby 2.0 引入了关键字参数，splat 参数可以使用它们。Splat 参数总是创建一个`Array`，所有不匹配的关键字参数都将被收集到一个`Hash`中，它将是该数组的最后一个元素。

```
def splat_test(*args)
  args
end 
```

```
splat_test('positional')
# => ['positional'] 
```

```
splat_test('positional', foo: 'bar')
# => ['positional', {:foo=>"bar"}] 
```

```
splat_test(foo: 'bar')
# => [{:foo=>"bar"}] 
```

单个 splat 操作符甚至对命名参数也是相反的。我们只需要添加一个`Hash`作为数组的最后一个元素。

```
def introduction(title, name:, surname:)
  puts "Hello #{title}  #{name}  #{surname}"
end 
```

```
args = ['Mr.', { name: 'John', surname: 'Goblikon' }]
introduction(*args) 
```

## 双泼符(**)

Double splat 操作符的工作方式类似于 splat 操作符，但它只收集关键字参数。因此，它总是生成一个`Hash`，而不是一个`Array`。

```
def do_something(**options)
  options.each { |k, v| puts "Options #{k}: #{v}" }
end 
```

```
do_something(color: 'green', weight: 'bold')
# => Options color: green
# => Options weight: bold 
```

```
things # => {:color=>"green", :weight=>"bold"}
things.class # => Hash 
```

Double splat 运算符将只收集与常规参数不匹配的命名参数。这种行为类似于单个 splat 操作符，但是它不依赖于参数的位置，而是依赖于它们的名称。

```
def do_something(action:, **options)
  puts "Action: #{action}"
  options.each { |k, v| puts "Options #{k}: #{v}" }
end 
```

```
do_something(action: 'print', color: 'green', weight: 'bold')
# => Action: print
# => Options color: green
# => Options weight: bold 
```

双 splat 运算符也反向工作。您可以将`Hash`转换成命名参数。

```
def print_multiple(value:, count:)
  count.times { puts value }
end 
```

```
hash_params = { value: 'Hello', count: 3 }
print_multiple(**hash_params)
# => Hello
# => Hello
# => Hello 
```

需要注意的是，哈希的键必须是符号。它们甚至不能是字符串。

```
hash_params = { 'value' => 'Hello', 'count' => 3 }
print_multiple(**hash_params)
# => wrong argument type String (expected Symbol) (TypeError) 
```

## 使用两个 splat 运算符

您可以在同一方法中混合 splat 和 double splat 参数。

如果两者同时存在，单个 splat 操作符将只收集位置参数，而双 splat 操作符将照常工作。它将收集所有不匹配的命名参数。

```
def can_you_do_this?(*positional, **named)
  puts positional
  puts named
end 
```

```
can_you_do_this?('first', 'second', name: 'john', surname: 'doe')
# => first
# => second
# => {:name=>"john", :surname=>"doe"} 
```

您可以在同一个方法中定义 positional、splat、keyword 和 double splat 参数。但是，这里的顺序很重要。关键字参数需要跟在位置参数之后，并且 double splat 操作符必须在最后。

```
def can_you_do_this?(first, *positional, second, name:, **named)
# This is perfectly ok! 
```

## 结论

尽管一开始看起来有点吓人，splat 操作符逻辑实际上非常简单。

请记住:

*   常规参数具有优先权，将首先被赋值。
*   Double splat 总是只对命名参数有效。
*   除非在同一个方法中还定义了 double splat 参数，否则 Single splat 既收集位置参数也收集命名参数。

## 解

所以要从一开始就回答这个问题。这个方法调用会发生什么？

```
my_method('Go', 'Now', name: 'John Goblikon') 
```

### 例 1

第一种方法可以，它将所有参数收集到一个`Array`中，命名的参数存储为一个`Hash`。

```
def my_method(*args)
# args => ['Go', 'Now', {name: 'John Goblikon'}] 
```

### 例 2

第二个例子是无效的，当两个位置参数被传递给方法时，我们只收集命名参数。我们会得到一个`ArgumentError`。

```
def my_method(**args)
# wrong number of arguments (2 for 0) (ArgumentError) 
```

### 例 3

第三个例子同样有效。这一次，单 splat 参数将只收集位置参数，而双 splat 将收集命名参数。

```
def my_method(*args1, **args2)
# args1 => ['Go', 'Now']
# args2 => {name: 'John Goblikon'} 
```