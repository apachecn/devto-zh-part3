# 支架学和二叉树

> 原文：<https://dev.to/loganwohlers/bracketology-and-binary-trees-1oen>

上周，在一个项目中，我和我的合作伙伴在 Rails 中建立了一个支架模拟器网站，使用一种叫做 Faker 的宝石播种假球员/球队，然后在播种支架之前随机化他们的统计数据。本周我想强调其中的一些代码——以及我所做的一些新的调整，这些调整允许通过二叉树递归地创建括号。播种一个支架被证明是容易的——但是创建实际的合适的比赛最终有点像噩梦。

该项目的最终目标是创建一个所有团队的数组-按实际括号中的比赛排序-删除前两个团队，让他们“玩”并让获胜者排队-因此，考虑到这种方法，我将通过实际排序适当括号中的困难来运行-作为参考，数组形式的正确种子 8 团队括号看起来像这样:

## 【1，8，4，5，3，6，2，7】

这个顺序将导致在第一轮比赛中进行正确的比赛，以及它们在括号中的正确位置，以确保后面的比赛能够正确进行。

让我们从几乎立即失败的最初想法开始——我们创建了一个所有球队的数组，根据他们的“实力得分”从高到低排序——为了获得合适的比赛，我们所做的就是迭代数组，并拉出第一个可用的球队和最后一个可用的球队——虽然这有助于创建合适的比赛——但它在实际创建一个括号方面失败了——因为 1 号和 2 号种子在第二轮比赛中相遇——而不是决赛。这导致了什么:

## 【1，8，2，7，3，6，4，5】

正确的比赛，但顺序错误-即 1v8 和 2v7 的获胜者将参加第 2 轮比赛(而不是第 3 轮/决赛)

在许多括号被抽出之后，一些模式被发现:
-在第一轮中，两个队的种子总是加起来等于参赛人数+1(即在 8 个队中，将种子放在 9 个括号中)

-根据以上内容，您可以通过做
(进入者+1)-当前种子来找到对手

-任何锦标赛的回合数= log2(参赛者)

考虑到以上因素，扩展支架成为可能

-为了进行新一轮比赛，我们将获取一个种子，并在括号大小加倍的情况下找出比赛结果-因此，从 2 队到 4 队的比赛-1 队现在将进行 4 个种子(通过#teams*2+1-seed(本例中为 2*2+1-1)计算)，结果为 4。在 2 号种子上的相同数学导致他们玩 3 号种子)。

在我们的项目中，我们使用这个过程来创建一个比赛阵列——开始时有 4 个种子队阵列，然后根据参赛者的数量进行多次扩展:

```
def double_bracket(bracket)
    doubled_size=bracket.length*2
    doubled=[]
    bracket.each do |seed|
        doubled.push(seed)
            doubled.push((doubled_size+1)-seed)
    end
    doubled
end

def bracket_order(entrants)
    starting=[1,4,3,2]
if entrants==4
        return starting
    else        
        for i in 1..(Math.log2(entrants)-2)
            starting=double_bracket(starting)
        end
    return starting
    end     
end 
```

然后，使用正确排序的种子数组(bracket_order ),我们可以将有序的团队对象数组(ordered_teams)映射到它上面——以获得包含正确排序的团队对象的最终数组——这将用于模拟锦标赛

```
def map_bracket (ordered_teams, bracket_order)
for i in 0..(bracket_order.length-1)
  team=ordered_teams[(bracket_order[i]-1)]
  bracket_order[i]=team
end
    bracket_order 
end 
```

然而，我们不认为括号像一个数组——我们这样认为(也许转了 90 度)

```
 [1, 2]---------------------2
                      /   \
                 [1,4]     [2,3]----------------4   
                 /  \       /  \
             [1,8] [4,5] [2,7] [3,6]------------8 
```

-看起来和二叉树非常相似...

* * *

二叉树和递归

我试图用二叉树在 Ruby 中复制上述内容——使用每个比赛的数组作为节点数据。

首先，我创建了一个 BracketNode 类和一个 BracketTree 类

```
class BracketNode
    attr_accessor :data, :left, :right
    def initialize (data=nil, left=nil, right=nil)
      @data=data
      @left=left
      @right=right
    end
end

class BracketTree
  attr_reader :root
  def initialize (overall_root=nil)
    @overall_root=overall_root
  end 
end 
```

然后，我用[1，2] matchup 初始化一个节点，用作根节点，并从那里递归展开。

```
def build_bracket(node, entrants, curr_level)
    if curr_level<=Math.log2(entrants)
        node.left = BracketNode.new([node.data[0],((2**curr_level+1)-node.data[0])])                                      
        node.right = BracketNode.new([node.data[1],((2**curr_level+1)-node.data[1])])                    
        build_bracket(node.left, entrants, curr_level+1)
        build_bracket(node.right, entrants, curr_level+1)
      end
  nil
end 
```

这看起来有点荒谬——但上面的模式都是一样的——获取每场比赛的种子，并从 log2(参赛者)+1 中减去它，以找到他们的对手种子——这次递归地构建括号节点，而不是推入一个数组，直到达到所需的参赛者数量。

然后有一些辅助方法——一个用来计算树的高度，另一个用来打印当前级别上的所有节点——通过组合这些方法，下面的函数打印出每轮之间有一条线的所有比赛

```
def print_all_levels (node)
  h=self.height(node)
  for i in 1..h
    print_level(node, i, 1)
    puts "----------------------"
  end
end 
```

实际创建一个括号树并打印它的比赛的最终代码看起来像这样

```
root=BracketNode.new([1,2])
bracket=BracketTree.new(root)
# #start on level "2" as level 1 is the [1,2] (aka the 'final')
bracket.build_bracket(root, 64, 2)
bracket.print_all_levels(root) 
```

再次尝试使用二叉树很有趣，因为它们在 ruby 中是找不到的——递归地构建括号树最终成为一个巨大的挑战，并再次提醒我递归的力量。如果您看到任何反馈或实际代码中的改进空间，请留下！

感谢阅读，

洛根

链接到支架模拟器:[https://github.com/loganwohlers/Mod2Final](https://github.com/loganwohlers/Mod2Final)