# 让我们用栈和队列来狂欢吧！Pt 3

> 原文：<https://dev.to/aritdeveloper/let-s-party-with-stacks-and-queues-pt-3-40b5>

在第 2 部分中，我们举行了一个全面的聚会！但事情是方便的；因为我们为每位客人准备了一顿饭，所以最终每个人都得到了服务。现在我们将讨论一个可能的场景——没有客人想要的顶级大餐:

```
my_party = Party.new(
    %w[vegetarian vegan keto normal paleo],
    %w[normal paleo vegan vegetarian]) 
```

Enter fullscreen mode Exit fullscreen mode

控制台输出:

```
Party Status:
 Meals: ["paleo", "normal", "keto", "vegan", "vegetarian"]
 Guests: ["normal", "paleo", "vegan", "vegetarian"]

No one was served the last round

Party Status:
 Meals: ["paleo", "normal", "keto", "vegan", "vegetarian"]
 Guests: ["paleo", "vegan", "vegetarian", "normal"]

Guest wanting a paleo meal has been served

Party Status:
 Meals: ["normal", "keto", "vegan", "vegetarian"]
 Guests: ["vegan", "vegetarian", "normal"]

No one was served the last round

Party Status:
 Meals: ["normal", "keto", "vegan", "vegetarian"]
 Guests: ["vegetarian", "normal", "vegan"]

No one was served the last round

Party Status:
 Meals: ["normal", "keto", "vegan", "vegetarian"]
 Guests: ["normal", "vegan", "vegetarian"]

Guest wanting a normal meal has been served

Party Status:
 Meals: ["keto", "vegan", "vegetarian"]
 Guests: ["vegan", "vegetarian"]

No one was served the last round

Party Status:
 Meals: ["keto", "vegan", "vegetarian"]
 Guests: ["vegetarian", "vegan"]

No one was served the last round

. . . . <hundreds of lines later> . . . . .

Party Status:
 Meals: ["keto", "normal", "vegetarian"]
 Guests: ["vegan", "vegetarian", "normal"]

No one was served the last round

Traceback (most recent call last):
    11913: from stacksandqueues2.rb:104:in `<main>'
    11912: from stacksandqueues2.rb:97:in `meal_service'
    11911: from stacksandqueues2.rb:91:in `move_queue'
    11910: from stacksandqueues2.rb:91:in `move_queue'
    11909: from stacksandqueues2.rb:91:in `move_queue'
    11908: from stacksandqueues2.rb:91:in `move_queue'
    11907: from stacksandqueues2.rb:91:in `move_queue'
    11906: from stacksandqueues2.rb:91:in `move_queue'
     ... 11901 levels...
        4: from stacksandqueues2.rb:91:in `move_queue'
        3: from stacksandqueues2.rb:82:in `move_queue'
        2: from stacksandqueues2.rb:74:in `view_party'
        1: from stacksandqueues2.rb:21:in `display_meals'
stacksandqueues2.rb:21:in `inspect': stack level too deep (SystemStackError) 
```

Enter fullscreen mode Exit fullscreen mode

Welp！发生了什么事？嗯，我们得到了一个错误，因为队列不断循环，将不同的客人与“酮”餐对齐，但没有匹配。我们需要一种方法来扔掉这种不想要的食物，但只有在确保没有客人偏好酮餐之后。

我们知道，如果整个队列循环了一次，顶餐是不需要的。所以我们需要记录有多少客人拒绝了顶餐；当这个数字等于排队的客人数时，我们知道没有人想要我们的顶餐。

让我们引入一个`rejected`计数器作为`move_queue`方法的参数。该计数器最初被设置为零。对于每一个餐饮客人不匹配，`rejected`增加 1。然后将`rejected`与队列大小进行比较；如果两个值相等，则顶层膳食被“丢弃”，并且`rejected`被重置为 0:

```
def move_queue(rejected=0)
    view_party

    if match?
      meals.take_meal
      served = guests.leave_queue
      puts "Guest wanting a #{served} meal has been served\n\n"
    else
      rejected += 1
      guests.rejoin_queue
      puts "No one was served the last round\n"
      puts "Top Meal Rejections: #{rejected}\n\n"

      if rejected == guests.size
        rejected = 0
        trashed_meal = meals.take_meal

        puts "Unwanted #{trashed_meal} meal has been trashed!\n\n"
      end
      move_queue(rejected)
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

```
Party Status:
 Meals: ["paleo", "keto", "normal", "vegetarian"]
 Guests: ["normal", "paleo", "vegan", "vegetarian"]

No one was served the last round
Top Meal Rejections: 1

Party Status:
 Meals: ["paleo", "keto", "normal", "vegetarian"]
 Guests: ["paleo", "vegan", "vegetarian", "normal"]

Guest wanting a paleo meal has been served

Party Status:
 Meals: ["keto", "normal", "vegetarian"]
 Guests: ["vegan", "vegetarian", "normal"]

No one was served the last round
Top Meal Rejections: 1

Party Status:
 Meals: ["keto", "normal", "vegetarian"]
 Guests: ["vegetarian", "normal", "vegan"]

No one was served the last round
Top Meal Rejections: 2

Party Status:
 Meals: ["keto", "normal", "vegetarian"]
 Guests: ["normal", "vegan", "vegetarian"]

No one was served the last round
Top Meal Rejections: 3

Unwanted keto meal has been trashed!

Party Status:
 Meals: ["normal", "vegetarian"]
 Guests: ["vegan", "vegetarian", "normal"] 
```

Enter fullscreen mode Exit fullscreen mode

厉害！我们不想要的酮餐已经被扔掉，膳食服务可以不受阻碍地继续——希望如此！

现在，是的，是的，我听到你们所有的时间复杂性爱好者:

> “这种反制方法可能适用于小型聚会，但如果你有 500 名或更多的客人呢？我们*真的*要循环通过他们所有的垃圾*一个*餐吗？”

你说得对——那会感觉像永远一样，让我们的客人无休止地烦恼。那么，我们(令人愉快的聚会主持人)站在餐台旁边，一旦第一个客人拒绝了一顿饭，我们就对所有人大喊:“嘿！你们有人想要这种类型的饭吗？”如果没人回答，我们就扔了它。让我们创建必要的方法:

```
# Add this method to the GuestQueue class:
def include?(meal)
    guests.include?(meal)
end

# Add this method to the Party class:
def does_any_guest_want(meal)
    guests.include?(meal)
end

# Modify the 'Party#move_queue' method to the following:
def move_queue
    view_party

    if match?
      meals.take_meal
      served = guests.leave_queue
      puts "Guest wanting a #{served} meal has been served\n\n"
    else
        puts "No one was served the last round\n"

        if does_any_guest_want(meals.top_meal)
            guests.rejoin_queue
        else
            trashed_meal = meals.take_meal
            puts "Unwanted #{trashed_meal} meal has been trashed!\n\n"
        end
        move_queue
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

另一个要探索的场景是，当饭菜都吃完了，但仍有客人在等着上菜。我会留给你:)就像，也许是一种方法，堆叠额外的饭菜来服务剩下的客人？

* * *

我真的希望这篇教程能让堆栈和队列的概念更加充实！请在评论中告诉我你的想法。非常感谢你一直支持我。