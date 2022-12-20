# 使用 RSpec 测试输出

> 原文：<https://dev.to/ellehallal/testing-output-with-rspec-134e>

<center>

### 最初发布于 [ellehallal.dev](https://ellehallal.dev) 👩🏽‍💻

</center>

* * *

这是一篇关于用 RSpec 测试输出的快速博客。目前，我正在用 Ruby 开发一款[井字游戏，用户可以和电脑对战。](https://github.com/ellehallal/ruby-tic-tac-toe)

* * *

## 以前的测试尝试

我在测试游戏中的循环时遇到过问题，它一直要求玩家移动，直到棋盘满了或者玩家赢了。

```
def main_game
  play_move until @game.over?
  end_of_game
end 
```

Enter fullscreen mode Exit fullscreen mode

测试这一点的一个尝试是窥探`play_move`和`end_of_game`方法。如果提供一个完整的板，这工作得很好。

然而，当存在不完整的板时，这被证明是难以测试的。尽管模拟了用户输入，测试在这个阶段还是会挂起。当使用[对象加倍](https://relishapp.com/rspec/rspec-mocks/v/3-8/docs/verifying-doubles/using-an-object-double)时，出现了同样的问题。

* * *

## 测试输出

我的一位导师建议测试`end_of_game`方法的输出。`end_of_game`方法打印井字游戏棋盘和游戏结果。

```
it 'plays a game that ends with a winning player' do

  expect { controller.main_game }
  .to output("""

  1 | x | x
----------------
  o | o | x
----------------
  x | o | o
The current player is x
Choose a position from 1-9:

  x | x | x
----------------
  o | o | x
----------------
  x | o | o
x is the winner!""")
  .to_stdout 
```

Enter fullscreen mode Exit fullscreen mode

多行和对齐也被证明很难达到预期的输出。需要测试的关键元素是游戏的结果，这是输出的最后一行:“x 是赢家！”

* * *

## 斯特林吉奥

StringIO 是 IO 对象的基于字符串的替换。它的作用和文件一样，但是它作为一个字符串保存在内存中。

想法是捕捉输出流并重定向它。为了检查输出的最后一行是否正确，我的导师建议尝试以下方法:

```
it 'plays a game that ends with a winning player' do
  controller = controller_setup([1, 'x', 'x', 4, 'o', 'x', 'x', 8,     'o'])
  allow($stdin).to receive(:gets).and_return('8', '4', '1')
  $stdout = *StringIO*.new

  controller.main_game
  output = $stdout.string.split("\n")

  expect(output.last).to eq('x is the winner!')
end 
```

Enter fullscreen mode Exit fullscreen mode

在这项测试中:

*   创建了一个`controller`类的新实例，带有一个不完整的电路板

*   模拟输入来玩棋盘上剩余的三个位置

*   标准输出`$stdin`被重定向到`StringIO`的新实例

*   调用`main_game`方法来玩并完成游戏

*   然后将输出保存到变量`output`，转换成一个字符串，并在出现新行的地方进行分割，以创建一个字符串数组

*   数组中的最后一项应该是“x 是赢家！”

当测试包含多行的大块文本时，我将继续使用这种方法。这比在测试中试图对齐多行输出要简单得多。

* * *

## 资源

*   [红宝石中的木卫一](https://thoughtbot.com/blog/io-in-ruby#stringio)
*   [类:IO (Ruby 2.3.1)](https://ruby-doc.org/core-2.3.1/IO.html)
*   如何将 STDOUT 捕获到字符串中？