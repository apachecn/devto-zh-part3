# 提升您的 Ruby Skillz:使用哈希

> 原文：<https://dev.to/molly/level-up-your-ruby-skillz-working-with-hashes-4bid>

上周我讨论了 Ruby 数组，并分享了一些我认为最有用的方法。本周我想谈谈散列！在我开始研究我最喜欢的方法之前，我想简要介绍一下 Ruby 中的 hash 是什么以及它是如何工作的。直接来自文档本身的[散列](https://ruby-doc.org/core-2.6.0.preview2/Hash.html)的定义:

> 散列是惟一键及其值的类似字典的集合(如果您是 Java 人员，可以考虑映射)。也称为关联数组(老实说，我从来没有听说过，但🤷)，它们类似于数组，但是数组使用整数作为它的索引，散列允许你使用任何对象类型。

其中一把钥匙(双关语😉)的定义是，散列允许您使用任何对象类型作为索引。大多数人一想到大麻就会想到这个。

```
{ a: 1, b: 2, c: 3 } 
```

Enter fullscreen mode Exit fullscreen mode

但是在 Ruby 中散列可以多得多！例如，所有这些都是散列。

```
# String Keys
{ "a" => 1, "b" => 2, "c" => 3 }

# Integer Keys 
{ 1 => "a", 2 => "b", 3 => "c" } 

# Symbol Keys (Hashrocket notation) 
{ :a => 1, :b => 2, :c => 3 } 

# Array Keys
{ ['a'] => 1, ['b'] => 2, ['c'] => 3 } 

# Hash Keys 
{ { :a => 1 } => 2, { :b => 2 } => 3 } 
```

Enter fullscreen mode Exit fullscreen mode

您想要的任何对象类型都可以是散列键。既然我们已经有了语义和定义，让我们来看看方法！

*   [T2`each`](#each)
*   [`each_key`](#each_key_value)/[`each_value`T5】](#each_key_value)
*   [`keys`](#keys_values)/[`values`T5】](#keys_values)
*   [`key?`](#key_value?)/[`value?`T5】](#key_value?)
*   [`transform_keys`](#transform_keys)/[`transform_values`T5】](#transform_values)
*   [T2`dig`](#dig)
*   [T2`fetch`](#fetch)
*   [T2`select`](#select)
*   [T2`reject`](#reject)
*   [T2`slice`](#slice)
*   [T2`chaining`](#chaining)

注意:就像在[数组教程](https://dev.to/molly_struve/level-up-your-ruby-skillz-working-with-arrays-hnn)中一样，如果你在代码片段中看到`irb`，这意味着我正在一个 Ruby 控制台中工作。此外，我做了一个代码[小抄](https://paper.dropbox.com/doc/Handy-Ruby-Hash-Methods--AdDf0Q90cMJJ8Xos__W_Un09AQ-vXrU498IlJByJtgHG9o6f)如果你想要没有所有解释的代码。

# [`each`T4】](https://ruby-doc.org/core-2.6.0.preview2/Hash.html#method-i-each)

你可以对一个散列做的最有价值的事情之一就是迭代它。循环遍历一个散列最简单的方法之一是使用 [`each`](https://ruby-doc.org/core-2.6.0.preview2/Hash.html#method-i-each) 。我不知道你是怎么想的，但是当我开始使用 Ruby 时，我的 hash `each`语句是这样的。

```
irb> { a: 1, b: 2, c: 3 }.each do |pair|
  puts "#{pair.first} #{pair.last}"
end
a 1
b 2
c 3
=> {:a=>1, :b=>2, :c=>3} 
```

Enter fullscreen mode Exit fullscreen mode

在这里，我执行我的`each`方法，就像我对我的数组所做的一样。现在不同的是`pair`本身就是一个数组。第一个元素是我的键，第二个元素是我的值。**注意:在为我们的每个键/值对执行块之后，我们迭代的原始散列被返回。**

上面的方法是可行的，但是还有一种更巧妙的方法，你可以通过向你的块传递两个参数来分离这一对。这意味着你可以这样重写上面的:

```
irb> { a: 1, b: 2, c: 3 }.each do |key, value|
  puts "#{key} #{value}"
end
a 1
b 2
c 3
=> {:a=>1, :b=>2, :c=>3} 
```

Enter fullscreen mode Exit fullscreen mode

现在我们有两个独立的变量，一个代表键，一个代表值。

# [T3](#-raw-eachkey-endraw-raw-eachvalue-endraw-)[T0](https://ruby-doc.org/core-2.6.0.preview2/Hash.html#method-i-each_key)/[T1](https://ruby-doc.org/core-2.6.0.preview2/Hash.html#method-i-each_value)

但是如果我们不想要所有的键和值呢？如果我们只想要键或者只想要值呢？每一个都有方法！

```
irb> { a: 1, b: 2, c: 3 }.each_key do |key|
  puts key
end
a
b 
c
=> {:a=>1, :b=>2, :c=>3}

irb> { a: 1, b: 2, c: 3 }.each_value do |value|
  puts value
end
1
2 
3
=> {:a=>1, :b=>2, :c=>3} 
```

Enter fullscreen mode Exit fullscreen mode

请注意，同样，不管我们使用`each_key`还是`each_value`，在我们完成迭代之后，我们仍然得到原始散列。

# [T3](#-raw-keys-endraw-raw-values-endraw-)[T0](https://ruby-doc.org/core-2.6.0.preview2/Hash.html#method-i-keys)/[T1](https://ruby-doc.org/core-2.6.0.preview2/Hash.html#method-i-values)

假设你只想要一个键的数组或者你只想要一个散列值的数组。为此，您可以使用`keys`或`values`。两者都将返回给定散列的键或值的数组。

```
irb> { a: 1, b: 2, c: 3 }.keys 
=> [:a, :b, :c]
irb> { a: 1, b: 2, c: 3 }.values 
=> [1, 2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

# [T3](#-raw-key-endraw-raw-value-endraw-)[T0](https://ruby-doc.org/core-2.6.0.preview2/Hash.html#method-i-key-3F)/[T1](https://ruby-doc.org/core-2.6.0.preview2/Hash.html#method-i-value-3F)

另外两个非常简单的方法是键和值[谓词](http://ruby-for-beginners.rubymonstas.org/objects/predicates.html)。如果你想知道一个键或者一个值是否存在于一个散列中，那么你可以使用`key?`或者`value?`。

```
irb> { a: 1, b: 2 }.key?(:b)
=> true
irb> { a: 1, b: 2 }.value?(2)
=> true
irb> { a: 1, b: 2 }.value?(5)
=> false 
```

Enter fullscreen mode Exit fullscreen mode

这很简单，但是我想指出一些细微的差别。首先，当你寻找一个键时，你必须确保你寻找的是正确的数据类型。例如，如果您有一个带有符号键的散列，搜索字符串将返回 false。

```
irb> { a: 1, b: 2 }.key?("b")
=> false 
```

Enter fullscreen mode Exit fullscreen mode

确保您正在搜索的数据类型与您的键或值的数据类型相匹配。

# [`fetch`T4】](https://ruby-doc.org/core-2.6.0.preview2/Hash.html#method-i-fetch)

现在我们知道了如何检查我们是否有一个特定的键或值，但是如果我们想实际查找一个键的值呢？嗯，总有标准的方法来做这件事，这是我首先学到的。

```
irb> h = { a: 1, b: 2, c: 3 }
irb> h[:a] 
=> 1
irb> h[:d] 
=> nil 
```

Enter fullscreen mode Exit fullscreen mode

简单易行。如果键在那里，我们返回它的值。如果键不在那里，我们返回零。但是如果我们有一些更复杂的逻辑呢？例如，如果我们想返回键值，或者如果键不存在，我们想返回一些默认值，比如 0，该怎么办？就我们目前所知，我们可以做到这一点

```
h = { a: 1, b: 2, c: 3 }
if h[:a]
  result = h[:a]
else
  result = 0
end 
```

Enter fullscreen mode Exit fullscreen mode

它是可行的，但是要完成它需要相当多的代码。相反，我们可以用一个简单的方法来替换这一大块代码，`fetch`。`fetch`有很多选项和行为，所以让我们一次打开一个。

1) **`fetch`不带参数**如果存在将返回值。如果该值不存在，`fetch`将引发一个错误。如果您想在找不到键时引发错误，这是很好的方法。

```
irb> h = { a: 1, b: 2, c: 3 }
irb> h.fetch(:a) 
=> 1
irb> h.fetch(:d) 
KeyError: key not found: :d
    from (irb):13:in `fetch'
    from (irb):13 
```

Enter fullscreen mode Exit fullscreen mode

2) **`fetch`带参数**将返回值，如果它存在的话，就像之前一样。这就是它变得巧妙的地方，它可以帮助我们完成最初的用例，如果你传递 fetch 参数，而键不存在，它将返回参数！🔥

```
irb> h = { a: 1, b: 2, c: 3 }
irb> h.fetch(:a, 0) 
=> 1
irb> h.fetch(:d, 0) 
=> 0 
```

Enter fullscreen mode Exit fullscreen mode

# [`dig`T4】](https://ruby-doc.org/core-2.6.0.preview2/Hash.html#method-i-dig)

当你有一个单级散列并想使用一个键从它返回一个值时，这个方法非常有用。但是如果你有几个嵌套的散列呢？例如

```
h = { a: { b: { c: 1 } } } 
```

Enter fullscreen mode Exit fullscreen mode

通常要得到 c 的值，你需要做

```
irb> h[:a][:b][:c] 
=> 1 
```

Enter fullscreen mode Exit fullscreen mode

它将遍历您的嵌套散列并返回您的值。但是如果您不确定是否所有这些级别都存在呢？例如，假设您正在使用一个 API 来获取用户数据。有时候你找到你要找的用户，把这个拿回来。

```
{ user: { first: 'mary', last: 'sue' } } 
```

Enter fullscreen mode Exit fullscreen mode

其他时候，你没有找到你想要的用户，你得到了这个

```
{ status: 'not found' } 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，我们不能仅仅假设我们有用户信息，因为如果我们没有用户信息，我们将最终引发一个错误。

```
irb> h = { status: 'not found' }
irb> h[:user][:first] 
=> NoMethodError: undefined method `[]' for nil:NilClass 
```

Enter fullscreen mode Exit fullscreen mode

为了避免这种情况，我们可以做

```
h = { status: 'not found' }
if h[:user].present?
  result = h[:user][:first]
else
  result = nil
end 
```

Enter fullscreen mode Exit fullscreen mode

这确保了如果我们有用户数据，我们返回名字。如果我们没有数据，那么我们返回零。事实证明，我们可以通过使用`dig`来避免这一切。 [`dig`](https://ruby-doc.org/core-2.6.0.preview2/Hash.html#method-i-dig) 提取由一系列关键对象指定的嵌套值。**然而，如果这些嵌套键中的任何一个不存在，它将返回 nil。**🎉

```
irb> h = { user: { first: 'mary', last: 'sue' } }
irb> h.dig(:user, :first) 
=> 'mary'
irb> h = { status: 'not found' }
irb> h.dig(:user, :first) 
=> nil 
```

Enter fullscreen mode Exit fullscreen mode

当你想遍历一个散列但不确定它的结构是什么的时候,`dig`非常有用。它允许您处理散列，而不用担心处理大量错误或使用 if/else 块进行大量的`present?`检查。

# [`transform_keys`](https://ruby-doc.org/core-2.6.0.preview2/Hash.html#method-i-transform_keys) (仅在 Ruby 2.5 及以上版本中可用)

现在我们要把它提升一个档次。如果我们有一个散列，其中所有的键都是符号，但我们想把它们都变成字符串，那该怎么办？早期我们会这样做。

```
new_hash = {}
{ a: 1, b: 2, c: 3 }.each do |key, value|
  new_hash[key.to_s] = value
end
# new_hash = { "a" => 1, "b" => 2, "c" => 3 } 
```

Enter fullscreen mode Exit fullscreen mode

它的工作，但你猜对了，现在有一个更好的方法。更好的方法是`transform_keys`。 [`transform_keys`](https://ruby-doc.org/core-2.6.0.preview2/Hash.html#method-i-transform_keys) 允许你迭代一个 hash 并将返回一个新的 hash。新的散列键将是对每个原始键执行块的结果。

```
h = { a: 1, b: 2, c: 3 }
new_hash = h.transform_keys do |k| 
  k.to_s 
end  
# new_hash = { "a" => 1, "b" => 2, "c" => 3 } 
```

Enter fullscreen mode Exit fullscreen mode

**重要的** `transform_keys`(上图)和`transform_values`(下图)只有在 Ruby 2.5 及以上才有。如果你得到下面的错误，那么你可能正在使用低于 2.5 版本的 Ruby`NoMethodError: undefined method`transform _ keys`for {:a=>1, :b=>2, :c=>3}:Hash`

# [`transform_values`](https://ruby-doc.org/core-2.6.0.preview2/Hash.html#method-i-transform_values) (仅在 Ruby 2.5 及以上版本中可用)

`transform_values`的工作方式与`transform_keys`完全相同，只是它允许您更新值。当它执行完您的块时，它将返回一个包含新值集的新散列。

```
h = { a: 1, b: 2, c: 3 }
new_hash = h.transform_values do |value| 
  value * 2 
end  
# new_hash = { :a => 2, :b => 4, :c => 6 } 
```

Enter fullscreen mode Exit fullscreen mode

# [`select`T4】](https://ruby-doc.org/core-2.6.0.preview2/Hash.html#method-i-select)

如果你读过我的第一篇[阵列教程](https://dev.to/molly_struve/level-up-your-ruby-skillz-working-with-arrays-hnn)，现在我们将进入一些熟悉的领域。就像数组一样，您可以将`select`用于散列！ [`select`](https://ruby-doc.org/core-2.6.0.preview2/Hash.html#method-i-select) 对于一个 hash 来说，它的工作方式基本上和数组一样，它返回一个新的 hash，其中包含你的块评估为真的键/值对。

```
# clunky way
new_hash = {}
h = { a: 1, b: 2, c: 3, d: 4 }
h.each do |key, value| 
  new_hash[key] = value if value.even?
end  
# new_hash = { :b => 2, :d => 4 }

# slick select way
h = { a: 1, b: 2, c: 3, d: 4 }
new_hash = h.select do |key, value| 
  value.even?
end  
# new_hash = { :b => 2, :d => 4 } 
```

Enter fullscreen mode Exit fullscreen mode

# [`slice`T4】](https://ruby-doc.org/core-2.6.0.preview2/Hash.html#method-i-slice)

假设你有一个 hash，你只想得到一组特定的键和它们的值。你可以这样做

```
key_list = [:a, :d]
h = { a: 1, b: 2, c: 3, d: 4 }
new_hash = h.select do |key, value| 
  key_list.include?(key)
end  
# new_hash = { :a => 1, :d => 4 } 
```

Enter fullscreen mode Exit fullscreen mode

但是 Ruby 有一个更简单的方法，那就是使用`slice`。 [`slice`](https://ruby-doc.org/core-2.6.0.preview2/Hash.html#method-i-slice) 将返回一个散列，其中只包含您通过参数请求的键。我们上面的可以简化为

```
h = { a: 1, b: 2, c: 3, d: 4 }
new_hash = h.slice(:a, :d) 
# new_hash = { :a => 1, :d => 4 } 
```

Enter fullscreen mode Exit fullscreen mode

# [`reject`T4】](https://ruby-doc.org/core-2.6.0.preview2/Hash.html#method-i-reject)

回头我们再去熟悉的阵列领域。散列的工作方式与数组相同。 [`reject`](https://ruby-doc.org/core-2.6.0.preview2/Hash.html#method-i-reject) 将返回一个新的散列，它由您的块返回 false 的所有键/值对组成。

```
h = { a: 1, b: 2, c: 3, d: 4 }
new_hash = h.reject do |key, value| 
  value.even?
end  
# new_hash = { :a => 1, :c => 3 } 
```

Enter fullscreen mode Exit fullscreen mode

# `chaining`

最后，但同样重要的是，我们需要介绍链接。上面任何返回散列的方法，你都可以链接在一起。对于这些例子，我将使用括号符号，因为我认为从左至右阅读链接方法比从上至下更容易。

下面是一个将上述一些方法链接在一起的例子。

```
h = { a: 1, b: 2, c: 3, d: 4 }
result = h.transform_keys{|k| k.to_s}.slice("a", "b").reject{|k, v| v == 2}.transform_values{|value| "hi #{value}"} 
# result = {"a"=>"hi 1"} 
```

Enter fullscreen mode Exit fullscreen mode

根据我们在上面学到的知识，让我们来分析一下这里正在发生的事情。
1) `transform_keys`将 hash 的每个键更改为一个字符串并返回`{ "a" => 1, "b" => 2, "c" => 3 , "d" => 4}`
2) `slice`将只选择键“a”和“b”以及它们的值并返回`{ "a" => 1, "b" => 2 }`
3) `reject`将删除任何值等于 2 的键/值对，剩下的是`{ "a" => 1 }`
4)我们最后的`transform_values`将把 hash 的值更改为一个带有“hi”和数字的字符串。最后的结果是`{"a"=>"hi 1"}`

# 轰！💥

[![](img/73aa870efe9ceaa1ab20bb167f6939f6.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--bJcsceji--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_66%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/omdld3l7yz2zsc0xuewb.gif) 
你坚持到了最后！！！希望您现在对 Ruby hashes 感觉更舒服了，并且在您的工具箱中有更多的方法可以使用。如果你有任何问题或需要澄清，请不要犹豫，在评论中提出问题！

如果你想快速参考这些方法，我做了一个[方便的备忘单](https://paper.dropbox.com/doc/Handy-Ruby-Hash-Methods--AdDf0Q90cMJJ8Xos__W_Un09AQ-vXrU498IlJByJtgHG9o6f)，里面有每个例子的代码。