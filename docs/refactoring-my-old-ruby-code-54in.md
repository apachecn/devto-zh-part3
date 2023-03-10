# 重构我的旧 Ruby 代码

> 原文：<https://dev.to/clairemuller/refactoring-my-old-ruby-code-54in>

在我的第一篇博文中，我认为回顾和重构我刚开始学习 Ruby 时写的一些代码会很有趣。这是任何熨斗学校的学生都会记得的一课: [Hashketball](https://learn.co/lessons/advanced-hashes-hashketball) 。

首先，给你一堆关于两支篮球队的信息，并让你建立一个嵌套散列。然后，指导您编写返回某个球员或球队的某些信息的方法。例如:player_numbers 接受一个球队名称的参数，并返回该队球衣号码的数组。

当时，这一课对我来说真的很难。我以前从未使用过如此大的嵌套散列，对这个想法来说是相当新的。你被指示使用。each 和 binding.pry 熟悉迭代。这在当时是很棒的，因为它帮助我了解了从迭代中可以期待什么，并且我非常习惯使用 binding.pry。

有七种方法可以编写，我的原始代码有 128 行，有大量的重复和 if 语句。现在我学习 Ruby 已经几个月了，我知道有更好、更有效的方法来编写这段代码。所以我要用我的 helper 方法知识来缩短它！

* * *

我想做的第一件事是创建一些助手方法，让我自己做任何事情都更容易。我之前的代码让我遍历游戏散列，然后遍历数据，然后遍历数据…但是现在我知道了编程规则干，不要重复自己。

我准备写一个方法，all_players，简单的取主队球员和客场球员，合二为一。

```
def all_players
  game_hash[:home][:players].merge(game_hash[:away][:players])
end 
```

现在我可以用一种方法访问所有的球员和他们的数据，不再需要重复迭代。我将使用这个方法来重构 player_stats 方法。这个函数接受一个玩家名字的参数，只返回他们的统计数据。

```
def player_stats(name)
  all_players[name.to_sym]
end 
```

这个方法是测试的一部分，但它本身是一个辅助方法。我可以用它来简化 num_points_scored 和 shoe_size，使它们更容易阅读。它们都接受一个玩家名字的参数，并返回它们的一个统计数据。

```
def num_points_scored(name)
  player_stats(name)[:points]
end

def shoe_size(name)
  player_stats(name)[:shoe]
end 
```

多美啊！多干净啊！接下来，我们有一些方法采用一个团队名称参数，所以我将为此创建两个助手。一个返回团队的数据，另一个接受团队名称的参数，只返回其数据。

```
def all_teams
  game_hash.values
end

def find_team(team_name)
  all_teams.find { |team| team[:team_name] == team_name }
end 
```

太好了，现在我可以重构 team_names 方法了。这个函数只返回两个团队的名字，所以我可以使用我的 all_teams 辅助方法。

```
def team_names
  all_teams.map { |team| team[:team_name] }
end 
```

好满足！现在我可以使用我的另一个助手 find_team 来缩短球队颜色和球员号码。

```
def team_colors(team_name)
  find_team(team_name)[:colors]
end

def player_numbers(team_name)
  find_team(team_name)[:players].map {|name, stats| stats[:number]}
end 
```

最后，我们有怪物方法大 _ 鞋 _ 篮板，这只是痛苦地看:

```
def big_shoe_rebounds
  shoe_sizes_w_names = {}
  game_hash.each do |location, team_data|
    team_data.each do |attribute, data|
      if attribute == :players
        data.each do |name, stats|
          stats.each do |k, v|
            if k == :shoe
              shoe_sizes_w_names[name] = v
            end
          end
        end
      end
    end
  end
  player_w_big_shoes = shoe_sizes_w_names.key(shoe_sizes_w_names.values.max)
  game_hash.each do |location, team_data|
    team_data.each do |attribute, data|
      if attribute == :players
        data.each do |name, stats|
          if name == player_w_big_shoes
            stats.each do |k, v|
              if k == :rebounds
                return v.to_i
              end
            end
          end
        end
      end
    end
  end
end 
```

呀。一个方法有 34 行！这里最大的问题是这个方法做了两件不同的事情:

1.  找到鞋码最大的球员
2.  找出那个球员的篮板数

编程中有一个哲学:方法应该做一件事，并且做好。考虑到这一点，我决定首先创建一个方法来找到鞋码最大的玩家。

```
def player_w_biggest_feet
  all_players.max_by { |name, stats| stats[:shoe] }
end 
```

现在我可以用这种方法找到自己的篮板，并通过最后的测试。然而，如果我想的话，我也可以用它来查找任何关于脚最大的球员的数据。

```
def big_shoe_rebounds
  player_w_biggest_feet[1][:rebounds]
end 
```

就是这样！最后，我把我的代码从 128 行减少到 43 行，每个方法只有 3 行。结果是一样的，但是现在代码更容易阅读和理解。

* * *

总的来说，我第一次上这一课的时候学到了很多关于 Ruby 的基础知识。知道怎么做。每个作品都非常重要，为理解更抽象的方法打下了坚实的基础。map 和. find。重构和第二次使用它比我想象的有趣多了！回顾您的早期代码可能有点令人畏缩，但从中可以学到很多东西。感谢阅读！