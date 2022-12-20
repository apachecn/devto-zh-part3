# Ruby 中的绑定和词法范围

> 原文：<https://dev.to/appsignal/bindings-and-lexical-scope-in-ruby-27o1>

新年快乐，欢迎回到 Ruby Magic！在这一集的冬季节目中，我们将深入探讨捆绑和范围。所以穿上你的滑雪板，跟着我们深入森林。

上次，我们通过比较 blocks、procs 和 lambdas 来看 Ruby 中的[闭包。除了这三种类型之间的区别，我们还提到了什么定义了*闭包*。](https://blog.appsignal.com/2018/09/04/ruby-magic-closures-in-ruby-blocks-procs-and-lambdas.html)

> 闭包是环境的一级函数。环境是创建闭包时存在的变量的映射。闭包保留了对这些变量的访问，即使它们是在另一个作用域中定义的。

我们已经探索了 Ruby 的一级函数，但是我们方便地跳过了*环境*。在这一集中，我们将通过研究 Ruby 如何通过其*绑定*处理*词法范围*，来看看这个环境是如何为闭包、类和类实例工作的。

## 词法范围

在编程中，作用域指的是在代码的特定部分可用的*绑定*。绑定，或*名称绑定*，将名称绑定到内存引用，就像变量的名称绑定到它的值一样。作用域定义了`self`的含义、可以调用的方法以及可用的变量。

像大多数现代编程语言一样，Ruby 使用静态作用域，通常称为*词法作用域*(与[动态作用域](https://en.wikipedia.org/wiki/Scope_(computer_science)#Lexical_scope_vs._dynamic_scope)相对)。当前范围基于代码的结构，并确定代码特定部分的可用变量。这意味着当代码在方法、块和类之间跳转时，作用域会发生变化——例如，它们可能都有不同的局部变量。

```
def bar
  foo = 1
  foo
end

bar #  => 1 
```

在这个方法中，我们在一个方法中创建一个*局部变量*，并将其打印到控制台。变量在方法内部的范围内*，因为它是在那里创建的。* 

```
foo = 1

def bar
  foo
end

bar # => NameError (undefined local variable or method `foo' for main:Object) 
```

在这个例子中，我们在方法之外创建变量。当我们在方法内部调用变量时，我们会得到一个错误，因为变量*超出了范围*。局部变量是严格限定范围的，这意味着一个方法不能访问自身以外的变量，除非它是作为参数传递的。

```
@foo = 1

def bar
  @foo
end

bar #  => 1 
```

当局部变量在本地可用时，*实例变量*可用于一个类实例的所有方法。

## 继承的作用域和过程绑定

正如我们在前面的例子中看到的，范围是基于代码中的位置的。在方法外部定义的局部变量不在方法内部的范围内，但可以通过将其转换为实例变量来使用。方法不能访问在它们之外定义的局部变量，因为方法有它们自己的范围，有它们自己的绑定。

Procs(通过扩展，包括块和 lambda)是不同的。每当 proc 被实例化时，就会创建一个绑定，该绑定继承对创建块的上下文中的局部变量的引用。

```
foo = 1
Proc.new { foo }.call # => 1 
```

在这个例子中，我们将名为`foo`的变量设置为`1`。在内部，第二行创建的 Proc 对象创建了一个新的绑定。当调用 proc 时，我们可以询问变量的值。

因为绑定是在初始化过程时创建的，所以我们不能在定义变量之前创建过程，即使在定义变量之后才调用块。

```
proc = Proc.new { foo }
foo = 1
proc.call # => NameError (undefined local variable or method `foo' for main:Object) 
```

调用 proc 将产生一个`NameError`,因为变量没有在 proc 的绑定中定义。因此，在创建 proc 或将其作为参数传递之前，应该定义在 proc 中访问的任何变量。

```
foo = 1
proc = Proc.new { foo }
foo = 2
proc.call # => 2 
```

但是，我们可以在主上下文中定义了变量后更改它，因为 proc 的绑定保存了对它的引用，而不是复制它。

```
foo = 1
Proc.new { foo = 2 }.call
foo #=> 2 
```

在这个例子中，我们可以看到`foo`变量在进程中和在进程外指向同一个对象。我们可以在 proc 内部更新它，以便让它外部的变量也得到更新。

## 绑定

为了跟踪当前范围，Ruby 使用了*绑定*，它封装了代码中每个位置的执行上下文。`binding`方法返回一个描述当前位置绑定的`Binding`对象。

```
foo = 1
binding.local_variables # => [:foo] 
```

绑定对象有一个名为`#local_variables`的方法，该方法返回当前范围内所有可用的局部变量的名称。

```
foo = 1
binding.eval("foo") # => 1 
```

可以使用`#eval`方法在绑定上评估代码。上面的例子不是很有用，因为简单地调用`foo`会有相同的结果。然而，由于绑定是一个可以传递的对象，它可以用于一些更有趣的事情。让我们看一个例子。

### 现实生活中的例子

现在我们已经了解了车库安全捆绑的方法，比如把它们带到斜坡上，在雪地里玩耍。除了 Ruby 在语言内部使用绑定之外，还有一些情况下绑定对象是显式使用的。一个很好的例子是 ERB——Ruby 的模板系统。

```
require 'erb'

x = 1

def y
  2
end

template = ERB.new("x is <%= x %>, y() returns <%= y %>, self is `<%= self %>`")
template.result(binding) # => "x is 1, y() returns 2, self is `main`" 
```

在这个例子中，我们创建了一个名为`x`的变量，一个名为`y`的方法，以及一个引用这两者的 ERB 模板。然后，我们将当前绑定传递给`ERB#result`，它评估模板中的 ERB 标签，并返回一个填充了变量的字符串。

在幕后，ERB 使用`Binding#eval`在传递的绑定范围内评估每个 ERB 标签的内容。适用于上述示例的简化实现如下所示:

```
class DiyErb
  def initialize(template)
    @template = template
  end

  def result(binding)
    @template.gsub(/<%=(.+?)%>/) do
      binding.eval($1)
    end
  end
end

x = 1

def y
  2
end

template = DiyErb.new("x is <%= x %>, y() returns <%= y %>, self is `<%= self %>`")
template.result(binding) # => "x is 1, y() returns 2, self is `main`" 
```

`DiyErb`类在初始化时接受一个模板字符串。它的`#result`方法找到所有 ERB 标签，并用评估其内容的结果替换它们。为此，它用 ERB 标签的内容调用传递的绑定上的`Binding#eval`。

通过在调用`#result`方法时传递当前绑定，`eval`调用可以访问在方法之外定义的变量，甚至是在类之外定义的变量，而不必显式地传递它们。

## 我们在森林里跟丢了吗？

我们希望你喜欢我们的森林滑雪之旅。在略述了作用域和闭包之后，我们更深入地研究了它们。我们希望没有在森林里跟丢你。如果您想了解更多关于绑定的知识，或者有任何其他您想深入研究的 Ruby 主题，请告诉我们。

感谢您关注我们，请在将绑定留给下一个开发人员之前，将绑定上的雪踢掉。如果你喜欢这些神奇的旅行，你可能想[订阅 Ruby Magic](https://blog.appsignal.com/ruby-magic) 以便在我们大约每月发布一篇新文章时收到一封电子邮件。