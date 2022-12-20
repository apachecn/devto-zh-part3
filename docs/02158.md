# 做水晶#3:类型，类型，类型

> 原文：<https://dev.to/watzon/doing-crystal-3-types-types-types-5gdf>

欢迎来到我做水晶系列的第三篇帖子。在这篇文章中，我将重点介绍 Crystal 的类型系统，它的优点和缺点。如果你没有读过这个系列的其他文章，你可以在这里找到它们的，在这里找到[的](https://dev.to/watzon/doing-crystal-2-getting-started-with-crystal-13dk)，分别是第一篇和第二篇。

## 静态 vs 动态打字

静态类型在编程中已经存在很多年了，最早可以追溯到 1957 年首次发布的 FORTRAN。事实上，所有的编程语言都是类型化的，甚至像 JavaScript 和 Ruby 这样的语言也有整数、数组和字符串这样的类型。这是必要的，因为在现实世界中，事物都有其独特的属性。数字和字符串本质上是不同的，即使从技术上讲，你可以通过将字符串转换为二进制表示形式，然后将数字添加到字符串中，但在现实世界中，这是没有意义的。因此，我们有类型。

区别在于类型的处理方式。对于像 JavaScript 和 Ruby 这样的语言，类型是动态处理的。这允许你像下面的 Ruby 例子一样做事情:

```
def first_and_last(arr)
  if arr.length > 0
    return [arr.first, arr.last]
  end

  []
end

puts first_and_last(["Hello", "fellow", "developers"])
# => ["Hello", "developers"]

puts first_and_last(42)
# => NoMethodError (undefined method `length' for 42:Integer) 
```

Enter fullscreen mode Exit fullscreen mode

如您所见，方法`first_and_last`应该接受一个数组，并将第一个和最后一个元素作为新数组返回。当传递给它期望的数据类型时，它工作得很好，但是当传递给它一个数字时，它抛出一个运行时错误`NoMethodError`。动态类型可能非常方便，但它也可能是有害的，因为当程序试图使用不存在的方法时，或者当变量的类型意外改变时，会出现大量的运行时错误(或者在程序运行时而不是编译时出现的错误)。

现在让我们看看之前的相同代码，但是是在 Crystal 中。

```
def first_and_last(arr : Array(U)) forall U
  if arr.size > 0
    return [arr.first, arr.last]
  end

  [] of U
end

puts first_and_last(["Hello", "fellow", "developers"])

puts first_and_last(42) 
```

Enter fullscreen mode Exit fullscreen mode

对于这一个，我没有显示输出。为什么？因为程序不会编译。让我们一行一行地检查程序，然后我会解释编译失败的原因。

```
def first_and_last(arr : Array(U)) forall U 
```

Enter fullscreen mode Exit fullscreen mode

首先我们做一个方法定义。这类似于 Ruby 的例子，但是有一些重要的区别。首先，我们有奇怪的`arr : Array(U)`语法。这是将属性名设置为`arr`，将`arr`的类型设置为`U`类型的`Array`。在这种情况下，`U`是一个占位符类型，超出了本教程的范围，但是可以说它允许`U`是任何东西。最后的`forall U`零件创建了`U`类属。

```
if arr.size > 0
  return [arr.first, arr.last]
end 
```

Enter fullscreen mode Exit fullscreen mode

这与 Ruby 示例完全相同，只是在获取数组大小时稍微改变了方法名。在 Ruby 中是`length`，在 Crystal 中是`size`。

```
[] of U 
```

Enter fullscreen mode Exit fullscreen mode

在水晶中，所有的东西都有特定的类型。这防止了运行时错误并降低了内存使用，因为程序可以分配它知道它需要的资源。因此，所有数组、散列、集合等。必须显式类型化，这就是这行代码所做的。如果传递给方法的数组中没有任何东西，我们就返回一个空数组。事实上，我们可能应该返回给我们的同一个数组，但是我想展示一个给数组赋值的例子。

```
puts first_and_last(["Hello", "fellow", "developers"]) 
```

Enter fullscreen mode Exit fullscreen mode

这一行将按预期工作并返回`["Hello", "developers"]`。

```
puts first_and_last(42) 
```

Enter fullscreen mode Exit fullscreen mode

这是打破东西的线。我们的方法需要一个`Array`，但是我们给了它一个`Int32`。由于这种违反契约的行为，编译器抛出了一个异常。

```
no overload matches 'first_and_last' with type Int32
Overloads are:
 - first_and_last(arr : Array(U))

  first_and_last(42)
  ^~~~~~~~~~~~~~ 
```

Enter fullscreen mode Exit fullscreen mode

是的，它仍然是一个错误，但是这次它发生在你发布代码之前。早点抓虫子是一件很美好的事情。

## 晶体中的类型声明

你已经看到了一些类型是如何在 Crystal 中声明的，现在让我们看看更多的例子。类型几乎总是以下面的方式声明:

```
# var_name : Type
arr  : Array(Int32)         = [1, 2, 3]
str  : String               = "Hello, world!"
int  : Int32                = 42
hash : Hash(String, String) = {"user" => "watzon"} 
```

Enter fullscreen mode Exit fullscreen mode

像 Ruby 一样，Crystal 中的所有东西都是对象，所有对象都是有效类型，所以自定义类、结构等。也是有效的类型。现在，对于散列和数组，有了另一种声明类型的方法。

```
arr : Int32
arr = [] of Int32            # Assigning type declaration

hsh : Hash(String, String)
hsh = {} of String => String # Assigning type declaration 
```

Enter fullscreen mode Exit fullscreen mode

对于大多数类型(Int、String、Class ),您可以直接将对象赋给变量，而无需显式声明类型。这叫做类型推断，非常方便。如果数组和散列包含数据，您也可以对它们做同样的事情，但是如果它们像前面的例子一样是空的，您就必须显式声明数组或散列的类型。

### 式推断

类型推断是一个非常方便的特性，几乎就像动态类型一样...有时候。让我们再次使用我们的第一个例子。

```
def first_and_last(arr : Array(U)) forall U
  if arr.size > 0
    return [arr.first, arr.last]
  end

  [] of U
end 
```

Enter fullscreen mode Exit fullscreen mode

因为我们显式声明参数`arr`是一个数组，所以我们知道该参数将有几个有用的方法，允许我们对数组中存储的数据进行操作。然而，Array 并不是唯一包含这些方法的类。Crystal 中还有其他可枚举类，它们有`#size`、`#first`和`#last`方法，如`Set`和`Deque`。但是目前，您不能在我们的`first_and_last`方法中使用这些类中的任何一个。你当然可以这样做:

```
def first_and_last(arr : Indexable(U)) forall U
  if arr.size > 0
    return [arr.first, arr.last]
  end

  [] of U
end 
```

Enter fullscreen mode Exit fullscreen mode

现在，任何包含`Indexable`模块的类都可以被传递到`first_and_last`中，但是有一种更简单但不太明确的方式来处理事情。

```
def first_and_last(arr)
  if arr.size > 0
    return [arr.first, arr.last]
  end

  arr
end 
```

Enter fullscreen mode Exit fullscreen mode

但是等等，这些类型去哪里了？它们仍然在那里，不要担心，但是现在你不必显式声明参数`arr`的类型，编译器将根据你对它执行的操作来推断类型。有几个类有`#first`、`#last`和`#size`方法，现在它们都是有效的输入。

### 工会类型

Crystal 类型系统的一个非常强大的方面是创建类型“联合”的能力。下面是一个联合类型的示例。

```
arr = [] of Int32 | String 
```

Enter fullscreen mode Exit fullscreen mode

管道`|`操作符在两种类型之间创建一个联合，允许您在该数组中同时使用`Int32`和`String`类型。多棒啊。联合类型也可以由编译器动态生成。

```
arr = ["Age", 32] 
```

Enter fullscreen mode Exit fullscreen mode

在这种情况下，变量`arr`将被编译器赋予类型`Array(String, Int32)`。当然，这也意味着对数组中的数据执行的任何操作都必须在执行任何操作之前检查该项的类型，除非它们执行的操作同时适用于这两种类型。比如:

```
arr = ["Age", 32]
arr.map { |a| a.chars } 
```

Enter fullscreen mode Exit fullscreen mode

`chars`是一个存在于`String`类中的方法，它返回字符串中所有字符的数组。然而，这个方法并不存在于`Int32`类中。因此，这段代码无法编译。相反，你必须做如下的事情:

```
arr = ["Age", 32]
arr.map { |a| a.chars if a.is_a?(String) } 
```

Enter fullscreen mode Exit fullscreen mode

与工会打交道时，事情肯定会变得一团糟，所以要记住这一点。

### 结论

Crystal 的类型系统非常强大，我只是触及了它的表面。如果你想了解更多，我建议你看看[水晶参考](https://crystal-lang.org/reference/syntax_and_semantics/types_and_methods.html)。

请不要忘记点击自我助推器按钮之一(个人来说，我喜欢独角兽)，如果你这么想分享到社交媒体。如果你分享到 twitter，请务必在@_watzon 标记我。

一些有帮助的链接:
[https://crystal-lang.org/](https://crystal-lang.org/)
[https://github.com/kostya/benchmarks](https://github.com/kostya/benchmarks)
[https://github.com/kostya/crystal-benchmarks-game](https://github.com/kostya/crystal-benchmarks-game)
[https://github.com/crystal-lang/crystal](https://github.com/crystal-lang/crystal)

在线找我:
[https://medium.com/@watzon](https://medium.com/@watzon)
[https://twitter.com/_watzon](https://twitter.com/_watzon)
[https://github.com/watzon](https://github.com/watzon)
[https://watzon . tech](https://watzon.tech)