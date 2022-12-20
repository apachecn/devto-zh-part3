# 记住布尔值时要小心！

> 原文：<https://dev.to/codeandclay/a-memoization-gotcha-14c6>

我花了大约一个小时才弄明白为什么一个方法被多次调用，尽管我试图记住它的返回值。

## 问题

我的问题看起来有点像这样。

```
def happy?
  @happy ||= post_complete?
end 
```

Enter fullscreen mode Exit fullscreen mode

我的意图是将`post_complete?`的值存储为`@happy`，这样`post_complete?`将只被触发一次。

然而，这并不保证会发生在这里。每次我调用`happy?`时，`post_complete?`可能会被求值并将其值赋给`@happy` *。*

你能看出为什么吗？

```
 @happy ||= post_complete? 
```

Enter fullscreen mode Exit fullscreen mode

## 到底怎么回事？

问号表示`post_complete?`应该返回一个布尔值。但是，如果那个值总是`false`呢？

语句的另一种写法是:

```
@happy || @happy = post_complete? 
```

Enter fullscreen mode Exit fullscreen mode

在上面的例子中，我想知道是否至少有一边是真的。

请记住，如果一条`||`语句的左边是`false`，那么右边就会被求值。如果左边是真的，就没有必要评价右边——这个陈述已经被证明是真的——所以陈述[短路](https://blog.revathskumar.com/2013/05/short-circuit-evaluation-in-ruby.html)。

如果我用布尔值代替`post_complete?`，就更容易看到发生了什么。

在这个例子中，`@happy`变成了`true` :

```
def happy?
  @happy || @happy = true
  # @happy == true
end 
```

Enter fullscreen mode Exit fullscreen mode

然而，在这个例子中，`@happy`变成了`false` :

```
def happy?
  @happy || @happy = false
  # @happy == false
end 
```

Enter fullscreen mode Exit fullscreen mode

在前一种情况下，第一次调用该方法时，`@happy`为 falseyy，然后在后续调用中，`true`为 false。在这个例子中，右边只计算一次。在后者中，`@happy`总是`false`，所以双方总是被评估。

当使用`||=`样式的记忆时，只有真值会被记忆。

所以问题是，如果第一次调用`happy?`时`post_complete?`返回`false`，那么它将被求值，直到它返回`true`。

## 一解

那么我如何去记忆一个错误的值呢？

我可以检查它是否有赋值，而不是测试`@happy`的真实性。如果有，我可以返回`@happy`。如果没有，我会指定一个。我会用`Object#defined?`。

[文档](http://ruby-doc.org/docs/keywords/1.9/Object.html#method-i-defined-3F)声明:

> 定义？expression 测试 expression 是否引用了任何可识别的内容(文字对象、已初始化的局部变量、从当前作用域可见的方法名等)。).如果表达式无法解析，返回值为 nil。否则，返回值提供有关表达式的信息。
> 
> 请注意，表达式不会被执行。

我是这样用的:

```
def happy?
  return @happy if defined? @happy
  @happy = false
end 
```

Enter fullscreen mode Exit fullscreen mode

回头参考文档，有一件事我需要注意。这与检查`nil`或`false`不同。这有点违反直觉，但是`defined?`不返回布尔值。相反，它以字符串的形式返回关于参数对象的信息:

```
> @a, $a, a = 1,2,3
> defined? @a
#=> "instance-variable"
> defined? $a
#=> "global-variable"
> defined? a
#=> "local-variable"
> defined? puts
#=> "method" 
```

Enter fullscreen mode Exit fullscreen mode

如果我把`nil`赋给一个变量，你认为当我用那个变量调用`defined?`时，返回值会是什么？

```
> defined? @b
#=> nil
> @b = nil
#=> nil
> defined? @b
#=> "instance-variable" 
```

Enter fullscreen mode Exit fullscreen mode

所以，只要变量已经被赋予了*某物*(偶数`nil`)，那么`defined?`将为真。只有当变量未初始化时，它才返回`nil`。

当然，您可以猜测当我们将变量的值设置为`false`时会发生什么。

```
> @c = false
#=> false
> defined? @c
=> "instance-variable" 
```

Enter fullscreen mode Exit fullscreen mode

## 更新:改进的解决方案

在瓦伦丁·巴卡的评论的提示下，我重新评估了我最初的解决方案。我真的需要检查变量是否被初始化，或者检查`nil`就足够了吗？

`@happy.nil?`应该足够了，因为我只对知道变量是`nil`而不是`false`感兴趣。(`false`和`nil`是[Ruby 中唯一的假值](http://ruby-doc.org/core-2.1.1/FalseClass.html)。)

我觉得这个版本可读性更强:

```
def happy?
  @happy = post_complete? if @happy.nil?
  @happy
end 
```

Enter fullscreen mode Exit fullscreen mode

## 包装完毕

我现在知道`||=`记忆方式利用了短路。如果左边的变量是`false`，那么语句的右边部分将被求值。如果这是一个总是返回`false`的昂贵的方法调用，那么我的程序的性能将会受到影响。所以我可以检查变量是否被初始化，或者检查它是否是`nil`，而不是`||=`。

现在我很开心。

```
def happy?
  @happy = post_complete? if @happy.nil?
  @happy
end 
```

Enter fullscreen mode Exit fullscreen mode