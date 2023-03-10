# Bash 中的短路

> 原文：<https://dev.to/rpalo/short-circuiting-in-bash-4jam>

在 Bash 中，`&&`和`||` (AND 和 OR)操作符通过“短路”策略工作。我的意思是。

考虑如何在布尔比较中使用它们会有所帮助:

```
if [[ "$1" -gt 5 ]] && [[ "$1" -lt 10 ]]; then 
```

Enter fullscreen mode Exit fullscreen mode

这检查了第一个条件。如果第一个条件为真，那么有可能整件事都为真，所以它检查第二个条件。如果第二个条件成立，那么整件事就是真的！然而，如果第一个条件是假的，那么就没有理由检查第二个条件，因为如果第一个条件是假的，整个事情就不可能*可能*为真。

我们也可以在命令中使用这个属性！

```
[[ "$1" -gt 5 ]] && echo "It's such a big number!" 
```

Enter fullscreen mode Exit fullscreen mode

如果第一次比较成功(退出代码为 0 表示成功)，那么第二个命令运行并回显(echos？埃克斯？有回声)！但是，如果比较失败，Bash 根本不会运行 echo 命令。

最好的部分是不需要`if`。你也可以通过数学比较和任意代码做到这一点。

```
(( "$1" % 17 == 0 )) && result+="Banana" 
```

Enter fullscreen mode Exit fullscreen mode

您甚至可以使用可以通过或失败的命令来完成它！

```
$ mkdir "fish" && echo "Directory created"
Directory created
$ # Now let's try making it again, which will fail
$ mkdir "fish" && echo "Directory created"
# No output 
```

Enter fullscreen mode Exit fullscreen mode

## `||`:做 X 不然！

事实上，这个错误消息是 *other* 符号:`||`的一个很好的用例。`||`反其道而行之。再一次，让我们从如何在条件:
中评估的角度来思考这个问题

```
if [[ "$1" -lt 0 ]] || [[ "$1" -gt 10 ]]; then 
```

Enter fullscreen mode Exit fullscreen mode

如果第一个条件没有满足，没问题。我们还有第二个可能是真的，所以我们会评估它。但是，如果第一个条件为真，那么我们甚至不需要检查第二个条件，因为我们已经知道这个条件作为一个整体将要为真。所以我们跳过它！

这与常规命令的工作方式完全相同:

```
$ mkdir "fish" || echo "There was a problem."
# Nothing printed.  'fish' dir got made, so the second condition
# had no need to run!
$ mkdir "fish" || echo "There was a problem."
There was a problem. 
```

Enter fullscreen mode Exit fullscreen mode

## 将它们组合为三进制幂

现在我们有了这些新的超能力，像“bash 三元”模式这样的新模式的大门就打开了

```
[[ "$1" == good ]] && echo "Good input!" || echo "Bad input!" 
```

Enter fullscreen mode Exit fullscreen mode

如果第一个命令成功，我们运行中间的命令。否则第一个子句(`[[ ]] && echo`)会失败，所以最后一个命令会运行。

然而，有一点需要注意的是，如果中间子句是一个命令，*可能会使*失败，而*确实会使*失败，那么第三个命令也会运行。这是一个奇怪的、意想不到的边缘情况，传统的三元运算不会想到:所有三个命令都运行！

```
$ name="bob"
$ [[ "$name" == bob ]] && mkdir "fish" || echo "Name not bob"
# Makes a directory called 'fish', no output
$ [[ "$name" == bob ]] && mkdir "fish" || echo "Name not bob"
Name not bob
# Well, that's just confusing.  Name *is* bob!  But mkdir
# failed, so the "else" case gets run anyways, confusingly. 
```

Enter fullscreen mode Exit fullscreen mode

## 告诫

还有一个警告。就像在任何其他语言中一样，试图使用过于花哨的一行程序可能会导致一些密集、难以阅读的“聪明”代码(带有讽刺性的引号)，这会让任何阅读您代码的人感到难过。当你使用这种“一行条件语句”时，确保你特别注意下一个阅读代码的人。尽量保持它的整洁和可读性，如果你做不到这一点，可能是时候回到多行`if`或者使用一个帮助函数了。