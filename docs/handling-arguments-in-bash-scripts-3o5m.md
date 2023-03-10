# 在 Bash 脚本中处理参数

> 原文：<https://dev.to/rpalo/handling-arguments-in-bash-scripts-3o5m>

使用 Bash 脚本自动化一组命令是构建工具并使您的生活变得更简单的旅程的第一步。每次都以相同方式运行的简单、自顶向下的脚本非常强大，可以节省您的时间。但是，当您想要动态定制脚本的行为时，就会出现这样的情况:使用定制名称创建目录、从特定的 git 存储库下载文件、指定 IP 地址或端口等等。这就是**脚本参数**的用武之地。

## 内置变量

Bash 为我们提供了一些变量，这些变量存在于您编写的任何脚本中。以下是一些最有用的:

### `$1, $2, $3, ...`:立场论点

这些是*立场论点*。当脚本在命令行上运行时，它们保存脚本后给出的参数。例如，如果您有一个这样运行的脚本:

```
$ ./my_script 200 goats 
```

Enter fullscreen mode Exit fullscreen mode

变量`$1`将保存值`"200"`，变量`$2`将保存值`"goats"`。

例如，我在一个最简单的脚本中使用了位置参数，但这是我几乎每天都在工作中运行的一个脚本(暂时简化了):

```
#!/usr/bin/env bash

project_name="$1"

mkdir -p ${project_name}/{CAD,drawings,mold,resources}

echo "New project '$project_name' created!" 
```

Enter fullscreen mode Exit fullscreen mode

2019 年 3 月 4 日编辑:大括号展开不应被引用，否则它们不会被展开。mkdir 的参数周围没有引号。谢谢你本杰明。

你看到我是如何获取特殊变量`$1`并将其值存储在名为变量的实际*中的吗？你不一定需要这样做。我可以说`mkdir -p "$1/{CAD,drawings,mold,resources}"`，一切仍然会很好。但是，我喜欢将位置参数存储到脚本顶部附近的命名变量中，这样任何阅读我的脚本的人都会知道脚本在期待什么。当然，它不能代替良好的文档和健壮的错误处理，但是它是一个很好的自文档可读性的小奖励，有一点帮助。这绝对是一个很好的练习。*

当我这样运行它的时候:

```
$ new_project "catheter-01" 
```

Enter fullscreen mode Exit fullscreen mode

生成目录结构:

```
- catheter-01
|- CAD
|- drawings
|- mold
|- resources 
```

Enter fullscreen mode Exit fullscreen mode

### `$0`:脚本名

这提供了调用时的脚本名称。这是一个很好的快捷方式，对于像输出使用信息和帮助这样的事情特别有用。

```
#!/usr/bin/env bash

function usage() {
  echo "Usage: $0 <name> [options]"
}

# Error handling omitted (for now)

if [[ "$1" == -h ]]; then usage
  exit 0
fi name="$1"
echo "Hello, ${name}!" 
```

Enter fullscreen mode Exit fullscreen mode

然后，我们可以这样运行它:

```
$ ./greeting -h
Usage: ./greeting <name> [options]

$ bash greeting -h
Usage: greeting <name> [options]

$ ./greeting "Ryan"
Hello, Ryan! 
```

Enter fullscreen mode Exit fullscreen mode

### `$#`:自变量计数

这种方法非常适合错误处理。当我们的脚本没有得到它需要的参数时会发生什么？让我们用一些错误处理来更新上面的问候脚本。

```
#!/usr/bin/env bash

function usage() {
  echo "Usage: $0 <name> [options]"
}

### Hot new error handling!
# We expect one argument.  Otherwise tell the user how to
# call your script.
if [[ "$#" -ne 1 ]]; then usage
  exit 1
fi

if [[ "$1" == -h ]]; then usage
  exit 0
fi name="$1"
echo "Hello, ${name}!" 
```

Enter fullscreen mode Exit fullscreen mode

### `$?`:最近退出代码

我个人在脚本中不怎么使用这个，但是我*在命令行中经常使用*。很多命令在失败时甚至不会给出任何输出。他们什么都不做。那么你怎么知道它是否失败了呢？最后一次命令运行的退出代码存储在`$?`变量中。

```
$ ls test.txt    code    strudel.py
$ echo $?
0
$ ls lamedir
ls: cannot access 'lamedir': No such file or directory
$ echo $?
2 
```

Enter fullscreen mode Exit fullscreen mode

这里有一个脚本中的例子:

