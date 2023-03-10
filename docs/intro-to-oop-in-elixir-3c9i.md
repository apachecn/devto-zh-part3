# Elixir 中的 OOP 简介

> 原文：<https://dev.to/bakenator/intro-to-oop-in-elixir-3c9i>

# 这是给谁的？

像许多其他人一样，我通过 Phoenix 框架接触到了 Elixir 语言，然后对在 Phoenix 之外编写 Elixir 产生了兴趣。但是由于它的不变性和缺少类方法，我最大的挑战是弄清楚如何完成任何事情。

这篇文章旨在回顾我学到的关于如何将我的 OOP 编程知识转化为高效的灵丹妙药代码的一些重要经验。这些点大部分都很简单，只需要在你的思维中“点一下”就可以了。

# 功能和数据是分开的

在大多数 OOP 语言中，你使用类(对象)的实例来完成程序的工作。每个实例都有一个独立的状态，然后函数可以通过引用该状态来工作。例如，一个简单的 Ruby 类可能如下所示。

```
class Person
   def initialize(name)
      @cust_name = name
   end

   def name_backwards
      @cust_name.reverse
   end
end

person_1 = Person.new('Ted')
person_1.name_backwards
# outputs "deT" 
```

Elixir 函数不能有对`this`、`@`或任何其他实例变量的任何引用。因此，在 Elixir 中做类似事情的方法是让一个函数将数据(实例)作为第一个参数。下面是上面用 Elixir 重写的 Ruby 代码。

```
person = %{name: "Ted"}

defmodule Person.Functions do
  def name_backwards(person) do
    String.reverse(person.name)
  end
end

Person.Functions.name_backwards(person) 
```

在上面的代码中，我们可以看到 person 实例的数据完全保存在 Person 之外。功能模块。当我们想在 person 实例上使用一个函数时，我们将它传递给一个以 person 实例作为第一个参数的函数。

实际上，一个模块永远不会被命名为`Person.Functions`，因为根据定义，该模块并不与 person 实例相关联。`name_backwards`函数可以用在任何带有名称字段的对象上。

# 通过替换更新

OOP 中最常见的事情之一是使用对象的实例来跟踪某种状态。例如在 Ruby 中

```
class Person
   def initialize(age)
      @person_age = age
   end

   def add_year
      @person_age = @person_age + 1
   end

   def get_age
      @person_age
   end
end

person_1 = Person.new(30)
person_1.add_year
person_1.add_year
person_1.get_age
# outputs 32 
```

在 Elixir 中，所有对象都是不可变的，那么我们究竟如何更新这个 person 实例的字段呢？这可以通过创建一个新的 person 实例并替换旧的实例来实现。

```
person = %{age: 30}

defmodule Person.Functions do
  def add_year(person) do
    new_age = person.age + 1
    %{age: new_age}
  end

  def get_age(person) do
    person.age
  end
end

person = Person.Functions.add_year(person)
person = Person.Functions.add_year(person)
Person.Functions.get_age(person) 
```

因此，我们在第`%{age: new_age}`行返回一个新的实例，而不是改变实例中的字段值。

# 全局对象存储在流程中

我并不提倡使用全局状态，但是我确实认为这个想法对于学习长生不老药的基础知识非常重要。这也可能是一个人为的例子，但我认为它将展示将 OOP 转化为灵丹妙药的挑战。

我添加了一个售票类。它跟踪每张票的去向，这样每个人都能得到一张独一无二的票。在 Ruby 中相当简单，因为我们可以使用静态字段`@@ticket_count`来跟踪票。

```
class Tickets
  @@ticket_count = 1
  def self.get_ticket
    next_ticket = @@ticket_count
    @@ticket_count = @@ticket_count + 1
    next_ticket
  end
end

class Person
   def initialize()
      @ticket_num = -1
   end

   def get_ticket
    @ticket_num = Tickets.get_ticket
    @ticket_num
   end
end

person_1 = Person.new()
puts person_1.get_ticket
#outputs 1
person_2 = Person.new()
puts person_2.get_ticket
#outputs 2 
```

那么我们如何在 Elixir 中跟踪票的数量呢？通过使用所谓的发电机。

```
defmodule Tickets do
  use GenServer

  def start_link(start_ticket_num) do
    GenServer.start_link(Tickets, start_ticket_num, name: Tickets)
  end

  def get_ticket() do
    GenServer.call Tickets, :get_ticket
  end

  def handle_call(:get_ticket, _from, curr_ticket_num) do
    {:reply, curr_ticket_num, curr_ticket_num + 1}
  end
end

defmodule Person.Functions do
  def get_ticket(person) do
    next_ticket = Tickets.get_ticket()
    %{ticket_num: next_ticket}
  end
end

Tickets.start_link(1)
person_1 = %{ticket_num: -1}
IO.inspect Person.Functions.get_ticket(person_1)
#output %{ticket_num: 1}
person_2 = %{ticket_num: -1}
IO.inspect Person.Functions.get_ticket(person_2)
# %{ticket_num: 2} 
```

现在这个越来越高级了，但这才是仙丹真正的妙处。在`start_link(1)`函数中，我们所做的是用一个名为 Tickets 的 GenServer 实例启动另一个进程，初始值为 1。

GenServers 可以做很多事情，但是这里我们用它来存储当前的票号。每次我们调用`get_ticket`时，代码都会传递给响应`{:reply, curr_ticket_num, curr_ticket_num + 1}`，这是一个发送给 GenServer 的命令，格式基本上是`{command_to_genserver, response_value, new_state_value}`。现在任何一个仙丹模块只要调用`Tickets.get_ticket`函数就可以得到一张票。

在大多数 OOP 语言中，启动另一个进程是一个非常高级的任务。事实上，一个开发人员可以在主进程中工作几年。然而，在 Elixir 中，与其他流程合作是一项基本技能。

如果你想了解更多关于 GenServers 的知识，我强烈推荐你自己写！这里有一个[教程](https://medium.com/qixxit-development/build-your-own-genserver-in-49-lines-of-code-1a9db07b6f13)

# 总结起来

当我回想起学习长生不老药的挑战时，我首先想到的就是这些。希望其中一个对你有帮助。

用 Elixir 编码很有趣！