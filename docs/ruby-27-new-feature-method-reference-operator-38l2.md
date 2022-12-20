# 还原的 Ruby 2.7 新特性:方法引用操作符

> 原文：<https://dev.to/hanachin/ruby-27-new-feature-method-reference-operator-38l2>

## 2019/11/12 已还原

👋

[https://github . com/ruby/ruby/commit/fb6a 489 af 2765 a3 b 56 e 301 ADF 0019 af 6 bbad 6156](https://github.com/ruby/ruby/commit/fb6a489af2765a3b56e301adf0019af6bbad6156)
T3】https://bugs.ruby-lang.org/issues/16275

## 方法引用操作符

新增操作员`.:`。
[https://bugs.ruby-lang.org/issues/12125](https://bugs.ruby-lang.org/issues/12125)
T5】https://github . com/ruby/ruby/commit/67c 574736912003 c 377218153 f 9 d3b 9 c 0 c 96 a 17b

它像`method`方法一样将方法转换为`Method`对象。

```
42.:to_s
# => #<Method: Integer#to_s> 
```

## 什么是`method`法？

它将该方法转换为`Method`对象。
T2【https://docs . ruby-lang . org/en/trunk/object . html # method-I-methodT4】

```
42.method(:to_s)
# => #<Method: Integer#to_s> 
```

## 什么是`Method`对象？

`Method`对象是一对`receiver`和方法，它像`Proc`一样可调用。
【https://docs.ruby-lang.org/en/trunk/Method.html】T4

```
meth = 42.method(:to_s)
# => #<Method: Integer#to_s>
meth.receiver
# => 42
meth.name
# => :to_s
meth.call
# => "42"
meth.call(16)
# => "2a" 
```

## 方法引用操作符`.:`和`method`方法有什么区别？

如果`method`方法被覆盖，`.:`仍然有效。

```
class Object
  def method(*); end
end

42.method(:to_s)
# => nil
42.:to_s
# => #<Method: Integer#to_s> 
```

就算`method`方法再精炼，`.:`依然管用。

```
using Module.new {
  refine(Object) do
    def method(*); end
  end
}

42.method(:to_s)
# => nil
42.:to_s
# => #<Method: Integer#to_s> 
```

## 有没有可能将炼法转化为`Method`？

否

```
using Module.new {
  refine(Object) do
    def foo(*); end
  end
}

42.:foo
# undefined method `foo' for class `Integer' (NameError) 
```

## 如何把一元法转换成`Method`？

像`method`方法参数一样添加后缀`@`。

```
42.method(:-@).call
# => -42
42.:-@.call
# => -42 
```

## 有用吗？

是啊！这对于获取块的 API 很有用，就像`Enumerable`中的方法一样。

```
[1,2,3].map { |n| 2 * n }
# => [2, 4, 6]
[1,2,3].map(&2.:*)
# => [2, 4, 6]

["NG"].any? { |word| "COPYING".include?(word) }
# => true
["NG"].any?(&"COPYING".:include?)
# => true

require "prime"

(1..10).select { |n| Prime.prime?(n) }
# => [2, 3, 5, 7]
(1..10).select(&Prime.:prime?)
# => [2, 3, 5, 7] 
```

## 结论

你可以使用新的语法方法引用运算符`.:`现在< 3

```
$  rbenv install 2.7.0-dev
$  rbenv global 2.7.0-dev
$  ruby -e 'self.:puts.("Enjoy!")'
Enjoy! 
```