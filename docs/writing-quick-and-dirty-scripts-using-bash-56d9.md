# 使用 bash 编写快速而肮脏的脚本

> 原文：<https://dev.to/intricatecloud/writing-quick-and-dirty-scripts-using-bash-56d9>

您已经在 shell 中输入了几行代码，您希望像运行任何其他 CLI(包括选项)一样运行它。如果您正在编写一个 shell 脚本，这里有一些提示可以帮助您抢占先机。

1.  #!舍邦线
2.  使用文档
3.  标志和选项
4.  错误处理
5.  处理输出

本文假设您在 OSX/Linux 环境中使用 bash。

## 1。舍邦线

例如，你通常会在 shell 脚本`#!/bin/bash`的顶部看到一行。这被称为 shebang 行——它是 shell 使用该路径上的程序运行该文件的指令。例如，如果这是一个 node.js 脚本，我会有这样的代码:

```
#!/usr/bin/node

console.log('hello world') 
```

Enter fullscreen mode Exit fullscreen mode

因为我们想使用 bash 运行这个脚本，所以我们将 shebang 行设置为 bash。您可以做`#!/bin/bash`——但是这将默认为您的用户系统安装 bash。如果在那个目录中有 bash 的另一个版本，您可以尝试一下`#!/usr/local/bin/bash`。很难解释你当前的外壳可能在哪里——在这种情况下，你可以简单地用`env`询问环境它应该在哪里。

```
#!/usr/bin/env bash 
# will run the `bash` that is currently in your environment (i.e. in your $PATH). 
```

Enter fullscreen mode Exit fullscreen mode

## 2。使用文档

用户可能期望看到如何使用该工具的一种方式是仅仅键入裸命令，例如`mytool`。如果没有参数传递给工具
，下面将打印工具的使用文档

```
function usage {
    echo "usage: `basename "$0"` /path/to/file"
    echo ''
    echo 'ex. mytool /tmp/foobar'
    echo ''
    echo 'Does something useful to the target file'
    echo ''
    echo 'Options:'
    echo '-h : show this doc
}

if [ "$#" -lt 1 ]; then
  usage
fi 
```

Enter fullscreen mode Exit fullscreen mode

## 2。选项、标志、参数和环境变量

一旦您想要设置 CLI 标志或选项，请移至`getopts`。语法可能有点混乱，但希望这种分解是有意义的。

*   一个限制是你只能设置像`-x -V`这样的短标志
*   所有 CLI 标志的定义发生在 getops: `:ht:`的参数中
*   第一个`:`意味着禁用 getopts 的默认错误处理
*   第二个`h`，这意味着我们有一个没有参数的选项(`-h`)
*   第三部分是`t:`(标志名后面有一个冒号)，这意味着选项(`-t`)需要一个参数

```
while getopts ":ht:" opt; do
  case $opt in
    h)
      usage
      ;;
    t)
      type=$OPTARG
      ;;
    \?)
      echo 'Invalid option: -$OPTARG'
      ;;
  esac
done

shift $(($OPTIND - 1))
remaining_args=$@ 
```

Enter fullscreen mode Exit fullscreen mode

在本例中，我们有一个带选项`-h -t`的 CLI，选项`-t`需要一个参数。最后两行允许您获得提供给脚本的其余参数。他们都在`$@`变量中结束。

如果您运行`mytool -t foo /path/to/file`(使用这些选项)，您将得到结果:

```
type="foo"
remaining_args="/path/to/file" 
```

Enter fullscreen mode Exit fullscreen mode

如果您想检查一个变量是否已经定义，如果没有就中止，您可以使用 bash 条件。

```
if [ -z "$SOMETHING_IMPORTANT" ]; then
  echo '$SOMETHING_IMPORTANT wasn't defined'
  usage
fi 
```

Enter fullscreen mode Exit fullscreen mode

你可以检查很多东西，`-z`是我最常用的一个——参见[http://tldp.org/LDP/abs/html/comparison-ops.html](http://tldp.org/LDP/abs/html/comparison-ops.html)

如果您想为环境变量设置默认值(并允许用户覆盖它们)，这将把`USE_HEADLESS_CHROME`设置为`0`，如果它还没有被定义的话。`USE_HEADLESS_CHROME=${USE_HEADLESS_CHROME:-0}`

## 3。处理输出

您是否正在尝试运行一个长时间运行的命令，但是您希望保存输出以供以后进一步调试，并且您还希望看到它运行？输入`tee`！它可以让你同时将输出通过管道传输到两个 stdout &文件。

```
apt-get update -y | tee apt-get.log   # view and save the output
apt-get update -y > /dev/null 2>&1    # ignore all output
apt-get update -y 2>&1 > /dev/null    # Keep only stderr 
```

Enter fullscreen mode Exit fullscreen mode

在这里可以看到关于 bash 中重定向的有趣的[栈溢出讨论。](https://stackoverflow.com/questions/2342826/how-to-pipe-stderr-and-not-stdout)

## 4。错误处理

第一次运行脚本时，您可能会看到，即使一些命令失败，脚本仍会继续运行。不，我不想创建名为`404 Not Found`的文件。/facepalm。

当任何命令失败时,`set -e`将导致您的 shell 脚本中止。相反，如果您想忽略任何错误并让脚本继续运行，请使用`set +e`(这是默认设置)

```
set -e
false
echo "You won't see this line" 
```

Enter fullscreen mode Exit fullscreen mode

不幸的是，如果您正在使用管道，并且仍然想在管道中的任何地方发生错误时中止脚本，那么`set -e`将不起作用。您必须使用`set -o pipefail`来启用该行为。

```
ps aux | grep java | awk '{print $2}' | tee pids.log # grepping for java fails, but the pipe continues 
```

Enter fullscreen mode Exit fullscreen mode

有时候，你可能会想到会有错误发生，你想对此做些什么。`$?`将包含最后执行的命令的退出代码。您甚至可以将它赋给一个变量，用于以后退出。

```
set +e                                  # so that errors do not cause the script to abort
apt-get install $somethingReallyNew     # I know this is going to fail
apt_exit_code=$?
if [ "$apt_exit_code" != 0 ];then  
    echo 'whoops, need to run apt-get update first'
    exit $apt_exit_code
fi 
```

Enter fullscreen mode Exit fullscreen mode

您也可以在脚本顶部的 shebang 行中包含这些选项:`#!/usr/bin/env bash -eo pipefail`

Bash 对于任何 linux 机器上可用的小脚本来说都是非常强大的，比如看到名为‘foo’-`ps | grep foo`的运行进程。我更喜欢使用 node.js 或 ruby 这样的高级语言，但是对于只做一件小事的小脚本来说——很难打败 bash。希望有了这些指南，您可以立即开始在 bash 中拼凑一个快速而肮脏的工具。

* * *

对于更多 bash 脚本资源，我发现这些链接特别有用:

*   shell 命令的交互式解释器-[https://explainshell.com](https://explainshell.com)
*   编写安全外壳脚本-[https://sipb.mit.edu/doc/safe-shell/](https://sipb.mit.edu/doc/safe-shell/)