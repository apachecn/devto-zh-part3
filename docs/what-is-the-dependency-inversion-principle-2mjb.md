# 依赖倒置原理是什么？

> 原文：<https://dev.to/ellehallal/what-is-the-dependency-inversion-principle-2mjb>

<center>

### 最初发布于 [ellehallal.dev](https://ellehallal.dev) 👩🏽‍💻

</center>

* * *

这是一个关于我对依赖倒置原则理解的快速博客。还有一些我不确定的地方，所以请随时留下反馈。

## 依存倒置原理是什么？

> 高层模块不应该依赖低层模块。两者都应该依赖于抽象。抽象不应该依赖于细节。细节应该依赖于抽象。

*—罗伯特·马丁*

太好了，但这意味着什么呢？🤷🏽‍♀️:我将用我的[井字游戏应用程序](https://github.com/ellehallal/ruby-tic-tac-toe)中的一个类作为例子来展示我的理解。

## 井字游戏中的依存倒置

在应用程序中，有一个名为`GameFactory`的类。`GameFactory`的目的是用指定的玩家和棋盘创建一个`Game`类的实例。

下面是这个类的压缩版本:

```
class GameFactory
    def initialize(player_factory)
    @player_factory = player_factory
    end

    def new_game(squares)
    board = Board.new(squares)
    player1 = @player_factory.create_player('x', 1)
    player2 = @player_factory.create_player('o', 2)
    Game.new(board, player1, player2)
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

在`new_game`方法中，在其中创建了`Board`和`Game`类的新实例。然而，这违反了依赖倒置原则。

### 怎么了？

高级类`GameFactory`依赖于低级类`Board`和`Game`。因此，它们是紧密耦合的。低级别类的更改会影响高级别类。

如果更改了`Board`或`Game`类的名称，那么`GameFactory`中的`new_game`方法将不起作用。因此，需要对其进行修改以适应重命名的类。

如果`Board`和`Game`的子类被用来创建一个新游戏，(例如，`BestBoard`和`FunGame`)`new_game`方法将需要再次改变以适应这种情况。

解决上述问题的一个方法是将类传递给`GameFactory`的构造函数:

```
class GameFactory
    def initialize(player_factory, board, game)
    @player_factory = player_factory
    @board = board
    @game = game
    end

    def new_game(squares)
    board = @board.new(squares)
    player1 = @player_factory.create_player('x', 1)
    player2 = @player_factory.create_player('o', 2)
    @game.new(board, player1, player2)
    end
end 
```

Enter fullscreen mode Exit fullscreen mode

初始化过程中作为棋盘和游戏传入的东西，在`GameFactory.`中变成`@board`和`@game`

如果`Board`和`Game`类的名字要改变，用重命名的类初始化`GameFactory`不会影响`GameFactory`。

如果`Board`和`Game`的子类(例如`BestBoard`和`FunGame`)被用于初始化`GameFactory`的实例，这不会影响`new_game`的功能。

总之，我的理解是初始化一个特定的类，否则另一个类中的类会导致紧耦合。能够通过构造函数注入类，有助于使类松散耦合。

* * *

## 资源

*   [Ruby 中的依赖倒置原则](http://www.getlaura.com/dependency-inversion-principle-in-ruby/) [稳固原则# 5——依赖倒置原则](https://www.netguru.com/codestories/solid-5-dip)