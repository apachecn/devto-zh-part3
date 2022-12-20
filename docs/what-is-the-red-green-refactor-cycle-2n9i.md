# 什么是红绿重构周期

> 原文：<https://dev.to/ellehallal/what-is-the-red-green-refactor-cycle-2n9i>

<center>

### 最初发布于 [ellehallal.dev](https://ellehallal.dev) 👩🏽‍💻

</center>

* * *

本周，我继续使用测试驱动开发，用 Ruby 创建了一个人对人[的井字游戏。我也一直在努力熟悉一些原则和规则，比如坚实的原则和简单设计的四条规则。](https://github.com/ellehallal/ruby-tic-tac-toe)

当创建井字游戏时，测试驱动开发帮助我将游戏分解成小块。我还有一段路要走，但是红色、绿色的重构周期正在帮助我写出更干净的代码。

* * *

## 红色，绿色，重构循环

### 红色

*红色*阶段意味着应该编写一个测试，它应该失败。这意味着还没有编写任何与测试相关的代码。此外，运行测试以确保失败也很重要。这里有一个测试我的井字游戏的例子:

```
it 'returns false if there are available spaces on the board' do
  board = Board.new([1, 2, 3, 'x', 5, 6, 7, 8, 9])
  expect(board.complete?).to be false
end 
```

Enter fullscreen mode Exit fullscreen mode

### 绿色

绿色的阶段意味着代码应该通过测试。在这个阶段，代码不需要最干净，只要能让测试通过就行。

通过上述测试的最简单方法是创建一个名为 complete？，它只返回 false。下面是我最初的尝试，通过了测试:

```
def complete?
  available_squares = @squares.count { |square| square.is_a? Integer }
  available_squares == 0
end 
```

Enter fullscreen mode Exit fullscreen mode

### 重构

*重构*阶段指的是编写一个更干净的代码版本，并且仍然通过测试。在 Sandi Metz 关于[可靠的面向对象设计](https://www.youtube.com/watch?v=v-2yFMzxqwU)的演讲中，重构时需要考虑很多因素:

*   是干的吗？

*   它有单一的责任吗？

*   里面的一切都以同样的速度变化吗？

*   它是否依赖于变化频率比它低的事物？

看上面的方法，显得干巴巴的。然而，它确实有不止一个责任。

*   计算可用方块的数量

*   根据可用方块的数量返回真或假

我的理解是应该有两种不同的方法。考虑到这一点，创建了另一个测试来测试计算可用方块数量的方法。相关代码是否完整？被提取并添加到新方法:

```
//test
it 'returns the amount of available squares' do
  board = Board.new(['x', 2, 'o', 4, 5, 6, 7, 8, 9])
  expect(board.available_squares).to eq(7)
end

//method
def available_squares
  @squares.count { |square| square.is_a? Integer }
end 
```

Enter fullscreen mode Exit fullscreen mode

重构后的`complete?`方法现在只做一件事:根据可用方块的数量返回 true 或 false】

```
def complete?
  available_squares.zero?
end 
```

Enter fullscreen mode Exit fullscreen mode

在整个井字游戏中坚持单一责任原则，我还有一段路要走。遵循红色、绿色、重构循环和重构要点有助于应用这一原则。

* * *

## 有用资源:

*   [Sandi Metz——固体面向对象设计](https://youtu.be/v-2yFMzxqwU)
*   [贝克设计规则](https://martinfowler.com/bliki/BeckDesignRules.html)
*   [TDD 的周期](https://blog.cleancoder.com/uncle-bob/2014/12/17/TheCyclesOfTDD.html)