# 从 JavaScript 到 Ruby:我最喜欢的几个特性

> 原文：<https://dev.to/harri_etty/from-javascript-to-ruby-a-few-of-my-favourite-features-37mf>

[![Ruby](img/7a276bb131ea05e80c53887096a6af0f.png)](https://res.cloudinary.com/practicaldev/image/fetch/s--tcM4XmO3--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/ag34t8l3d6abb8onugfn.jpg)

从 JavaScript 到 Ruby，我很兴奋地学习这种承诺“对开发者友好”和“为开发者快乐而设计”的语言。我发现了一种语言，像 JS 一样，是动态的、面向对象的和通用的。像 JS 一样，它也提供了很多方法来做同样的事情，使开发人员拥有相当大的风格自主权(如果你喜欢这种东西的话)。

Ruby 的学习曲线相当低，因为它似乎被设计成具有许多名称合理、一致且易于使用的方法，并且它也没有开箱即用的异步概念，这使得代码比 JavaScript 更容易推理。

以下是我注意到的一些事情，我认为与 JavaScript 相比，Ruby 非常棒！

## 能够检查存储位置

与 JavaScript 不同，Ruby 允许您使用`object_id`方法检查一个值在内存中的位置:

例如，如果我们看 2 个散列的对象 ID:

```
a = {name: 'Harriet'}
b = {name: 'Heather'}
puts a.object_id # 6478367
puts b.object_id # 6471222 
```

这些数字是内存地址。实际的地址并不那么有用，但是当你处理对内存中相同位置的两个引用，或者对内存中不同位置的引用时，它可能会有所帮助。

我从未在实际意义上使用过它，但是当我想探索 Ruby 中字符串和符号工作方式的差异时，它很有帮助(见下一节)。JavaScript 中没有办法检查项目在内存中的位置，当我试图演示 JavaScript 如何通过引用传递对象和通过值传递原语时，这很烦人。

## 符号

在 JavaScript 中，有几种创建字符串的方法，这里的前两种是最常见的:

```
let a = 'Hello world'
let b = "Hello world" // Functionally no different to using single quotes
let b = new String('Hello world') // Creates a String object
let c = `Hello world` // ES6 String Literal 
```

在 Ruby 中，也有几个选项:

```
a = 'Hello world'
b = "Hello world" # Double quotes allow for string interpolation & escape characters
c = String.new('Hello world')
d = String('Hello world') 
```

在 Ruby 中，默认情况下，所有字符串都是字符串对象的类型，作为对象，它们在内存中占据不同的位置，即使两个或更多字符串的内容是相同的。将完全相同的信息存储两次可能有点浪费！

您可以通过查看两个相同字符串的对象 ID 来检查这一点:

```
a = 'Hello world'
b = 'Hello world'
puts a.object_id # 6478367
puts b.object_id # 6471222 
```

这就是符号出现的地方。创建的符号开头带有:,这意味着任何时候使用该符号时，它都将引用相同的值。

```
a = :hello
b = :hello
puts a.object_id # 1111708
puts b.object_id # 1111708 
```

这对于单个单词非常有用，但是您甚至可以将一个较长的字符串转换成一个符号，并使用`.to_sym`方法提高效率:

```
a = 'Hello world'.to_sym
b = 'Hello world'.to_sym
puts a.object_id # 92880
puts b.object_id # 92880 
```

我尽可能在字符串上使用符号，而不仅仅是当我知道一个值会在程序中再次使用时！为什么不呢，因为这很容易做到，而且不会有什么损失。

## 简单循环➰

在 JavaScript 中，有时你只想循环一定的次数。只要你的代码执行了 n 次，你就不在乎起点或者终点。然而，您被迫自己显式地构造迭代的条件，从 i = 0 开始，并定义您希望循环何时结束:

```
for (let i = 0; i < 10; i++) {
 // do stuff
} 
```

在 Ruby 中，你可以简单地做:

```
10.times do
  # do stuff
end 
```

这是一种简单的、不太强制的执行代码一定次数的方法。

## 函数对参数要求更严格

我喜欢在 Ruby 中，如果你给一个函数错误的参数个数，你会得到一个错误。它只是加快了调试代码的过程。

```
def greet(name)
  puts "Hello, #{name}"
end

greet()

# wrong number of arguments (given 0, expected 1) 
```

你也可以命名你的参数，如果它们没有被传递，或者你传递了一些意想不到的东西，你会得到一个错误:

```
def greet(name:, city:)
  puts "Hello, #{name} from #{city}"
end

greet(name: 'Harriet', city: 'Manchester')

greet(name: 'Harriet') # missing keyword: city

greet(name: 'Harriet', city: 'Mancheseter', age: 27) # unknown keyword: age 
```

## 没有函数调用括号

在 JavaScript 中，调用函数时必须使用括号，例如`add(1, 2)`。

在 Ruby 中，括号通常是可选的，这有时会导致 Ruby 看起来非常自然，易于阅读。例如，一个测试库可以提供一个`to`方法，在没有括号的情况下，读起来是这样的:

```
expect(result).to be_null 
```

虽然如果有多个参数，会有点混乱。例如，`5`是`bar`的第二个自变量，还是`foo`的第二个自变量？没有括号就不清楚:

```
def foo(a, b)
  puts "in foo #{a}, #{b}"
end

def bar(a)
 12 + a
end

foo bar 55, 5 # wrong number of arguments (given 2, expected 1)

foo bar(55), 5 # Correct - 5 is the second argument to foo 
```

调用不带括号的函数也意味着我们可以这样做:

```
def greet(name = 'Harriet')
  puts "Hello, #{name}"
end

greet 
```

看看只引用`greet`方法实际上是如何不带参数地调用它的吧？这就是 Ruby 在对象上实现 getter 方法的方式。例如，当您调用`person.name`时，`name`实际上是该对象上的一个方法，它检索`name`实例变量。它不仅仅是 JavaScript 中的对象属性。

无括号方法调用的一个影响是，我们不能像在 JavaScript 中那样将方法作为值传递。在 JavaScript 中，我们可以这样做:

```
function greet(name) {
  console.log(`Hello, ${name}`);
}

const welcomer = greet;

welcomer('Harriet'); 
```

但是在 Ruby 中，试图传递对方法的引用实际上会调用它！所以我们以:
结束

```
def greet(name = 'Harriet')
  puts "Hello, #{name}"
end

welcome = greet # This line actually executes the greet function

welcome "Paula" # undefined method `welcome' for main:Object 
```

## 只是创建类的一种方式

在 JavaScript 中，没有真正的类的概念，至少没有真正面向对象语言的人所期望的那样。相反，我们有一个原型链，至少有 4 种不同的方法来创建具有共享方法和行为的对象。这非常令人困惑，所以我真的很喜欢 Ruby 提供了一种方法来做到这一点！

在 Ruby 中创建类方法、类变量、实例方法和实例变量要简单得多:

```
class Person
  attr_reader :name, :title

  # Class variables
  @@legs = 2
  @@arms = 2
  @@diet = 'omnivore'

  def initialize(name, title)
    # @name and @title are instance variables
    @name = name
    @title = title
  end

  # Instance method
  def greet
    puts "Good day, #{title}  #{name}!"
  end

  # Class method
  def self.describe
    puts "A person is a #{@@legs}-legged, #{@@arms}-armed #{@@diet}"
  end
end

jerry = Person.new('Jerry Jones', 'Mr')

jerry.greet

Person.describe 
```

## 隐式返回

在 Ruby 中，`return`语句是可选的，或者可以用来从函数中提前返回。如果忽略它，函数将返回最后一次求值。

```
def double(nums)
  nums.map{ |n| n * 2 }
end 
```

## 元编程

这是一个相当大的话题，我对它不是很了解，所以我只简单地谈一下。元编程意味着程序能够在运行时根据程序当时的状态修改自身。

Rails 使用元编程来允许我们做这样的事情:

`Book.find_by_ISBN("19742084746")`

您在建立模型时定义了`Book`类，但是没有定义`find_by_ISBN`方法。为你所有的专栏定义一个`find_by_x`真的很烦人；难怪 Rails 框架不想让你去做那些工作。但是 Rails 本身也没有为您添加那个方法。Rails 如何神奇地知道您的 Book 实例需要什么`find_by_ISBN`方法？

相反，当 Rails 看到你试图使用`find_by_ISBN`方法时，它将提取`ISBN`部分，并试图将其与数据库中的一列匹配，如果成功，它将执行一些代码来基于`ISBN`列查找你的项目，就像`find_by_ISBN`是在 Book 实例上定义的实际方法一样做出响应。

这只是元编程所能做的一个例子！

我个人认为这很酷，一旦你知道它的存在，你就会开始在 Ruby 的任何地方看到它。它是能够创建像 Rails 这样的 DSL(领域特定语言)的基石，并使 Ruby 非常灵活。