# Ruby 2.7 —模式匹配—点析构

> 原文：<https://dev.to/baweaver/ruby-2-7-pattern-matching-destructuring-on-point-3hdb>

### Ruby 2.7 —模式匹配—点上析构

既然模式匹配已经成为 Ruby Nightly 的一个实验性特性，让我们从析构开始，看看它的一些潜在用例。

[![](img/695b54fe81fb36b264547741ab1aaa95.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--Ha4wwkeZ--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://cdn-images-1.medium.com/proxy/1%2Ap0Ad_zpGIOEFOTX4Yft1Fw.png)

如果您还没有看到第一篇详细介绍该规范的文章，您可以在下面找到它:

[Ruby 2.7 —模式匹配—第一印象](https://dev.to/keystonelemur/ruby-2-7-pattern-matching-first-impressions-2dnm-temp-slug-4930565)

这篇文章会更有条理一些。

### 测试警告！

请务必记住，变量赋值析构分配局部变量。这种*会*干扰你的测试，除非你在方法中而不是在直接的`Pry`或`IRB`会话中进行。我们将在本文后面对此进行更深入的探讨。

如果有什么不匹配，就会引发一个错误，所以一定要使用 else 来处理默认情况。

### 上点！

我注意到的一件有趣的事情是从一个对象中析构的能力。假设我们有一个`Point`，它有一个`x`和`y`坐标:

```
Point = Struct.new(:x, :y) do
  def deconstruct
    self.to_a
  end

  def deconstruct_keys(keys)
    self.to_h
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们现在将把它作为我们的基本例子。

### 数组解构

在 Ruby 中，析构是一种从对象中提取值的方法。你可能熟悉赋值语句中的左手风格:

```
x, y = Point.new(0, 1).to_a
x # => 0
y # => 1

*coords = Point.new(2, 3).to_a
coords # => [2, 3] 
```

Enter fullscreen mode Exit fullscreen mode

这些在模式匹配上下文中的 in 表达式中都是有效的。这包括 splatting 值。

**直接值**

我们可以通过析构来直接匹配值:

```
case Point.new(0, 1)
in 0, 1 then Point.new(0, 2)
end
=> #<struct Point x=0, y=2> 
```

Enter fullscreen mode Exit fullscreen mode

这些项目都响应`===`，我们将在本文后面看到。

**三重等于解构**

任何回应`===`的东西在这里都是完全公平的游戏:

```
case Point.new(0, 1)
in 0.., 1.. then Point.new(0, 2)
end 
```

Enter fullscreen mode Exit fullscreen mode

**直接变量**

如果我们只想向北移动，我们可以使用模式匹配来根据位置提取我们的`x`和`y`值:

```
case Point.new(0, 1)
in x, y then Point.new(x, y + 1)
end
=> #<struct Point x=0, y=2> 
```

Enter fullscreen mode Exit fullscreen mode

所以看起来`then`关键字在这里仍然有效。很高兴知道。

现在有趣的事情也在这里发生。它在给局部变量赋值，意思是在这个语句之后，它就起作用了:

```
[x, y]
=> [0, 1] 
```

Enter fullscreen mode Exit fullscreen mode

这适用于任何变量赋值风格，虽然它确实有意义，但还是让我有点吃惊。

**三重等于析构成变量**

如果我们有一些范围怎么样？

```
case Point.new(0, 1)
in 0..5 => x, 0..5 => y
  Point.new(x, y + 1)
end

#<struct Point x=0, y=2> 
```

Enter fullscreen mode Exit fullscreen mode

这里需要注意的是格式是:

```
value or matcher => destructured variable 
```

Enter fullscreen mode Exit fullscreen mode

这些将响应任何实现`===`的东西，这就是 Ruby 中 case 语句如此强大的原因。阅读此处了解更多关于`===`的信息:

[三重等于黑魔法](https://medium.com/rubyinside/triple-equals-black-magic-d934936a6379)

### **关键词解构**

关键词呢？

在 Ruby 中，我们不一定能够对关键字进行原生析构，但是有了模式匹配，当且仅当定义了 `deconstruct_keys`并返回一个类似哈希的对象时，我们才可以使用*，就像上面的* 

```
def deconstruct_keys(keys)
  self.to_h
end 
```

Enter fullscreen mode Exit fullscreen mode

> 我不确定这些键在这里做什么，我必须试着看一看，看看以后会发生什么。如果你有想法让我知道！

考虑到已经做了一些，这是一个有趣的技术细节，但不是我们现在要担心的。

**关键字不是变量赋值**

这里要注意的是，这些键是用来析构的，而不是用来赋值的:

```
case Point.new(0, 1)
in x: 0, y: 1..5 then Point.new(x, y + 1)
end

NameError: undefined local variable or method `x' for main:Object 
```

Enter fullscreen mode Exit fullscreen mode

**箭头仍然用于赋值**

所以这没用。我们必须在这里使用= >将它们绑定到一个局部变量:

```
case Point.new(0, 1)
in x: 0 => x, y: 1..5 => y then Point.new(x, y + 1)
end
=> #<struct Point x=0, y=2> 
```

Enter fullscreen mode Exit fullscreen mode

这意味着我们在这里也可以完全访问===，这非常有用。

### 仿效 Qo —预览

我不久前写了一个模式匹配库，我有点想看看它是如何堆积起来的:

[baweaver/qo](https://github.com/baweaver/qo)

这是下一篇文章的一些预览。

先来个`Person` :

```
Person = Struct.new(:name, :age) do
  def deconstruct
    self.to_a
  end

  def deconstruct_keys(keys)
    self.to_h
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们还将使用`Any`宝石作为通配符:

[巴韦弗/任何](https://github.com/baweaver/any)

**名称超过 3 个字符**

`Qo`方式:

```
name_longer_than_three = -> person { person.name.size > 3 }

people_with_truncated_names = people.map(&Qo.match { |m|
  m.when(name_longer_than_three) { |person|
    Person.new(person.name[0..2], person.age)
  }

  m.else
}) 
```

Enter fullscreen mode Exit fullscreen mode

模式匹配方式

```
person = Person.new('Edward', 20)

case person
in name: -> n { n.size > 3 } => name, age: Any => age
  Person.new(name[0..1], age)
else
  person
end

=> #<struct Person name="Ed", age=20> 
```

Enter fullscreen mode Exit fullscreen mode

### 总结起来

这是我第一次有机会在 Ruby 中尝试一些模式匹配，到目前为止我相当喜欢它。有一些明确的问题和大量的语法需要理解，但肯定有很大的力量。

还有一些非常奇怪的事情，比如我不明白的键，如果你试图在这样的模式匹配中做一些奇特的事情会发生什么

```
case Point.new(0, 1)
in x: :even?.to_proc => x then Point.new(0, 0)
end

endSyntaxError: unexpected '.', expecting `then' or ';' or '\n'
 in x: :even?.to_proc => x then Point.new(0... 
```

Enter fullscreen mode Exit fullscreen mode

我相信这可能是解析器中的一个错误，但是这是实验性的，这是可以预料的。

我接下来对模式匹配的一些探索可能会尝试模仿我使用`Qo`的各种特性:

[baweaver/qo](https://github.com/baweaver/qo)

2.7 将会是一个非常有趣的版本。让我们看看它从这里走向何方！