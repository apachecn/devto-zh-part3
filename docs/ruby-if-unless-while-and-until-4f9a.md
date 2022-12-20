# Ruby If，Until，While and Until

> 原文：<https://dev.to/anthonyharvey/ruby-if-unless-while-and-until-4f9a>

Ruby 提供了常见编程语言中常见的控制结构，如`if`、`else`和`while`，然而，Ruby 也提供了不太常见的选项，如`until`和`unless`。这些控制结构一开始可能看起来很奇怪(“谁会这么说话？”)，但他们有他们的优势。

## If 语句

先说一个基本的`if`语句。这里我们有一个具有几个属性和功能的`Movie`类，只在`in_progress`属性为`true`时更新电影的标题；很简单。如果`in_progress`为真，那么`title`属性可以被赋予一个新值。

```
class Movie
  attr_accessor :in_progress
  attr_reader :title, :length, :budget

  def title=(new_title)
    if @in_progress
      @title = new_title
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

现在让我们想象一下，如果属性是`finalized`而不是`in_progress`。在它前面加上一个`not`或`!`并结束一天的工作可能很有诱惑力。

```
class Movie
  attr_accessor :finalized
  attr_reader :title, :length, :budget

  def title=(new_title)
    if not @finalized
      @title = new_title
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

这个 if 语句是有效的，但是比它应该有的要冗长一些。另一种方法(也更简洁)是使用`unless`。

```
class Movie
  attr_accessor :finalized
  attr_reader :title, :length, :budget

  def title=(new_title)
    unless @finalized
      @title = new_title
    end
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

使用`unless`，只有当条件(`@finalized`)为`false`时，才执行语句体(`@title = new_title`)。使用`unless`有几个好处:

1.  比使用`if not`或`if !`略短
2.  一旦你习惯了，它可能会变得更容易阅读和理解

第二点很重要。阅读一个`unless`语句起初可能看起来很奇怪(当我第一次在一个生产应用程序中遇到它时，我不得不把它理解为“如果不是”，直到我习惯它为止)。但是过了一会儿，它就变得不那么尴尬了，只是另一种表达否定或反过来的 if 语句的方式。

做这件事没有“正确”的方法；这只是一个偏好。一个`if not`和`unless`的区别就像说:

1.  “苹果是桔子是不争的事实”
2.  “苹果不是橘子”

## 当且直到

类似于`unless`是`if`的否定版，`until`是`while`的否定版

当条件(`! download.is_finished?`)为`true`时，`while`循环将继续循环**。在本例中，条件为“当未下载完成时(执行代码块)”。** 

```
while !download.is_finished?
  spinner.keep_spinning
end 
```

Enter fullscreen mode Exit fullscreen mode

一个`until`循环将一直循环**，直到**条件(`download.is_finished?`)为`true`。在本例中，条件为“直到下载完成(执行代码块)”。

```
until download.is_finished?
  spinner.keep_spinning
end 
```

Enter fullscreen mode Exit fullscreen mode

同样，这是一个偏好问题；但是一旦我理解了其中的区别并习惯了，使用`until`就变得更加清晰了。

我们还可以让这些例子更简洁，可读性更强。注意这些语句的代码块只有一行？因为它只有一行，而且非常短，所以我们可以将语句压缩成一行。

```
spinner.keep_spinning while !download.is_finished? 
```

Enter fullscreen mode Exit fullscreen mode

```
spinner.keep_spinning until download.is_finished? 
```

Enter fullscreen mode Exit fullscreen mode

和以前一样，这是一个偏好的问题，但对我来说使用`until`更清晰，读起来更好一点。

这也是我喜欢用 Ruby 写代码的部分原因，有很多方法可以做同样的事情。这种语言提供了你表达自己所需的工具，并尽力不碍事。是`not if`对你更有意义，还是写`unless`语句感觉更自然？任何一种方式都可以，因为 Ruby 提供了两种选择！同样的事情还有`while`和`unless`。

*本文原载于 2019 年 3 月 5 日[anthonygharvey.com](https://anthonygharvey.com/ruby/ruby_if,_unless,_while_and_until)。*