# 什么是单一责任原则？

> 原文：<https://dev.to/ellehallal/what-is-the-single-responsibility-principle-3259>

<center>

### 最初发布于 [ellehallal.dev](https://ellehallal.dev) 👩🏽‍💻

</center>

* * *

单一责任原则(SRP)是面向对象编程的五个坚实原则之一。目的是“[让软件设计更容易理解，更灵活，更易维护](https://en.wikipedia.org/wiki/SOLID)”。

SRP 的概念是一个类应该有**一个**责任，因此只有一个改变的理由。

## 实施原则

我目前正在用 Ruby 开发一款井字游戏，在那里我获得了许多用户故事。第一个用户故事是:*作为用户，我可以看到初始板*。

分解一下，我的理解是需要一个井字游戏棋盘，**和**用户需要能够看到它。

### 创建棋盘

```
class Board
  attr_reader :board

  def initialize(board)
    @board = board
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

上面，Board 类在初始化的时候把一个板子作为参数。对于井字游戏，当初始化棋盘实例时，将传递包含数字 1-9 的数组。

```
board = Board.new([1, 2, 3, 4, 5, 6, 7, 8, 9]) 
```

Enter fullscreen mode Exit fullscreen mode

### 显示棋盘

要显示井字游戏棋盘，需要一种方法来向用户显示输出。例如:

```
def display_board(board)
  print """
    #{board[0]} | #{board[1]} | #{board[2]}
    -----------
    #{board[3]} | #{board[4]} | #{board[5]}
    -----------
    #{board[6]} | #{board[7]} | #{board[8]}\n"""
end 
```

Enter fullscreen mode Exit fullscreen mode

很自然地认为这个方法应该在`Board`类中，因为这是井字游戏棋盘所在的地方。

然而，如果将来发生变化呢？例如，如果应用程序是基于 web 的，而不是基于命令行的，`display_board`将是一个多余的方法。因此，很可能 Board 中包含输出的任何方法都需要更改。

如果所有输出都包含在一个单独的类中，会发生什么？在上面的例子中，`Board`类不需要修改，因为它不包含打印到命令行的方法。

考虑到这一点，创建了一个名为`Display`的新类，并添加了`display_board`方法:

```
class Display
  def display_board(board)
    ...
  end
end 
```

Enter fullscreen mode Exit fullscreen mode

对于其他需要在命令行打印消息的用户场景，比如提示用户移动和显示当前玩家，它将包含在这个类中。

## 结论

通过实现单一责任原则，`Board`和`Display`类各有一个责任，并且只有一个改变的理由。

`Board`类管理井字游戏棋盘的状态。关于用户故事，它创建了一个井字游戏棋盘。

`Display`类管理命令行的打印输出，以供用户查看。关于用户故事，用户可以看到最初的公告板。