# BASH 技巧:dir 堆栈。

> 原文：<https://dev.to/cpu/bash-tricks-the-dir-stack-4mei>

BASH shell 挤满了特性。我最喜欢的事情之一是当我学会了一个很酷的 BASH 技巧。在你的工具带上增加一个新工具感觉很棒。这里有一个工具，当我第一次了解它的时候，我很快就把它集成到了我的日常工作中:**BASH 目录栈**。

## 敬酒不吃吃罚酒

使用命令行的一个常见场景是发现自己在一个目录中，但需要快速检查另一个目录中的某些内容。也许您甚至需要查看另一个目录的子目录中的一些内容。当你完成后，如何回到你开始的地方？

假设我在`~/go/src/github.com/cpu/someproject`中开发一个 Go 应用程序，我想通过检查`/etc/mysql/`及其子目录[的配置片段](https://lists.debian.org/debian-devel/2010/04/msg00352.html) `/etc/mysql/my.cnf.d`来提醒自己如何配置 MySQL。

最直截了当的方法就是`cd`绕过

```
daniel@noir:~/go/src/github.com/cpu/someproject $> cd /etc/mysql/
daniel@noir:/etc/mysql $> ls -l
total 8
-rw-r--r-- 1 root root  208 Jan 25 09:21 my.cnf
drwxr-xr-x 2 root root 4096 Feb  2 12:57 my.cnf.d
daniel@noir:/etc/mysql $> # Oh wait, I need to check the fragments in my.cnf.d
daniel@noir:/etc/mysql $> cd my.cnf.d
daniel@noir:/etc/mysql/my.cnf.d $> # Nope! Didn't find what I wanted, guess I need to check the base `my.cnf`.
daniel@noir:/etc/mysql/my.cnf.d $> cd ..
daniel@noir:/etc/mysql $> # Aha! That had what I needed! Back to work
daniel@nori:/etc/mysql $> cd ~/go/src/github.com/cpu/someproject
daniel@noir:~/go/src/github.com/cpu/someproject $> 
```

这是四个独立的`cd`命令！更糟糕的是，我不得不记住项目的完整路径，在那里我开始`cd`回到它。您可能会想到一个更复杂的例子，其中包含深度嵌套的目录，这些问题会更加明显。在我的 BASH 目录堆栈的通知中，我让画外音宣布*“肯定有更好的方法！”*

[![infomercial struggle](img/4c8df4e7edb426972f07147079758444.png)](https://i.giphy.com/media/PTRuOrLT3xOUw/giphy.gif)

## 简单易行的方法

当然答案是[BASH 目录栈](https://www.gnu.org/software/bash/manual/html_node/Directory-Stack-Builtins.html)！它的工作方式就像你可能已经熟悉的任何其他[栈数据结构](https://en.wikipedia.org/wiki/Stack_(abstract_data_type))(经典的现实生活例子是一堆餐盘)。从栈顶添加和移除条目，我们可以根据它们与栈顶的相对位置来引用栈中较低的其他条目。

可以使用`pushd`将目录添加到堆栈中。`pushd`命令接受相对路径和绝对路径。你可以用一个`popd`命令移除最顶层的目录并自动`cd`到那里。最后，你可以用`dirs`查看当前的目录堆栈。我更喜欢使用`dirs -v`来查看每一个栈条目，每一行都有一个数字索引(`0`是栈顶，`1`是第一个，依此类推)。

让我们回到前面的例子来看看这一点，但是这次我将使用目录栈:

```
daniel@noir:~/go/src/github.com/cpu/someproject $> pushd /etc/mysql/
daniel@noir:/etc/mysql $> ls -l
total 8
-rw-r--r-- 1 root root  208 Jan 25 09:21 my.cnf
drwxr-xr-x 2 root root 4096 Feb  2 12:57 my.cnf.d
daniel@noir:/etc/mysql $> # Oh wait, I need to check the fragments in my.cnf.d
daniel@noir:/etc/mysql $> pushd my.cnf.d
daniel@noir:/etc/mysql/my.cnf.d $> # Nope! Didn't find what I wanted, guess I need to check the base `my.cnf`.
daniel@noir:/etc/mysql/my.cnf.d $> popd
daniel@noir:/etc/mysql $> # Aha! That had what I needed! Back to work
daniel@nori:/etc/mysql $> popd
daniel@noir:~/go/src/github.com/cpu/someproject $> 
```

那感觉更容易！你可以看到我以两种方式使用了`pushd`:一次使用绝对路径(`pushd /etc/mysql/`)，一次使用相对路径(`pushd my.cnf.d`)。我能够使用`popd`从我更改的目录返回，并且我不需要记住任何关于我想要返回的先前目录的信息。

如果我在第二个`pushd`之后将`dirs -v`运行到`my.cnf.d`中，那么堆栈应该是这样的:

```
 0  /etc/mysql/my.cnf.d
 1  /etc/mysql
 2  ~/go/src/github.com/cpu/someproject 
```

## 脚本

我经常使用 BASH 目录堆栈的另一个地方是在小型 BASH 脚本中。通常我需要在一个特定的目录下运行一个命令，但是之后我会希望返回到一个不同的目录。一种方法是仔细平衡脚本中的`cd`命令:

```
#!/usr/bin/env bash

# Sort some input data into an output directory
cat input/day1/data.csv | cut -f, -d1 | sort > output/day1/sorted.data.csv
# Change to the output directory
cd output/day1
# Run some tool that looks for *.csv files in the current directory
summaryTool -q -v
# Change back to the directory the script is being run in
cd ../../ 
```

这个脚本相当脆弱。很容易想象有人可能决定不需要`day1`目录，并重构`cat`命令和第一个`cd` to:

```
cat input/data.csv | cut -f, -d1 | sort > output/sorted.data.csv
cd output/ 
```

如果在第一个`cd`和第二个`cd`之间有许多命令，很容易意外地只更新其中一个。如果发生这种情况，那么第二个`cd ../../`将与第一个不同步，并向后改变一个目录太远。啊哦！

使用`pushd`和`popd`消除了这个 bug 的可能性:

```
#!/usr/bin/env bash

# Sort some input data into an output directory
cat input/day1/data.csv | cut -f, -d1 | sort > output/day1/sorted.data.csv
# Change to the output directory
pushd output/day1
  # Run some tool that looks for *.csv files in the current directory
  summaryTool -q -v
# Change back to the directory the script is being run in
popd 
```

*(为了让事情更清楚，我经常在`pushd`和`popd`之间缩进命令。)*

使用`popd`可以让你接受 [DRY 原则](https://en.wikipedia.org/wiki/Don%27t_repeat_yourself)，并且不会在两个独立的`cd`命令之间复制信息。

## 结论

在使用命令行时，试着每天都使用`pushd`、`popd`和`dirs`。当你发现自己在使用`cd`时，问问自己`pushd`是否会让你的生活更轻松。通过实践，您可能会发现您非常喜欢 BASH 目录栈，以至于可以将`alias cd=pushd`添加到您的`~/.bashrc` :-)