```
#!/usr/bin/env bash

dirname="$1"

mkdir "$dirname"  # This will fail if the directory exists already

if [[ "$?" -ne 0 ]]; then
  # If the directory is already created, that's fine
  # just print out a message to alert the user
  echo "Directory '$dirname' already exists.  Not making a new one."
fi 
```

Enter fullscreen mode Exit fullscreen mode

### `$@ and $*`:所有参数

这些变量似乎在 Bash 新手中造成了最大的困惑——这是合理的！他们做几乎完全一样的事情，但是根据你的情况不同，差别会很大。这是真相。

当你*不*引用这些变量时，它们都做同样的事情，删除在那个位置提供的所有参数。

```
#!/usr/bin/env bash

echo "===================="
echo "This is dollar star."
echo "===================="
for arg in $*; do echo "$arg"
done echo "===================="
echo "This is dollar at."
echo "===================="
for arg in $@; do echo "$arg"
done 
```

Enter fullscreen mode Exit fullscreen mode

运行此:

```
$ ./arg_printer abba dabba "dooby doo"
====================
This is dollar star.
====================
abba
dabba
dooby
doo
====================
This is dollar at.
====================
abba
dabba
dooby
doo 
```

Enter fullscreen mode Exit fullscreen mode

看到连引用的论点都被空格分开了吗？有时这是你想要的，但通常不是。这些变量的引用版本是事情变得有趣的地方。

当你引用`$*`时，它会将所有接收到的参数作为一个单独的字符串输出，由空格 <sup id="fnref1">[1](#fn1)</sup> 分隔，不管它们是如何被引用进来的，但它会引用该字符串，以便它不会在以后被拆分。

```
#!/usr/bin/env bash

echo "===================="
echo "This is quoted dollar star."
echo "===================="
for arg in "$*"; do echo "$arg"
done 
```

Enter fullscreen mode Exit fullscreen mode

运行它:

```
$ ./arg_printer abba dabba "dooby doo"
====================
This is quoted dollar star.
====================
abba dabba dooby doo 
```

Enter fullscreen mode Exit fullscreen mode

看到了吗？一个论点！你想欺骗我们自己吗？

```
#!/usr/bin/env bash

printf '%s\n' "$*" 
```

Enter fullscreen mode Exit fullscreen mode

```
$ ./my_echo hello my name is Ryan
hello my name is Ryan 
```

Enter fullscreen mode Exit fullscreen mode

整洁，对不对？

相反，当您引用`$@`时，Bash 将遍历并引用每个参数，就像它们最初被给出时一样。在我看来，这可能是最有用的版本，因为它允许您将所有参数传递给子命令，同时仍然保留空格和引号，而不会让 Bash 的自动字符串拆分功能把事情弄糟。

```
#!/usr/bin/env bash

echo "===================="
echo "This is quoted dollar at."
echo "===================="
for arg in "$@"; do echo "$arg"
done 
```

Enter fullscreen mode Exit fullscreen mode

```
$ ./arg_printer abba dabba "dooby doo"
====================
This is quoted dollar at.
====================
abba
dabba
dooby doo 
```

Enter fullscreen mode Exit fullscreen mode

你会在有大量函数的脚本中经常看到这种情况。如果你有很多函数，传统的做法是让脚本中的最后一个函数成为一个`main`函数，它处理所有的参数并包含脚本的编排逻辑。然后，为了运行主函数，通常，脚本的最后一行是`main "$@"`。因而:

```
#!/usr/bin/env bash

function usage() {
  echo "Usage: $0 <first> <second> [options]"
}

function bigger() {
  local first="$1"
  local second="$2"
  if [[ "$first" -gt "$second" ]]; then echo "$first"
  else echo "$second"
  fi
}

function main() {
  if [[ "$#" -ne 2 ]]; then usage
    exit 1
  fi local first="$1"
  local second="$2"
  bigger "$first" "$second"
}

main "$@" 
```

Enter fullscreen mode Exit fullscreen mode

## 向前看:Fancier Args

希望现在，你开始感受到定制的力量。您可以使用 Bash 脚本抽象出一些任务，即使它们包含特定于场景的逻辑，因为您可以在调用时提供参数！但这只是冰山一角。如果您已经使用命令行很长时间了，您可能会看到一些命令有大量的标志、选项、设置和子命令都需要处理，而使用简单的位置参数处理这些命令不足以完成工作。

我将很快准备好另一篇文章，讨论更复杂的参数解析:如何处理许多参数、标志、`getops`、`shift`、STDIN 等等。

* * *

1.  技术上来说，用一个[if](https://bash.cyberciti.biz/guide/%24IFS)隔开。通常，这是一个空格，但最好知道什么时候不是这样。 [↩](#fnref1)