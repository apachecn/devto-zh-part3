# 在 Bash 中合并管道“stdin”和命令行参数

> 原文：<https://dev.to/fortesdotcom/merging-piped-stdin-with-command-line-arguments-within-bash-1h3j>

我是一个计算机迷，很容易被自动化所诱惑，这意味着我最终会写很多 shell 脚本。尽管我已经写了很多 Bash，但我小小的灵长类大脑从来没有完全掌握这门语言，我总是复制粘贴手头的任务，什么也没有吸收，然后拍拍自己的背说自己是这样一个 *1337 h4x0r* 。

典型的我的需求是相当平淡无奇的，我可以立刻找到一个答案来抄袭。然而，我的最新剧本没有在几秒钟内解决，所以我为我非常英俊的未来自己写了解决方案。

## 这个“问题”

我的脚本接受一个或多个命令行参数，当您知道这些参数的值:
时，就可以直接调用这些参数

```
# Called via command-line arguments
my_script item_one item_two 
```

在其他情况下，我传入的值是其他命令的输出。作为一个[虔诚的 Unix 用户](https://linux.die.net/Linux-CLI/c1089.htm)，我的直觉是通过管道输出(`printf`只是为了说明):

```
# Called with piped input, one argument per line
printf "item_one\nitem_two" | my_script 
```

我每次复制粘贴的参数处理 Bash 代码只处理命令行参数，完全忽略任何管道输入。下面的代码将读入管道输入并转换成命令行参数，每行一个:

```
# Copy command-line arguments over to new array
ARGS=( $@ )

# Read in from piped input, if present, and append to newly-created array
if [! -t 0]; then readarray STDIN_ARGS < /dev/stdin
  ARGS=( $@ ${STDIN_ARGS[@]} )
fi

# Single loop to process all arguments
for ARG in "${ARGS[@]}"; do echo "$ARG"
done 
```

我已经忘记我写了这个代码，所以不要问我它是做什么的。几个月后我再复制粘贴一次，别烦我！

注意，如果你愿意使用`xargs`(我也确保经常忘记如何使用它)，你可以支持管道输入

```
# This is way easier than what I just showed you
printf "item_one\nitem_two" | xargs my_script 
```

永别了，未来的自己。很高兴你小小的大脑保留了*冰冰宝贝*的重要歌词，而不是像`xargs`和 Bash 数组这样无用的东西。