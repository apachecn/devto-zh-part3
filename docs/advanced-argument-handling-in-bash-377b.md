# Bash 中的高级参数处理

> 原文：<https://dev.to/rpalo/advanced-argument-handling-in-bash-377b>

在上一篇文章中，我们介绍了在 Bash 脚本中处理参数的基础知识。但是这些基础知识只能让你到此为止。如果您想构建一个具有非常好的感觉、精致的用户界面、多种长短选项、标志和参数的脚本，该怎么办呢？本文将介绍一些以健壮、可维护、可读的方式处理这些更复杂情况的技术。

## 1。参数代换

有一整类特性可以增强您对参数的控制:需要它们、提供默认值、替代值以及修改所提供的值。都是采取`"${variable_name_with_funky_symbols}"`的形式。所有这些对于使用像`$1, $2, ...`这样的位置参数来说都非常方便，但是它们实际上可以在脚本中的任何地方用于任何变量。

### 1.1。必需值:`${1:?Error Message}`

它试图评估指定的变量，但是如果变量未设置*或为空*，它将退出脚本并显示提供的错误消息。退出代码将会失败。

```
name="${1:?"A name is required."}"
echo "Hello, $name!" 
```

Enter fullscreen mode Exit fullscreen mode

我见过一些脚本将这种技术与`:`命令结合起来，这是一种确保参数存在而不将其存储在命名变量中的方法，但它什么也不做。

```
# This script expects 3 side lengths of a triangle
: ${1:?"Missing side 1"}
: ${2:?"Missing side 2"}
: ${3:?"Missing side 3"}

if [[ "$1" -eq "$2" && "$2" -eq "$3" ]]; then echo "Equilateral Triangle"
fi 
```

Enter fullscreen mode Exit fullscreen mode

