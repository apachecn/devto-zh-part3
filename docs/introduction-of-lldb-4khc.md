# LLDB 简介

> 原文：<https://dev.to/dannypsnl/introduction-of-lldb-4khc>

说真的，我不是调试器的忠实粉丝，因为我以前从未尝试过。但是这次我花了几个小时来使用它，这种体验令人惊讶。调试器提供了一种通过不同的输入窥视程序内部所有东西的方法，这种方法对于调试你不知道的东西几乎是完美的。但另一方面，我们应该创造一个充分的例子来确定问题，这并不容易。

无论如何，工具箱中有另一个工具是很好的，所以让我们开始看看 LLDB 如何与`C`一起工作。

第一步是创建一个小程序。由`clang-format`格式化的附言

```
// main.c
#include <stdio.h> 
int main(int argc, char **argv) {
  if (argc < 1)
    return 1;
  printf("hello, %s\n", argv[1]);
  int print_me = 10;
  printf("print me: %d\n", print_me);
  return 0;
} 
```

在程序中，我们留下一个变量`print_me`只是为了展示如何使用`display`钩子。

然后我们编译它，这里的要点是你应该确保编译器提供了调试信息，命令是:`clang -g main.c`，它将生成一个可执行文件`a.out`和一个包含调试信息的目录`a.out.dSYM`，现在我们可以启动我们的调试器了！

键入:`lldb`并输入。

现在我们在 **LLDB** 的交互环境中。

第一步是创建一个目标。键入并输入`target create a.out`，键入`target list`以确保您只是创建一个目标。您可以同时创建多个目标，并使用`target select <target-index>`来改变您要调试的目标。

下一步是设置一个断点，断点意味着当你运行目标可执行文件时，它会停止的位置让你观察进程的状态。让我们设置第一个断点，以了解它是如何工作的。键入`b main`并回车。然后运行`breakpoint list`来确保您创建了一个断点。现在，输入`run Danny`看看发生了什么，在我的电脑上，它显示:

```
(lldb) run Danny
Process 63370 launched: '/Users/dannypsnl/workspace/dannypsnl/lldb/a.out' (x86_64)
Process 63370 stopped
* thread #1, queue = 'com.apple.main-thread', stop reason = breakpoint 1.1
    frame #0: 0x0000000100000f16 a.out`main(argc=2, argv=0x00007ffeefbff138) at main.c:4
   1 #include <stdio.h>
   2
   3 int main(int argc, char **argv) {
-> 4 if (argc < 1)
   5 return 1;
   6 printf("hello, %s\n", argv[1]);
   7 int print_me = 10;
Target 0: (a.out) stopped. 
```

该过程在进入主函数时停止。LLDB 允许你转储帧来了解进程的状态，命令是`fr v`，它是`frame variable`的一个短命令，你会看到:

```
(lldb) frame variable
(int) argc = 2
(char **) argv = 0x00007ffeefbff138
(int) print_me = 0 
```

你也可以显示某个变量，例如:

```
(lldb) print argc
(int) $0 = 2 
```

但是如果你想通过不同的输入知道一个变量的值，键入`print`似乎不太好，对于这种情况，你可以使用`display`来代替它，`display`就像`print`一样工作，但是它会自动触发，所以你不必担心你忘记显示一些信息。顺便说一下，`print`支持高级表达，例如:

```
(lldb) print argv[1]
(char *) $9 = 0x00007ffeefbff358 "Danny" 
```

现在，我们知道了很多关于如何获得流程状态的信息，但是不要忘记我们仍然在第一个断点处，我们需要知道如何向前移动。这个命令是`step`和`continue`，它们之间的区别是`step`会移动一条指令(尽可能小)，而`continue`会移动到下一个断点。其中的快捷方式是`s`和`c`，你可以试着感受一下不同。

关于断点，我们有更多的方法来设置断点，我们使用函数名来设置一个，我们也可以使用位置来设置一个，例如

```
(lldb) breakpoint set -l 7 
```

这将在第 7 行添加一个断点，我们可以使用`breakpoint delete <breakpoint-index>`来删除它们。

要获得更多信息，你应该看看[https://lldb.llvm.org/tutorial.html](https://lldb.llvm.org/tutorial.html)，感谢阅读。