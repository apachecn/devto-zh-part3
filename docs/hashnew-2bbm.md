# 哈希#新

> 原文：<https://dev.to/scottw/hashnew-2bbm>

我在 [Hash#fetch](https://dev.to/raquelxmoss/using-hashfetch-in-ruby-for-better-nil-handling-2l8h) 上读了这篇文章，它让我想起了 Ruby 的 Hash 对象的另一个很少使用/理解的方法`new`。

通常，在 Ruby 中，散列是通过使用`{}`文字创建的。这相当于只做`Hash.new`。

```
h1 = {}
h2 = Hash.new 
```

不管你选择哪一个选项，结果都是一样的。

然而，就像在大多数 Ruby 中一样，有不止一种方法。哈希初始化器有三个选项:

1.  没有参数
2.  默认参数
3.  一个街区

除了字面意义，我最常用的是选项#2，一个默认参数。通常，当我想数一堆东西的时候，我会用这个。

```
a = [1,2,3,4,1,1,3]
h = Hash.new(0)
a.each {|i| h[i] += 1}
puts h #{1=>3, 2=>1, 3=>2, 4=>1} 
```

如果没有`Hash.new(0)`,我们将不得不检查 nil，或者做一个像`(h[i] ||= 0) += 1`这样可读性差得多的赋值，这取决于你迭代的内容，可能会变得复杂。