如果这让你感到焦虑，[这里有一些关于`:`命令的更多信息。](https://www.gnu.org/software/bash/manual/html_node/Bourne-Shell-Builtins.html#Bourne-Shell-Builtins)

> 这些操作中的大多数都有一种替代形式，不包括括号内的冒号:`${1?Error Message}`。这个版本的工作原理是一样的，但是如果变量是空的，只要它被设置，它就不会引发错误消息。这个版本通常不能提供你想要的安全性，但是我想我应该提到它。

### 1.2。默认值:`${1:-default}`

当您希望帮助新用户在默认情况下做出好的选择，并使更常见的情况变得更容易时，这是很好的。

```
name="${1:-World}"
echo "Hello, $name!" 
```

Enter fullscreen mode Exit fullscreen mode

```
$ ./greet "Ryan"
Hello, Ryan!

$ ./greet
Hello, World! 
```

Enter fullscreen mode Exit fullscreen mode

### 1.3。分配默认值:`${var:=value}`

非常类似于上面的默认值。不同之处在于，这会将值赋给那个变量——`:-`版本只做一次替换。

```
$ echo "${username:=supercoolfriend}"   # username was undefined before this
supercoolfriend
$ echo "$username"   # Now it's defined!  The value sticks!
supercoolfriend 
```

Enter fullscreen mode Exit fullscreen mode

有一点需要注意，因为您不能以正常的方式给位置参数赋值，所以您不能将这个方法与`$1, $2, ...`一起使用。

```
# This won't work
$1=banana
# So, neither will this
echo ${1:=banana} 
```

Enter fullscreen mode Exit fullscreen mode

我还没有看到一个真正令人信服的用例，所以如果你已经很好地使用了它，请告诉我！

### 1.4。可选值:`${1:+value}`

这是另一个我从未真正从中获益的例子。但是它*是*更有趣的操作之一，因为它说“如果提供了变量，那么忽略那个值，而使用我们的值。”

```
$ name="Ryan"
$ echo "${name:+Shaq}"
Shaq

$ unset name
$ echo "${name:+Shaq}"
# Empty string 
```

Enter fullscreen mode Exit fullscreen mode

### 1.5。还有更多！

上面的例子与处理变量有关，不管它们是否被提供，也不管它们是否有值。还有很多这样的变量/括号功能，但是它们更侧重于修改/操作现有的变量，所以我想我会把它们留到以后的“Bash 中的字符串操作”文章中。同时，每当我忘记确切的语法时，这个文档就是我的参考。

## 2。让我们开始吧

就其本身而言，`shift`并不是一个令人印象深刻的命令 <sup id="fnref1">[1](#fn1)</sup> ，但是我们打算将它与下面的一些想法结合起来，所以我认为现在介绍它会很好。在阅读了我的上一篇文章之后，你显然是一个使用位置参数的有经验的专家:`$1, $2, $3, ...`。没问题！

`shift`接受这些参数并删除第一个，*将其余的*下移一位。

```
echo "$# arguments: $1  $2  $3  $4"
# 4 arguments: nick nack patty whack

shift
# drop the first one.  Now we have "nack" "patty" "whack"

echo "$1"
# nack

shift 2
# drop two more!  Now we have "whack"

echo "$1"
# whack 
```

Enter fullscreen mode Exit fullscreen mode

## 3。处理选项

在某种程度上，您可能需要选项和标志来调整脚本的工作方式。Bash 提供了一个很好的名为`getopts`的内置命令，它为您提供了一个框架，用于定义哪些参数有参数，以及在出错时如何处理。它有一些权衡，因为它可能比普通的 ol' while/case 更不透明/隐含，并且灵活性稍差。因此，根据您的需要，您可以选择运行自己的参数处理循环。我将在这里展示这两个选项，以便您在需要时可以参考它们。

### 3.1。getopts

这里是一个`getopts`循环的基本布局。我将向您展示一个示例，然后逐一检查所有的位。

```
#!/usr/bin/env bash

function usage() {
  echo "Usage: $0 [-i] [-v*] [-h] [-f FILENAME] [-t TIMES] <word>"
}

VERBOSE=0
INTERACTIVE=  
FILENAME=memo.txt
TIMES=1

while getopts "ivhf:t:" opt; do
  case "$opt" in i) INTERACTIVE=1;;
  v) (( VERBOSE++ ));;
  h) usage && exit 0;;
  f) FILENAME="$OPTARG";;
  t) TIMES="$OPTARG";;
  esac
done shift $(( OPTIND - 1 ))

name=${1:?$( usage )}

if [[ "$TIMES" -le 0 ]]; then echo "TIMES must be a positive integer." >&2
  exit 1
fi

if [[ -n "$INTERACTIVE" ]]; then echo "Are you sure you want to party?"
  read -r -p"[yn] " answer
  if [[ "$answer" != y ]]; then echo "Exiting."
    exit 0
  fi
fi printf "We are being "
for (( i=0; i<VERBOSE; i++ )); do printf "very "
done printf "verbose.\n"

for (( i=0; i<TIMES; i++ )); do echo "Hello, $name!" >> $FILENAME
done echo "Complete!"
exit 0 
```

Enter fullscreen mode Exit fullscreen mode

咻！深呼吸。

#### 3.1.1。主选项循环

好吧！这里有很多内容要介绍，所以让我们开始吧。前几行是 setup，为我们将要用到的标志、变量和函数获取占位符。这个例子的肉在这里:

```
while getopts "ivhf:t:" opt; do
  case "$opt" in i) INTERACTIVE=1;;
  v) (( VERBOSE++ ));;
  h) usage && exit 0;;
  f) FILENAME="$OPTARG";;
  t) TIMES="$OPTARG";;
  esac
done shift $(( OPTIND - 1 ))

name=${1:?$( usage )} 
```

Enter fullscreen mode Exit fullscreen mode

在 while 循环中，我们调用`getopts`。`getopts`处理一个接一个传递的参数。`getopts`的第一个参数是一个字符串，它列出了我们期望的选项以及哪些选项需要参数。如果一个选项接受/需要一个参数，我们在它的字母后面放一个冒号。第二个参数是一个变量名，它将保存当前正在处理的字母选项。

#### 3.1.2。匹配选项

现在，我们来谈谈循环内部的 case 语句。

```
 case "$opt" in i) INTERACTIVE=1;;
  v) (( VERBOSE++ ));;
  h) usage && exit 0;;
  f) FILENAME="$OPTARG";;
  t) TIMES="$OPTARG";;
  esac 
```

Enter fullscreen mode Exit fullscreen mode

这是我们根据选项决定做什么的地方。注意`getopts`是如何为你去掉字母前的`-`的。案例陈述本身可能看起来令人生畏。有很多奇怪的语法，你在别的地方是看不到的。[看看这些例子，了解如何使用 case 语句。](https://bash.cyberciti.biz/guide/The_case_statement)

您还会注意到，在任何接受参数的选项中，该参数会自动存储在变量`$OPTARG`中。

在当前循环结束时，`getopts`将跳过与它在这一轮中使用的一样多的参数，但它*不会* `shift`它们。只要有更多的参数需要`getopts`去寻找(任何以单个破折号(`-`)开始的参数)，循环就会继续。一旦没有更多的选项，循环将结束，我们将看到下一行:

#### 3.1.3。`$OPTIND`的魔力

```
shift $(( OPTIND - 1 )) 
```

Enter fullscreen mode Exit fullscreen mode

`getopts`跟踪另一个名为`$OPTIND`的自动变量中要处理的下一个参数。(这些神奇的变量是我更喜欢使用自己的参数处理的一个原因，稍后会有更多的介绍。)所以，在解析完选项后，如果我们想继续任何常规参数，我们必须知道它们在列表中的起始位置！我们可以继续返回到`$OPTIND`获取信息，或者，通常，我们可以转移到目前为止已经处理的所有选项，也就是`$OPTIND - 1`参数。

> 长话短说，这一行允许我们继续我们的脚本，就像没有选项解析发生一样，其中`$1`是传递给脚本的第一个位置参数，依此类推。

#### 3.1.4。处理位置参数

你看我们在下一行处理这个位置论点:

```
name=${1:?$( usage )} 
```

Enter fullscreen mode Exit fullscreen mode

我们甚至使用了我们新的奇特的“必需参数”语法——有所改变！您知道可以将命令输出插入到错误消息中吗？你现在知道了。

脚本的其余部分是我们继续处理已经设置的任何标志或常量。我希望您注意的一件事是，您可以多次传递同一个标志，如`$VERBOSE`变量所示。

#### 3.1.5。尝试一下

这里有一个我们运行上面脚本的例子:

```
$ ./args_example -i -vvvv -t 3 -f banana.txt GLOOMP
Are you sure you want to party?
[yn] y
We are being very very very very verbose.
Complete!

$ cat banana.txt
Hello, GLOOMP!
Hello, GLOOMP!
Hello, GLOOMP! 
```

Enter fullscreen mode Exit fullscreen mode

因为我们提供了`-i`(交互式)选项，它会询问我们是否确定(我们稍后会用到`read`命令)。我们传递 4 个`-v`选项，所以我们变得*多余*冗长，正如你从输出中看到的。我们将默认次数改为 3，默认文件名改为`banana.txt`，最后我们将`GLOOMP`作为第一个真正的位置参数进行传递。

酷吗？酷毙了。那是`getopts`命令。

#### 3.1.6。处理错误

在前面的例子中，我们在你可能称之为“大声”的模式中使用了`getopts`。任何无法识别的选项或需要参数但没有得到参数的选项都将导致警告输出。除此之外，一切都会正常进行。

如果你想对输出有更多的控制，或者完全静音，你可以使用“静音”模式，在你的`getopts`选项字符串前面加一个冒号(`:`:

```
while getopts ":ivhf:t:" opt; do 
```

Enter fullscreen mode Exit fullscreen mode

如果该字符串以冒号开头，任何无法识别的输入都会导致`$opt`被设置为文字“？”，`$OPTARG`将被设置为发现的未知字符，不写输出。您可以通过在案例陈述中添加一个案例来解决这个问题:

```
 case "$opt" in
  # ...
  \?) echo "Invalid option $OPTARG" && exit 1;;
  esac 
```

Enter fullscreen mode Exit fullscreen mode

在这种“静默”报告模式下，如果您有一个需要参数的选项，但没有提供该参数，则使用“？”，则`$opt`将被设置为“: <sup id="fnref2">[2](#fn2)</sup> 和`$OPTARG`将被设置为缺少参数的选项字母。

```
 case "$opt" in
  # ...
  :) echo "Option '$OPTARG' missing a required argument." && exit 1;;
  esac 
```

Enter fullscreen mode Exit fullscreen mode

### 3.2。选择语句

哦，伙计，太多了。做得好，通过了`getopts`示例。站起来喘口气。四处走走。告诉你所有的朋友你现在是一个`getopts`老板有多酷。

回去工作。

很酷，但是 T2 也有不好的一面。

1.  它不能处理长选项。这是主要的。
2.  有很多魔法。从半神秘的 opts-string 到您是否处于“静默模式”，再到神奇的`$OPTARG`和`$OPTIND`变量，在某一点上，您可能会写出一个不错的脚本解析选项，但必须检查文档(或本文！)半年后再回来修改吧。

根据您的用例以及该脚本被读取/更新/修改的频率，*编写您自己的选项解析循环可能*有意义。

* * *

注意:互联网上的人对 Bash 和参数解析有强烈的意见。Bash 脚本对于那些放弃自行车的人来说是唾手可得的。

> 如果人们大声和/或糟糕地表达他们对你的选项处理的意见，不要让它使你沮丧。给他们打印一张大大的结肠图片，告诉他们你把他们设置为“静音”模式。

* * *

基本过程与`getopts`相同。不同的是，我们必须自己处理和转移。

为了强调这一点，让我们用自己的版本重写上面的例子！我*现在要*增加长期期权——因为我们可以！

```
#!/usr/bin/env bash

# ... Same setup code

while [[ "$1" == -* ]]; do
  case "$1" in
  -i|--interactive)
    INTERACTIVE=1
    ;;
  -v*)
    (( VERBOSE += ${#1} - 1 ))
    ;;
  --verbose)
    (( VERBOSE++ ))
    ;;
  -h|--help)
    usage
    exit 0
    ;;
  -f|--filename) 
    shift FILENAME="$1"
    ;;
  -t|--times) 
    shift TIMES="$1"
    ;;
  --)
    shift
    break
    ;;
  esac
  shift done

# ... The rest is the same 
```

Enter fullscreen mode Exit fullscreen mode

让我们来看看不同之处。希望您看到了自己循环的利弊:更加灵活，但是有一些更好的特性(将标志合并到一个 blob 中，有选项的参数，等等)。)，你必须多做一点工作，对你的用户多一点信心。

#### 3.2.1。新的循环条件

我们从顶部开始:

```
while [[ "$1" == -* ]]; do 
```

Enter fullscreen mode Exit fullscreen mode

因为我们必须驱动自己的环路，所以我们必须手动检查是否存在任何选项。这里我们使用“shell globbing”(一个更小、更轻便的 regex 版本)来检查第一个参数是否是一个破折号后面跟有任何字符。这一行也可以读作:

```
while [[ "$1" =~ ^- ]]; do 
```

Enter fullscreen mode Exit fullscreen mode

对于那些更熟悉 regex 的人来说，这可能看起来更熟悉一些。它说“如果第一个参数以破折号开头，那么将其作为一个选项来处理。”

如果你不想在你的选项后有任何额外的争论，我也见过有人用更简单的:

```
while [[ -n "$1" ]]; do 
```

Enter fullscreen mode Exit fullscreen mode

只要有一个参数需要处理，它就会处理，但是它不会检查参数是否以破折号开头。

#### 3.2.2。匹配选项

```
case "$1" in
  -i|--interactive) INTERACTIVE=1;; 
```

Enter fullscreen mode Exit fullscreen mode

到目前为止变化不大。我们直接匹配第一个位置参数。对我们来说不再有神奇的脱衣。但是，我们获得了做多头期权的能力！

#### 3.2.3。在单个仪表板上处理多个选项

```
 -v*) (( VERBOSE += ${#1} - 1 ));;
  --verbose) (( VERBOSE++ ));; 
```

Enter fullscreen mode Exit fullscreen mode

这就是我们的生活变得艰难的地方。当`getopts`无缝地为我们处理多个`v`选项时，我们必须记住自己用正确的 shell glob match 来处理它。由于这里的复杂性，我最终将多头和空头选项分成了多种情况。短选项匹配`-v`后面的任何字符。然后，它会将这个数字添加到`$VERBOSE`中。表面上，我们相信用户通常会做`-vvvv`，但是如果他们做了`-vslfjadsfjadklsfjsl`，我们的脚本仍然会允许。

如果这真的很麻烦，我们可以明智地使用`shopt -s extglob`来改进它。现在，如果你想的话，我会让你自己去研究。

#### 3.2.4。处理带参数的选项

```
 -f|--filename) 
    shift FILENAME="$1"
    ;; 
```

Enter fullscreen mode Exit fullscreen mode

这里，我们将`-f`或`--filename`选项`shift`去掉，然后将下一个参数*保存为实际的`$FILENAME`。这就是错误处理变得有点棘手的地方。如果你想确保他们为我们提供了一个文件名，我的建议是在主选项循环完成后做一些错误检查。您至少可以确保文件名参数不是以`-`开头，但是这样会阻止用户创建以破折号开头的文件名。也许这是你想要的，也许不是。这部分取决于你。*

但是，这个代码片段展示了多行形式的大小写匹配。如果你能避免它变得太长而不可读，你会看到如何在这里加入额外的逻辑。

我们为什么不在争论结束后也把它搁置一边呢？过一会儿你会看到循环的底部。

#### 3.2.5。争论结束

```
 --)
    shift
    break
    ;; 
```

Enter fullscreen mode Exit fullscreen mode

命令中包含一个表示标志选项结束的`--`选项是相当常见的。`getopts`在幕后为我们做这件事。在这里我们必须自己做。因为打破循环导致我们错过最后的`shift`，我们不得不手动`shift`这个`--`选项退出队列。

#### 3.2.6。完成循环

```
 esac shift 
```

Enter fullscreen mode Exit fullscreen mode

在每次迭代之后，我们必须假设我们找到了一个匹配，处理了它，并使用队列中的当前参数完成了任务。所以我们把它移开，循环继续尝试计算下一个。

#### 3.2.7。处理错误

在上面的代码中，我们根本没有真正处理错误。实际上有两种类型的错误:我们可能得到一个我们不期望的选项，一个选项可能期望一个它没有得到的参数。

对于我们没有预料到的选项，目前，我们的脚本让它们悄悄地过去。它读他们，他们不匹配，然后它把他们转移，没有伤害和犯规。那可能没问题。如果不是，主要的处理方法是添加一个包罗万象的案例:

```
 *) echo "Unrecognized option $1." && usage && exit 1;; 
```

Enter fullscreen mode Exit fullscreen mode

确保这是在底部。它将匹配任何案例(感谢 shell glob asterisk)，但是案例是从上到下进行评估的，没有遗漏，所以如果它在列表的底部，它将捕获我们还没有捕获/关心的所有案例，这正是我们想要的。

对于没有收到参数的选项，我们已经讨论过一点了。我建议在主循环的之后处理*。在我们当前的脚本中，它将捕获这个:* 

```
if [[ "$TIMES" -le 0 ]]; then echo "TIMES must be a positive integer." >&2
  exit 1
fi 
```

Enter fullscreen mode Exit fullscreen mode

如果`$TIMES` *的参数*不是一个数字，那么`-le`会自动将该值转换为一个零来进行比较。例如，如果我们跑

```
$ ./case_example -t -f test.txt garbanzo 
```

Enter fullscreen mode Exit fullscreen mode

`$TIMES`的值实际上是其后的`-f`。因为这不是一个数字，所以它被转换为零。零小于或等于零，所以我们的脚本将抛出一个错误。

如果零是您的特定用例的有效输入，这可能是一个问题。在这种情况下，正则表达式检查可能更好:

```
if ! [[ "$TIMES" =~ ^[[:digit:]]+$ ]]; then 
```

Enter fullscreen mode Exit fullscreen mode

### 3.3。比较

到目前为止，您已经看到了这两种方法。

有很多内置的优点，但是设置起来有点神秘，而且它不能处理长选项。在处理论点的方式上，就有点死板了。但有时这是件好事！

手工开发的解决方案增加了灵活性优势和长期选项，并且更易于阅读/立即理解。你对巴什的怪异之处不太了解。但是，像错误处理、自动移位和选项结束标志这样的好东西在默认情况下并没有包含在内。你必须确保他们得到处理和处理*正确*。如果事情变得*真的*复杂，这个过程会导致更多潜在的错误。

所以这要由你来决定，但是现在，至少当你想弄明白的时候，你有一个很好的参考资料可以查阅。

当然，如果您的应用程序很大/复杂/重要/缓慢，那么将这个脚本转换成您选择的更健壮、更强大、更快的语言可能是一个好主意。

但是，如果您想在 Bash 中实现它，至少现在您知道如何实现了！

## 4。期待更多

我想包含一个关于从 STDIN 读取和从管道接受输入的部分，但是当我完成了上面的部分，我的字数统计达到了 3400 字。因此，也许我们已经得到了足够的信息，我们将在下一篇文章中处理 STDIN。

我在这里得到的是一个很好的起点，也是我见过的人们使用的一系列不同方法的综合。不过，我确信有多少 shell 用户，就有多少方法论和很酷的用例。因此，如果你有一些我错过的处理输入和选项的巧妙方法，一定要和我分享！

快乐脚本！

* * *

1.  It *是*，然而，这是最容易输入错误并导致尴尬结果的命令。 [↩](#fnref1)

2.  这也让它成为*最幸福的*案例！耶！:) [↩](#fnref2)