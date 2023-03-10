# 得到这个的 wiff-rails conf 2016-Sandi Metz

> 原文：<https://dev.to/cjthedev/get-a-wiff-of-this---railsconf-2016---sandi-metz-50d9>

这是我的谈话笔记系列的第三篇文章。桑迪·梅斯是一个众所周知的名字。她已经帮助许多团队从软件腐烂中重构他们的系统。这些是我在她谈论代码气味的轨道上的笔记。

# 代码气味

“代码气味”是 Kent Beck 创造的一个术语，指的是代码中经常观察到的模式。

Martin Fowler 写了一本关于如何通过封装这些模式来避免重复的“重构”书。

代码气味根据相似的特征进行分组，如下所示:

## 腌鱼

*   长方法
*   大班
*   数据块
*   长参数列表
*   原始痴迷

## 工具滥用者

*   Switch 语句
*   被拒绝的遗留:子类覆盖基类方法并抛出一个异常来表示它没有实现该行为。
*   具有不同接口的可选类
*   临时字段:可以是一个方法

## 更换防护装置

*   不同的变更:变更需要在同一层级的不同分支进行修改。
*   猎枪手术
*   并行继承层次结构

他们的摩擦有时会改变，这很好。这要看上下文。

## 可有可无

*   懒惰的班级:做得不够的班级
*   推测性的一般性:对预期的未来用例进行抽象
*   数据类:具有耦合数据和行为的类
*   重复代码

## 车钩

*   特征嫉妒:向其他对象发送比自己更多的消息，即耦合
*   不适当的亲密:触及其他对象的私人方法
*   消息链:发送一条消息，接收一个对象，发送一条消息给接收的对象，如此循环。如果物体的类型在这两者之间发生了变化，那就更值得关注了。
*   中间人:唯一目的是将消息路由到其他对象的对象。

# 重构

重构是有名字的，它们有非常具体的配方。

每一种代码味道都包裹在精心策划的重构食谱中。这是一个已经解决的问题。

[气味到重构 Cheatsheet -工业逻辑](https://www.industriallogic.com/blog/smells-to-refactorings-cheatsheet/)

# 举例

```
class Sale < Persistence
end

class Expense < Persistence
end

class Foo
  def sales_total(params)
    Sale.where(date:(Date.parse(params[:starting]))..(Date.parse(params[:ending]))).sum('cost')
  end
end

class Bar
  def weekly_sales_total(params)
    start_date = (Date.parse(params[:starting]))
    end_date = start_date + 6
    Sale.where(date:start_date..end_date).sum("cost")
  end
end

class Baz
  def expense_total(params)
    start_date = (Date.parse(params[:starting])) rescue Date.today
    end_date = (Date.parse(params[:ending]) rescue start_date
    Expense.where(date:start_date..end_date).sum("cost")
  end
end 
```

## 数据丛

这段代码有一个日期范围的数据块，在进行“提取类”重构后，代码看起来像这样。

```
class DateRange
  attr_reader :starting, :ending
  def initialiaze(starting:, ending: nil)
    @starting = Date.parse(starting) rescue Date.today
    @ending = Date.parse(ending) rescue @starting
  end

  def range
    starting..ending
  end

  def week_range
    starting..(starting + 6)
  end
end

class Sale < Persistence
end

class Expense < Persistence
end

class Foo
  def sales_total(params)
    range = DateRange.new(starting: params[:starting], ending: params[:ending]).range
    Sale.where(date:range).sum('cost')
  end
end

class Bar
  def weekly_sales_total(params)
    range = DateRange.new(starting: params[:starting]).week_range
    Sale.where(date:range).sum('cost')
  end
end

class Baz
  def expense_total(params)
    range = DateRange.new(starting: params[:starting], ending: params[:ending]).range
    Expense.where(date: range).sum("cost")
  end
end 
```

请注意，行为是在类内部合并的，并且被隔离在一个地方。

## 消息链

`Foo`、`Bar`和`Baz`有消息链，即调用`Expense`和`Sale`返回的对象。现在，这些类与它们的依赖项的内部工作相结合。

当你测试时，你应该只需要模仿对象的直接合作者，但是在这种情况下，我们必须模仿返回的对象。这通常意味着我们应该在方法中提取模式。

```
class DateRange
  attr_reader :starting, :ending

  def initialiaze(starting:, ending: nil)
    @starting = Date.parse(starting) rescue Date.today
    @ending = Date.parse(ending) rescue @starting
  end

  def range
    starting..ending
  end

  def week_range
    starting..(starting + 6)
  end
end

class Sale < Persistence
 def self.total(within:)
   where(date: within).sum("cost")
 end
end

class Expense < Persistence
 def self.total(within:)
   where(date: within).sum("cost")
 end
end

class Foo
  def sales_total(params)
    range = DateRange.new(starting: params[:starting], ending: params[:ending]).range
    Sale.total(within: range)
  end
end

class Bar
  def weekly_sales_total(params)
    range = DateRange.new(starting: params[:starting]).week_range
    Sale.total(within: range)
  end
end

class Baz
  def expense_total(params)
    range = DateRange.new(starting: params[:starting], ending: params[:ending]).range
    Expense.total(within: range)
  end
end 
```

我们已经提取了`total`方法内部的链。

相关:你要测试的是取舍。你只能在你的测试中模仿/引入这么多的现实。

【2012 年落基山红宝石——嘲笑还是不嘲笑贾斯汀·瑟尔斯

将你的测试耦合到非常远的对象上，那么测试套件会变得很慢。如果你把你的对象和慢的东西结合起来，那么你的测试会变得更慢。

使用“依赖注入”,我们可以模仿对象扮演的“角色”,而不是它们是什么类。这就是我们在进行面向对象编程时应该思考的方式。

## 代码重复

`Expense`和`Sale`都实现了相同的`total`方法。这种重复可以通过“拉起法”重构来消除。猴子给`Persistence`打补丁看起来是个坏主意。因此，我们将模块内部的逻辑取出，并从两个类中扩展它。

```
class DateRange
  attr_reader :starting, :ending
  def initialiaze(starting:, ending: nil)
    @starting = Date.parse(starting) rescue Date.today
    @ending = Date.parse(ending) rescue @starting
  end

  def range
    starting..ending
  end

  def week_range
    starting..(starting + 6)
  end
end

module Totalable
  def total(within:, date_field: :date, on: "cost")
    where({date_field => within}).sum(on)
  end
end

class Sale < Persistence
 extend Totalable
end

class Expense < Persistence
 extend Totalable
end

class Foo
  def sales_total(params)
    range = DateRange.new(starting: params[:starting], ending: params[:ending]).range
    Sale.total(within: range)
  end
end

class Bar
  def weekly_sales_total(params)
    range = DateRange.new(starting: params[:starting]).week_range
    Sale.total(within: range)
  end
end

class Baz
  def expense_total(params)
    range = DateRange.new(starting: params[:starting], ending: params[:ending]).range
    Expense.total(within: range)
  end
end 
```

我们已经将`total`转换成一个更通用的 API。这可以被看作是“推测性的一般性”，但是直到方法很小，这是一个很好的权衡，因为灵活性比增加的复杂性大得多。

[链接到原发言](https://www.youtube.com/watch?v=PJjHfa5yxlU)

请务必查看我在该系列中的其他帖子:)