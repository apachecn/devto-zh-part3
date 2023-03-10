# 让我们用栈和队列来狂欢吧！Pt 1

> 原文：<https://dev.to/aritdeveloper/let-s-party-with-stacks-and-queues-pt-1-35g>

我已经阅读了一些关于堆栈和队列的优秀的书面/视频教程，以及它们的特点和局限性。然而，除了定义它们的类和相关方法之外，我还没有见过很多实现这两种数据结构的例子。

所以我创建了一个 Ruby 教程，它在一个例子中实现了堆栈和队列，在这个例子中，在一个聚会上向一排客人提供食物！

在本教程中，我们将创建一个简短的程序，它采用两个数组(一堆饭菜和客人队列),并根据最上面的饭菜是否符合队列中第一个客人的烹饪偏好来推进队列。

听起来很有趣？让我们开始吧！

* * *

让我们首先构建 MealStack 类。这个类将接受一系列由其类型标识的膳食(`meals_array`)(比如‘素食’，‘普通’，‘纯素食’)。

```
class Stack
  attr_accessor :meals

  def initialize(meals_array)
    self.meals = meals_array
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们希望能够通过 MealStack 实现以下功能:

*   看看栈顶是什么饭(`top_meal`)，也就是数组的最后一项:

```
 def top_meal
        meals.last
    end 
```

Enter fullscreen mode Exit fullscreen mode

*   从堆叠中取出最上面的食物(`take_meal`):

```
 def take_meal
        meals.pop
    end 
```

Enter fullscreen mode Exit fullscreen mode

*   知道是否所有的饭都吃完了(`stack_empty?`):

```
 def stack_empty?
        meals.empty?
    end 
```

Enter fullscreen mode Exit fullscreen mode

*   查看堆栈中所有剩余的餐食(`display_meals`)。我们在`meals`上调用`.reverse`，这样最后一个数组条目首先出现(作为堆栈的顶部):

```
 def display_meals
        meals.reverse.inspect
    end 
```

Enter fullscreen mode Exit fullscreen mode

*   知道栈中有多少餐(`size`):

```
 def size
        meals.length
    end 
```

Enter fullscreen mode Exit fullscreen mode

下面是完整的 MealStack 类:

```
class MealStack
  attr_accessor :meals

  def initialize(meals_array)
    meals = meals_array
  end

  def top_meal
    meals.last
  end

  def take_meal
    meals.pop
  end

  def stack_empty?
    meals.empty?
  end

  def display_meals
    meals.reverse.inspect
  end

  def size
    meals.length
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

* * *

现在让我们构建 GuestQueue 类，该类接受一组根据用餐偏好确定的客人。为简单起见，客人的膳食偏好与膳食排列相同——“素食”、“纯素”等。

```
class GuestQueue
  attr_accessor :guests

  def initialize(guest_array)
    self.guests = guest_array
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

我们希望能够使用队列堆栈完成以下任务:

*   知道谁在排队(`first_guest`):

```
 def first_guest
    guests.first
 end 
```

Enter fullscreen mode Exit fullscreen mode

*   让游客能够加入队列(`join_queue(guest)`):

```
 def join_queue(guest)
    guests.push(guest)
  end 
```

Enter fullscreen mode Exit fullscreen mode

*   让游客离开队伍(`leave_queue`):

```
 def leave_queue
    guests.shift
  end 
```

Enter fullscreen mode Exit fullscreen mode

*   让游客能够重新加入队列(`rejoin_queue`):

```
 def rejoin_queue
        join_queue(leave_queue)
    end 
```

Enter fullscreen mode Exit fullscreen mode

*   了解是否所有客人都被招待过(`queue_empty?`):

```
 def queue_empty?
        guests.empty?
    end 
```

Enter fullscreen mode Exit fullscreen mode

*   查看队列中所有剩余的游客(`display_queue`):

```
 def display_queue
        guests.inspect
    end 
```

Enter fullscreen mode Exit fullscreen mode

*   知道有多少客人在排队(`size`):

```
 def size
        guests.length
    end 
```

Enter fullscreen mode Exit fullscreen mode

下面是完整的 GuestQueue 类:

```
class GuestQueue
    attr_accessor :guests

    def initialize(guest_array)
        self.guests = guest_array
    end

    def first_guest
        guests.first
    end

    def join_queue(guest)
        guests.push(guest)
    end

    def leave_queue
        guests.shift
    end

    def rejoin_queue
        join_queue(leave_queue)
    end

    def queue_empty?
        guests.empty?
    end

    def display_queue
        guests.inspect
    end

    def size
        guests.length
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

* * *

最后，让我们确保我们的 MealStack 和 GuestQueue 类正常工作:

```
my_meals = MealStack.new(%w[vegetarian vegan normal paleo vegan normal paleo])

puts my_meals.size # 7
puts my_meals.top_meal # paleo
puts my_meals.take_meal # paleo
puts my_meals.stack_empty? # false
puts my_meals.take_meal # normal
puts my_meals.take_meal # vegan
puts my_meals.size # 4

- - -

my_friends = GuestQueue.new(%w[normal paleo vegan vegetarian vegan normal paleo])

puts my_friends.size # 7
puts my_friends.first_guest # normal
puts my_friends.leave_queue # normal
puts my_friends.leave_queue # paleo
puts my_friends.size # 5
puts my_friends.rejoin_queue # vegan
puts my_friends.leave_queue # vegetarian
puts my_friends.first_guest # vegan 
```

Enter fullscreen mode Exit fullscreen mode

* * *

在下一部分中，当我们构建我们的党课时，事情变得有趣了！