# 让我们用栈和队列来狂欢吧！Pt 2

> 原文：<https://dev.to/aritdeveloper/let-s-party-with-stacks-and-queues-pt-2-jol>

在第 1 部分中，我们为我们的一堆饭菜和客人队列配置了类。现在让我们设置我们的 Party 类，它接受我们的餐饮和客人数组，并初始化 MealStack 和 GuestQueue 的实例。

```
class Party
  attr_accessor :meals, :guests

  def initialize(meals_array, guest_array)
    self.meals = MealStack.new(meals_array)
    self.guests = GuestQueue.new(guest_array)
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

在聚会期间的任何时候查看食物和客人都很好，所以让我们为此创建一个`view_party`方法:

```
def view_party
    puts "Party Status:\n Meals: #{meals.display_meals}\n Guests: #{guests.display_queue}\n\n"
end 
```

Enter fullscreen mode Exit fullscreen mode

为了给顾客提供合适的服务，排在最前面的餐点必须与队列中第一个顾客的餐点相匹配。所以我们创建了一个方法`match?`来检查这个:

```
def match?
    meals.top_meal == guests.first_guest
end 
```

Enter fullscreen mode Exit fullscreen mode

接下来我们创建一个`move_queue`方法，它做三件事:

*   向我们显示聚会状态
*   删除已被成功服务的客人
*   当第一餐不符合他们的偏好时，将队列前面的客人移到后面

我们也包括了一些陈述来提示我们正在发生的事情。

对于餐客匹配，最上面的餐被移走，第一个客人离开队列。对于不匹配，第一个客户移动到队列的后面，并为更新后的第一个客户再次调用`move_queue`方法:

```
def move_queue
    view_party

    if match?
      meals.take_meal
      served = guests.leave_queue
      puts "Guest wanting a #{served} meal has been served\n\n"
    else
      guests.rejoin_queue
      puts "No one was served the last round\n\n"
      move_queue
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

最后，我们将创建一个`meal_service`方法来启动一切。用餐服务将一直运行，直到所有的餐点都用完，或者所有的客人都被服务完。用餐服务结束后会生成一份可爱的“派对报告”:

```
def meal_service
    until meals.stack_empty? || guests.queue_empty?
      move_queue
    end
    puts "Meals Left: #{meals.display_meals}\nUnserved Guests: #{guests.display_queue}"
end 
```

Enter fullscreen mode Exit fullscreen mode

厉害！让我们用下面的食物和客人建立一个聚会:

```
my_party = Party.new(%w[vegetarian vegan normal paleo], %w[normal paleo vegan vegetarian])

my_party.meal_service 
```

Enter fullscreen mode Exit fullscreen mode

这里是完整的文件:

```
class MealStack
  attr_accessor :meals

  def initialize(meals_array)
    self.meals = meals_array
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

class Party
  attr_accessor :meals, :guests

  def initialize(meals_array, guest_array)
    self.meals = MealStack.new(meals_array)
    self.guests = GuestQueue.new(guest_array)
  end

  def view_party
    puts "Party Status:\n Meals: #{meals.display_meals}\n Guests: #{guests.display_queue}\n\n"
  end

  def match?
    meals.top_meal == guests.first_guest
  end

  def move_queue
    view_party

    if match?
      meals.take_meal
      served = guests.leave_queue
      puts "Guest wanting a #{served} meal has been served\n\n"
    else
      guests.rejoin_queue
      puts "No one was served the last round\n\n"
      move_queue
    end
  end

  def meal_service
    until meals.stack_empty? || guests.queue_empty?
      move_queue
    end
    puts "Meals Left: #{meals.display_meals}\nUnserved Guests: #{guests.display_queue}"
  end
end

my_party = Party.new(%w[vegetarian vegan normal paleo], %w[normal paleo vegan vegetarian])

my_party.meal_service 
```

Enter fullscreen mode Exit fullscreen mode

以下是我在终端中运行程序文件后的输出:

```
Party Status:
 Meals: ["paleo", "normal", "vegan", "vegetarian"]
 Guests: ["normal", "paleo", "vegan", "vegetarian"]

No one was served the last round

Party Status:
 Meals: ["paleo", "normal", "vegan", "vegetarian"]
 Guests: ["paleo", "vegan", "vegetarian", "normal"]

Guest wanting a paleo meal has been served

Party Status:
 Meals: ["normal", "vegan", "vegetarian"]
 Guests: ["vegan", "vegetarian", "normal"]

No one was served the last round

Party Status:
 Meals: ["normal", "vegan", "vegetarian"]
 Guests: ["vegetarian", "normal", "vegan"]

No one was served the last round

Party Status:
 Meals: ["normal", "vegan", "vegetarian"]
 Guests: ["normal", "vegan", "vegetarian"]

Guest wanting a normal meal has been served

Party Status:
 Meals: ["vegan", "vegetarian"]
 Guests: ["vegan", "vegetarian"]

Guest wanting a vegan meal has been served

Party Status:
 Meals: ["vegetarian"]
 Guests: ["vegetarian"]

Guest wanting a vegetarian meal has been served

Meals Left: []
Unserved Guests: [] 
```

Enter fullscreen mode Exit fullscreen mode

耶！每个人都得到了招待，我们的聚会很成功！但是让我们想一分钟:尽管我们的一些客人由于用餐不匹配而不得不重新排队，但每个人最终都得到了服务，因为每个客人都有一份餐。

如果我们的书架上有一顿没人要的饭呢？嗯，由于堆栈的工作方式，我们无法访问不需要的饭菜下的任何饭菜，直到我们摆脱了所说的饭菜。然而，我们不想浪费任何顶级大餐，直到我们*确定*没有客人想要它。这就是我们将在第 3 部分看到的...