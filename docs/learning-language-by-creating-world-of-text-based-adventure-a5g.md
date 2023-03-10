# 通过创造基于文本的冒险世界来学习语言

> 原文：<https://dev.to/aandreev0/learning-language-by-creating-world-of-text-based-adventure-a5g>

[![logo](img/3cafd0485ab1e1eda3f93c1721db4e63.png "coverImage")](https://res.cloudinary.com/practicaldev/image/fetch/s--Z2DrHZI6--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_880/https://thepracticaldev.s3.amazonaws.com/i/nv9zxd4oeog6hwlro7zo.jpg)

我最近开始了小 Python 项目——基于文本的冒险。像计算机一样古老的游戏类型，这种类型的游戏非常容易开发，而且开发过程是一种有益的学习和创造性的体验。

现代语言让编程过程接近无痛苦。这并不意味着你可以放松对世界和底层代码的设计和规划。它允许你逐渐在你的世界中增加物品，玩家可以用新的方式与他们互动，创造任务和谜题。

在这里，我想展示这个过程的前几个步骤，无对象的空白是如何被新元素填充的。

游戏设计是逃生室。玩家的角色发现自己在一个关着门的实验室里。为了赢得游戏，他们必须与物体互动，找到打开门的方法，然后离开实验室。每一次与世界互动的尝试都算作一次“转向”。玩家通过输入英语命令与世界互动。游戏在终端运行。

我们从简单的实现开始，玩家可以选择`look`或`exit the lab`来赢得:

```
def parseInput(s):
    if s == "look":
        return True, "You are in the middle of laboratory. There is a lot of equipment and door leading outside"
    elif s =="exit the lab":
        return False, "Congratulations, you've escaped the lab!"
    else:
        return True, "Sorry, I don't know what you mean."

game = True
turns = 0
while game:
    inp = raw_input(">") # here we ask user to type something
    turns += 1
    game, result = parseInput(inp)
    print ("%d$\n%s" % (turns, result)) # this is called String Formatting print "Game over." 
```

现在，我们可以开始向房间添加一些对象。假设钥匙在书桌抽屉里。

让我们开始以面向对象的方式添加一些对象。这个类将包含这个小世界中的一切，包括角色本身。首先，我们希望能够命名对象，在对象中存储内容，并且能够看到对象内部的内容。

```
class Object():
    def __init__(self,description):
        self.contents = []
        self.description = description

    def add_item(self,item):
        self.contents.append(item)
        return True

    def remove_item(self,item):
        if item in self.contents:
            self.contents.remove(item)
            return True
        else:
            return False

    def inspect(self):
        return "\n".join(map(lambda x: x.description, self.contents)) 
```

在这个阶段，我们已经学习了现代语言的小魔法，即`map`:它允许我们在给定的对象中构造条目列表，通过将对象列表转换成它们的描述列表。我们在这里还使用了匿名(lambda)函数，这是一个极其有用的实用工具。

```
# if items are: [red hat, blue apron, green sunglasses]
# then map(lambda x: x.color, items)
# equals to [red, blue, green] 
```

让我们看看`Object`类允许我们做什么:

```
from Object import Object

room = Object('Big laboratory room with one door')

desk   = Object('Desk with one drawer')
drawer = Object('Drawer with some stuff inside')
desk.add_item(drawer)

key    = Object('Key to the door')
papers = Object('Bunch of useless papers')
drawer.add_item(key)
drawer.add_item(papers)

player = Object('You, trying to escape this weird place')

room.add_item(desk)
room.add_item(player) 
```

这段代码在我看来完全可读，它创建了空间，在其中创建了一些项目，并把它们放在正确的容器中。现在，如果你想看看房间里有什么，你可以简单地调用`room.inspect()`并获得房间里物品的描述列表:

```
if s == "look":
        room_description = "You are in the middle of laboratory. There is a lot of equipment and door leading outside.\nIn this room:\n"
        room_description += room.inspect()
        return True, room_description
# >look
# 1$
# You are in the middle of laboratory. There is a lot of equipment and door leading # outside.
# In this room:
# Desk with one drawer
# You, trying to escape this weird place 
```

让我们加固一下门，结束我们的逃生室。它不应该这么容易退出，所以我们将创建新的对象，并将其与世界挂钩:

```
door = Door('Door leading to freedom', key)
room.add_item(door)

[...skipped...]

    elif s =="exit the lab":
        if door.opened:
            return False, "Congratulations, you've escaped the lab!"
        else:
            return True, "Door is closed" 
```

现在，这个`Door`到底是什么东西？嗯，这是一个学习 Python 中继承的机会。这个物体看起来像这样:

```
from Object import Object
class Door(Object):
    def __init__(self,description,key):
        # Door is just another Object, so we initialize it as such
        Object.__init__(self,description)
        # but also Door has unique properties, such as key that unlocks this particular door
        self.key = key
        self.unlocked = False
        self.opened = False

    def open(self):
        if self.unlocked:
            if not self.opened:
                self.opened = True
                return "Door is now open"
            else:
                return "Door is already open"
        else:
            return "Door is locked"

    def unlock(self, who):
        if self.key not in who.contents:
            return False
        else:
            self.unlocked = True
            return True 
```

现在，我要去吃早餐，并停止在观察这个职位。在这段代码中，当有人试图打开门时，`Door`会检查这个人是否有正确的钥匙。事情应该是这样的吗？实际上，它看起来像:

```
door.unlock(player) 
```

或者也许玩家应该检查他们是否有钥匙？

```
player.unlock(door) 
```

还有第三种选择，添加某种类型的`Interaction`对象来检查动作的所有必要要求，这样我们就必须编写

```
world.unlock(what, who) 
```

这是一个重要的通用编程问题:如何划分对象的责任，以及为什么一个选项比另一个好。我将在第二部分尝试探索这个问题，同时这里有 [GitHub 链接](https://github.com/aandreev0/threadEscape/tree/26a66d1029e65f716111d24364f3b940321a59bf/games/devto)到这里描述的当前游戏

这里提到的主题的简短列表:

*   读取用户输入
*   创建类
*   `Map`和匿名功能
*   类继承
*   责任分离